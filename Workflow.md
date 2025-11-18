## 4. Functional Workflow (Filter Panel Module)

## Purpose

This module helps users discover travel destinations based on personal preferences and desired weather conditions. The system uses real-time weather data and destination metadata to suggest places that match the user’s ideal climate, terrain, mood, and travel month.

---

## 4.1 Step 1: User Input Capture

The user interacts with the Filter Panel on the homepage. They select preferences for their upcoming travel.

### Basic Filters (Visible by Default)

Displayed in a 2×2 grid.

| Filter      | Type         | Example Input                    |
| ----------- | ------------ | -------------------------------- |
| Month       | Dropdown     | December                         |
| Climate     | Dropdown     | Sunny / Rainy / Cold             |
| Temperature | Range Slider | 20°C – 30°C                      |
| Terrain     | Dropdown     | Beach / Mountain / City / Desert |

The user can click the "Advanced Filters" button to view more options.

### Advanced Filters (Shown on Click)

| Filter     | Type                                                | Example Input                    |
| ---------- | --------------------------------------------------- | -------------------------------- |
| Budget     | Two fields: number of travelers + per-person budget | 2 people, ₹20,000 per person     |
| Trip Type  | Dropdown                                            | Family, Business, Educational    |
| Activities | Multi-select                                        | Trekking, Scuba Diving, Shopping |
| Mood       | Multi-select                                        | Relaxing, Adventurous, Romantic  |

After selecting filters, the user clicks "Search for Destination".
All inputs are sent to the backend through an API request.

---

## 4.2 Step 2: Destination Filtering Logic

After receiving inputs:

1. The system retrieves destinations from the internal database.
   Each record contains:

   * City
   * Country
   * Best Time to Visit (Months)
   * Terrain Type
   * Mood and Activity Tags
   * Budget Range
   * Latitude and Longitude
   * Popularity Score or Editorial Tags

2. The system applies filtering rules:

   * Match user-selected month with "Best Time to Visit".
   * Match terrain type.
   * Compare user’s temperature range with average temperature.
   * Remove destinations outside the user’s budget.
   * If provided, match mood and activity tags.

The filtered list moves to the weather and ML scoring stage.

---

## 4.3 Step 3: Weather API Integration

For each shortlisted destination, the system calls the OpenWeatherMap API.

The API provides:

* Current temperature
* Forecast temperature
* Weather conditions (Sunny, Rainy, Snowy, etc.)

The system verifies whether real-time or forecasted conditions meet the user’s selected climate.

Only destinations matching the preferred weather proceed to the ranking stage.

---

## 4.4 Step 4: Result Display and User Actions

The system prepares a list of matching destinations and displays them as cards on the Result Page.

Each destination card includes:

* City name and image
* Weather summary (example: Sunny, 28°C)
* Short reason for recommendation
* Short description and activity highlights
* Buttons:

  * View More (destination details)

---

## 4.5 User Actions (View More, Book Now)

The user may click "View More" to see additional details:

* Best time to visit
* Full weather calendar
* Popular activities
* Photos

A "Book Now" button is displayed on the detailed page.
This redirects the user to a booking page for hotels or flights.

Users may return to the filter panel to update selections and search again.

---
End of document.
---
