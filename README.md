# PAM Native Health

Health Connect 1.1 and HealthKit access for steps, heart rate, weight, active calories and sleep. Authorization is granular and requested only for enum types supplied by the app.

HealthKit intentionally does not reveal denied read access, so the API exposes `needsAuthorization()` rather than claiming a read permission is granted. Empty reads may mean no data or no accessible data. Treat all health data as sensitive, minimize retention, and never log samples by default.
