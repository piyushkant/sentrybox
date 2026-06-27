# SentryBox

A motion-triggered smart camera system. When movement is detected, ESP32-CAM captures an image, uploads it to AWS S3, and an AI pipeline (AWS Rekognition) analyzes it — results visible in a web dashboard and mobile app.

**Status:** Planned — starting after [Stashbox](https://github.com/piyushkant/stashbox) is complete.

## Tech Stack

- **Hardware:** ESP32-CAM (MicroPython)
- **Backend:** Kotlin + Spring Boot 3, PostgreSQL (AWS RDS)
- **Cloud:** AWS S3, Lambda, Rekognition, IoT Core, SNS
- **Frontend:** Vue 3
- **Mobile:** Kotlin Multiplatform (Android + iOS)
- **CI/CD:** GitHub Actions

## Architecture

```
ESP32-CAM → motion detected
    → capture image
    → AWS IoT Core (MQTT)
    → Lambda
    → Rekognition (AI labels)
    → RDS (store event)
    → Spring Boot API
    → Vue dashboard / KMP mobile app
```

## Plan

See [PLAN.md](PLAN.md) for the full phase-by-phase roadmap.

## Bundle ID

`io.github.kantpiyush.sentrybox`
