# PAM Native Health

Health Connect 1.1 and HealthKit access for steps, heart rate, weight, active calories and sleep. Authorization is granular and requested only for enum types supplied by the app.

HealthKit intentionally does not reveal denied read access, so the API exposes `needsAuthorization()` rather than claiming a read permission is granted. Empty reads may mean no data or no accessible data. Treat all health data as sensitive, minimize retention, and never log samples by default.

## Install

```bash
composer require pushinbr/pam-native-health
pam mobile prepare
```

PAM Native autolinks Health Connect on Android and HealthKit, usage descriptions, and entitlements on iOS. The application still controls when the system authorization UI appears.

## Read health data

```php
use Pam\Native\Health\Health;
use Pam\Native\Health\HealthDataType;

$health = new Health();
$types = [HealthDataType::Steps, HealthDataType::HeartRate];

$health->requestAuthorization($types, [], function (bool $granted, ?string $error) use ($health): void {
    if (!$granted) {
        return;
    }

    $health->read(
        HealthDataType::Steps,
        (time() - 86400) * 1000,
        time() * 1000,
        fn (array $samples) => renderDailySteps($samples),
    );
});
```

Supported typed data includes steps, heart rate, weight, active calories, and sleep. Test authorization denial and empty-result behavior on physical devices before shipping.
