# Concurrency Roadmap - What You MUST Know

**You asked: "What should I must know? Should I learn tokio?"**

## 🎯 Short Answer

**Must know:** Channels (mpsc)  
**Should you learn tokio?** NO. Not yet.

---

## 📚 Your Exact Learning Path

### ✅ **You Already Know:**
- TCP/HTTP servers (std::net)
- TcpListener/TcpStream
- Blocking I/O
- Basic thread spawning

### 🔥 **What You MUST Learn Next (In Order):**

#### **Week 1: Channels** ← START HERE
**File:** `channels_foundation.rs`

```rust
let (tx, rx) = mpsc::channel();
thread::spawn(move || tx.send(42).unwrap());
println!("{}", rx.recv().unwrap());
```

**Why first:**
- Solves 80% of concurrency problems
- Most idiomatic Rust pattern
- Foundation for everything else
- Works in sync AND async

**What to build:**
1. Task queue (send tasks through channel)
2. Logging system (all threads → one log writer)
3. Simple worker pool

**Time:** 5-7 minutes to code from memory

---

#### **Week 2: Worker Pool**
**File:** `worker_pool_channels.rs`

```rust
let pool = ThreadPool::new(4);
pool.execute(|| {
    // work here
});
```

**Why:**
- Practical pattern you'll use constantly
- Combines channels + threads
- Powers most production servers

**What to build:**
1. HTTP server with worker pool (`tcp_server_pool.rs`)
2. Batch job processor
3. Parallel file processor

**Time:** 10 minutes to code from memory

---

#### **Week 3: Shared State (Only When Needed)**

**Arc (Atomic Reference Counting):**
```rust
let data = Arc::new(vec![1, 2, 3]);
let data_clone = Arc::clone(&data);
thread::spawn(move || println!("{:?}", data_clone));
```

**When to use:**
- Sharing read-only data across threads
- Configuration objects
- Reference-counted shared ownership

**Mutex (Last Resort!):**
```rust
let counter = Arc::new(Mutex::new(0));
*counter.lock().unwrap() += 1;
```

**When to use:**
- Shared mutable state (rare!)
- Caches
- When channels won't work

**IMPORTANT:** Prefer channels over Arc<Mutex<>>!

---

#### **Month 2: Async Concepts (Not tokio!)**

**Understand these CONCEPTS:**
- What is a Future?
- What is an executor?
- What does async/await actually do?
- When is async ACTUALLY faster?

**DON'T WRITE CODE YET** - just understand the model.

**Resources:**
- Rust Book Chapter 16
- "Async Book" (first 3 chapters)
- Jon Gjengset's "Crust of Rust: async/await"

---

#### **Month 3+: Maybe Tokio**

**Only learn tokio if:**
- ❌ You need 10,000+ concurrent connections
- ❌ You're calling async APIs (database drivers, etc.)
- ❌ Your boss requires it

**Don't learn tokio if:**
- ✅ Your std::net server works fine (it does!)
- ✅ You have < 1000 concurrent connections
- ✅ You're still learning fundamentals

---

## 🔥 The #1 Most Important Thing

**CHANNELS (mpsc)**

Why channels are #1:
1. **Idiomatic Rust** - "Share memory by communicating"
2. **Prevents data races** - Compiler-enforced safety
3. **Solves most problems** - Task distribution, events, actors
4. **Easier than shared state** - No locks to manage
5. **Foundation for async** - Same concept in tokio

---

## ⚡ Channels vs Mutex - When to Use What

### **Use Channels When:**
✅ Passing data between threads  
✅ Task queues  
✅ Event systems  
✅ One-way communication  
✅ You can avoid shared state  

**Example:**
```rust
// DON'T: Arc<Mutex<Vec<Task>>>
// DO: mpsc::channel::<Task>()
```

### **Use Mutex When:**
✅ Shared configuration (read by many, written rarely)  
✅ Caches (LRU cache, memoization)  
✅ Complex shared state that can't be message-passed  

**Example:**
```rust
// Cache that many threads read/write
let cache = Arc::new(Mutex::new(HashMap::new()));
```

---

## 🚀 Do You Actually Need Tokio?

### **Reality Check:**

**Your std::net + channels server can handle:**
- ✅ 1,000 concurrent connections easily
- ✅ 10,000 req/sec throughput
- ✅ Most real-world applications

**Tokio is needed for:**
- ❌ 100,000+ concurrent connections
- ❌ When you're building Discord/Cloudflare
- ❌ Microservices calling 10+ async APIs

### **Benchmark First!**

