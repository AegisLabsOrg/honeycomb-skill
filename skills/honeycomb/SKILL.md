---
name: honeycomb
description: 使用 aegis_honeycomb 状态管理库编写 Flutter 代码。当用户提到 Honeycomb、StateRef、Computed、Effect、HoneycombScope 或 HoneycombConsumer 时自动使用。
argument-hint: [描述你要创建的状态或功能]
---

# Honeycomb 状态管理规范

使用 `aegis_honeycomb` 库编写代码时，严格遵循以下规范。

## 安装

```yaml
dependencies:
  aegis_honeycomb: ^1.1.1
```

## 核心概念

### StateRef — 可读写状态

```dart
// 定义（全局）
final counterState = StateRef(0);
final userState = StateRef<User?>(null);

// 读取
final count = container.read(counterState);

// 写入
container.write(counterState, 1);

// 函数式更新（1.1.1+）
container.update(counterState, (value) => value + 1);
```

### Computed — 派生状态（自动依赖追踪）

```dart
// 定义
final doubledCounter = Computed((watch) => watch(counterState) * 2);

// 多依赖
final fullName = Computed((watch) {
  final first = watch(firstNameState);
  final last = watch(lastNameState);
  return '$first $last';
});
```

### Effect — 一次性事件（不存储历史）

```dart
// 定义
final toastEffect = Effect<String>();

// 触发
container.emit(toastEffect, '保存成功');

// 监听（在 UI 层）
ref.on(toastEffect, (message) {
  ScaffoldMessenger.of(context).showSnackBar(...);
});
```

### EagerComputed — 立即计算的派生状态

```dart
final searchResults = EagerComputed((watch) {
  final query = watch(searchQueryState);
  return allItems.where((item) => item.contains(query)).toList();
});
```

## Flutter 集成

### 应用入口

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

### UI 中读取状态

```dart
HoneycombConsumer(
  builder: (context, ref, child) {
    final count = ref.watch(counterState);
    return Text('$count');
  },
)
```

### 局部 Scope（依赖注入 / 覆盖）

```dart
HoneycombScope(
  overrides: [
    counterState.overrideWith(StateRef(100)),
  ],
  child: ChildWidget(),
)
```

## Context-Free 用法（Service 层）

在非 UI 层（Service、Repository）直接用全局 container：

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

## 最佳实践

1. **StateRef / Computed 定义在全局**，不要放在类内部
2. **业务逻辑放在 Service 层**，通过 container 访问状态，不依赖 BuildContext
3. **UI 只负责展示**，用 HoneycombConsumer + ref.watch 响应状态变化
4. **一次性事件用 Effect**，不要用 StateRef 模拟（避免重复触发）
5. **不可变集合更新**用 update 方法：
   ```dart
   container.update(listState, (list) => [...list, newItem]);
   ```

## 测试

```dart
test('counter increments', () {
  final container = HoneycombContainer();
  expect(container.read(counterState), 0);
  container.update(counterState, (v) => v + 1);
  expect(container.read(counterState), 1);
});
```

## 用户请求处理

用户提供的参数：$ARGUMENTS

根据用户描述，生成符合以上规范的 Honeycomb 代码。包括：
- StateRef / Computed / Effect 定义
- Service 层实现（如需要）
- HoneycombConsumer UI 代码（如需要）
- 对应的测试代码（如需要）
