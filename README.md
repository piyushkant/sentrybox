# SentryBox

A motion-triggered smart camera system. When movement is detected, an ESP32-CAM captures an image and uploads it to AWS S3. From there, AWS Rekognition analyzes the image and the results show up in a web dashboard and mobile app.

**Status:** Planned. Starting after [Stashbox](https://github.com/piyushkant/stashbox) is complete.

## Tech Stack

- **Hardware:** ESP32-CAM (MicroPython)
- **Backend:** Kotlin + Spring Boot 3, PostgreSQL (AWS RDS)
- **Cloud:** AWS S3, Lambda, Rekognition, IoT Core, SNS
- **Frontend:** Vue 3
- **Mobile:** Kotlin Multiplatform (Android + iOS)
- **CI/CD:** GitHub Actions

## How it works

```
ESP32-CAM detects motion
    -> captures image
    -> sends to AWS IoT Core (MQTT)
    -> triggers Lambda
    -> Rekognition analyzes image
    -> results saved to RDS
    -> visible via Spring Boot API
    -> Vue dashboard and KMP mobile app
```

## Plan

See [PLAN.md](PLAN.md) for the full phase-by-phase roadmap.

## Bundle ID

`io.github.kantpiyush.sentrybox`
