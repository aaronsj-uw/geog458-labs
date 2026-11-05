# Lab 5: San Francisco Bay Vessel Traffic

Aaron Jacowitz, GEOG 458

[aaronjacowitz_Lab5.html](aaronjacowitz_Lab5.html) animates one day of AIS vessel traffic in central San Francisco Bay with deck.gl's TripsLayer. Readers can filter by vessel type (Passenger, Tanker, Cargo, Other vessels), stop and resume playback, and move to any moment with a time slider. The current data time is shown in Pacific time (PDT).

## Study area and dates

**Region.** Central San Francisco Bay, requested as the bounding box longitude -122.521 to -122.224 and latitude 37.637 to 37.815. The box runs from just inside the Golden Gate and Ocean Beach east to Oakland and Alameda, south to about South San Francisco, and north to about the Golden Gate Bridge and Treasure Island.

**Dates.** July 11, 2025 (start and end date both 2025-07-11). AccessAIS timestamps are in UTC, so this request covers 00:00 to 23:59 UTC on July 11, which is **5:00 pm PDT on July 10 to 4:59 pm PDT on July 11**. All clock times below are PDT.

**Source.** U.S. vessel traffic from [AccessAIS](https://marinecadastre.gov/accessais/), part of the BOEM, NOAA, and U.S. Coast Guard MarineCadastre partnership (request 179391411000677844). I converted the CSV to JSON with the class Colab notebook.

**Why this region.** I wanted a place with a very different mix of traffic than the Puget Sound practice data, in the same Pacific time zone. Central San Francisco Bay has a busy commuter ferry network, the Port of Oakland's container terminals, and designated anchorages where large ships wait, so all four filters have something to show. Keeping the same time zone also meant the date and time display could stay on `America/Los_Angeles`.

**Why this date.** A single summer weekday captures a full daily cycle of ferry service without making the file large. July 11, 2025 is a Friday, so it includes regular weekday service plus the start of weekend travel.

**Keeping it manageable.** I limited the request to one day and a tight box around the central bay instead of the whole Bay Area, and checked the estimated size before ordering. The CSV had 54,146 rows. The notebook removed 17 duplicate MMSI and time pairs, leaving 54,129 observations for 222 vessels. The map skips 3 vessels with only one position, so it draws 219 paths.

## Interpretation

Path counts by filter: **Passenger 39, Tanker 12, Cargo 20, Other vessels 148.**

**Passenger vessels are the most active and most spread out.** Passenger paths cross the bay all day in repeated corridors connecting San Francisco's waterfront with Oakland, Alameda, and South San Francisco. The same lines are traced over and over, which looks like scheduled routes rather than one-off trips. The busiest stretch I saw starts around **12:30 PDT on July 11**, when many passenger trails are moving at once across several routes. These corridors match the terminals served by [San Francisco Bay Ferry](https://sanfranciscobayferry.com/routes), which runs routes between San Francisco and Oakland, Alameda, and South San Francisco. The midday increase is my observation from the map. A possible explanation is added Friday midday and leisure service heading into the weekend, but I did not confirm it against the ferry schedule. Also, because the request ends at 4:59 pm PDT on July 11, the map cannot show that day's evening commute.

**Tankers mostly sit still.** The Tanker filter shows very little movement. Several tankers stay in one spot in the middle of the bay for the whole day, so their "paths" barely change position. This fits how the bay is used: [KQED describes Anchorage 9](https://www.kqed.org/news/12092878/welcome-to-anchorage-9-the-giant-ship-parking-lot-you-pass-every-day), between the Bay Bridge and the San Mateo Bridge, as the bay's largest anchorage, where ships wait for a berth, repairs, or fuel. The stationary tankers are an observed pattern. That they are waiting at anchorage is a likely explanation based on that source, not something the AIS data states. My box also stops south of the Richmond refineries, which probably explains why so few tankers appear at all.

**Cargo ships mostly wait near Oakland ports, with occasional trips to San Francisco.** Most cargo paths are short or nearly stationary around Oakland port areas rather than in the middle of the bay. A few times during the day, a cargo ship comes in from the ocean through the Golden Gate and heads across the bay toward Oakland, which is home to the Port of Oakland's container terminals. These long, single trips look very different from the repeated ferry lines.

**"Other vessels" is the largest group and is concentrated between San Francisco and Oakland.** With 148 paths, Other vessels outnumber the other three groups combined. Most of this movement runs between San Francisco and Oakland, with occasional routes south toward South San Francisco. This group mixes many vessel types (tugs, pleasure craft, sailing and fishing vessels, and vessels with missing type codes), so I can't attribute the pattern to one kind of boat. One possible explanation for the SF to Oakland concentration is tugs and service vessels supporting the port, but the map alone can't confirm that.

**What the controls made easier to see.** Filtering was essential. With all 219 paths shown together, the busy Other and Passenger traffic hides the few tanker and cargo movements. Stopping the animation and dragging the slider made it possible to tell whether a ship was truly stationary or just slow, and to find the 12:30 PDT increase in ferry traffic.

## Feature proposal for a potential user

**User and need.** A service planner at San Francisco Bay Ferry who wants to know when the central bay is most crowded with passenger vessels, so they can adjust schedules and avoid conflicts at busy terminals.

**Resource reviewed.** [kepler.gl Time Playback guide](https://docs.kepler.gl/docs/user-guides/h-playback). kepler.gl's time playback shows a small histogram of how many records fall in each time period, right above the time slider, and lets the user choose a time window instead of a single moment.

**Proposed feature.** Add a **time distribution chart above the slider** that shows the number of active vessels in the selected category for each 15-minute period, plus the option to drag a **time window** (for example, 12:00 to 14:00 PDT) instead of a single instant. When the planner picks Passenger, the chart would immediately show the busiest periods, like the increase around 12:30 PDT, without having to watch the whole animation. Selecting a window would draw every passenger path in that period at once, making it easy to compare routes during peak and off-peak times.

**Why it would help.** Right now, the map shows only the last hour of movement at the current moment, so a user has to watch carefully to notice when traffic peaks. The kepler.gl guide showed me that pairing a slider with a summary of the time distribution turns playback into a question-answering tool: the chart answers "when is it busiest," and the map answers "where is it busy at that time."
