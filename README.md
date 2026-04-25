# UTMSport 🏸

A Flutter mobile application for managing sports court bookings and events at Universiti Teknologi Malaysia (UTM). UTMSport provides a unified platform for students, event organizers, and administrators to book courts, manage sporting events, and oversee user accounts — all backed by Firebase.

---

## Table of Contents

- [Features](#features)
- [Tech Stack](#tech-stack)
- [Architecture](#architecture)
- [Supported Sports & Courts](#supported-sports--courts)
- [User Roles](#user-roles)
- [Project Structure](#project-structure)
- [Getting Started](#getting-started)
- [Firebase Setup](#firebase-setup)
- [Running the App](#running-the-app)
- [Screenshots](#screenshots)
- [Contributing](#contributing)
- [License](#license)

---

## Features

### Student
- Register and log in with email/password via Firebase Auth
- Browse and book sports courts by sport, date, time slot, and court number
- View upcoming and past reservations with full details
- Check in to confirmed bookings
- Register for published sporting events
- Edit profile information and upload a profile photo

### Organizer
- Create, edit, and publish sporting events
- Set event name, description, sport type, date, time slots, courts, max participants, and registration fee
- Cancel events and view participant lists
- Track event status (Draft → Published → Completed / Cancelled)

### Admin
- Dashboard with overview statistics and charts (`fl_chart`)
- View and manage all user accounts (Students, Organizers)
- Filter and search users by role
- Change user roles directly from the dashboard
- Manage all court bookings across the platform

---

## Tech Stack

| Layer | Technology |
|---|---|
| Framework | Flutter 3.x (Dart) |
| Authentication | Firebase Authentication |
| Database | Cloud Firestore |
| File Storage | Firebase Storage |
| State Management | Provider + ValueNotifier (MVVM) |
| Payments | Stripe (`flutter_stripe`) |
| Charts | fl_chart |
| QR Codes | qr_flutter |
| Image Handling | image_picker, cached_network_image |
| HTTP Client | Dio |
| Internationalisation | intl |

---

## Architecture

UTMSport follows the **MVVM (Model-View-ViewModel)** pattern:

```
lib/
├── models/          # Data classes (e.g. User)
├── viewmodels/      # Business logic, Firebase calls, ValueNotifiers
└── views/           # UI widgets and screens, split by role
```

Views listen to ViewModels via `ValueNotifier` and `Provider`, keeping business logic out of the widget tree. Firebase interactions (Firestore reads/writes, Auth) live exclusively in ViewModels.

---

## Supported Sports & Courts

| Sport | Courts Available | Time Slots |
|---|---|---|
| Badminton | 11 | 8:00–10:00, 10:00–12:00, 14:00–16:00, 16:00–18:00 |
| Ping Pong | 5 | 9:00–11:00, 13:00–15:00 |
| Volleyball | 4 | 10:00–12:00, 14:00–16:00 |
| Squash | 3 | 8:00–9:00, 9:00–10:00, 16:00–17:00 |

---

## User Roles

The app supports three roles, determined at login from the user's Firestore document:

| Role | Home Screen | Key Capabilities |
|---|---|---|
| `student` | `StudentPage` | Book courts, register for events, manage reservations |
| `organizer` | `OrganizerPage` | Create & manage events, view participants |
| `admin` | `AdminDashboard` | Full user & booking management, analytics |

After authentication, the `LoginViewModel` reads the `role` field from Firestore and navigates to the appropriate screen automatically.

---

## Project Structure

```
utmsport/
└── flutter_application_1/
    ├── android/                    # Android-specific config & build files
    ├── ios/                        # iOS-specific config
    ├── assets/
    │   └── images/                 # Court images and background assets
    │       ├── BadmintonCourt.jpeg
    │       ├── BasketballCourt.jpeg
    │       ├── FutsalCourt.jpeg
    │       ├── TennisCourt.jpeg
    │       └── background.png
    ├── lib/
    │   ├── main.dart               # App entry point, Firebase init, routing
    │   ├── models/
    │   │   └── user_model.dart     # User data class
    │   ├── viewmodels/
    │   │   ├── booking_viewmodel.dart   # Court booking logic
    │   │   ├── event_viewmodel.dart     # Event creation/management logic
    │   │   ├── login_viewmodel.dart     # Auth & role resolution
    │   │   └── register_viewmodel.dart  # New user registration
    │   └── views/
    │       ├── user_auth/
    │       │   ├── login_page.dart
    │       │   └── register_page.dart
    │       ├── student/
    │       │   ├── student_page.dart         # Student home & tabs
    │       │   └── user_manage_account.dart
    │       ├── organizer/
    │       │   ├── organizer_page.dart       # Organizer home & tabs
    │       │   ├── event_booking.dart        # Create/edit event form
    │       │   └── event_details.dart        # Event detail view
    │       ├── admin/
    │       │   ├── admin_page.dart           # Admin dashboard
    │       │   └── user_account_management.dart
    │       ├── court_booking.dart            # Court selection & booking form
    │       ├── booking_management.dart       # Admin booking overview
    │       ├── reservations.dart             # Student reservations list
    │       ├── reservation_details.dart      # Individual reservation detail
    │       └── edit_profile_page.dart        # Profile edit with photo upload
    ├── pubspec.yaml
    └── analysis_options.yaml
```

---

## Getting Started

### Prerequisites

Make sure you have the following installed:

- [Flutter SDK](https://docs.flutter.dev/get-started/install) `^3.5.3`
- Dart `^3.5.3`
- Android Studio or Xcode (for device/emulator)
- A Firebase project (see [Firebase Setup](#firebase-setup))

Verify your Flutter installation:

```bash
flutter doctor
```

### Clone the Repository

```bash
git clone https://github.com/Mohamedarshathsaleem/utmsport.git
cd utmsport/flutter_application_1
```

### Install Dependencies

```bash
flutter pub get
```

---

## Firebase Setup

UTMSport uses Firebase for authentication, database, and storage. You need to connect your own Firebase project.

1. Go to the [Firebase Console](https://console.firebase.google.com/) and create a new project.

2. Enable the following services:
   - **Authentication** → Email/Password sign-in
   - **Cloud Firestore** → Start in test mode (configure rules for production)
   - **Firebase Storage** → For profile image uploads

3. Register your app:
   - **Android**: Download `google-services.json` and place it in `android/app/`
   - **iOS**: Download `GoogleService-Info.plist` and place it in `ios/Runner/`

4. Set up your Firestore collections:

   **`users` collection** (one document per user, keyed by UID):
   ```json
   {
     "name": "string",
     "email": "string",
     "role": "student | organizer | admin",
     "profileImage": "string (Storage URL)"
   }
   ```

   **`bookings` collection**:
   ```json
   {
     "userId": "string",
     "email": "string",
     "sport": "string",
     "court": "number",
     "date": "timestamp",
     "timeSlot": "string",
     "paxCount": "number",
     "status": "Pending | Confirmed | Checked-In | Cancelled"
   }
   ```

   **`event` collection**:
   ```json
   {
     "eventName": "string",
     "description": "string",
     "sport": "string",
     "date": "timestamp",
     "timeSlots": ["string"],
     "courts": ["number"],
     "maxParticipants": "number",
     "registrationFee": "number",
     "status": "Draft | Published | Completed | Cancelled",
     "organizerId": "string",
     "registeredParticipants": [{ "uid": "string", "email": "string", "registeredAt": "string" }]
   }
   ```

5. (Optional) Configure **Stripe** for payment processing:
   - Add your Stripe publishable key where `flutter_stripe` is initialised.
   - Set up a backend endpoint (e.g., Firebase Cloud Functions) to create PaymentIntents.

---

## Running the App

### Android

```bash
flutter run
```

Or to build an APK:

```bash
flutter build apk --release
```

### iOS

```bash
flutter run -d iphone
```

Or to build:

```bash
flutter build ios --release
```

---

## License

This project is developed as part of an academic project at **Universiti Teknologi Malaysia (UTM), Fakulti Computing**. All rights reserved.

---

> Built with ❤️ using Flutter & Firebase · [Mohamed Arshath](https://github.com/Mohamedarshathsaleem)
