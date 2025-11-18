## 6. Backend Logic

The backend of the Weather-Based Destination Recommendation System is responsible for processing user inputs, querying the internal database, interacting with the OpenWeatherMap API, preparing feature vectors, applying machine learning scoring, and returning structured recommendations to the frontend.

---

## 6.1 Input Collection

Purpose: Capture and validate user preferences from the frontend filter panel.

How it works:

1. User selects filters such as month, climate, temperature range, terrain, budget, activities, and mood.
2. Frontend sends a POST request to the backend.

Example Request:

```
POST /api/recommendations
Content-Type: application/json

{
  "month": "December",
  "climate": "Sunny",
  "temperature_range": [20, 30],
  "terrain": "Beach",
  "budget_range": "Mid",
  "activities": ["Scuba Diving", "Relaxing"],
  "mood": ["Romantic"]
}
```

Validation Layer:

* Checks for missing or conflicting parameters.
* Sanitizes user input.
* Converts dropdown selections into internal enums.

Temporary Storage:

* Validated input may be stored in a session-level cache for the processing cycle.

---

## 6.2 Destination Metadata Structure

Each destination in the internal database contains detailed metadata used for filtering and scoring.

Example Schema:

```
{
  "city": "Goa",
  "country": "India",
  "terrain": "Beach",
  "best_time": ["November", "December", "January"],
  "activities": ["Relaxing", "Cultural"],
  "mood_tags": ["Romantic", "Adventurous"],
  "budget_range": "Mid",
  "latitude": 15.2993,
  "longitude": 74.1240,
  "popularity_score": 85,
  "average_cost": 18000,
  "images": ["goa_beach.jpg", "sunset_view.jpg"]
}
```

---

## 6.3 Fetch Weather Data

The backend sends a request to the OpenWeatherMap API for each destination using its coordinates.

Example API Call:

```
https://api.openweathermap.org/data/2.5/forecast?q=Goa&appid=YOUR_API_KEY&units=metric
```

Returned Data:

* Forecast temperature (min/max)
* Weather condition (Clear, Rain, Snow, etc.)
* Forecast timestamps

---

## 6.4 Match Filters and Prepare Feature Vectors

The backend compares destination metadata with user preferences:

* Check if weather condition matches user-selected climate.
* Check if forecasted temperature falls in user’s range.
* Match terrain type.
* Match mood and activity tags.
* Compare budget range with average destination cost.

Each destination is converted into a feature vector for ML scoring.

---

## 6.5 Scoring Logic

A trained XGBoost model assigns a relevance score (0–1) to each destination based on how well it matches the user’s preferences.

### Scoring Steps

1. Fetch weather data.
2. Compare metadata attributes.
3. Encode attributes into a numerical feature vector.
4. Pass vector to ML model.
5. Receive a score (e.g., 0.95 → 95%).

### Scoring Weight Distribution

| Criteria              | Weight |
| --------------------- | ------ |
| Weather Match         | 35%    |
| Terrain Match         | 15%    |
| Budget Fit            | 15%    |
| Mood & Activity Match | 20%    |
| Popularity & Tags     | 15%    |

### Example: Goa

User Preferences:

* Month: December
* Climate: Sunny
* Temperature: 20–30°C
* Terrain: Beach
* Mood: Relaxing
* Activity: Scuba Diving
* Budget: ₹20,000 per person

Destination Data:

* Weather: Sunny, 28°C → match
* Terrain: Beach → match
* Budget: ₹18,000 → match
* Tags: Relaxing, Scuba Diving → match
* Popularity Score: 85 → strong match

Final Score:

```
0.95 (95%)
```

---

## 6.6 API Response Format

Once scoring and ranking are complete, a structured JSON response is sent to the frontend.

Example Response:

```
{
  "status": "success",
  "query_id": "REQ_2025_10_30_12345",
  "recommendations": [
    {
      "city": "Goa",
      "country": "India",
      "weather": "Sunny",
      "temperature": "28°C",
      "score": 0.95,
      "reason": "Matches your mood, climate, and activity filters",
      "tags": ["Relaxing", "Romantic"],
      "image": "goa_beach.jpg"
    },
    {
      "city": "Bali",
      "country": "Indonesia",
      "weather": "Sunny",
      "temperature": "27°C",
      "score": 0.92,
      "reason": "Great for beach lovers and scuba diving",
      "tags": ["Adventure", "Cultural"],
      "image": "bali_temple.jpg"
    }
  ]
}
```

---

# 7. Machine Learning Model

## 7.1 Model Overview

The machine learning model evaluates how well each destination matches the user’s preferences. It considers:

* Real-time weather data
* Metadata (terrain, mood, activities, budget)
* User-selected filters
* (Future) Historical user behavior

The model outputs a score for each destination.

---

## 7.2 How It Improves Personalization

The model adapts to user behavior patterns.

Examples:

* If a user prefers "Relaxing" destinations, future scores prioritize that tag.
* If a user consistently chooses warm climates, heat-related factors get more weight.

Over time, the model can incorporate:

* Click behavior
* Save/book actions
* Browsing patterns

---

## 7.3 Algorithm Choice: XGBoost

Reasons for selecting XGBoost:

* High predictive accuracy
* Fast inference for real-time recommendations
* Scales efficiently
* Offers feature importance insights
* Handles missing or skewed data well

---
End of document.
---

