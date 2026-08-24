<!-- pam:product-page:start -->
<div align="center">

# PAM Native Health

**One strict PHP contract for Health Connect and HealthKit.**

Request scoped authorization and read or write health records through privacy-conscious, lifecycle-safe native operations.

[![Latest version](https://img.shields.io/packagist/v/pushinbr/pam-native-health?style=flat-square&label=stable)](https://packagist.org/packages/pushinbr/pam-native-health)
[![CI](https://img.shields.io/github/actions/workflow/status/push-in/pam-native-health/ci.yml?branch=main&style=flat-square&label=CI)](https://github.com/push-in/pam-native-health/actions)
![PHP](https://img.shields.io/badge/PHP-8.5-777BB4?style=flat-square&logo=php&logoColor=white)
![Android](https://img.shields.io/badge/Android-API%2026%2B-3DDC84?style=flat-square&logo=android&logoColor=white)
![iOS](https://img.shields.io/badge/iOS-15%2B-000000?style=flat-square&logo=apple&logoColor=white)

**[Documentation](https://push-in.github.io/pam-docs/native/overview/) · [Quick start](#quick-start) · [What you can build](#what-you-can-build) · [PAM ecosystem](https://push-in.github.io/pam-docs/ecosystem/) · [Issues](https://github.com/push-in/pam-native-health/issues)**

</div>

---

## Why PAM Native Health

Request scoped authorization and read or write health records through privacy-conscious, lifecycle-safe native operations. The public API is strictly typed for PHP 8.5; expensive or frame-sensitive work stays in Rust or the platform SDK instead of crossing the application boundary every frame.

| | |
| --- | --- |
| **Best for** | A focused capability you can add to any PAM Native application |
| **Native path** | Health Connect · HealthKit |
| **Application model** | Composer package + generated native integration |
| **Design rule** | Independent module; no feed, vertical, or application template bundled |

## What you can build

- Fitness and recovery applications
- Connected wellness devices and dashboards
- User-consented health data synchronization

## Quick start

Already have a PAM Native project? Add only this capability:

```bash
pam composer require pushinbr/pam-native-health
pam doctor --fix
```

New to PAM? Follow the **[five-minute PAM Native setup](https://push-in.github.io/pam-docs/native/overview/)** once, then return here. Your application stays a normal Composer project with a committed lockfile.
<!-- pam:product-page:end -->

## See it in action

Health Connect 1.1 and HealthKit access for steps, heart rate, weight, active calories and sleep. Authorization is granular and requested only for enum types supplied by the app.

HealthKit intentionally does not reveal denied read access, so the API exposes `needsAuthorization()` rather than claiming a read permission is granted. Empty reads may mean no data or no accessible data. Treat all health data as sensitive, minimize retention, and never log samples by default.

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

## Compatibility and support

This package targets PAM Native `0.8.x`, Android API 26+, and iOS 15+ unless a platform-specific section above states a stricter requirement. Platform SDKs, credentials, entitlements, physical hardware, and store configuration remain application responsibilities.

- [PAM documentation](https://push-in.github.io/pam-docs/introduction/)
- [PAM Native overview](https://push-in.github.io/pam-docs/native/overview/)
- [Plugin and native capability model](https://push-in.github.io/pam-docs/native/plugins/)
- [Report an issue](https://github.com/push-in/pam-native-health/issues)

Security vulnerabilities should be reported through the repository security policy or GitHub private vulnerability reporting, not a public issue.
