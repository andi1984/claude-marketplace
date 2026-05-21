# Rust Desktop Applications

Build cross-platform desktop applications with Rust using Tauri framework and native GUI alternatives.

## When to Use

Activate when building desktop applications needing **native performance**, **small bundle sizes**, **system integration**, or **memory safety**. Specifically:

- Electron alternative with web UI + Rust backend (Tauri)
- High-performance developer tools or productivity apps
- System utilities requiring native OS integration
- Cross-platform apps for Windows, macOS, Linux
- Need <10MB bundle sizes vs 100MB+ Electron
- Real-time apps (audio/video processing, games)

## Don't Use When

- Simple web apps → use Next.js/Vite
- Mobile-first → use Flutter/React Native
- Purely CLI tools → use clap
- Quick prototypes (setup overhead)

## The Iron Law

**TAURI FOR WEB UI + RUST BACKEND | NATIVE GUI FOR PURE RUST | NEVER MIX BUSINESS LOGIC IN FRONTEND**

## Framework Decision Tree

```
Need desktop app?
├─ Have web frontend skills (React/Vue/Svelte)?
│  └─ YES → Tauri
│     ├─ <5MB bundles ✓
│     ├─ System integration ✓
│     └─ Rapid UI dev ✓
└─ Pure Rust, no web frontend?
   ├─ Immediate mode tools / game editor → egui
   ├─ Elm-style reactive → iced
   ├─ Declarative / embedded → slint
   └─ Data-first reactive → druid
```

## Quick Start

```bash
# Tauri
cargo install tauri-cli
cargo create-tauri-app my-app

# Native (egui)
cargo new my-app && cargo add eframe egui
```

## Tauri

### Project Structure
```
src-tauri/
  src/
    main.rs          # tauri::Builder setup
    commands/        # #[tauri::command] handlers
    state/           # AppState structs
    error.rs         # AppError type
  Cargo.toml
  tauri.conf.json
src/                 # Frontend (React/Vue/Svelte)
```

### Commands
```rust
#[tauri::command]
async fn fetch_data(
    state: tauri::State<'_, AppState>,
    id: u32,
) -> Result<MyData, String> {
    state.service.get(id).await.map_err(|e| e.to_string())
}
```

### State Management
```rust
struct AppState {
    db: Arc<Mutex<rusqlite::Connection>>,
    config: Arc<RwLock<Config>>,
}

fn main() {
    tauri::Builder::default()
        .manage(AppState { ... })
        .invoke_handler(tauri::generate_handler![fetch_data])
        .run(tauri::generate_context!())
        .expect("error while running tauri application");
}
```

### Events (Rust → Frontend)
```rust
app_handle.emit_all("data-updated", payload).unwrap();
```

### File System & Paths
```rust
// Always use Tauri's path resolver, not std::env
let data_dir = app.path_resolver().app_data_dir().unwrap();
```

### Security
- `"withGlobalTauri": false` in `tauri.conf.json` unless needed
- Use `allowlist` to restrict which APIs frontend can access
- Validate and sanitize all `#[tauri::command]` arguments
- Configure Content Security Policy

## egui / eframe

Immediate-mode GUI without a web frontend.

```rust
impl eframe::App for MyApp {
    fn update(&mut self, ctx: &egui::Context, _frame: &mut eframe::Frame) {
        egui::CentralPanel::default().show(ctx, |ui| {
            ui.heading("My App");
            if ui.button("Click me").clicked() { self.counter += 1; }
            ui.label(format!("Count: {}", self.counter));
        });
    }
}
```

Background work pattern (non-blocking):
```rust
let ctx = ctx.clone();
let (tx, rx) = std::sync::mpsc::channel();
std::thread::spawn(move || {
    let result = do_work();
    tx.send(result).ok();
    ctx.request_repaint();
});
// In update():
if let Ok(result) = rx.try_recv() { ... }
```

## iced

Elm-architecture style:

```rust
fn update(&mut self, msg: Message) -> Command<Message> {
    match msg {
        Message::Increment => { self.count += 1; Command::none() }
        Message::DataLoaded(Ok(items)) => { /* handle */ Command::none() }
    }
}
```

Use `Command::perform(async_fn(), Message::DataLoaded)` for async. Keep `view()` pure.

## Cargo Setup

```toml
[dependencies]
tokio = { version = "1", features = ["full"] }
thiserror = "1"
anyhow = "1"
serde = { version = "1", features = ["derive"] }
serde_json = "1"
tracing = "0.1"
tracing-subscriber = { version = "0.3", features = ["env-filter"] }
rusqlite = { version = "0.31", features = ["bundled"] }
dirs = "5"

[profile.release]
lto = true
codegen-units = 1
```

Platform-safe paths:
```rust
let config_dir = dirs::config_dir().unwrap().join("my-app");
let data_dir = dirs::data_dir().unwrap().join("my-app");
std::fs::create_dir_all(&config_dir)?;
```

## Correct Patterns

```rust
✅ Commands in Rust backend, frontend calls via invoke()
✅ Type-safe IPC with serde
✅ Async with Tokio, spawn_blocking for CPU/sync work
✅ Arc<Mutex<T>> or channels for shared state
✅ Result<T, E> error propagation with thiserror
✅ tokio::sync::mpsc for actor-style message passing
```

## Red Flags — STOP

- **Blocking the main/UI thread** → `tokio::task::spawn_blocking`
- **Exposing sensitive commands** → validate inputs, minimize surface
- **Missing CSP in Tauri** → configure Content Security Policy
- **Business logic in frontend JS** → move to Rust backend
- **Direct frontend file access** → use Tauri file system APIs
- **Hardcoded paths** → use `dirs` crate or Tauri path resolver
- **No auto-update strategy** → users won't manually update

## Performance Reality

| Metric | Tauri | Electron |
|--------|-------|----------|
| Bundle size | 3–5 MB | 100–200 MB |
| Memory | 50–100 MB | 500 MB–1 GB |
| Startup | <1s | 3–5s |

Production examples: Warp Terminal, Lapce, Zed editor.
