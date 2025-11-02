# ZynxStack

**ZynxStack** is an open-source hybrid storage-and-compute platform built from first principles — combining a **C-based key–value engine** with a **Zig-powered distributed job runner**.

It aims to teach and empower developers to understand and build real infrastructure: from page-cache design and write-ahead logging to network protocols, sandboxing, and observability.

---

## ⚡ Core Components

| Component | Language | Description |
|------------|-----------|--------------|
| `zynxcache` | C | High-performance key–value engine with WAL and snapshots |
| `zynxrun` | Zig | Distributed job runner with sandboxed plugin execution |
| `proto` | Zig + C | Shared FFI types and protocol definitions |
| `ops` | Bash + YAML | Deployment scripts and systemd units |
| `bench` | Zig/C | Load testing and latency metrics |

---

## 🧠 Key Features

- Deterministic and crash-safe KV store (WAL + snapshots)
- Async Zig network daemon (text & binary protocols)
- Secure plugin sandboxing via namespaces, seccomp, cgroups
- Structured JSON logs, Prometheus metrics, latency histograms
- CLI tools (`zynxctl`) for queues, jobs, and monitoring

---

## 🧩 Architecture

