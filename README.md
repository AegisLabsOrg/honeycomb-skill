# honeycomb-skill

A Claude Code plugin that teaches Claude how to write idiomatic [aegis_honeycomb](https://pub.dev/packages/aegis_honeycomb) code — a lightweight, codegen-free Flutter state management library.

## Install

```bash
claude plugin install github:AegisLabsOrg/honeycomb-skill
```

Restart Claude Code after installing.

## Usage

Once installed, just describe what you need:

```
/honeycomb 创建一个购物车状态，支持添加商品和计算总价
```

```
/honeycomb create a user authentication state with login and logout
```

Claude will automatically generate idiomatic Honeycomb code including `StateRef`, `Computed`, `Effect`, service layer, and UI widgets.

You can also just mention Honeycomb naturally in conversation — Claude will recognize it and apply the correct patterns automatically.

## What it covers

- `StateRef` — read/write state atoms
- `Computed` — derived state with automatic dependency tracking
- `EagerComputed` — eagerly evaluated derived state
- `Effect` — one-time events (toasts, navigation, etc.)
- `HoneycombScope` / `HoneycombConsumer` — Flutter UI integration
- Context-free usage in service/repository layers
- Testing patterns
- Best practices and common pitfalls

## About aegis_honeycomb

A type-safe, codegen-free Flutter state management library with automatic dependency tracking and clear State/Effect separation.

- pub.dev: https://pub.dev/packages/aegis_honeycomb
- Publisher: aegis-labs.com
