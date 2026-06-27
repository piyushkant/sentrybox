# SentryBox - PLAN.md

A motion-triggered smart camera system built on ESP32-CAM + AWS serverless.
Level 2 project after Stashbox. Uses everything learned there plus IoT and hardware.

## App / Bundle ID (all platforms)
`io.github.kantpiyush.sentrybox`

## Tech Stack

| Layer | Technology |
|-------|-----------|
| Hardware | ESP32-CAM (MicroPython) |
| Backend | Kotlin + Spring Boot 3.x, Java 21 |
| Database | PostgreSQL via Spring Data JPA (AWS RDS free tier) |
| Cloud | AWS S3, Lambda, Rekognition, RDS, IoT Core, SNS |
| Frontend | Vue 3 |
| CI/CD | GitHub Actions |
| Mobile | KMP (Android + iOS) |

## Prerequisites
- Complete Stashbox (Level 1) first, this project reuses all those skills
- ESP32-CAM hardware kit (Freenove)
- AWS account with personal profile configured (`aws-p`)

---

## Phase 0 - Hardware Basics
**Goal:** Prove the hardware works before touching the cloud.

- Flash MicroPython onto ESP32-CAM
- Blink onboard LED
- Read a button press
- Capture a test image and print confirmation to serial
- Set up local dev environment (Thonny or VS Code + MicroPython extension)

## Phase 1 - Motion Detection + Local Capture
**Goal:** Standalone hardware pipeline working end to end, no cloud yet.

- Wire PIR motion sensor to ESP32-CAM GPIO
- On motion trigger -> capture image -> save to ESP32 flash
- Serial log showing: timestamp, trigger count
- Test reliability: does it catch motion consistently?

## Phase 2 - WiFi + S3 Upload
**Goal:** Hardware talking to AWS.

- Connect ESP32-CAM to WiFi
- On motion -> capture image -> upload to S3 bucket via HTTPS
- S3 bucket config: private, lifecycle policy (auto-delete after 30 days)
- IAM role with least-privilege S3 write-only policy for the device
- Verify image appears in S3 console after trigger

## Phase 3 - Lambda + Rekognition
**Goal:** Serverless AI pipeline working end to end.

- S3 PutObject event triggers Lambda function (Python or Kotlin)
- Lambda calls AWS Rekognition to detect labels, objects, faces
- Results written to RDS PostgreSQL (event table: id, timestamp, s3_key, labels JSON)
- CloudWatch logs for Lambda debugging

## Phase 4 - Spring Boot Backend API
**Goal:** Clean API layer over the data, same pattern as Stashbox.

- REST API endpoints:
  - `GET /events` - paginated event history
  - `GET /events/{id}` - single event with labels
  - `GET /events/latest` - most recent event
  - `GET /events/{id}/image` - presigned S3 URL (no public bucket)
- Deployed to AWS Elastic Beanstalk (free tier, single instance)
- Spring profiles: local (H2) + prod (RDS)

## Phase 5 - Vue Dashboard
**Goal:** Usable web UI for monitoring.

- Live event feed (polling every 5s or WebSocket)
- Event card: thumbnail, timestamp, Rekognition labels as tags
- Filter by label (e.g. show only events containing "Person")
- Hosted on S3 static site or Elastic Beanstalk

## Phase 6 - AWS IoT Core
**Goal:** Learn the proper way to connect IoT devices to AWS.

- Replace direct HTTP from ESP32 with MQTT via AWS IoT Core
- Provision device certificate + policy in IoT Core
- Device publishes to topic: `sentrybox/events`
- IoT Rule routes message to Lambda (replaces S3 trigger from Phase 3)
- Understand why IoT Core vs direct S3 (device management, auth, rules engine)

## Phase 7 - GitHub Actions CI/CD
**Goal:** Automated deploy on push, same pattern as Stashbox Phase 7.

- Build + test Spring Boot backend
- Deploy to Elastic Beanstalk on merge to main
- Separate workflows for backend and frontend

## Phase 8 - KMP Mobile App
**Goal:** Full mobile client, reusing KMP skills from Stashbox.

- View live event feed + image thumbnails
- Event detail screen: full image + Rekognition labels
- Push notification on motion detection (AWS SNS -> APNs/FCM)
- Shared KMP business logic (networking, models) between Android + iOS

---

## AWS Services Used (course mapping)

| Service | Phase | Why |
|---------|-------|-----|
| S3 | 2 | Image storage, static hosting |
| Lambda | 3 | Serverless image processing trigger |
| Rekognition | 3 | Object and face detection |
| RDS (PostgreSQL) | 3 | Event persistence |
| Elastic Beanstalk | 4 | Backend deploy |
| IoT Core | 6 | Proper IoT device connectivity |
| SNS | 8 | Push notifications |
| CloudWatch | 3+ | Logs and monitoring |

---

## Project Status

- [ ] Phase 0 - Hardware Basics
- [ ] Phase 1 - Motion + Local Capture
- [ ] Phase 2 - WiFi + S3 Upload
- [ ] Phase 3 - Lambda + Rekognition
- [ ] Phase 4 - Spring Boot Backend API
- [ ] Phase 5 - Vue Dashboard
- [ ] Phase 6 - AWS IoT Core
- [ ] Phase 7 - GitHub Actions CI/CD
- [ ] Phase 8 - KMP Mobile App

**Start date:** After Stashbox is complete.