Before learning tokio, benchmark your current approach:
```bash
# Install apache bench
brew install httpd  # or apt-get install apache2-utils

# Test your server
ab -n 10000 -c 100 http://localhost:7878/
```

If you can handle your load, you DON'T need tokio!

---

## 💡 Practical Example: Your TCP Server Evolution

### **Version 1: Your Current Approach**
```rust
let listener = TcpListener::bind("127.0.0.1:7878")?;
for stream in listener.incoming() {
    thread::spawn(move || handle_connection(stream));
}
```
**Problem:** One thread per connection = OOM with many connections

### **Version 2: With Worker Pool** ← DO THIS
```rust
let listener = TcpListener::bind("127.0.0.1:7878")?;
let pool = ThreadPool::new(4);

for stream in listener.incoming() {
    pool.execute(move || handle_connection(stream));
}
```
**Better:** Fixed threads, handles 1000s of connections

### **Version 3: With Tokio** ← LATER
```rust
#[tokio::main]
async fn main() {
    let listener = TcpListener::bind("127.0.0.1:7878").await?;
    loop {
        let (stream, _) = listener.accept().await?;
        tokio::spawn(async move { handle_connection(stream).await });
    }
}
```
**When:** 10,000+ concurrent connections

---

## 🎓 What to Build (Exercises)

### **Week 1: Master Channels**
1. **Task Queue**
   - Producer sends tasks
   - Consumer processes them
   - Try bounded channel (backpressure)

2. **Logging System**
   - Multiple threads send log messages
   - One thread writes to file
   - No race conditions!

3. **Ping-Pong**
   - Two threads
   - Send messages back and forth
   - Practice bidirectional channels

### **Week 2: Worker Pool**
4. **HTTP Server with Pool** (provided: `tcp_server_pool.rs`)
   - Combine your TCP knowledge + channels
   - 4 worker threads
   - Benchmark it!

5. **Parallel File Processor**
   - Main thread sends file paths to channel
   - Workers process files in parallel
   - Collect results

6. **Chat Server**
   - Accept TCP connections
   - Broadcast messages to all clients
   - Use channels for message passing

---

## 📊 Comparison Table

| Pattern | When to Use | Performance | Complexity |
|---------|-------------|-------------|------------|
| **Channels** | Default choice | Fast | Easy |
| **Arc<T>** | Shared read-only | Free | Easy |
| **Arc<Mutex<T>>** | Shared mutable | Medium | Medium |
| **Atomics** | Flags/counters | Fastest | Medium |
| **Threads + Channels** | < 1000 connections | Great | Easy |
| **Tokio** | 10,000+ connections | Best | Hard |

---

## 🎯 Your Action Plan

### **This Week:**
- [ ] Code `channels_foundation.rs` from memory
- [ ] Build a task queue using channels
- [ ] Build a logging system using channels

### **Next Week:**
- [ ] Code `worker_pool_channels.rs` from memory
- [ ] Upgrade your TCP server to use worker pool
- [ ] Benchmark: measure req/sec

### **This Month:**
- [ ] Read Rust Book Chapter 16 (Concurrency)
- [ ] Build a chat server (channels + TCP)
- [ ] Avoid tokio! (seriously)

### **Next Month:**
- [ ] Read async/await concepts (not code yet!)
- [ ] Understand Futures (theory)
- [ ] Still avoid tokio!

### **Month 3+:**
- [ ] Benchmark your server under load
- [ ] If it handles your load, KEEP using threads!
- [ ] If you hit 10,000+ connections, consider tokio

---

## 🔥 Bottom Line

**Question:** "What should I must know?"  
**Answer:** Channels. Master `mpsc::channel()` first.

**Question:** "Should I learn tokio?"  
**Answer:** No. Not until you need 10,000+ connections.

**Why?**
- Your std::net + channels will handle 99% of use cases
- Learning async without understanding sync is backwards
- Tokio is complex - master the fundamentals first
- Most apps don't need tokio's complexity

**The Truth:**
- Channels solve most concurrency problems
- Worker pools handle most server workloads
- Tokio is overkill for most projects
- You'll appreciate tokio MORE after mastering this

---

## 📚 Files Created for You

1. **`channels_foundation.rs`** - THE fundamental pattern (COMPILE THIS FIRST)
2. **`worker_pool_channels.rs`** - Practical worker pool patterns
3. **`tcp_server_pool.rs`** - Your TCP knowledge + worker pool

**Start with #1. Code it from memory. Build stuff with it.**

---

**Master channels this week. Everything else can wait.** 🚀
