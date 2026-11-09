# Reference

Moved out of the README; nothing was removed.

> The current install path is `pam composer require pushinbr/pam-native-health` followed by `pam doctor --fix`; `pam add` below is the legacy alias.

## Install

```bash
pam add health
pam doctor
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

## What installation does

`pam add health` resolves the official compatible package, performs a non-mutating Composer preflight, updates the normal `composer.json` and `composer.lock`, refreshes generated native integration when required, and leaves the project ready for `pam doctor` validation.

Use `pam packages` to inspect availability and `pam remove health` to uninstall the capability safely. Direct Composer commands are an advanced interoperability path; PAM is the supported application workflow.

## API guide

| API | Responsibility |
| --- | --- |
| `Health` | Check availability, request authorization, read, and write samples. |
| `HealthDataType` | Typed steps, heart rate, weight, calories, and sleep identifiers. |
| `HealthSample` | Normalized timestamped health value. |
| `HealthAvailability` | Typed platform availability result. |

All coded states, kinds, and variants are sequential integer-backed enums. Use enum cases in application code; do not depend on raw wire numbers.

## Production checklist

- Request only the data types required by the current feature.
- Explain the value before opening the operating-system permission sheet.
- Minimize retention and exclude health values from logs and analytics.
- Run `pam doctor`, `pam test`, and a signed release build on every supported platform.
- Exercise denial, cancellation, backgrounding, process restart, and offline behavior before release.

## Troubleshooting

- **Reads are empty on iOS:** HealthKit does not reveal denied read access.
- **Authorization UI does not appear:** verify platform entitlements and usage descriptions.
- **Simulator data differs from devices:** certify the complete flow on physical hardware.
- **Native integration is stale:** run `pam doctor --fix`, rebuild the native host, and inspect the first reported diagnostic.

