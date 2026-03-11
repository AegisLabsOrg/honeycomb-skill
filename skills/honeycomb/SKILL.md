---
name: honeycomb
description: Write Flutter code using the aegis_honeycomb state management library. Auto-activates when user mentions Honeycomb, StateRef, Computed, Effect, HoneycombScope, or HoneycombConsumer. / 使用 aegis_honeycomb 状态管理库编写 Flutter 代码。当用户提到 Honeycomb、StateRef、Computed、Effect、HoneycombScope 或 HoneycombConsumer 时自动使用。
argument-hint: [describe the state or feature you want to create / 描述你要创建的状态或功能]
---

# Honeycomb State Management Reference

When writing code with `aegis_honeycomb`, strictly follow the patterns below.

---

## Installation / 安装

```yaml
dependencies:
  aegis_honeycomb: ^1.1.1
```

---

## Core Concepts / 核心概念

### StateRef — Read/Write State / 可读写状态

```dart
// Define globally / 全局定义
final counterState = StateRef(0);
final userState = StateRef<User?>(null);

// Read / 读取
final count = container.read(counterState);

// Write / 写入
container.write(counterState, 1);

// Functional update (1.1.1+) / 函数式更新
container.update(counterState, (value) => value + 1);
```

### Computed — Derived State / 派生状态（自动依赖追踪）

```dart
// Single dependency / 单依赖
final doubledCounter = Computed((watch) => watch(counterState) * 2);

// Multiple dependencies / 多依赖
final fullName = Computed((watch) {
  final first = watch(firstNameState);
  final last = watch(lastNameState);
  return '$first $last';
});
```

### Effect — One-time Events / 一次性事件（不存储历史）

```dart
// Define / 定义
final toastEffect = Effect<String>();

// Emit / 触发
container.emit(toastEffect, 'Saved successfully');

// Listen in UI / 在 UI 层监听
ref.on(toastEffect, (message) {
  ScaffoldMessenger.of(context).showSnackBar(...);
});
```

### EagerComputed — Eagerly Evaluated Derived State / 立即计算的派生状态

```dart
final searchResults = EagerComputed((watch) {
  final query = watch(searchQueryState);
  return allItems.where((item) => item.contains(query)).toList();
});
```

---

## Flutter Integration / Flutter 集成

### App Entry / 应用入口

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

### Reading State in UI / UI 中读取状态

```dart
HoneycombConsumer(
  builder: (context, ref, child) {
    final count = ref.watch(counterState);
    return Text('$count');
  },
)
```

### Local Scope (Dependency Injection / Override) / 局部 Scope（依赖注入 / 覆盖）

```dart
HoneycombScope(
  overrides: [
    counterState.overrideWith(StateRef(100)),
  ],
  child: ChildWidget(),
)
```

---

## Context-Free Usage (Service Layer) / Context-Free 用法（Service 层）

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

## Best Practices / 最佳实践

1. **Define StateRef / Computed globally** — not inside classes / **StateRef / Computed 定义在全局**，不要放在类内部
2. **Business logic in Service layer** — access state via container, no BuildContext dependency / **业务逻辑放在 Service 层**，通过 container 访问状态
3. **UI only renders** — use HoneycombConsumer + ref.watch / **UI 只负责展示**，用 HoneycombConsumer + ref.watch
4. **Use Effect for one-time events** — don't simulate with StateRef / **一次性事件用 Effect**，不要用 StateRef 模拟
5. **Immutable collection updates** / **不可变集合更新**：
   ```dart
   container.update(listState, (list) => [...list, newItem]);
   ```

---

## Testing / 测试

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

Respond in the same language the user used.
