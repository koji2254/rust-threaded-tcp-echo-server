# Multi-Threaded TCP Echo Server in Rust

A lightweight, high-performance TCP echo server built entirely with Rust's standard library (`std`). It uses standard operating system threads (`std::thread`) to seamlessly accept and process incoming data streams concurrently from multiple connected clients.

## 🚀 Features

- **Concurrent Connection Handlers:** Spins up a dedicated native thread per client using `thread::spawn`, preventing blocking operations from freezing the primary socket listener.
- **Robust Error Tolerances:** Smart handle exceptions for interrupted system calls (`io::ErrorKind::Interrupted`) and explicit client disconnects (`ConnectionReset`).
- **Configurable Port Binding:** Accepts a custom IP/Port binding argument via the CLI interface, gracefully falling back to a standard configuration if omitted.

## 🛠️ Prerequisites

Ensure you have the following installed on your machine:
- Rust (Stable toolchain)
- Cargo Package Manager
- `netcat` (for testing utility)

If you are on an Ubuntu/Debian system and need `netcat`, install it using:
```bash
sudo apt update && sudo apt install netcat-openbsd -y
```

## 🏃 Getting Started

### 1. Run the Server
Launch the server in your terminal. By default, it binds to `127.0.0.1:9090`:
```bash
cargo run
```

*(Optional)* You can supply a custom socket address as a command line argument:
```bash
cargo run -- 127.0.0.1:8080
```

### 2. Connect and Test with Netcat
Open a second terminal window to simulate a client connecting to your server. 

#### Interactive Chat/Typing Mode
To open an ongoing interactive connection where everything you type gets instantly echoed back to you, execute:
```bash
nc localhost 9090
```
Type any message and hit `Enter` to see the server bounce it back. To close the interactive session, press `Ctrl + C`.

#### Single Pipe Mode
To quickly stream a single automated message string into the server pipeline, run:
```bash
echo "Hello World" | nc localhost 9090
```

## 🧩 Code Architecture Deep Dive

- **`main`:** Responsible for extracting configurations from runtime arguments, setting up the `TcpListener`, loop-blocking on incoming client streams, and safely offloading processing streams to background workers.
- **`handle_client`:** Owns the dedicated client mutable `TcpStream`. It processes inbound telemetry through a stack buffer arrays of `10,124` bytes inside an execution loop until a termination state ($0$ bytes read) or connection drops occur.
