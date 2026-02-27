# Seleda Finance

Personal finance MVP for tracking income and expenses, built with Flutter using strict Clean Architecture boundaries. Seleda Finance helps users manage their daily finances with an intuitive, offline-first mobile experience.

## Features

- **Transaction Management** -- Add, edit, and delete income and expense entries with category tagging
- **Dashboard** -- At-a-glance running balance, recent transactions, and summary cards
- **Reports & Charts** -- Time-range filtering (week/month/year) with Syncfusion line charts and category breakdowns
- **Voice Input** -- Speech-to-text prefill for quick transaction entry via microphone
- **Receipt Capture** -- Attach photos from camera or gallery to any transaction
- **Dark Mode** -- System-aware and manual dark/light theme toggle
- **Offline-First** -- All data persisted locally with Drift (SQLite); no internet required

## Tech Stack

| Layer        | Technology                        |
|--------------|-----------------------------------|
| Framework    | Flutter 3.16+, Dart 3.2+         |
| State Mgmt   | Riverpod 2.x                     |
| Routing      | GoRouter 16.x                    |
| Database     | Drift (SQLite) with code gen      |
| Charts       | Syncfusion Flutter Charts         |
| UI           | Material 3 Design System          |
| Voice        | speech_to_text                    |
| Image        | image_picker                      |

## Architecture Overview

The project follows **Clean Architecture** with five clearly separated layers. Dependencies point inward -- outer layers depend on inner layers, never the reverse.

```
+------------------------------------------------------------------+
|  External (Platform APIs: Camera, Microphone, File System)       |
+------------------------------------------------------------------+
|  Infrastructure (Drift DB, DAOs, Repository Implementations)     |
+------------------------------------------------------------------+
|  Application (Use Cases, DTOs, Business Logic Orchestration)     |
+------------------------------------------------------------------+
|  Domain (Entities, Value Objects, Repository Contracts)           |
+------------------------------------------------------------------+
|  Presentation (Pages, Widgets, Controllers / Riverpod Providers) |
+------------------------------------------------------------------+
```

- **Domain** -- Pure Dart entities (`Transaction`, `Attachment`), value objects (`Money`, `TransactionType`, `DateRange`), and repository interfaces. No Flutter imports.
- **Application** -- Use cases (`AddTransaction`, `DeleteTransaction`, `UpdateTransaction`, `WatchTransactions`, `ComputeBalance`) and DTOs that orchestrate domain logic.
- **Infrastructure** -- Drift database, DAOs, and concrete repository implementations that fulfill domain contracts.
- **Presentation** -- Flutter pages, widgets, and Riverpod controllers/providers that drive the UI.
- **External** -- Thin adapters wrapping platform capabilities (camera via `image_picker`, microphone via `speech_to_text`).

## Project Structure

```
lib/
  main.dart                         # App entry point
  app/
    colors.dart                     # Custom color palette
    di.dart                         # Dependency injection (Riverpod providers)
    router.dart                     # GoRouter route definitions (ShellRoute + bottom nav)
    theme.dart                      # Material 3 light/dark theme data
    theme_mode_provider.dart        # ThemeModeNotifier (Riverpod)
    widgets/
      app_bottom_navigation.dart    # Bottom navigation bar
      app_header.dart               # Shared app header
      expandable_fab.dart           # Expandable floating action button
  core/
    result.dart                     # Result<T> type (Success / Failure)
    error/
      failure.dart                  # Failure base class
  features/
    transactions/
      domain/
        entities/                   # Transaction, Attachment
        value_objects/              # Money, TransactionType, DateRange
        repositories/               # TransactionRepository (abstract)
      application/
        dto/                        # TransactionInputDto
        usecases/                   # AddTransaction, DeleteTransaction, UpdateTransaction,
                                    #   WatchTransactions, ComputeBalance
      infrastructure/
        data_sources/
          drift/                    # AppDatabase, TransactionDao (+generated files)
        repositories/               # TransactionRepositoryImpl, transaction seeds
      presentation/
        controllers/                # Riverpod controllers (add/edit, balance, list)
        pages/                      # Dashboard, Transactions, AddTransaction, Report,
                                    #   Settings, Account, Search, CategorySummary, Splash
        widgets/                    # BalanceSummaryCard, TransactionListItem
        style.dart                  # Presentation-layer style constants
      external/
        image/                      # ImagePickerAdapter
        voice/                      # SttAdapter (speech-to-text)
  shared/
    formatters/
      money_formatter.dart          # Currency display formatting
    utils/
      voice_parser.dart             # Parse voice input into transaction fields
```

## Getting Started

### Prerequisites

- **Flutter SDK** 3.16 or higher (stable channel)
- Android Studio / Xcode for platform tooling
- A physical device or emulator/simulator

### Setup

```bash
# 1. Clone the repository
git clone https://github.com/baakal/seleda_mobile.git
cd seleda_mobile

# 2. Install dependencies
flutter pub get

# 3. Run Drift code generation
dart run build_runner build --delete-conflicting-outputs

# 4. Launch the app
flutter run
```

### Platform Permissions

The app uses the camera, photo library, and microphone. Make sure the following permissions are configured:

- **Android** -- Update `android/app/src/main/AndroidManifest.xml` with camera, storage, and microphone permissions.
- **iOS** -- Add `NSCameraUsageDescription`, `NSPhotoLibraryAddUsageDescription`, and `NSMicrophoneUsageDescription` to `Info.plist`.

If permissions are denied at runtime, the voice and image features gracefully degrade.

### Running Tests

```bash
flutter test
```

### Re-running Code Generation

Whenever you modify Drift table definitions or annotated classes, regenerate with:

```bash
dart run build_runner build --delete-conflicting-outputs
```

## Roadmap / Planned Features

- **Localization** -- Multi-language support for Amharic, Tigrigna, and Oromiffa using ARB / intl
- **Authentication & Cloud Sync** -- User accounts with remote backup and cross-device sync
- **Recurring Transactions** -- Scheduled entries (daily, weekly, monthly) with auto-creation
- **Data Export** -- Export transactions to CSV / Excel for external use
- **Advanced Reports** -- Stacked bar charts, pie charts, and category breakdowns
- **Transaction Search** -- Full-text search with suggestion UI and debounced queries
- **Budget Tracking** -- Set monthly budgets per category with alerts
- **Persisted Preferences** -- Save theme and language choices locally

## License

This project is proprietary. All rights reserved.
