# Android gRPC Messenger

An Android chat application that communicates with a backend server using gRPC. Messages, chat rooms, and peer information are synchronized in real time through a bidirectional streaming connection, with local data cached in a Room database for offline access.

## Features

* User registration with the chat server, including the device location
* Bidirectional message synchronization over gRPC streaming
* Support for multiple chat rooms
* Peer discovery showing each user's name, last seen time, and location
* Local persistence with Room so chat history is available offline
* Background synchronization handled through a work manager
* Location tagging for outgoing messages using the device GPS

## Tech Stack

* Java
* Android SDK (minimum 29, target 35, compiled with 36)
* gRPC and Protocol Buffers for client and server communication
* Room for local storage
* Android Jetpack components: ViewModel, LiveData, Lifecycle, Fragment
* Material Design components

## Project Structure

```
app/src/main/java/edu/stevens/cs522/chat/
├── activities      Screens for chatting, registration, and viewing peers
├── databases       Room database and data access objects
├── entities        Data models for messages, chat rooms, and peers
├── dialog          Dialogs used in the chat interface
├── location        Device location retrieval
├── services        Background registration service
├── settings        App preferences such as username and server address
├── ui              RecyclerView adapters
├── viewmodels      ViewModels backing each screen
└── web             gRPC client, request and response models, and background workers

app/src/main/proto/chat.proto   Protocol buffer definitions and the ChatService contract
```

## Getting Started

### Prerequisites

* Android Studio (recent stable release)
* Android SDK Platform 36
* JDK 17
* A running instance of the gRPC chat server, or access to an existing one

### Clone the repository

```
git clone https://github.com/BeefHitTheFan/Android-gRPC-Messenger.git
```

Open the `Chat-App-gRPC` project folder in Android Studio and let Gradle sync.

### Configure the server address

The server address is read from `app/src/main/res/values/strings.xml`, under the `base_uri` string. Update this value to point to your own server before building, or change it later from the app's settings screen.

### Build and run

Build the project from Android Studio or with the Gradle wrapper:

```
./gradlew assembleDebug
```

Install the generated APK on a device or emulator running Android 10 (API 29) or later.

## gRPC Service Overview

The client communicates with the server through the `ChatService` defined in `chat.proto`:

* `register` sends the device's chat name and location to the server
* `sync` opens a bidirectional stream used to upload new chat rooms and messages while downloading updates from other peers

## Permissions

The app requests the following permissions:

* Internet and network state, for communicating with the chat server
* Foreground service and data sync, for background registration and synchronization
* Post notifications, for status updates on registration progress

## Architectural Takeaways

The app follows an offline first design where user actions write to the local Room database immediately and are pushed to the server later through a background sync, so the interface stays responsive regardless of network conditions. Synchronization is handled over a single bidirectional gRPC stream that uploads local changes and downloads server updates in one exchange, using a stored sequence number so only new data needs to move on each sync rather than the full chat history. Business logic sits in a dedicated request processor that works through model classes instead of calling gRPC directly, and each screen follows MVVM with ViewModels backed by LiveData, keeping the UI layer, sync logic, and data storage cleanly separated.
