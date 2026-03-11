---
name: honeycomb
description: Write Flutter code using the aegis_honeycomb state management library. Auto-activates when user mentions Honeycomb, StateRef, Computed, Effect, HoneycombScope, or HoneycombConsumer.
argument-hint: "[describe the state or feature you want to create]"
---

# Honeycomb State Management Reference

When writing code with `aegis_honeycomb`, strictly follow the patterns below.

---

## Installation

```yaml
dependencies:
  aegis_honeycomb: ^1.1.1
```

---

## Core Concepts

### StateRef — Read/Write State

```dart
// Define globally
final counterState = StateRef(0);
final userState = StateRef<User?>(null);

// Read
final count = container.read(counterState);

// Write
container.write(counterState, 1);

// Functional update (1.1.1+)
container.update(counterState, (value) => value + 1);
```

### Computed — Derived State with Auto Dependency Tracking

```dart
// Single dependency
final doubledCounter = Computed((watch) => watch(counterState) * 2);

// Multiple dependencies
final fullName = Computed((watch) {
  final first = watch(firstNameState);
  final last = watch(lastNameState);
  return '$first $last';
});
```

### Effect — One-time Events (no history stored)

```dart
// Define
final toastEffect = Effect<String>();

// Emit
container.emit(toastEffect, 'Saved successfully');

// Listen in UI
ref.on(toastEffect, (message) {
  ScaffoldMessenger.of(context).showSnackBar(...);
});
```

### EagerComputed — Eagerly Evaluated Derived State

```dart
final searchResults = EagerComputed((watch) {
  final query = watch(searchQueryState);
  return allItems.where((item) => item.contains(query)).toList();
});
```

---

## Flutter Integration

### App Entry

```dart
final appContainer = HoneycombContainer();

void main() {
  runApp(
    HoneycombScope(
      container: appContainer,
      child: const MyApp(),
    ),
  );
}
```

### Reading State in UI

```dart
HoneycombConsumer(
  builder: (context, ref, child) {
    final count = ref.watch(counterState);
    return Text('$count');
  },
)
```

### Local Scope (Dependency Injection / Override)

```dart
HoneycombScope(
  overrides: [
    counterState.overrideWith(StateRef(100)),
  ],
  child: ChildWidget(),
)
```

---

## Context-Free Usage (Service Layer)

Access state in pure Dart services without BuildContext:

```dart
class UserService {
  final HoneycombContainer _container;

  UserService(this._container);

  void login(User user) {
    _container.write(userState, user);
  }

  User? getUser() => _container.read(userState);
}
```

---

## Best Practices

1. **Define StateRef / Computed globally** — not inside classes
2. **Business logic in Service layer** — access state via container, no BuildContext dependency
3. **UI only renders** — use HoneycombConsumer + ref.watch
4. **Use Effect for one-time events** — don't simulate with StateRef
5. **Immutable collection updates**:
   ```dart
   container.update(listState, (list) => [...list, newItem]);
   ```

---

## Testing

```dart
test('counter increments', () {
  final container = HoneycombContainer();
  expect(container.read(counterState), 0);
  container.update(counterState, (v) => v + 1);
  expect(container.read(counterState), 1);
});
```

---

## Instructions

The user's request: $ARGUMENTS

Based on the description above, generate idiomatic Honeycomb code including:
- `StateRef` / `Computed` / `Effect` definitions
- Service layer implementation (if needed)
- `HoneycombConsumer` UI code (if needed)
- Corresponding test code (if needed)
