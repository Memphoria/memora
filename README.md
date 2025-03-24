# 🧠 Memora

> **A blazing-fast, zero-network, all-in-one runtime for distributed microservices using shared memory.**

---

## 🚀 What is Memora?

**Memora** is a lightweight Java-native runtime designed to unify core service infrastructure — such as distributed cache, inter-service communication, and embedded databases — in a single memory-based module.  
It uses **shared memory** (`shmget`, `mmap`) and `JNI` to enable fast, zero-network data exchange between services, **with no external dependencies**.

---

## ✨ Features

- ⚡ **Distributed cache** with shared memory access (per JVM or inter-process)
- 🔄 **Communication between microservices** (asynchronous, memory-ring based)
- 💾 **Embedded in-memory database** with optional file persistence
- 🧩 **All-in-One Java library**: cache, DB, messaging in one `.jar`
- 🔧 Built in **Java + C (via JNI)** for direct memory-level control
- 🧪 Benchmark-tested at nanosecond latency

---

## 🔧 Architecture

| Component              | JVM (Micro A)     | Shared Memory           | JVM (Micro B)     |
|------------------------|-------------------|--------------------------|-------------------|
| Cache                  | 🧠 Memora Cache   | ⬌ mmap / shmget / ring  | 🧠 Memora Cache   |
| Communication          | 🔁 Memora Comms   | ⬌ mmap / shmget / ring  | 🔁 Memora Comms   |
| Embedded DB            | 💾 Memora DB      | ⬌ mmap / shmget / ring  | 💾 Memora DB      |
| Communication Layer    | via JNI           |                          | via JNI           |

---

## 📦 Tech Stack

- **Java 21–24** with preview features (`--enable-native-access`)
- **JNI** for ultra-fast native bridging
- **C (GCC)**: memory loop, struct copy, array ops
- **Shared memory**: `System V IPC`, `mmap`, `tmpfs`, `shmget`
- Optional: `Unsafe`, `DirectByteBuffer`

---

## 🧪 Benchmarks

> ⚠️ All operations measured with `1_000_000` iterations.  
> Native operations represent the **latency floor**, others build overhead.

---

### ⚙️ Native C Baselines (no JNI, no JVM)

| Technique              | Total Time   | Avg Latency (µs/op) |
|------------------------|--------------|----------------------|
| `memcpy` (int[256])    | 0.000 ms     | ~0.00                |
| `Loop struct`          | 0.018 ms     | ~0.00                |
| `Loop mmap`            | 10.936 ms    | ~0.01                |
| `Loop shmget`          | 37.209 ms    | ~0.04                |

These represent the **ideal C-side performance floor**.  
Useful to compare JVM/JNI/Unsafe overhead.

---

### 🔬 Unsafe Access (Java)

| Technique                      | Total Time | Avg Latency (µs/op) |
|--------------------------------|------------|----------------------|
| `Unsafe.putInt/getInt`         | 3.0 ms     | 0.003                |
| `Unsafe + DirectByteBuffer`    | 4.0 ms     | 0.004                |
| `Unsafe.copyMemory` (array)    | —          | 0.025                |

---

### 📦 JNI Benchmarks

| Technique                          | Total Time | Avg Latency (µs/op) |
|-----------------------------------|------------|----------------------|
| `JNI write/read`                  | 14 ms      | 0.014                |
| `JNI copyIntArray`                | —          | 0.293                |
| `JNI copyIntArrayCritical`        | —          | 0.221                |
| `JNI struct` access               | 0.158 ms   | 0.158                |
| `JNI aligned` access              | 0.076 ms   | 0.076                |
| `JNI unaligned` access            | 0.151 ms   | 0.151                |
| `JNI concurrency (2 threads)`     | 168.618 ms | 168.618              |

---

### 🧠 Shared Memory Techniques (Java-side)

| Technique                       | Total Time | Avg Latency (µs/op) |
|---------------------------------|------------|----------------------|
| `MappedByteBuffer`              | 44.544 ms  | 44.544               |
| `MappedByteBuffer` (batch/struct) | 11.280 ms | 11.280               |
| `Shared Memory` (JNA)           | 85.172 ms  | 85.172               |

---

### 🌐 Foreign Memory API

| Technique                             | Total Time | Avg Latency (µs/op) |
|--------------------------------------|------------|----------------------|
| `MemorySegment` (basic)              | 18.346 ms  | 18.346               |
| `Aligned access`                     | 0.403 ms   | 0.403                |
| `Unaligned access`                   | 1.592 ms   | 1.592                |
| `Struct access (int + long)`         | 4.043 ms   | 4.043                |
| `Concurrency (2 threads)`            | 165.365 ms | 165.365              |

---

### 🌀 Classic Java

| Technique            | Total Time | Avg Latency (µs/op) |
|----------------------|------------|----------------------|
| `System.arraycopy`   | —          | 0.010                |

---

## ⏳ Status

🚧 **Actively under development**  
Memora is modular by design and already runs in Docker/Kubernetes via:

- `--ipc=host` (to enable shared memory)
- Or volume-mounted files (`/dev/shm`, `tmpfs`, etc.)

No external brokers. No remote caches. No I/O bottlenecks.

---

## 🧠 Why Memora?

Because most service-to-service problems can be solved in memory:

- Traditional solutions introduce **serialization**, **network latency**, and **runtime cost**
- Existing platforms require **external infrastructure** (message brokers, caches, databases)
- Java apps pay a price in **GC pressure** and **duplicated data**

Memora solves this with:

- 🧵 **Lock-free memory sharing** via ring buffers and mmap
- 💾 **Shared data layers** without I/O or sockets
- 🔌 **Single library, native speed** — no infrastructure needed
- 📦 **All-in-One**, with atomic updates, signals, and persistence where required

---

## 🚀 Getting Started (Coming Soon)

You'll be able to:

```bash
java -jar memora.jar
```
...and instantly get:

- 🧠 **Cache**
- 🔁 **Messaging**
- 💾 **Embedded DB**

All running **in-memory**, across **multiple JVMs** — with **zero external services**.

## 🤝 Want to contribute?

We'd love your input! You can:

- ⭐ Star the project to show support
- 🐛 Report bugs or unexpected behaviors
- 🚀 Suggest improvements or features
- 📥 Submit a PR (we'll review with care!)
- 🔍 Help us test under real-world load

---

## 📜 License

**License: Business Source License (BSL 1.1)**  
You are free to use Memora in **commercial, production** environments.

However:

- ❌ You **cannot fork and modify** the project to create your own derivative product.
- ✅ All improvements and modifications must be contributed back to this repo via pull requests.

This ensures a **healthy, open, and evolving ecosystem** while preserving the integrity of the original project.

For details, see [`LICENSE`](./LICENSE.md).