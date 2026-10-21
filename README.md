<div align="center">

# 🤖 Android Development Master Guide

### Architecture · Coroutines · Flow · RxJava · Room · SQLite · Compose · Hilt

![Kotlin](https://img.shields.io/badge/Kotlin-7F52FF?style=for-the-badge&logo=kotlin&logoColor=white)
![Android](https://img.shields.io/badge/Android-3DDC84?style=for-the-badge&logo=android&logoColor=black)
![Jetpack Compose](https://img.shields.io/badge/Jetpack%20Compose-4285F4?style=for-the-badge&logo=jetpackcompose&logoColor=white)
![RxJava](https://img.shields.io/badge/RxJava-B7178C?style=for-the-badge&logo=reactivex&logoColor=white)
![SQLite](https://img.shields.io/badge/SQLite-003B57?style=for-the-badge&logo=sqlite&logoColor=white)
![License](https://img.shields.io/badge/License-MIT-yellow?style=for-the-badge)

**Learn not just _what_ each technology does — but _why_ it exists and _when_ to use it.**

[Table of Contents](#table-of-contents) • [Big Picture](#-the-big-picture) • [Case Study](#-real-world-case-study-taskflow-app) • [Cheat Sheet](#27-quick-revision-cheat-sheet)

</div>

---

## 🗺️ The Big Picture

![Diagram 1](diagrams/diagram-01.svg)


## 📱 Real-World Case Study: TaskFlow App

Every section of this guide maps onto one realistic app — an **offline-first task manager** with sync.

![Diagram 2](diagrams/diagram-02.svg)


| Concern | Technology used | Guide section |
|---|---|---|
| Screens | Jetpack Compose | [13](#13-jetpack-compose) |
| Background work | Coroutines + Dispatchers.IO | [5](#5-kotlin-coroutines) |
| Reactive state | StateFlow / SharedFlow | [7](#7-stateflow-and-sharedflow) |
| Local storage | Room over SQLite | [18](#18-room-database) |
| Offline-first sync | Repository + WorkManager | [21](#21-repository-pattern-and-offline-first-architecture) |
| Dependency wiring | Hilt | [22](#22-dependency-injection-dagger-and-hilt) |

---

A comprehensive Android development and interview preparation guide covering Android fundamentals, XML layouts, Jetpack Compose, application architecture, Activity and Fragment lifecycles, synchronous and asynchronous programming, Kotlin Coroutines, Flow, StateFlow, SharedFlow, RxJava, Room, SQLite, Dependency Injection, Jetpack components, and OOP principles.

The goal is to understand not only **what each technology does, but also why it exists, what problem it solves, how it relates to other technologies, and when to use it in a real Android application.**

---

## Table of Contents

1. [Android Architecture Overview](#1-android-architecture-overview)
2. [Android Application Components](#2-android-application-components)
3. [Synchronous vs Asynchronous Programming](#3-synchronous-vs-asynchronous-programming)
4. [Processes, Threads, Tasks and Executors](#4-processes-threads-tasks-and-executors)
5. [Kotlin Coroutines](#5-kotlin-coroutines)
6. [Kotlin Flow](#6-kotlin-flow)
7. [StateFlow and SharedFlow](#7-stateflow-and-sharedflow)
8. [Flow Operators with Examples](#8-flow-operators-with-examples)
9. [RxJava and Reactive Programming](#9-rxjava-and-reactive-programming)
10. [RxJava Operators and Schedulers](#10-rxjava-operators-and-schedulers)
11. [Flow vs RxJava vs LiveData](#11-flow-vs-rxjava-vs-livedata)
12. [XML Layouts and View Binding](#12-xml-layouts-and-view-binding)
13. [Jetpack Compose](#13-jetpack-compose)
14. [Activity and Fragment Lifecycles](#14-activity-and-fragment-lifecycles)
15. [MVC, MVP, MVVM and Clean Architecture](#15-mvc-mvp-mvvm-and-clean-architecture)
16. [Android Jetpack Components](#16-android-jetpack-components)
17. [SQLite Database Fundamentals](#17-sqlite-database-fundamentals)
18. [Room Database](#18-room-database)
19. [Room with Flow and ViewModel](#19-room-with-flow-and-viewmodel)
20. [SQLite vs Room](#20-sqlite-vs-room)
21. [Repository Pattern and Offline-First Architecture](#21-repository-pattern-and-offline-first-architecture)
22. [Dependency Injection: Dagger and Hilt](#22-dependency-injection-dagger-and-hilt)
23. [Data Binding vs View Binding vs Compose](#23-data-binding-vs-view-binding-vs-compose)
24. [OOP and SOLID Principles](#24-oop-and-solid-principles)
25. [Testing, Performance and Best Practices](#25-testing-performance-and-best-practices)
26. [Common Interview Questions](#26-common-interview-questions)
27. [Quick Revision Cheat Sheet](#27-quick-revision-cheat-sheet)

---

# 1. Android Architecture Overview

Android architecture can refer to two different things.

## 1.1 Android Operating System Architecture

Android provides the operating environment in which applications run.

```text
+-----------------------------------+
|           Applications            |
|     Your App, System Apps         |
+-----------------------------------+
                  |
+-----------------------------------+
|         Android Framework         |
| Activity Manager, Resources,      |
| Window Manager, Notifications     |
+-----------------------------------+
                  |
+-----------------------------------+
|     Android Runtime (ART)         |
|        Native Libraries           |
+-----------------------------------+
                  |
+-----------------------------------+
| Hardware Abstraction Layer (HAL)  |
+-----------------------------------+
                  |
+-----------------------------------+
|          Linux Kernel             |
| Processes, Memory, Drivers,       |
| Scheduling and Security           |
+-----------------------------------+
```

### Important concepts

- **Linux Kernel:** Provides core operating system functionality, including process scheduling, memory management, drivers and security mechanisms.
- **Hardware Abstraction Layer (HAL):** Provides standardized interfaces between Android system services and hardware implementations.
- **Android Runtime (ART):** Executes Android application code.
- **Native Libraries:** Provide capabilities such as graphics, media and database functionality.
- **Android Framework:** Provides APIs for application components, resources, windows, notifications and other platform features.
- **Applications:** Use the framework and platform APIs to deliver features to users.

## 1.2 Android Application Architecture

Application architecture describes how we organize the code inside our own application.

A common architecture is:

```text
+------------------------------------+
| Presentation Layer                 |
| Activity / Fragment / Compose      |
| ViewModel / UI State               |
+------------------------------------+
                  |
                  v
+------------------------------------+
| Domain Layer (Optional)             |
| Use Cases / Business Rules          |
| Repository Interfaces               |
+------------------------------------+
                  |
                  v
+------------------------------------+
| Data Layer                          |
| Repository Implementations          |
| Room / SQLite / Retrofit / API      |
+------------------------------------+
```

The purpose is to separate responsibilities, reduce coupling, improve testability and make the application easier to maintain.

### Visual: How the layers talk to each other

![Diagram 3](diagrams/diagram-03.svg)


> [!TIP]
> **Real-world example — Food delivery app (Swiggy / Zomato style)**
>
> - **Presentation:** `RestaurantListScreen` (Compose) observes `RestaurantViewModel.uiState`.
> - **Domain:** `GetNearbyRestaurantsUseCase` filters by distance and opening hours.
> - **Data:** `RestaurantRepository` reads cached restaurants from Room and refreshes from the API.
> - Result: the UI never knows whether data came from network or disk.


---

# 2. Android Application Components

Android has four traditional core application components.

| Component | Purpose | Example |
|---|---|---|
| Activity | Entry point for UI interaction | Login screen |
| Service | Component for work without its own UI | An appropriately managed playback service |
| BroadcastReceiver | Responds to broadcasts | A relevant system or app broadcast |
| ContentProvider | Manages or exposes structured data through content URIs | Sharing contacts or documents |

Other important concepts:

- **Intent:** A message used to request an action, such as opening an Activity.
- **AndroidManifest.xml:** Declares components, permissions and other app configuration.
- **Application class:** An optional app-level initialization point.
- **Resources:** Layouts, strings, drawables, colors and other app resources.
- **Permissions:** Control access to protected system features and data.

A Service is not automatically a background thread. Work executed on a Service's main thread can still block the UI. Use an appropriate execution mechanism for the workload.

![Diagram 4](diagrams/diagram-04.svg)


> [!TIP]
> **Real-world example — Music player (Spotify-like)**
>
> All four components cooperate: an **Activity** shows the player, a foreground **Service** keeps music playing when the screen is off, a **BroadcastReceiver** pauses on headphone disconnect, and the **ContentProvider** (MediaStore) supplies local songs.


---

# 3. Synchronous vs Asynchronous Programming

## 3.1 Synchronous Programming

Synchronous programming means the caller waits for an operation to return before proceeding to the next statement.

```kotlin
fun main() {
    println("Step 1")
    calculateTotal()
    println("Step 2")
}

fun calculateTotal() {
    println("Calculation completed")
}
```

Output:

```text
Step 1
Calculation completed
Step 2
```

The operation completes before the caller moves to the next statement.

Synchronous code is appropriate for many small operations, such as simple calculations and in-memory transformations.

## 3.2 Asynchronous Programming

Asynchronous programming allows an operation to be initiated and its result handled later, while the caller continues other work.

```text
User taps Refresh
        |
        v
Start API request
        |
        +------> UI remains responsive
        |
        v
API response arrives
        |
        v
Update UI state
```

For example, an Android app can request profile information while the user continues interacting with other parts of the screen.

## 3.3 Blocking vs Non-Blocking

These concepts are related but different.

| Concept | Meaning |
|---|---|
| Synchronous | Caller waits for an operation to return |
| Asynchronous | Completion or results are handled later |
| Blocking | Current thread waits and cannot continue |
| Non-blocking | Current thread can continue while work is pending |
| Concurrent | Multiple tasks make progress during overlapping periods |
| Parallel | Multiple tasks execute at the same instant |

An asynchronous operation does not necessarily require a new thread. For example, non-blocking network I/O can suspend while waiting for a response.

## 3.4 Why Does This Matter in Android?

Android normally handles UI events and rendering on the main thread.

Long-running operations on that thread can cause:

- Frozen screens.
- Dropped frames.
- Unresponsive controls.
- Application Not Responding (ANR) conditions if the thread remains blocked long enough.

Avoid performing blocking network calls, expensive calculations or large file operations directly on the main thread.

Use Coroutines, appropriate executors or an existing RxJava architecture to manage such work.

![Diagram 5](diagrams/diagram-05.svg)


> [!TIP]
> **Real-world example — Banking app balance check**
>
> A synchronous network call on the main thread would freeze the screen and can trigger an **ANR** after ~5 s. Async loading shows a shimmer placeholder while the balance is fetched on a background thread.


---

# 4. Processes, Threads, Tasks and Executors

## 4.1 Process

A process is a running program with its own execution environment and resources.

Android normally runs an app in its own process, although processes can be configured or shared in specific circumstances.

## 4.2 Thread

A thread is an execution path within a process.

- **Main thread:** Handles UI-related work and many lifecycle callbacks.
- **Background thread:** Can perform suitable I/O or computation without directly blocking UI interaction.

A process can have multiple threads.

```text
Android Device
|
+-- App Process A
|   |
|   +-- Main Thread
|   +-- Background Thread
|   +-- Worker Thread
|
+-- App Process B
    |
    +-- Main Thread
```

A thread is not the same as a task. A task is a unit of work that can be executed by different mechanisms.

## 4.3 Executors

Executors manage task execution and can reuse threads rather than creating a new thread for every operation.

```java
ExecutorService executor =
        Executors.newSingleThreadExecutor();

executor.execute(() -> {
    String result = loadData();
    // Deliver the result to the UI appropriately.
});
```

## 4.4 AsyncTask

`AsyncTask` was an older Android API designed to simplify background operations and UI result delivery.

Its commonly used methods included:

- `onPreExecute()`
- `doInBackground()`
- `onProgressUpdate()`
- `onPostExecute()`

`AsyncTask` was deprecated in Android 11 (API 30).

Modern alternatives include Kotlin Coroutines, Java executors and RxJava where appropriate. WorkManager is a separate choice for suitable persistent, scheduled background work.

---

# 5. Kotlin Coroutines

Kotlin Coroutines provide a structured way to write asynchronous code.

A coroutine can suspend without necessarily blocking its thread, then resume when the result is available.

## 5.1 What Is `suspend`?

The `suspend` modifier allows a function to suspend execution.

```kotlin
suspend fun fetchProfile(): Profile {
    return api.getProfile()
}
```

A suspending function does not automatically run on a background thread. Its implementation and execution context determine where the work runs.

## 5.2 Dispatchers

| Dispatcher | Typical purpose |
|---|---|
| `Dispatchers.Main` | UI-related work |
| `Dispatchers.IO` | Blocking I/O operations |
| `Dispatchers.Default` | CPU-intensive computation |
| `Dispatchers.Unconfined` | Special-purpose behavior, rarely needed in normal application code |

Example:

```kotlin
suspend fun readFile(): String =
    withContext(Dispatchers.IO) {
        file.readText()
    }
```

Use `Dispatchers.IO` when a blocking file operation needs to be moved off the main thread.

Do not automatically switch dispatchers for every suspending API. Many modern networking libraries already provide non-blocking suspending functions.

## 5.3 `launch` vs `async`

### `launch`

Starts a coroutine and returns a `Job`.

```kotlin
viewModelScope.launch {
    repository.saveInvoice(invoice)
}
```

Use it when you want to perform work without returning a result through the coroutine builder.

### `async`

Starts a coroutine and returns a `Deferred<T>`.

```kotlin
viewModelScope.launch {
    val profileDeferred = async {
        repository.getProfile()
    }

    val settingsDeferred = async {
        repository.getSettings()
    }

    val profile = profileDeferred.await()
    val settings = settingsDeferred.await()
}
```

The two independent operations can run concurrently.

## 5.4 Coroutine Scopes

| Scope | Lifetime |
|---|---|
| `viewModelScope` | Until the ViewModel is cleared |
| `lifecycleScope` | Until its lifecycle owner is destroyed |
| `rememberCoroutineScope()` | While its Compose call site remains in composition |
| Application-managed scope | Application-level work, when deliberately designed and managed |

For UI state, use lifecycle-aware collection so work tied to a screen does not continue unnecessarily when that screen is stopped.

## 5.5 Cancellation and Error Handling

```kotlin
viewModelScope.launch {
    try {
        val profile = repository.getProfile()
        // Update the state with success.
    } catch (e: CancellationException) {
        throw e
    } catch (e: Exception) {
        // Convert the failure into an appropriate UI state.
    }
}
```

Cancellation is part of coroutine control flow. Avoid swallowing `CancellationException`.

![Diagram 6](diagrams/diagram-06.svg)


> [!TIP]
> **Real-world example — Instagram-style home feed**
>
> ```kotlin
> fun loadHome() = viewModelScope.launch {
>     val posts   = async(Dispatchers.IO) { api.getPosts() }
>     val stories = async(Dispatchers.IO) { api.getStories() }
>     _uiState.value = HomeUiState(posts.await(), stories.await())
> }
> ```
> Both requests run in parallel; leaving the screen cancels them automatically.


---

# 6. Kotlin Flow

## 6.1 What Is Flow?

`Flow<T>` represents an asynchronous stream of values.

A suspending function is generally appropriate for one operation that returns one result. Flow is useful when values can arrive over time.

Examples:

- Database records changing over time.
- Search results changing as a user types.
- Sensor readings.
- Network or application event streams.
- Values produced by a sequence of transformations.

## 6.2 A Simple Flow Example

```kotlin
fun numberFlow(): Flow<Int> = flow {
    emit(1)
    emit(2)
    emit(3)
}
```

Collecting it:

```kotlin
lifecycleScope.launch {
    numberFlow().collect { number ->
        println(number)
    }
}
```

Output:

```text
1
2
3
```

`emit()` sends a value downstream, and `collect()` receives the emitted values.

## 6.3 Cold vs Hot Streams

### Cold Flow

A cold Flow generally starts its upstream work when a collector collects it.

```kotlin
val numbers = flow {
    println("Flow started")
    emit(1)
    emit(2)
}
```

Each new collection can execute the flow block again.

### Hot Stream

A hot stream exists independently of a particular collector.

`StateFlow` and `SharedFlow` are hot streams.

| Type | Typical behavior |
|---|---|
| `Flow<T>` | Asynchronous stream, cold by default |
| `StateFlow<T>` | Hot stream that holds current state |
| `SharedFlow<T>` | Hot broadcast stream with configurable replay and buffering |

## 6.4 Flow with a Repository

```kotlin
class UserRepository(
    private val api: UserApi
) {
    fun getUsers(): Flow<List<User>> = flow {
        emit(api.getUsers())
    }
}
```

This is a simplified example. In production, a one-off API call may be better represented by a suspending function, while a Flow is especially useful for continuously changing data, such as a Room query.

![Diagram 7](diagrams/diagram-07.svg)


> [!TIP]
> **Real-world example — Live stock ticker**
>
> A repository exposes `fun prices(symbol: String): Flow<Price>` that emits every second; the UI collects it and updates the chart. Since it is cold, polling only runs while someone is collecting.


---

# 7. StateFlow and SharedFlow

## 7.1 What Is StateFlow?

`StateFlow` is a hot stream that always has a current value.

It is especially useful for UI state managed by a ViewModel.

Examples:

- Loading state.
- Current profile.
- Search results.
- Form validation.
- Error or success status.

### StateFlow Example

```kotlin
data class ProfileUiState(
    val isLoading: Boolean = false,
    val name: String = "",
    val error: String? = null
)

class ProfileViewModel(
    private val repository: ProfileRepository
) : ViewModel() {

    private val _uiState =
        MutableStateFlow(ProfileUiState())

    val uiState: StateFlow<ProfileUiState> =
        _uiState.asStateFlow()

    fun loadProfile() {
        viewModelScope.launch {
            _uiState.update {
                it.copy(isLoading = true, error = null)
            }

            try {
                val profile = repository.getProfile()

                _uiState.value = ProfileUiState(
                    isLoading = false,
                    name = profile.name
                )
            } catch (e: CancellationException) {
                throw e
            } catch (e: Exception) {
                _uiState.value = ProfileUiState(
                    isLoading = false,
                    error = "Unable to load profile"
                )
            }
        }
    }
}
```

### Why use `MutableStateFlow`?

The ViewModel needs to update the value.

```kotlin
private val _uiState = MutableStateFlow(ProfileUiState())
```

The public API should normally be read-only:

```kotlin
val uiState: StateFlow<ProfileUiState> =
    _uiState.asStateFlow()
```

This prevents the UI from directly modifying the ViewModel's state.

### Important StateFlow behavior

- Requires an initial value.
- Holds the latest state.
- New collectors receive the current value.
- Conflates updates; slow collectors may skip intermediate states and receive the latest state.
- Uses equality-based conflation, so assigning an equal value does not produce a new emission.
- Is hot and does not complete normally like a finite cold Flow.

## 7.2 What Is SharedFlow?

`SharedFlow` broadcasts values to multiple collectors.

It can be useful for events or signals that are not naturally represented as the current screen state.

```kotlin
sealed interface ProfileEvent {
    data object NavigateToLogin : ProfileEvent
    data class ShowMessage(val message: String) : ProfileEvent
}

class ProfileViewModel : ViewModel() {

    private val _events = MutableSharedFlow<ProfileEvent>()
    val events = _events.asSharedFlow()

    fun requestLogin() {
        viewModelScope.launch {
            _events.emit(ProfileEvent.NavigateToLogin)
        }
    }
}
```

This is illustrative; production event handling should account for the lifecycle of collectors and whether an event must survive periods when the UI is not collecting.

### StateFlow vs SharedFlow

| Feature | StateFlow | SharedFlow |
|---|---|---|
| Initial value required | Yes | No |
| Holds current state | Yes | Not inherently |
| Replays latest/current state | Yes | Configurable replay |
| Main use | State | Broadcasting emissions/events |
| Equality conflation | Yes | Not inherently the same behavior |

Do not treat every event as a SharedFlow event. If a message or status must remain visible until acknowledged, representing it as state can be more reliable.

![Diagram 8](diagrams/diagram-08.svg)


> [!TIP]
> **Real-world example — E-commerce checkout (Amazon/Flipkart)**
>
> - `StateFlow<CartUiState>` holds items, total and loading state — survives rotation.
> - `SharedFlow<CheckoutEvent>` emits `NavigateToPayment` or `ShowError("Card declined")` exactly once, so a rotation does not re-open the payment screen.


---

# 8. Flow Operators with Examples

Flow operators transform, filter, combine or control the timing of emitted values.

## 8.1 `map`

Transforms each emitted value.

```kotlin
val names: Flow<String> = userFlow.map { user ->
    user.name
}
```

Example: Convert a database entity into a UI model.

## 8.2 `filter`

Emits only values matching a condition.

```kotlin
val activeUsers = usersFlow.filter { users ->
    users.isNotEmpty()
}
```

This example emits only non-empty lists. It does not filter individual users inside a list.

To filter individual users from a flow of users:

```kotlin
val activeUsers: Flow<User> =
    userFlow.filter { it.isActive }
```

## 8.3 `transform`

Provides more flexibility than `map`, because it can emit zero, one or multiple values for each upstream value.

```kotlin
val output = flowOf(2, 3).transform { number ->
    emit(number)
    emit(number * number)
}
```

## 8.4 `combine`

Combines the latest values from multiple flows.

```kotlin
val uiState = combine(
    profileFlow,
    settingsFlow
) { profile, settings ->
    ProfileScreenState(
        name = profile.name,
        darkMode = settings.darkMode
    )
}
```

Useful when a screen depends on multiple changing sources.

## 8.5 `zip`

Pairs emissions from two flows by position.

```kotlin
val combined = flowOf("A", "B")
    .zip(flowOf(1, 2)) { letter, number ->
        "$letter$number"
    }
```

Output:

```text
A1
B2
```

Unlike `combine`, `zip` pairs corresponding emissions rather than always combining the latest available values.

## 8.6 `flatMapLatest`

Switches to a new inner Flow when the upstream emits a new value. It cancels collection of the previous inner Flow.

```kotlin
val searchResults: Flow<List<Product>> =
    searchQuery
        .debounce(300)
        .distinctUntilChanged()
        .flatMapLatest { query ->
            repository.searchProducts(query)
        }
```

This is useful for search screens because a newer query can replace an older search operation.

Cancellation only works as intended when the underlying work cooperates with cancellation. It cannot guarantee that a server has not already processed a request.

## 8.7 `debounce`

Waits for a pause in emissions before passing the latest value downstream.

```kotlin
searchQuery.debounce(300)
```

Useful for avoiding an API request for every keystroke.

## 8.8 `distinctUntilChanged`

Suppresses consecutive duplicate values.

```kotlin
searchQuery.distinctUntilChanged()
```

For example, repeated emissions of `"android"` will not all pass downstream consecutively.

## 8.9 `catch`

Handles upstream exceptions.

```kotlin
repository.getUsers()
    .catch { exception ->
        emit(emptyList())
    }
```

This emits an empty list after an upstream failure. Use this only if an empty list is an appropriate fallback; otherwise expose an explicit error state.

`catch` does not automatically catch exceptions thrown by downstream code inside the collector.

## 8.10 `onStart`

Runs an action before upstream emissions.

```kotlin
repository.getUsers()
    .onStart {
        emit(emptyList())
    }
```

This can be used to emit an initial value, although loading indicators are often better modeled explicitly in a UI state object.

## 8.11 `onEach`

Performs an action for each emission without replacing the emitted value.

```kotlin
repository.getUsers()
    .onEach { users ->
        println("Received ${users.size} users")
    }
```

The action executes as part of collection.

## 8.12 `flowOn`

Changes the coroutine context used by upstream operators.

```kotlin
val users = flow {
    emit(readUsersFromBlockingSource())
}.flowOn(Dispatchers.IO)
```

`flowOn` affects upstream operations, not the downstream collector. It is not the same as RxJava's `subscribeOn` or `observeOn`, though there is some conceptual overlap.

## 8.13 `collect`

Collects the stream and handles each emitted value.

```kotlin
usersFlow.collect { users ->
    renderUsers(users)
}
```

Collection is a suspending terminal operation for a normal Flow. A cold Flow generally does not execute its upstream work until collection begins.

### Quick operator reference

| Operator | Purpose |
|---|---|
| `map` | Transform each value |
| `filter` | Keep matching values |
| `transform` | Emit zero or more values per input |
| `combine` | Combine latest values from multiple flows |
| `zip` | Pair emissions by position |
| `flatMapLatest` | Switch to latest inner stream |
| `flatMapConcat` | Process inner streams sequentially |
| `flatMapMerge` | Collect inner streams concurrently |
| `debounce` | Wait for a pause |
| `distinctUntilChanged` | Suppress consecutive duplicates |
| `catch` | Handle upstream exceptions |
| `onStart` | Perform action before upstream emissions |
| `onEach` | Perform action for every emission |
| `flowOn` | Change upstream coroutine context |
| `collect` | Consume the stream |

> [!TIP]
> **Real-world example — Search-as-you-type (Google Maps places)**
>
> ```kotlin
> val results = queryFlow
>     .debounce(300)
>     .map { it.trim() }
>     .filter { it.length >= 2 }
>     .distinctUntilChanged()
>     .flatMapLatest { q -> repo.searchPlaces(q) }
>     .catch { emit(emptyList()) }
>     .flowOn(Dispatchers.IO)
>     .stateIn(viewModelScope, SharingStarted.WhileSubscribed(5_000), emptyList())
> ```
> 

![Diagram 9](diagrams/diagram-09.svg)



---

# 9. RxJava and Reactive Programming

RxJava is a reactive programming library for Java and Kotlin.

It is based on streams of values and operators that transform, combine and control those streams.

It remains important for maintaining existing Android applications and for interviews involving reactive architectures.

## 9.1 RxJava Types

| Type | Purpose |
|---|---|
| `Observable<T>` | Emits zero or more values |
| `Flowable<T>` | Supports backpressure |
| `Single<T>` | Emits one success value or an error |
| `Maybe<T>` | Emits zero or one success value, or an error |
| `Completable` | Completes or errors without emitting a result |
| `Subject<T>` | Can act as both an observer and an observable |

Examples:

```kotlin
Single<User>
```

A request that returns one user.

```kotlin
Observable<List<Product>>
```

A stream of product lists.

```kotlin
Completable
```

An operation such as saving data where only completion or failure matters.

## 9.2 Observable Example

```kotlin
val observable = Observable.just(1, 2, 3)

observable.subscribe { number ->
    println(number)
}
```

Output:

```text
1
2
3
```

Unlike a plain synchronous loop, an RxJava stream can be scheduled, transformed and combined with asynchronous sources.

## 9.3 `map`

Transforms each emitted item.

```kotlin
Observable.just(1, 2, 3)
    .map { number -> number * 10 }
    .subscribe { println(it) }
```

Output:

```text
10
20
30
```

## 9.4 `filter`

Emits only values that satisfy a condition.

```kotlin
Observable.just(1, 2, 3, 4, 5)
    .filter { it % 2 == 0 }
    .subscribe { println(it) }
```

Output:

```text
2
4
```

## 9.5 `flatMap`

Transforms each value into an inner stream and merges the resulting emissions. The output order is not necessarily the same as the input order.

```kotlin
userIds
    .flatMap { id ->
        api.getUser(id)
    }
```

Useful when each input requires another asynchronous operation.

## 9.6 `concatMap`

Processes inner streams sequentially, preserving their order.

Use it when operations must be processed one after another.

## 9.7 `switchMap`

Switches to the newest inner stream when a new upstream value arrives.

```kotlin
searchText
    .debounce(300, TimeUnit.MILLISECONDS)
    .distinctUntilChanged()
    .switchMap { query ->
        api.searchProducts(query)
    }
```

Useful for search fields, where newer queries should replace earlier ones.

## 9.8 `zip`

Combines corresponding emissions from two streams.

```kotlin
Observable.zip(
    api.getProfile(),
    api.getSettings(),
    BiFunction<Profile, Settings, ProfileScreenData> { profile, settings ->
        ProfileScreenData(profile, settings)
    }
)
```

This is appropriate when both sources emit corresponding values that should be combined.

## 9.9 Error Operators

Important operators include:

- `onErrorReturn`: supplies a fallback value after an error.
- `onErrorResumeNext`: switches to another source after an error.
- `retry`: resubscribes after an error according to the retry strategy.
- `doOnError`: performs a side effect such as logging an error.

Retries should be limited and designed carefully. Repeating a non-idempotent operation can create duplicate side effects.

> [!TIP]
> **Real-world example — Ride-hailing app (Uber-like)**
>
> `Observable.interval(3, SECONDS).switchMap { api.driverLocation(rideId) }` streams driver position; `switchMap` drops stale requests so the car marker never jumps backwards.


---

# 10. RxJava Operators and Schedulers

## 10.1 `subscribeOn`

Controls the scheduler used for subscribing to the source. For typical cold sources, it determines where source work begins.

## 10.2 `observeOn`

Changes the scheduler used for downstream notifications after that point in the chain.

Example:

```kotlin
api.getProfile()
    .subscribeOn(Schedulers.io())
    .observeOn(AndroidSchedulers.mainThread())
    .subscribe(
        { profile ->
            render(profile)
        },
        { error ->
            showError(error)
        }
    )
```

Conceptually:

```text
API request
    |
    v
Schedulers.io()
    |
    v
API response
    |
    v
AndroidSchedulers.mainThread()
    |
    v
Update UI
```

Common schedulers:

| Scheduler | Typical use |
|---|---|
| `Schedulers.io()` | I/O-bound operations |
| `Schedulers.computation()` | CPU-intensive computations |
| `Schedulers.single()` | Sequential work on a single thread |
| `AndroidSchedulers.mainThread()` | Android UI updates |

## 10.3 `debounce` and `distinctUntilChanged`

```kotlin
searchText
    .debounce(300, TimeUnit.MILLISECONDS)
    .distinctUntilChanged()
```

- `debounce` waits until the user pauses typing.
- `distinctUntilChanged` prevents consecutive duplicate queries.

## 10.4 Disposable and CompositeDisposable

RxJava subscriptions must be managed so they do not continue unnecessarily after the owner is finished.

```kotlin
private val disposables = CompositeDisposable()

fun loadProfile() {
    val disposable = api.getProfile()
        .subscribeOn(Schedulers.io())
        .observeOn(AndroidSchedulers.mainThread())
        .subscribe(
            { profile -> render(profile) },
            { error -> showError(error) }
        )

    disposables.add(disposable)
}

override fun onCleared() {
    disposables.clear()
}
```

This example assumes the class is a ViewModel. `onCleared()` is the relevant ViewModel cleanup callback.

![Diagram 10](diagrams/diagram-10.svg)



---

# 11. Flow vs RxJava vs LiveData

| Feature | Flow | StateFlow | SharedFlow | RxJava | LiveData |
|---|---|---|---|---|---|
| Ecosystem | Kotlin Coroutines | Kotlin Coroutines | Kotlin Coroutines | Reactive Extensions | AndroidX Lifecycle |
| Main purpose | Asynchronous streams | Current state | Broadcast emissions | Reactive streams and operators | Lifecycle-aware observable state |
| Hot or cold | Cold by default | Hot | Hot | Depends on source/type | Lifecycle-aware state holder |
| Holds current value | Not inherently | Yes | Not inherently | Depends on the type | Yes |
| Backpressure | Suspending stream semantics | State conflation | Buffer/replay configuration | `Flowable` supports backpressure | Not a stream-processing abstraction |
| Common UI usage | Database/data streams | ViewModel UI state | Events or broadcasts where suitable | Existing reactive applications | Existing View-based UI |
| Cancellation | Coroutine cancellation | Collector cancellation | Collector cancellation | Disposal | Observer lifecycle management |

### Which should you choose?

For a Kotlin-first Android application:

- Use a **suspending function** for a single asynchronous operation.
- Use **Flow** for streams, especially data that changes over time.
- Use **StateFlow** for current UI state.
- Use **SharedFlow** for broadcasts when its replay and buffering semantics fit.
- Use **LiveData** when it fits an existing View-based architecture.
- Use **RxJava** when maintaining an Rx-based codebase or when its capabilities and ecosystem are deliberately part of the project.

Avoid mixing multiple reactive systems without a clear reason.

![Diagram 11](diagrams/diagram-11.svg)



---

# 12. XML Layouts and View Binding

XML-based Android development separates layout definition from application behavior.

Example:

```xml
<!-- res/layout/activity_main.xml -->
<LinearLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    android:padding="16dp">

    <EditText
        android:id="@+id/nameInput"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:hint="Enter your name" />

    <Button
        android:id="@+id/submitButton"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Submit" />

</LinearLayout>
```

Common layouts:

- `LinearLayout`: arranges children horizontally or vertically.
- `FrameLayout`: stacks children in a container.
- `ConstraintLayout`: positions views using constraints.
- `RecyclerView`: displays and recycles list items efficiently.

### View Binding

```kotlin
private lateinit var binding: ActivityMainBinding

override fun onCreate(savedInstanceState: Bundle?) {
    super.onCreate(savedInstanceState)

    binding = ActivityMainBinding.inflate(layoutInflater)
    setContentView(binding.root)

    binding.submitButton.setOnClickListener {
        binding.nameInput.text.toString()
    }
}
```

View Binding provides type-safe references to XML views.

For Fragments, clear the binding reference in `onDestroyView()` to avoid retaining references to destroyed views.

---

# 13. Jetpack Compose

Jetpack Compose is Android's declarative UI toolkit.

Instead of manually changing View properties for each UI update, you describe the UI based on state.

```kotlin
@Composable
fun GreetingScreen() {
    var name by rememberSaveable {
        mutableStateOf("")
    }

    Column {
        TextField(
            value = name,
            onValueChange = { name = it },
            label = { Text("Enter your name") }
        )

        Text("Hello, $name")
    }
}
```

Important concepts:

- `@Composable`: defines UI-producing functions.
- `remember`: retains local state across recompositions.
- `rememberSaveable`: can preserve supported UI state across recreation.
- `mutableStateOf`: creates observable Compose state.
- **Recomposition:** Compose re-executes relevant composables when observed state changes.
- **State hoisting:** moves state to an appropriate owner and passes it to child composables.
- **Unidirectional data flow:** state flows toward the UI and actions flow back to the state owner.

Compose changes how the UI is built, not the whole Android architecture. ViewModel, Coroutines, Room, Retrofit, Hilt and Activity lifecycle management remain relevant.

> [!TIP]
> **Real-world example — Compose counter used in a shopping cart**
>
> ```kotlin
> @Composable
> fun QuantityStepper(qty: Int, onChange: (Int) -> Unit) {
>     Row(verticalAlignment = Alignment.CenterVertically) {
>         IconButton(onClick = { onChange((qty - 1).coerceAtLeast(1)) }) { Text("−") }
>         Text("$qty")
>         IconButton(onClick = { onChange(qty + 1) }) { Text("+") }
>     }
> }
> ```
> State is hoisted to the ViewModel, so the cart total recomputes automatically.


---

# 14. Activity and Fragment Lifecycles

## Activity Lifecycle

```text
onCreate()
    |
onStart()
    |
onResume()
    |
onPause()
    |
onStop()
    |
onDestroy()
```

| Callback | Purpose |
|---|---|
| `onCreate()` | Initialize the Activity |
| `onStart()` | Activity becomes visible |
| `onResume()` | Activity is in the foreground and interactive |
| `onPause()` | Activity loses its resumed state |
| `onStop()` | Activity is no longer visible |
| `onDestroy()` | Activity instance is being destroyed |

## Fragment Lifecycle

```text
onAttach()
    |
onCreate()
    |
onCreateView()
    |
onViewCreated()
    |
onStart()
    |
onResume()
    |
onPause()
    |
onStop()
    |
onDestroyView()
    |
onDestroy()
    |
onDetach()
```

A Fragment can outlive its view. Clear view binding references in `onDestroyView()`.

For collecting Flow in a Fragment:

```kotlin
viewLifecycleOwner.lifecycleScope.launch {
    viewLifecycleOwner.repeatOnLifecycle(
        Lifecycle.State.STARTED
    ) {
        viewModel.uiState.collect { state ->
            render(state)
        }
    }
}
```

A ViewModel generally survives configuration changes but does not replace persistent storage. Use Room or another appropriate persistence mechanism for durable application data.

![Diagram 12](diagrams/diagram-12.svg)


> [!TIP]
> **Real-world example — Video call app (WhatsApp / Meet)**
>
> Release the camera in `onPause()`/`onStop()` so other apps can use it, and re-acquire in `onStart()`. Keep the call state in a ViewModel so rotating the phone does not drop the call.


---

# 15. MVC, MVP, MVVM and Clean Architecture

## MVC

- Model: data and related logic.
- View: user interface.
- Controller: coordinates actions and changes.

## MVP

- Model: data and logic.
- View: UI interface.
- Presenter: processes actions and prepares results for the View.

## MVVM

- View: Activity, Fragment or composable.
- ViewModel: presentation state and screen-related actions.
- Model: application data and domain behavior.

```text
User Action
    |
    v
ViewModel
    |
    v
Repository / Use Case
    |
    v
Database / API
    |
    v
ViewModel State
    |
    v
UI renders state
```

## Clean Architecture

A way to organize application dependencies around business rules.

```text
Presentation
    |
    v
Domain (optional)
    |
    v
Data
```

A repository interface can be defined in an inner layer, while the data layer provides its implementation.

MVVM and Clean Architecture can be combined: MVVM organizes the presentation layer, while Clean Architecture organizes broader responsibilities and dependencies.

![Diagram 13](diagrams/diagram-13.svg)


![Diagram 14](diagrams/diagram-14.svg)



---

# 16. Android Jetpack Components

| Component | Purpose |
|---|---|
| ViewModel | Holds screen-related state |
| Lifecycle | Lifecycle-aware behavior |
| Navigation | Manages app destinations |
| Room | Database abstraction over SQLite |
| DataStore | Stores preferences or typed settings |
| WorkManager | Schedules suitable persistent background work |
| Paging | Loads large datasets incrementally |
| View Binding | Type-safe XML view access |
| Data Binding | Binds data and expressions to XML layouts |
| Compose | Declarative UI |
| Hilt | Dependency injection built on Dagger |

ViewModel is not a persistent background scheduler. WorkManager is not a replacement for every coroutine. Room is not the same as DataStore.

---

# 17. SQLite Database Fundamentals

## 17.1 What Is SQLite?

SQLite is a lightweight relational database engine that stores structured data in a database file.

It supports SQL operations such as:

- `CREATE`
- `INSERT`
- `SELECT`
- `UPDATE`
- `DELETE`
- `JOIN`
- `WHERE`
- `ORDER BY`
- Transactions

SQLite is commonly used for structured, local, persistent data on Android.

## 17.2 Database Terminology

| Term | Meaning |
|---|---|
| Database | Collection of structured data |
| Table | Data organized into rows and columns |
| Row | One record |
| Column | One field in a record |
| Primary key | Uniquely identifies a row |
| Foreign key | Defines a relationship to another table |
| Index | Helps speed up appropriate queries |
| Query | A request to read or manipulate data |
| Transaction | A group of operations treated as one logical unit |
| Schema | Definition of database tables and relationships |
| Migration | A controlled change from one schema version to another |

## 17.3 Example Database Table

Imagine an application that stores tasks.

```sql
CREATE TABLE tasks (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    title TEXT NOT NULL,
    is_completed INTEGER NOT NULL DEFAULT 0
);
```

Example records:

| id | title | is_completed |
|---:|---|---:|
| 1 | Read Android documentation | 0 |
| 2 | Practice Room queries | 1 |
| 3 | Prepare interview notes | 0 |

## 17.4 SQL CRUD Operations

### Create

```sql
INSERT INTO tasks (title, is_completed)
VALUES ('Learn SQLite', 0);
```

### Read

```sql
SELECT * FROM tasks;
```

### Read filtered data

```sql
SELECT * FROM tasks
WHERE is_completed = 0;
```

### Update

```sql
UPDATE tasks
SET is_completed = 1
WHERE id = 1;
```

### Delete

```sql
DELETE FROM tasks
WHERE id = 1;
```

These operations are often summarized as **CRUD**: Create, Read, Update and Delete.

## 17.5 SQLite on Android

Android provides low-level SQLite APIs, including:

- `SQLiteOpenHelper`
- `SQLiteDatabase`
- `Cursor`
- `ContentValues`

`SQLiteOpenHelper` helps manage database creation and version upgrades.

A simplified example:

```kotlin
class TaskDbHelper(context: Context) :
    SQLiteOpenHelper(context, "tasks.db", null, 1) {

    override fun onCreate(db: SQLiteDatabase) {
        db.execSQL(
            """
            CREATE TABLE tasks (
                id INTEGER PRIMARY KEY AUTOINCREMENT,
                title TEXT NOT NULL,
                is_completed INTEGER NOT NULL DEFAULT 0
            )
            """.trimIndent()
        )
    }

    override fun onUpgrade(
        db: SQLiteDatabase,
        oldVersion: Int,
        newVersion: Int
    ) {
        // Add explicit migration logic when the schema changes.
    }
}
```

Insert a row:

```kotlin
val values = ContentValues().apply {
    put("title", "Learn SQLite")
    put("is_completed", 0)
}

val id = db.insert("tasks", null, values)
```

Query rows:

```kotlin
val cursor = db.query(
    "tasks",
    arrayOf("id", "title", "is_completed"),
    "is_completed = ?",
    arrayOf("0"),
    null,
    null,
    "id DESC"
)

cursor.use {
    while (it.moveToNext()) {
        val title = it.getString(
            it.getColumnIndexOrThrow("title")
        )
        println(title)
    }
}
```

### Important SQLite considerations

1. Avoid running potentially slow database operations on the main thread.
2. Close cursors and other resources properly.
3. Use parameterized queries instead of building SQL from untrusted strings.
4. Use transactions for related operations that must succeed or fail together.
5. Handle schema upgrades explicitly.
6. Add indexes where query patterns justify them.
7. Be careful with joins, large result sets and unnecessary database reads.

SQLite remains a valid database engine. Room does not replace SQLite; it provides a safer, higher-level abstraction over it.

---

# 18. Room Database

## 18.1 What Is Room?

Room is an Android Jetpack persistence library built on top of SQLite.

It reduces boilerplate and provides compile-time verification for many SQL queries and database mappings.

Room has three central building blocks:

| Component | Responsibility |
|---|---|
| `@Entity` | Defines a database table |
| `@Dao` | Defines database operations |
| `@Database` | Provides the database configuration and DAO access |

Room is particularly useful when an application needs structured data, relationships, reactive database observation and maintainable schema migrations.

## 18.2 Add Room Dependencies

For a Kotlin project using KSP, add the Room runtime, KTX and compiler dependencies using compatible versions from the official Android documentation.

A typical Gradle configuration uses:

```kotlin
plugins {
    id("com.google.devtools.ksp")
}

dependencies {
    implementation("androidx.room:room-runtime:<room-version>")
    implementation("androidx.room:room-ktx:<room-version>")
    ksp("androidx.room:room-compiler:<room-version>")
}
```

Replace `<room-version>` with a compatible stable version for your project. Configure KSP with a version compatible with your Kotlin toolchain.

## 18.3 Create an Entity

An Entity represents a database table.

```kotlin
@Entity(tableName = "tasks")
data class TaskEntity(
    @PrimaryKey(autoGenerate = true)
    val id: Long = 0,

    val title: String,

    val isCompleted: Boolean = false
)
```

This defines a `tasks` table with an automatically generated primary key.

## 18.4 Create a DAO

A Data Access Object defines database operations.

```kotlin
@Dao
interface TaskDao {

    @Query("SELECT * FROM tasks ORDER BY id DESC")
    fun observeTasks(): Flow<List<TaskEntity>>

    @Query("SELECT * FROM tasks WHERE id = :id")
    suspend fun getTaskById(id: Long): TaskEntity?

    @Insert
    suspend fun insert(task: TaskEntity): Long

    @Update
    suspend fun update(task: TaskEntity)

    @Delete
    suspend fun delete(task: TaskEntity)

    @Query("DELETE FROM tasks WHERE id = :id")
    suspend fun deleteById(id: Long)
}
```

Notice the distinction:

- `observeTasks()` returns Flow because the query can be observed over time.
- `getTaskById()` is a one-off suspending query.
- Insert, update and delete operations are suspending functions.

Room supports asynchronous database access patterns. Do not perform long-running work on the main thread.

## 18.5 Create the Room Database

```kotlin
@Database(
    entities = [TaskEntity::class],
    version = 1,
    exportSchema = true
)
abstract class AppDatabase : RoomDatabase() {

    abstract fun taskDao(): TaskDao
}
```

The `@Database` annotation declares the entities, version and database configuration.

## 18.6 Create the Database Instance

For a small demonstration, the database can be created using a singleton pattern:

```kotlin
object DatabaseProvider {

    @Volatile
    private var INSTANCE: AppDatabase? = null

    fun getDatabase(context: Context): AppDatabase {
        return INSTANCE ?: synchronized(this) {
            INSTANCE ?: Room.databaseBuilder(
                context.applicationContext,
                AppDatabase::class.java,
                "tasks_database"
            ).build().also { INSTANCE = it }
        }
    }
}
```

In a larger application, Hilt is often used to provide the database and DAO as dependencies instead of manually managing this singleton.

## 18.7 Insert a Task

```kotlin
viewModelScope.launch {
    val task = TaskEntity(
        title = "Learn Room",
        isCompleted = false
    )

    taskDao.insert(task)
}
```

## 18.8 Observe Tasks with Flow

```kotlin
val tasks: Flow<List<TaskEntity>> =
    taskDao.observeTasks()
```

Collecting this Flow allows the UI layer to receive updated lists when relevant database changes occur.

This is one of the most useful Room features: the application can observe the local database rather than manually refreshing every screen after each write.

## 18.9 Room Relationships

For relational data, Room supports:

- One-to-one relationships.
- One-to-many relationships.
- Many-to-many relationships.
- Embedded objects.
- Relations between entities.

For example, an invoice can have multiple invoice items.

```text
Invoice
-------
id (Primary Key)
customerName

InvoiceItem
-----------
id (Primary Key)
invoiceId (Foreign Key)
description
amount
```

One invoice can have many invoice items. The `invoiceId` foreign key connects each item to its invoice.

A simplified relationship:

```kotlin
@Entity
data class InvoiceEntity(
    @PrimaryKey(autoGenerate = true)
    val id: Long = 0,
    val customerName: String
)

@Entity(
    foreignKeys = [
        ForeignKey(
            entity = InvoiceEntity::class,
            parentColumns = ["id"],
            childColumns = ["invoiceId"],
            onDelete = ForeignKey.CASCADE
        )
    ],
    indices = [Index("invoiceId")]
)
data class InvoiceItemEntity(
    @PrimaryKey(autoGenerate = true)
    val id: Long = 0,
    val invoiceId: Long,
    val description: String,
    val amount: Double
)

data class InvoiceWithItems(
    @Embedded val invoice: InvoiceEntity,
    @Relation(
        parentColumn = "id",
        entityColumn = "invoiceId"
    )
    val items: List<InvoiceItemEntity>
)
```

The DAO can retrieve the parent and related records:

```kotlin
@Dao
interface InvoiceDao {

    @Transaction
    @Query("SELECT * FROM InvoiceEntity WHERE id = :id")
    suspend fun getInvoiceWithItems(
        id: Long
    ): InvoiceWithItems?
}
```

`@Transaction` ensures the related reads are performed within a transaction, helping provide a consistent result.

## 18.10 Room Migrations

When the database schema changes, the version must be updated and an appropriate migration supplied.

For example, adding a `priority` column to the tasks table:

```kotlin
val MIGRATION_1_2 = object : Migration(1, 2) {
    override fun migrate(db: SupportSQLiteDatabase) {
        db.execSQL(
            "ALTER TABLE tasks ADD COLUMN priority INTEGER NOT NULL DEFAULT 0"
        )
    }
}
```

Register the migration:

```kotlin
Room.databaseBuilder(
    context,
    AppDatabase::class.java,
    "tasks_database"
)
    .addMigrations(MIGRATION_1_2)
    .build()
```

Use migrations to preserve existing user data. Destructive migrations can erase data and should only be used when losing the existing database is acceptable.

![Diagram 15](diagrams/diagram-15.svg)



---

# 19. Room with Flow and ViewModel

This section connects the database to the application architecture.

## 19.1 Repository

```kotlin
class TaskRepository(
    private val taskDao: TaskDao
) {
    fun observeTasks(): Flow<List<TaskEntity>> =
        taskDao.observeTasks()

    suspend fun addTask(title: String) {
        taskDao.insert(
            TaskEntity(title = title)
        )
    }

    suspend fun deleteTask(id: Long) {
        taskDao.deleteById(id)
    }
}
```

The repository separates the rest of the application from direct DAO access.

## 19.2 ViewModel

```kotlin
class TaskViewModel(
    private val repository: TaskRepository
) : ViewModel() {

    val tasks: StateFlow<List<TaskEntity>> =
        repository.observeTasks()
            .stateIn(
                scope = viewModelScope,
                started = SharingStarted.WhileSubscribed(5_000),
                initialValue = emptyList()
            )

    fun addTask(title: String) {
        viewModelScope.launch {
            repository.addTask(title)
        }
    }

    fun deleteTask(id: Long) {
        viewModelScope.launch {
            repository.deleteTask(id)
        }
    }
}
```

`stateIn()` converts a Flow into a shared `StateFlow` within the specified scope.

`SharingStarted.WhileSubscribed(5_000)` keeps upstream collection active while subscribers exist and briefly delays stopping after the last subscriber disappears. This is one common policy, not a universal requirement.

A production implementation should also expose appropriate loading and error state where needed.

## 19.3 End-to-End Data Flow

```text
User adds a task
        |
        v
UI calls ViewModel
        |
        v
ViewModel launches coroutine
        |
        v
Repository inserts task
        |
        v
Room updates SQLite
        |
        v
DAO Flow emits updated list
        |
        v
StateFlow receives new state
        |
        v
UI renders updated list
```

The UI does not need to manually query SQLite again after every insert. The observed Room query can emit updated data automatically.

![Diagram 16](diagrams/diagram-16.svg)


> [!TIP]
> **Real-world example — To-do app (Google Tasks / Todoist)**
>
> Insert never needs to "refresh" the list manually — Room's `Flow` re-emits automatically after any write to the `tasks` table.


---

# 20. SQLite vs Room

| Feature | SQLite APIs | Room |
|---|---|---|
| Database engine | SQLite | SQLite underneath |
| SQL queries | Manually written and executed | Declared through annotations and DAO methods |
| Boilerplate | More | Less |
| Query verification | More responsibility on developer | Compile-time checking for supported queries |
| Object mapping | Often manual | Generated mapping |
| Database schema | Managed manually | Declared using entities and database annotations |
| Reactive query observation | Requires more manual work | Supports Flow and observable query results |
| Migrations | Manual | Explicit migration APIs and schema support |
| Transactions | Supported | Supported through database/DAO APIs |
| Best fit | Low-level control or legacy implementations | Most structured Android app persistence needs |

**Important:** Room does not eliminate SQL. You still need to understand SQL, indexes, relationships, transactions and database design.

## When should you use each?

Use Room when you want structured local persistence with a maintainable DAO and schema model.

Use raw SQLite APIs when you have a specific need for low-level control or are maintaining an existing implementation. SQLite is also the underlying engine used by Room.

Use DataStore for smaller preferences and settings rather than treating it as a relational database.

---

# 21. Repository Pattern and Offline-First Architecture

A repository provides a boundary between application logic and data sources.

```text
ViewModel
    |
    v
Repository
    |
    +------> Room Database
    |
    +------> Remote API
```

An offline-first design can use Room as the local source of truth for data that needs to remain available offline.

For example, in a field-billing application:

1. The user records a meter reading.
2. The app saves the reading locally.
3. The UI observes the local database and displays the saved record.
4. The app schedules suitable background sync work.
5. When constraints permit, the worker uploads pending data.
6. The server response updates the local sync status.
7. Failed work can be retried according to an appropriate policy.

Important considerations include idempotency, duplicate prevention, conflict resolution, upload ordering, transaction boundaries and secure data handling.

Room, Retrofit, Coroutines and WorkManager have different responsibilities:

- Room persists local data.
- Retrofit communicates with remote APIs.
- Coroutines structure asynchronous operations.
- WorkManager schedules suitable persistent work.
- Repository logic coordinates data sources and application rules.

![Diagram 17](diagrams/diagram-17.svg)


> [!TIP]
> **Real-world example — News app offline-first (Google News / Inshorts)**
>
> Articles open instantly from Room even in airplane mode. A WorkManager job syncs new headlines on Wi-Fi; bookmarks made offline are queued and uploaded later.


---

# 22. Dependency Injection: Dagger and Hilt

Dependency Injection means supplying an object with its dependencies instead of constructing every dependency internally.

Without DI, a repository might create its own Retrofit service, making it harder to replace that service in tests.

With constructor injection:

```kotlin
class TaskRepository(
    private val taskDao: TaskDao
)
```

The dependency is explicit.

Dagger is a compile-time dependency injection framework. Hilt builds on Dagger and provides Android-specific integration.

A Hilt application starts with:

```kotlin
@HiltAndroidApp
class MyApplication : Application()
```

A ViewModel can receive dependencies through constructor injection:

```kotlin
@HiltViewModel
class TaskViewModel @Inject constructor(
    private val repository: TaskRepository
) : ViewModel()
```

The repository, DAO and database must be provided through valid constructors or Hilt modules.

Dependency Injection is not the same as Clean Architecture. DI supplies dependencies; architecture determines how responsibilities and dependencies should be organized.

![Diagram 18](diagrams/diagram-18.svg)


> [!TIP]
> **Real-world example — Swap a fake API in tests**
>
> Bind `NewsApi` with `@Binds` in a Hilt module; in instrumentation tests replace it via `@TestInstallIn` with `FakeNewsApi` — no production code changes needed.


---

# 23. Data Binding vs View Binding vs Compose

| Feature | View Binding | Data Binding | Compose |
|---|---|---|---|
| UI definition | XML | XML | Kotlin |
| Safe view references | Yes | Yes | Not needed in the same way |
| XML expressions | No | Yes | No |
| Declarative UI toolkit | No | No, although it supports data-binding expressions | Yes |
| Replaces XML layout authoring | No | No | Can replace XML for migrated screens |
| Requires ViewModel | No | No | No |

View Binding is useful when you mainly need safe references to XML views.

Data Binding is useful when its expression and binding features are valuable.

Compose is useful for building declarative UI and can coexist with XML-based screens.

---

# 24. OOP and SOLID Principles

## Four Pillars of OOP

| Principle | Meaning | Example |
|---|---|---|
| Encapsulation | Protect internal state and control access | Private mutable state, public read-only StateFlow |
| Abstraction | Expose essential behavior | Repository interface |
| Inheritance | Extend another type | Specialized class extending a base class |
| Polymorphism | Different implementations share a contract | Multiple repository implementations |

## SOLID

- **S — Single Responsibility Principle:** A class should have one coherent responsibility.
- **O — Open/Closed Principle:** Software should support extension without repeated modification of stable code.
- **L — Liskov Substitution Principle:** Implementations should respect the guarantees of their base contract.
- **I — Interface Segregation Principle:** Prefer small, focused interfaces.
- **D — Dependency Inversion Principle:** High-level policy should depend on abstractions rather than concrete low-level details.

Dependency inversion is a design principle. Dependency Injection is one way to provide the dependencies that a design needs.

> [!TIP]
> **Real-world example — SOLID in a payment module**
>
> - **S:** `PaymentValidator` only validates; `PaymentProcessor` only charges.
> - **O/L:** `interface PaymentMethod { fun pay(amount) }` with `UpiPayment`, `CardPayment` — add `WalletPayment` without editing existing code.
> - **D:** `CheckoutViewModel` depends on `PaymentMethod`, not a concrete class.


---

# 25. Testing, Performance and Best Practices

| Area | Best practice |
|---|---|
| Main thread | Avoid blocking I/O and expensive computation |
| Coroutines | Use appropriate scopes, structured concurrency and cancellation |
| Flow | Choose the correct cold/hot semantics and handle upstream errors |
| RxJava | Manage subscriptions and dispose of work when appropriate |
| Room | Use appropriate indexes, transactions and migrations |
| SQLite | Use parameterized queries and manage cursors/resources properly |
| ViewModel | Keep presentation state separate from Activity/Fragment view references |
| Compose | Keep state ownership clear and avoid unnecessary recomposition |
| Repository | Keep data access boundaries clear |
| Dependency Injection | Prefer constructor injection when practical |
| Testing | Unit-test business rules, state transitions, repositories and error handling |
| Offline support | Design sync status, retries, conflict handling and idempotency explicitly |

For Coroutines, use coroutine test utilities such as `runTest` and test dispatchers when appropriate. For database behavior, use suitable Room or SQLite tests. For RxJava code, test stream transformations, errors and subscription lifecycle behavior.

---

# 26. Common Interview Questions

### Q1. What is the difference between synchronous and asynchronous programming?

Synchronous code waits for the current operation to return. Asynchronous code allows completion and results to be handled later. Asynchronous does not necessarily mean multithreaded.

### Q2. What is the difference between a process and a thread?

A process is a running program with its own execution environment and resources. A thread is an execution path within a process.

### Q3. What is the difference between a thread and a coroutine?

A thread is an operating-system execution resource. A coroutine is a lightweight unit of execution that can suspend and resume using available threads.

### Q4. What is the difference between `launch` and `async`?

`launch` returns a `Job`. `async` returns a `Deferred<T>` whose result can be obtained with `await()`.

### Q5. Does `suspend` automatically run code on a background thread?

No. A suspending function can suspend without blocking, but its implementation and dispatcher determine where the work runs.

### Q6. What is the difference between Flow and StateFlow?

Flow represents an asynchronous stream and is cold by default. StateFlow is a hot stream that holds a current value and is commonly used for UI state.

### Q7. What is the difference between StateFlow and SharedFlow?

StateFlow holds current state and requires an initial value. SharedFlow broadcasts emissions and supports configurable replay and buffering.

### Q8. What is the difference between `map` and `flatMapLatest`?

`map` transforms each value. `flatMapLatest` switches to the newest inner Flow and cancels collection of the previous inner Flow.

### Q9. What is the difference between `debounce` and `distinctUntilChanged`?

`debounce` waits for a pause in emissions. `distinctUntilChanged` suppresses consecutive duplicate values.

### Q10. What is the difference between RxJava and Kotlin Flow?

Both support asynchronous stream processing. Flow integrates with Kotlin Coroutines and structured concurrency. RxJava provides its own reactive types, operators, schedulers and subscription-management model.

### Q11. What is the difference between `subscribeOn` and `observeOn`?

`subscribeOn` controls the scheduler used for subscription/source execution. `observeOn` changes the scheduler used for downstream notifications.

### Q12. What is the difference between SQLite and Room?

SQLite is the database engine. Room is an abstraction over SQLite that provides entities, DAOs, query verification and integration with observable data streams.

### Q13. What is the difference between Room and DataStore?

Room is suitable for structured relational data and SQL queries. DataStore is suitable for smaller preference or typed settings data.

### Q14. Why use a repository?

A repository provides a boundary around data sources and helps keep data access decisions out of the UI and ViewModel.

### Q15. Why use WorkManager instead of a coroutine for every background task?

A coroutine is suitable for work tied to a managed scope. WorkManager is intended for suitable persistent, deferrable background work that may need to run or retry after the app or process is no longer active.

### Q16. Why was Jetpack Compose introduced?

Compose provides a declarative way to build UI with Kotlin. It reduces some XML and View boilerplate and makes UI rendering naturally state-driven. It does not replace the whole Android architecture.

### Q17. What is the difference between MVVM and Clean Architecture?

MVVM organizes presentation responsibilities around the View and ViewModel. Clean Architecture organizes broader application responsibilities and dependencies around business rules. They can be used together.

---

# 27. Quick Revision Cheat Sheet

| Problem | Common solution |
|---|---|
| Build traditional Android UI | XML and Views |
| Build declarative Android UI | Jetpack Compose |
| Access XML views safely | View Binding |
| Manage screen state | ViewModel |
| Represent current UI state | StateFlow |
| Expose lifecycle-aware state in an existing View UI | LiveData |
| Run structured asynchronous work | Kotlin Coroutines |
| Represent an asynchronous stream | Flow |
| Transform and combine streams | Flow or RxJava operators |
| Maintain an existing reactive codebase | RxJava |
| Store structured local data | Room / SQLite |
| Store small preferences | DataStore |
| Communicate with a remote API | Retrofit or another HTTP client |
| Organize data sources | Repository pattern |
| Schedule suitable persistent background work | WorkManager |
| Supply dependencies | Constructor injection, Dagger or Hilt |
| Organize presentation | MVVM |
| Organize broader business dependencies | Clean Architecture |
| Reduce main-thread blocking | Appropriate asynchronous I/O and computation |
| Support offline use | Local persistence and explicit synchronization design |

---

## Recommended Learning Order

1. Android OS architecture and application components.
2. Kotlin fundamentals, OOP and SOLID.
3. Synchronous/asynchronous programming, threads and executors.
4. Kotlin Coroutines, dispatchers and cancellation.
5. Flow, StateFlow, SharedFlow and operators.
6. RxJava types, operators, schedulers and disposal.
7. Activity/Fragment lifecycle and ViewModel.
8. XML, View Binding and Jetpack Compose.
9. MVVM, repositories and Clean Architecture.
10. SQL, SQLite, Room, relationships and migrations.
11. Retrofit, offline-first design and WorkManager.
12. Hilt, testing, performance and production debugging.

## Final Takeaway

The key to Android development is understanding the responsibility of each technology rather than memorizing isolated definitions.

- **Coroutines** help structure asynchronous operations.
- **Flow** represents asynchronous streams.
- **StateFlow** holds current state.
- **SharedFlow** broadcasts emissions.
- **RxJava** provides a reactive programming model with rich operators and schedulers.
- **SQLite** is the local relational database engine.
- **Room** makes SQLite easier to use safely and maintainably.
- **ViewModel and MVVM** help organize presentation logic.
- **Clean Architecture** helps organize responsibilities and dependencies.
- **Compose and XML** are different approaches to building Android UI.
- **WorkManager** supports suitable persistent background work.
- **Hilt** helps construct and provide dependencies.

Understanding how these technologies work together will help you design maintainable applications and explain your architectural decisions confidently in Android interviews.

---

### Further Reading

- [Android Developers](https://developer.android.com/)
- [Kotlin Coroutines Guide](https://kotlinlang.org/docs/coroutines-guide.html)
- [Kotlin Flow Documentation](https://kotlinlang.org/docs/flow.html)
- [Room Persistence Library](https://developer.android.com/training/data-storage/room)
- [SQLite Documentation](https://www.sqlite.org/docs.html)
- [Jetpack Compose](https://developer.android.com/compose)
- [Android Architecture Recommendations](https://developer.android.com/topic/architecture)
- [RxJava 3 Documentation](https://github.com/ReactiveX/RxJava)
- [Hilt Documentation](https://developer.android.com/training/dependency-injection/hilt-android)

*This guide is intended as a learning resource and interview preparation reference. Adapt dependency versions, lifecycle choices, database schemas and architecture to the requirements of your actual project.*

---

<div align="center">

## 🤝 Contributing

Found a mistake or want to add an example? PRs are welcome — open an issue or submit a pull request.

⭐ **If this guide helped you, give the repo a star!** ⭐

</div>
