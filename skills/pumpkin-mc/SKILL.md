---
name: pumpkin-mc
description: Use when writing or loading a Pumpkin MC plugin, porting Bukkit or Paper plugin code, or changing how the Pumpkin server loads plugins. Covers Rust, Python, C#, Go, C, C++, Kotlin, D, Zig, and TypeScript, plus a wasm32-wasip2 or wasm32-wasip3 target, the plugins/ directory, and host signatures.
---

# Pumpkin MC plugins

Pumpkin is a Rust Minecraft server. Its plugins are Wasm components or native Rust libraries. Read the English guides. Do not copy them into the answer.

Sources, in order:

1. [Pumpkin-Docs](https://github.com/Pumpkin-MC/Pumpkin-Docs) `docs/en/` only. Ignore every other language tree.
2. [Pumpkin](https://github.com/Pumpkin-MC/Pumpkin) `crates/pumpkin-plugin-api` and `crates/pumpkin-plugin-wit`, only when a signature or the Wasm target is unclear. If a guide and the crate disagree, follow the crate.

## When to use

Use this for a Pumpkin plugin or for plugin loading on a Pumpkin server. A Paper, Bukkit, or Spigot plugin is a different runtime.

## Rules

- A Paper or Bukkit JAR does not load on Pumpkin. Do not put one in `plugins/`.
- An unknown signature comes from `pumpkin-plugin-api` and the WIT files, not from memory and not from a stale guide snippet.
- Do not paste guide pages, translations, or API listings. Open the page and follow it.

## Load path

The server scans `./plugins` (working directory).

| File | Loader |
| --- | --- |
| `.wasm` | Wasm component. The default server accepts an export named `pumpkin:plugin/metadata@0.1.*` and links WASI preview 2. A `wasi:*@0.3.*` import on that export is rejected. `@0.2.*` and the preview 3 linker require the server feature `plugin-v0_2`, which stays off. |
| `.so` (not Windows or macOS), `.dll` (Windows), `.dylib` (macOS) | Native library. |

Native entry symbols come from `#[plugin_impl]` in `pumpkin-api-macros`: `PUMPKIN_API_VERSION` (`u32`, server constant `2`), `METADATA`, and `plugin`. The loaders guide still says `pumpkin_plugin_init`. That name is stale. Native unload is refused on Windows only.

Plugin config lives in `[plugins]` in `pumpkin.toml`. Read `docs/en/config/plugins.md`. Do not restate the keys.

## Wasm target and Rust layout

`docs/en/plugin-dev/rust/creating-project.md`, `docs/en/plugin-dev/c/quick-start.md`, and `docs/en/plugin-dev/d/quick-start.md` name the target `wasm32-wasip3`. The guest crate still emits API 0.1, so the host rule above wins.

- `pumpkin-plugin-api` generates bindings from `crates/pumpkin-plugin-wit/v0.1` (`package pumpkin:plugin@0.1.0`) and exports through `register_plugin!`.
- Rust target `wasm32-wasip2` (`rustup target add wasm32-wasip2`, `.cargo/config.toml` `[build] target = "wasm32-wasip2"`).
- Layout: `.cargo/config.toml`, `src/lib.rs`, `Cargo.toml`. `[lib] crate-type = ["cdylib"]`.

## Route by language

Read the language directory. Build and entry facts below are the procedure. The pages have the rest.

| Language | Read | Entry | Build |
| --- | --- | --- | --- |
| Rust | `docs/en/plugin-dev/rust/` | `src/lib.rs` | `wasm32-wasip2`, `cdylib`. The page names `wasm32-wasip3`. |
| Python | `docs/en/plugin-dev/python/` | `main.py` | `pumpkin-plugin-build`, output `.wasm` into `plugins/` |
| C# | `docs/en/plugin-dev/csharp/` | class library | `RuntimeIdentifier` `wasi-wasm`; output under `bin/Release/net10.0/wasi-wasm/publish/` |
| Go | `docs/en/plugin-dev/go/` | `main.go` | `tinygo build -target=wasi` |
| C | `docs/en/plugin-dev/c/` (C++ too) | `main.c` | Page names wasi-sdk `wasm32-wasip3-clang` and `-mexec-model=reactor`. An `@0.1.*` component uses `wasm32-wasip2-clang` with the same flag. |
| Kotlin | `docs/en/plugin-dev/kotlin/` | `src/wasmWasiMain/kotlin/…` class named by `pumpkin.pluginClass` | `wasmWasi`; `./gradlew build`; `build/<name>.wasm` |
| D | `docs/en/plugin-dev/d/` | `source/app.d` | Page names `dub build -a wasm32-wasip3`. An `@0.1.*` component uses `wasm32-wasip2`. |
| Zig | `docs/en/plugin-dev/zig/` | `src/main.zig` | `zig build`; output `zig-out/*.wasm` |
| TypeScript | `docs/en/plugin-dev/typescript/` | `src/index.ts` | `pumpkin-plugin-build`; output `build/<name>.wasm` |

Start at `docs/en/plugin-dev/introduction.md` for the binding repositories.

## Bukkit and Paper

Point at `docs/en/plugin-dev/migrating-from-bukkit/` (`index.md`, `commands.md`, `events.md`, `inventories.md`, `configuration.md`). Do not rewrite those guides.

Server-admin differences, including anything outside the Pumpkin plugin loader, are `docs/en/admin/migrating-from-bukkit.md`.

## Server plugin engine

When changing the loader itself, read `docs/en/developer/plugins/` (`index.md`, `loaders.md`, `wasm-signing.md`). Prefer the crate over `loaders.md` where they disagree.

## Folder map

- `docs/en/plugin-dev/introduction.md`
- `docs/en/plugin-dev/rust/` `python/` `csharp/` `go/` `c/` `kotlin/` `d/` `zig/` `typescript/`
- `docs/en/plugin-dev/migrating-from-bukkit/`
- `docs/en/developer/plugins/`
- `docs/en/config/plugins.md` when plugin load or config rules are in play
- `docs/en/admin/migrating-from-bukkit.md` for server migration only
