# Mobile Programming Course: Complete Lectures Summary

This comprehensive summary captures and organizes the complete syllabus, programming paradigms, and coding patterns from all 15 weeks of the mobile programming course.

---

## Table of Contents

1. [Course Admin & Syllabus](#1-course-admin--syllabus)
2. [Fundamentals of Kotlin](#2-fundamentals-of-kotlin)
3. [Introduction to Jetpack Compose](#3-introduction-to-jetpack-compose)
4. [Basic Layouts & The Modifier System](#4-basic-layouts--the-modifier-system)
5. [Core Components & Interactive Elements](#5-core-components--interactive-elements)
6. [Scrollable Lists & Lazy Layouts](#6-scrollable-lists--lazy-layouts)
7. [Advanced Components, App Bars & Dialogs](#7-advanced-components-app-bars--dialogs)
8. [State Management, UDF & Architecture](#8-state-management-unidirectional-data-flow-udf--architecture)
9. [Navigation, Lifecycle & Intents](#9-navigation-lifecycle--intents)
10. [Coroutines & Asynchronous Programming](#10-coroutines--asynchronous-programming)
11. [Data Management Layer (Room DB)](#11-data-management-layer-room-db)
12. [Cloud Integration (Firebase & Firestore)](#12-cloud-integration-firebase--firestore)
13. [Themes, File Handling & Animations](#13-themes-file-handling--animations)
14. [Testing, Stability & Advanced Debugging Tools](#14-testing-stability--advanced-debugging-tools)

---

## 1. Course Admin & Syllabus

### Academic Parameters

**Lecturers:** Amela Vatreš Mudželet, Ilma Gusinac, Mirza Krupić, Ajla Korman.

**Platforms:**
- **LMS** (lms.ibu.edu.ba): Code checks, material downloads, project submissions.
- **SIS** (mine.ibu.edu.ba): Tracking of grades, scholarships, and attendance records.

**Grading Breakdown:**
- Final Exam: **50%**
- Quizzes: **25%** (+ 5% bonus) — 12 quizzes (2.5 points each), held during the first 5 minutes of every lab session.
- Assignments/Projects: **25%** (+ 5% bonus) — Individual student projects.

**Attendance Mandate:** A minimum of 55% attendance on both lectures and labs is strictly required to qualify for exams.

### 15-Week Plan

| Week | Topic |
|------|-------|
| W1 | Course Overview, Android Studio Setup & Debugging |
| W2 | Kotlin Fundamentals |
| W3 | Jetpack Compose Introduction |
| W4 | Layout Components |
| W5 | State in Compose |
| W6 | Scrollable Lists |
| W7 | Navigation & Intents |
| W8 | ViewModel & State Management |
| W9 | Coroutines & Asynchronous Coding |
| W10–11 | Local Storage (SQL, Room DB) & Deep Dive |
| W12 | Cloud Integration (Firebase / Firestore) |
| W13 | Themes, Animations & File Exporting |
| W14 | Testing, Stability & Advanced Debugging |
| W15 | Project Review & Wrap-up |

---

## 2. Fundamentals of Kotlin

### Conciseness, Interoperability & Safety

Kotlin is Google's official Android language (declared in 2017). It compiles into Java-compatible bytecode but brings modern language design:

- **Conciseness:** Approximately 40% fewer lines of code than Java.
- **Interoperability:** 100% compatible with existing Java codebases.
- **Null Safety:** Built directly into the type system to catch null pointer errors at compile time rather than runtime.

### Immutable vs. Mutable Variables

- **`val` (Value):** Read-only reference (immutable). Once assigned, it cannot be reassigned.
- **`var` (Variable):** Mutable reference. Allows reassignment.

> **Best Practice:** Always prefer `val` over `var` to enforce state predictability and write safer code.

```kotlin
val name = "Alice" // Immutable
var age = 25       // Mutable
age = 26           // Allowed
// name = "Bob"    // Compile error!
```

### Static Type System & Type Inference

Kotlin is statically typed. Types are determined at compile time and do not change.

- **Type Inference:** The compiler automatically deduces types when clear from context (e.g. `val count = 42` is inferred as `Int`).
- **Explicit Types:** Required for function parameters, return types, and when initializing variables without immediate assignment.

```kotlin
val count: Int = 42
val price: Double = 19.99
```

### Control Flow Expressions

In Kotlin, control flow structures like `if` and `when` are expressions that can return values.

#### `if` Expression

```kotlin
val max = if (a > b) a else b
```

#### `when` Expression

Serves as an improved, highly readable replacement for `switch` blocks. It supports individual values, ranges, type checks, and boolean expressions.

```kotlin
val grade = when (score) {
    in 90..100 -> "A"
    in 80..89  -> "B"
    is Int     -> "Numeric check passed"
    else       -> "F"
}
```

### Null Safety Mechanisms

To prevent the "Billion Dollar Mistake" (`NullPointerException`), Kotlin divides types into non-nullable (default) and nullable (notated with `?`).

```kotlin
var nonNullString: String = "Hello"
// nonNullString = null // Compile Error!

var nullableString: String? = "Hello"
nullableString = null // Legal
```

#### Operators for Handling Nullability

| Operator | Name | Behaviour |
|----------|------|-----------|
| `?.` | Safe Call | Executes accessors only if non-null; otherwise returns `null`. |
| `?:` | Elvis Operator | Provides a fallback default value when expression is `null`. |
| `!!` | Non-Null Assertion | Asserts non-null; throws NPE if actually `null`. Avoid in production. |
| `?.let` | Safe Let Scoping | Runs a block exclusively when the variable is non-null. |

```kotlin
val length: Int = nullableString?.length ?: 0

nullableString?.let {
    println("Non-null. Value: $it")
}
```

### Functions

- **Default Arguments:** Avoids function overload clutter.
- **Named Arguments:** Allows parameters to be specified in any order at the call site.
- **Single-Expression Functions:** Shorthand syntax for simple, single-line functions.

```kotlin
// Default params & named args
fun createUser(name: String, role: String = "User", status: Boolean = true) { ... }
createUser(status = false, name = "Bob") // Reordered call

// Single-expression function
fun double(x: Int) = x * 2
```

### Extension Functions

Allows developers to inject new methods into existing classes without inheritance or modifying the original source.

```kotlin
fun String.addExclamation(): String {
    return this + "!"
}

val loudGreeting = "Hello".addExclamation() // "Hello!"
```

### Functional Collections (List, Set, Map)

Kotlin divides collection APIs into read-only and mutable wrappers (e.g., `listOf()` vs `mutableListOf()`).

#### Functional Operators

- **`map`:** Transforms elements within a collection.
- **`filter`:** Keeps elements matching a specified boolean predicate.
- **`forEach`:** Iterates through elements to perform an action.

```kotlin
val numbers = listOf(1, 2, 3, 4, 5)
val doubledEvens = numbers.filter { it % 2 == 0 }.map { it * 2 } // [4, 8]
```

---

## 3. Introduction to Jetpack Compose

### Declarative vs. Imperative UI

- **Imperative UI (XML + Views):** Android's legacy layout system. Developers construct a layout tree (XML) and manually manipulate it via Java/Kotlin code (using `findViewById` or `ViewBinding`). The developer describes *how* to mutate the visual state.
- **Declarative UI (Compose):** The developer describes *what* the UI looks like for any given application state. When state changes, Compose automatically regenerates and updates the affected components.

### Composable Functions

UI elements are declared programmatically using standard Kotlin functions annotated with `@Composable`.

- Must start with an uppercase letter (PascalCase) and represent a noun or role-based name (e.g., `SubmitButton`).
- Must return `Unit`.
- Can only be called from within another `@Composable` function.
- Accept parameters as input data dependencies.

```kotlin
@Composable
fun MessageCard(name: String) {
    Text(text = "Hello $name!")
}
```

### Live Previews (`@Preview`)

Compose uses `@Preview` to render components directly inside Android Studio without running an emulator.

- **Rule:** Preview functions must not accept parameters.
- **Parameters:** Supports `showBackground = true`, `uiMode` (dark mode), `fontScale`, and device templates.

```kotlin
@Preview(showBackground = true, name = "Light Mode")
@Composable
fun PreviewMessageCard() {
    MessageCard("Android")
}
```

### Recomposition

When state values change, Compose re-executes the layout tree to reflect the new state. This process is called **Recomposition**.

- **Smart Recomposition:** Compose skips composables whose inputs have not changed. Only views reading the mutated state are re-run.

---

## 4. Basic Layouts & The Modifier System

### Core Layout Containers

- **`Column`:** Positions child components sequentially in a vertical column.
- **`Row`:** Positions child components sequentially in a horizontal row.
- **`Box`:** Stacks child components on top of one another — useful for layers, badges, or overlays.

| Container | Main Axis Direction | Main Axis Spacing | Cross-Axis Positioning |
|-----------|--------------------|--------------------|------------------------|
| `Column` | Vertical ↓ | `verticalArrangement` | `horizontalAlignment` |
| `Row` | Horizontal → | `horizontalArrangement` | `verticalAlignment` |
| `Box` | Stacked (depth) ↗ | N/A | `contentAlignment` |

### The Modifier System

Modifiers are ordered chains of instructions that change a composable's size, layout, appearance, and interaction behaviour.

#### Chaining Order Rule

Modifiers are evaluated **sequentially from left-to-right** (outer-to-inner). Changing the order changes the output:

- `.background(Color.Blue).padding(16.dp)` → solid blue container, spacing applied to its contents.
- `.padding(16.dp).background(Color.Blue)` → spacing on the outer boundary, only the inner container is blue.
- Applying `.clickable { }` before `.padding` ensures the entire padded area registers clicks.

```kotlin
// Clicking the entire padded box is possible here
Box(
    modifier = Modifier
        .size(100.dp)
        .clickable { /* action */ }
        .padding(16.dp)
        .background(Color.Green)
)
```

### Advanced Sizing & Layout Weights

- **`Modifier.weight`:** Used inside a `Column` or `Row` to distribute remaining space dynamically among children based on relative float parameters.
- **Fractions:** Functions like `fillMaxWidth(0.5f)` scale sizes based on proportions (`0.0f` to `1.0f`) of the maximum available space.

---

## 5. Core Components & Interactive Elements

### Buttons & FABs

Compose provides semantic button variations to establish clear interface hierarchies:

- **`Button`:** Standard filled button — primary action of a screen.
- **`OutlinedButton`:** Medium-emphasis action (e.g., cancel / secondary choices).
- **`TextButton`:** Low-emphasis text-only trigger (e.g., "Skip", "Dismiss").
- **`IconButton`:** Custom clickable vector icon.
- **`FloatingActionButton` / `ExtendedFloatingActionButton`:** Floating action node positioned above background content.

```kotlin
Button(
    onClick = { /* callback */ },
    enabled = isFormValid
) {
    Text("Submit Form")
}
```

### Text Input with Controlled State

Text entry uses `TextField` (filled style) or `OutlinedTextField`.

**The Controlled Pattern:** These are stateless controls — they do not maintain internal text memory. The developer is responsible for storing typed characters in state and updating it on every keystroke inside `onValueChange`. If you pass a constant value and omit updating the state, nothing will be typed on screen.

```kotlin
var textState by remember { mutableStateOf("") }

OutlinedTextField(
    value = textState,
    onValueChange = { textState = it },
    label = { Text("Username") },
    singleLine = true
)
```

### Selection Controls

- **`Checkbox`:** Binary checked/unchecked element, for choosing multiple options from a set.
- **`RadioButton`:** Selection control for mutually exclusive choices in grouped arrays.
- **`Switch`:** Toggle representing binary choices that take immediate effect.
- **`Slider` / `RangeSlider`:** Maps selected coordinates to a continuous or stepped `Float` range.

### Image and Icon Assets

- **`Icon`:** Utility container optimized for vector resources, automatically colored via a `tint` parameter.
- **`Image`:** Renders raster/vector graphics. Sizing adaptations use `ContentScale` strategies (`Crop`, `Fit`, `FillBounds`).
- **Coil Integration:** Since Compose's default image loader does not process network URLs natively, Coil enables `AsyncImage` to manage network fetching, disk caching, and loading placeholders in background coroutines.

```kotlin
AsyncImage(
    model = "https://picsum.photos/400/200",
    contentDescription = "Cover Image",
    contentScale = ContentScale.Crop,
    modifier = Modifier.fillMaxWidth().height(200.dp)
)
```

---

## 6. Scrollable Lists & Lazy Layouts

### Column with Scroll vs. Lazy Layouts

- **`Column + verticalScroll`:** Instantiates and renders all child components immediately, regardless of visibility. Causes significant performance and memory overhead for large datasets.
- **`LazyColumn` / `LazyRow`:** Implements **UI virtualization**. Only compiles, measures, and renders components visible within the viewport. Off-screen layouts are recycled to render newly entering items.

### Lazy DSL Layout Builders

Inside a lazy component block, children are declared using dedicated DSL methods:

- **`item { ... }`:** Emits a single item.
- **`items(list) { ... }`:** Loops over a list to generate rows dynamically.
- **`itemsIndexed(list) { index, item -> ... }`:** Loops over a list, providing both item and its index (e.g., for alternating row colors).

```kotlin
LazyColumn(
    contentPadding = PaddingValues(16.dp),
    verticalArrangement = Arrangement.spacedBy(8.dp)
) {
    item { Text("Header Block", style = MaterialTheme.typography.titleMedium) }

    items(items = userList) { user ->
        UserCard(user = user)
    }
}
```

### Preserving State via Item Keys

By default, Compose uses an item's position index as its unique identifier. Deleting or reordering items can cause state loss or wrong values being displayed.

> **Solution:** Always define a custom, unique `key` lambda inside your `items` block to lock component state correctly to the underlying data object.

```kotlin
items(
    items = taskList,
    key = { task -> task.id } // Stable unique identity
) { task ->
    TaskRow(task = task, modifier = Modifier.animateItem())
}
```

---

## 7. Advanced Components, App Bars & Dialogs

### Modal Dialogs & Overlays

**`AlertDialog`:** Displays a modal dialog overlaying the screen, blocking interaction with the background until the user chooses an option.

```kotlin
if (showDialog) {
    AlertDialog(
        onDismissRequest = { showDialog = false },
        title = { Text("Confirm Delete") },
        text = { Text("Are you sure you want to delete this?") },
        confirmButton = {
            Button(onClick = { showDialog = false }) { Text("Delete") }
        }
    )
}
```

**`DropdownMenu`:** Floating popup menu. Position it inside a parent `Box` container alongside the trigger button for correct anchoring.

### Material 3 Scaffold Framework

`Scaffold` provides a structured template implementing standard slots for material components, ensuring correct padding distribution across layouts.

```kotlin
val snackbarHostState = remember { SnackbarHostState() }
val scope = rememberCoroutineScope()

Scaffold(
    topBar = { TopAppBar(title = { Text("App Title") }) },
    snackbarHost = { SnackbarHost(hostState = snackbarHostState) },
    floatingActionButton = {
        FloatingActionButton(onClick = {
            scope.launch { snackbarHostState.showSnackbar("Action complete") }
        }) { Icon(Icons.Default.Add, contentDescription = "Add") }
    }
) { innerPadding ->
    Column(modifier = Modifier.padding(innerPadding)) {
        // Safe screen content inside the padding boundaries
    }
}
```

### Badges & Notification Tags

Using `BadgedBox`, developers can overlay numeric values or badge dots directly onto parent assets.

```kotlin
BadgedBox(
    badge = { Badge { Text("5") } }
) {
    Icon(Icons.Default.Email, contentDescription = "Inbox")
}
```

---

## 8. State Management, Unidirectional Data Flow (UDF) & Architecture

### What is State?

State is any variable value in your app that can change over time. Since Jetpack Compose is declarative, you don't mutate UI elements directly — you update the state, and the UI recomposes automatically.

- **`mutableStateOf(value)`:** Returns an observable state holder. When its value changes, Compose schedules a recomposition for all composables reading it.
- **`remember { }`:** Saves the state value inside composition memory, preserving it across recompositions.

```kotlin
// ❌ Resets to 0 on every recomposition
@Composable
fun BadCounter() {
    var count = 0
    Button(onClick = { count++ }) { Text("Count: $count") }
}

// ✅ State is preserved across recompositions
@Composable
fun GoodCounter() {
    var count by remember { mutableStateOf(0) }
    Button(onClick = { count++ }) { Text("Count: $count") }
}
```

### Survival Across Configuration Changes

`remember { }` preserves state during recompositions but loses values when a composable leaves the composition entirely (e.g., device rotation destroys and recreates the host Activity).

> **Solution:** Use `rememberSaveable { }`. It automatically saves state to an Android `Bundle` on configuration changes and restores it when the composable re-enters the layout tree.

| API | Survives Recomposition | Survives Screen Rotation | Survives Process Death |
|-----|:---:|:---:|:---:|
| `var x = mutableStateOf(0)` | ❌ | ❌ | ❌ |
| `remember { mutableStateOf(0) }` | ✅ | ❌ | ❌ |
| `rememberSaveable { mutableStateOf(0) }` | ✅ | ✅ | ❌ |
| `ViewModel (StateFlow)` | ✅ | ✅ | ❌ |

### Unidirectional Data Flow (UDF) & State Hoisting

To write modular, clean, and testable layouts, enforce **Unidirectional Data Flow (UDF)**:

- **State flows down** from parent containers into child components as parameters.
- **Events flow up** from child components to the parent via function callbacks.

#### State Hoisting Pattern

State hoisting moves state values up to a parent component, transforming children into pure, stateless views.

- **Stateful Composable:** Manages its own state internally. Convenient but difficult to reuse or preview.
- **Stateless Composable:** Receives state via arguments and passes events up via callbacks. Highly reusable, easy to preview, and simple to unit test.

```kotlin
// ✅ Stateless — easy to test and preview
@Composable
fun CounterDisplay(
    count: Int,
    onIncrement: () -> Unit,
    modifier: Modifier = Modifier
) {
    Column(modifier = modifier, horizontalAlignment = Alignment.CenterHorizontally) {
        Text("Count: $count")
        Button(onClick = onIncrement) { Text("+") }
    }
}

// Stateful wrapper
@Composable
fun CounterScreen() {
    var count by rememberSaveable { mutableStateOf(0) }
    CounterDisplay(count = count, onIncrement = { count++ })
}
```

### Derived State Optimization

**`derivedStateOf { ... }`:** A performance optimization wrapper. Use it when a state value is dynamically computed from other changing states. It monitors mutations but triggers recomposition only when the final computed result changes — ideal for scroll position checks, pagination, or real-time validation.

```kotlin
val cartItems = remember { mutableStateListOf<CartItem>() }

// Recomposes only when total crosses the threshold, not on every item add/remove
val isEligibleForFreeShipping by remember {
    derivedStateOf { cartItems.sumOf { it.price } >= 50.0 }
}
```

---

## 9. Navigation, Lifecycle & Intents

### Core Navigation Architecture

- **`NavController`:** Central API manager tracking back-stack history and routing transitions.
- **`NavHost`:** Container viewport that dynamically swaps composable screens based on the `NavController`'s active destination.
- **`NavGraph`:** Logical map that registers route strings and associates them with composable screens.

```kotlin
@Composable
fun AppNavHost() {
    val navController = rememberNavController()

    NavHost(navController = navController, startDestination = "home") {
        composable("home") { HomeScreen(navController) }
        composable("profile") { ProfileScreen(navController) }
    }
}
```

### Passing Arguments via Routes

Routes function like website URLs. Parameters are passed within route path patterns:

- **Required Parameters:** Declared within curly braces, e.g., `"details/{id}"`.
- **Optional Parameters:** Declared using query string syntax, e.g., `"list?category={category}"`. Must include a default value.
- **`NavType` Configuration:** Ensure parameters are parsed correctly using the `arguments` parameter on the composable declaration.

```kotlin
composable(
    route = "details/{itemId}",
    arguments = listOf(navArgument("itemId") { type = NavType.IntType })
) { backStackEntry ->
    val itemId = backStackEntry.arguments?.getInt("itemId") ?: 0
    DetailsScreen(itemId = itemId)
}
```

### Controlling the Back Stack

To prevent back-stack clutter (e.g., preventing users from returning to the login screen after authenticating):

- **`popUpTo(route)`:** Pops items off the stack up to the specified route before navigating.
- **`inclusive = true`:** Also pops the specified route itself.
- **`launchSingleTop = true`:** Ensures only one instance of a screen exists at the top of the stack.
- **`restoreState = true` / `saveState = true`:** Saves and restores scroll states when switching between tabs.

```kotlin
navController.navigate("home") {
    popUpTo("login") { inclusive = true } // Login screen popped entirely
    launchSingleTop = true
}
```

---

## 10. Coroutines & Asynchronous Programming

### The Main Thread Challenge

Android runs all user interactions and screen rendering on a single **Main (UI) Thread**.

- To render a smooth 60 FPS profile, the UI thread must complete all draw operations within a **16ms window**.
- Running blocking tasks (API requests, Room writes, large file parsing) on the UI thread causes lag or an **App Not Responding (ANR)** crash.

> **The Golden Rule: Never block the Main (UI) thread.**

### Coroutines Framework

Coroutines are lightweight, cooperative execution blocks running on top of system threads.

- Creating an OS thread consumes **1–2MB** of stack memory.
- A coroutine costs only **a few hundred bytes** in heap memory.

### Coroutine Scope Lifecycle Management

Every coroutine must run within a defined scope to ensure structural concurrency and prevent memory leaks:

- **`viewModelScope`:** Scoped to the life of a `ViewModel`. Automatically cancels all child coroutines when the `ViewModel` is destroyed.
- **`lifecycleScope`:** Scoped to an `Activity` or `Fragment`'s lifecycle.
- **`rememberCoroutineScope()`:** Composable scope used to launch coroutines directly inside click handlers or UI events.

```kotlin
@Composable
fun EventButton(snackbarState: SnackbarHostState) {
    val scope = rememberCoroutineScope()
    Button(onClick = {
        scope.launch {
            snackbarState.showSnackbar("Button Clicked!")
        }
    }) { Text("Trigger") }
}
```

### Coroutine Builders

- **`launch`:** Fire-and-forget. Returns a `Job` handle and does not yield a direct return result.
- **`async`:** For concurrent work. Returns a `Deferred<T>` handle; fetch the result using `.await()`.

```kotlin
// Runs both network requests concurrently
val userDeferred  = async { api.getUser() }
val postsDeferred = async { api.getPosts() }

val user  = userDeferred.await()
val posts = postsDeferred.await()
```

### The `suspend` Contract

A function marked with `suspend` can pause execution without blocking the calling thread. While suspended, the thread is released to perform other work (such as rendering UI).

> **Rule:** Suspend functions can only be called from within a coroutine scope or another suspend function.

```kotlin
suspend fun fetchUserData(): User = withContext(Dispatchers.IO) {
    apiService.getUserProfile()
}
```

### Dispatchers Mapping

| Dispatcher | Purpose |
|------------|---------|
| `Dispatchers.Main` | Restricts execution to the UI thread; use for updating states or rendering. |
| `Dispatchers.IO` | Optimized for disk read/write, network requests, and database transactions. |
| `Dispatchers.Default` | Optimized for CPU-intensive calculations (sorting datasets, processing images). |

---

## 11. Data Management Layer (Room DB)

### Room Abstraction Layer

Room is an official Jetpack abstraction layer over raw SQLite. It brings database validation checks at **compile time** and maps raw SQL records directly into Kotlin data classes.

### The Three Pillars of Room

- **Entities (`@Entity`):** Data classes annotated with `@Entity` that map directly to SQLite tables.
- **DAOs (`@Dao`):** Data Access Objects. Interfaces defining queries, insertions, deletions, and updates — verified at compile time.
- **`RoomDatabase`:** Abstract holder class representing the main database structure on disk.

```kotlin
// 1. Entity table definition
@Entity(tableName = "todos")
data class TodoEntity(
    @PrimaryKey(autoGenerate = true) val id: Int = 0,
    val title: String,
    val isCompleted: Boolean = false
)

// 2. Data Access Object
@Dao
interface TodoDao {
    @Query("SELECT * FROM todos")
    fun getAllTodos(): Flow<List<TodoEntity>> // Reactive data flow

    @Insert(onConflict = OnConflictStrategy.REPLACE)
    suspend fun insertTodo(todo: TodoEntity)
}
```

### Reactive Persistence Flow

- **Read Operations:** Returning `Flow<List<Entity>>` in your DAO sets up reactive triggers. Any write, deletion, or update automatically emits the latest dataset to all active subscribers.
- **Write Operations:** Insert, update, and delete queries must be marked `suspend` to execute safely on background threads via `Dispatchers.IO`.

---

## 12. Cloud Integration (Firebase & Firestore)

### Cloud-Hosted NoSQL Databases

Firebase Firestore is a hosted NoSQL database structuring data into flat collections and flexible JSON-like documents, replacing relational tables with schema-free structures.

```json
{
    "userId": "uid_abc123",
    "title": "Buy milk",
    "isCompleted": false,
    "tagIds": ["1", "2"]
}
```

### `callbackFlow` for Real-time Streams

Firestore uses event listeners to push database updates to clients in real time. Use `callbackFlow` to convert these listeners into standard, reactive Kotlin Flows.

```kotlin
override fun getTodos(userId: String): Flow<List<Todo>> = callbackFlow {
    val listener = firestore.collection("todos")
        .whereEqualTo("userId", userId)
        .addSnapshotListener { snapshot, error ->
            if (error != null) { close(error); return@addSnapshotListener }
            if (snapshot != null) {
                val list = snapshot.documents.mapNotNull { doc ->
                    doc.toObject(TodoEntity::class.java)?.toDomain()
                }
                trySend(list) // Push updated list down the Flow
            }
        }
    awaitClose { listener.remove() } // Clean up the listener
}
```

### Offline Data Synchronization

The Firebase Firestore SDK enables **disk persistence** on Android out of the box. Read requests fall back to cached local storage when offline, and write operations are queued and replayed automatically when connectivity is restored.

---

## 13. Themes, File Handling & Animations

### Semantic Material 3 Theming

Material Design 3 replaces hardcoded hex colors with paired system roles (`primary` + `onPrimary`, `surface` + `onSurface`).

- **The "On" Color Rule:** Every primary role color has an associated "On" color, guaranteeing readable contrast and accessibility (a11y) compliance across theme shifts.
- **Dynamic Color:** The application's color palette can adapt dynamically to match the user's chosen wallpaper.

```kotlin
Text(
    text = "Important Alert",
    color = MaterialTheme.colorScheme.onErrorContainer,
    modifier = Modifier.background(MaterialTheme.colorScheme.errorContainer)
)
```

### File Extraction & Scoped Storage APIs

Android isolates public storage configuration depending on OS version:

- **Modern (Android 10+):** Uses the `MediaStore` API. Files are written into shared media domains (e.g., Downloads) without requiring runtime permissions.
- **Legacy (Android 9-):** Uses traditional `File` API structures, which require explicit runtime storage permissions.

```kotlin
// Android 10+ media file creation
val contentValues = ContentValues().apply {
    put(MediaColumns.DISPLAY_NAME, "report.csv")
    put(MediaColumns.MIME_TYPE, "text/csv")
    put(MediaColumns.RELATIVE_PATH, Environment.DIRECTORY_DOWNLOADS)
}

val uri = context.contentResolver.insert(MediaStore.Downloads.EXTERNAL_CONTENT_URI, contentValues)
context.contentResolver.openOutputStream(uri!!)?.use { outputStream ->
    outputStream.write(csvData.toByteArray())
}
```

### UX Motion & Animations

Compose provides rich animation APIs to make interfaces feel polished and responsive:

- **`AnimatedVisibility`:** Animates visual entries and exits (fade, scale, expand) for conditional UI elements.
- **`AnimatedContent`:** Smoothly morphs layouts during transition state shifts (e.g., crossfading between screens).
- **`animateItem`:** A specialized `LazyList` modifier that automatically animates items when added, deleted, or reordered.
- **`animateFloatAsState` / `animateColorAsState`:** Automatically interpolates single properties (like a progress bar's percentage or a container's color) whenever their values change.

```kotlin
val progress by animateFloatAsState(targetValue = percentCompleted)

LinearProgressIndicator(progress = { progress })
```

---

## 14. Testing, Stability & Advanced Debugging Tools

### Modern Architecture Testing Stack

High-quality code testing uses a standardized library stack:

- **JUnit 4:** Base engine providing structural test annotations (`@Test`, `@Before`, `@After`).
- **MockK:** Kotlin-native mocking library with built-in support for intercepting coroutine suspend functions.
- **Turbine:** Asynchronous testing library for validating Kotlin Flows sequentially.
- **In-Memory Room Databases:** Tests write transactions safely inside system RAM using `Room.inMemoryDatabaseBuilder`, ensuring clean environments for each test run and preventing flaky data anomalies.

```kotlin
@Test
fun testViewModelFlowUpdates() = runTest {
    viewModel.onAddTodo("New Task")

    viewModel.uiState.test {
        val state = awaitItem()
        assertEquals(1, state.todos.size)
        assertEquals("New Task", state.todos[0].title)
    }
}
```

### Production Diagnostics & App Stability Tools

- **LeakCanary:** Memory inspection library for staging builds. Automatically monitors un-reclaimed object allocations and alerts developers with a detailed stack trace when a memory leak is identified.
- **Firebase Crashlytics:** Crash reporting service that monitors production runs, groups similar exceptions, and logs detailed diagnostic reports.

### Diagnostic Tools within Android Studio

| Tool | Description |
|------|-------------|
| **Layout Inspector** | Real-time 3D representation of your Composable tree; analyze layout boundaries and recomposition counts. |
| **Database Inspector** | Connects to running devices to inspect and modify local SQLite tables in real time. |
| **Network Inspector** | Intercepts and logs request headers and payload responses of your network client (OkHttp/Retrofit). |
| **Memory Profiler** | Renders a continuous timeline of memory allocations to help identify and fix memory leaks. |
