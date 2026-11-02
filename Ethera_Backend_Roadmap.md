# One-Week Prep Roadmap: Ethera Backend Engineer

**Role:** Backend Engineer, Ethera (backed by Bluestone), Bangalore
**Their stack:** Go, MongoDB, Redis (caching and queues), with PostgreSQL, Node.js and React/Next.js alongside

## Goals for the week

1. Get interview-ready on backend fundamentals and Ethera's stack.
2. Build one small **Go + MongoDB + Redis-queue** project and put it on GitHub, so Go can honestly go on the resume.

**Time budget:** about 4–5 hours on weekdays (alongside full-time work) and 8–10 hours on the weekend days. Each day mixes learning, building and interview practice.

## Project for the week: Ethera-lite

A small e-commerce backend in Go:

- A catalog of diamonds or products
- Inventory with stock reservation
- Orders with idempotent checkout
- Pricing with a Redis cache
- A Redis-backed job queue for order confirmation and stock release
- Packaged with Docker, built and tested by GitHub Actions, deployed to GCP Cloud Run

---

## Day 1: Go basics

**Learn (2.5h)**

- [ ] Work through the [Tour of Go](https://go.dev/tour) fully: types, structs, methods, interfaces, slices, maps, and the `error` type.
- [ ] Read [*Effective Go*](https://go.dev/doc/effective_go), focusing on errors, interfaces and naming.
- [ ] Compare Go to TypeScript as you go:
  - Go has no classes. Interfaces are satisfied implicitly. Errors are returned as values instead of thrown.
- [ ] Learn the toolchain: `go mod init`, `go run`, `go test`, `go vet`, `gofmt`.

**Build (1.5h)**

- [ ] Set up the project folders: `cmd/api`, `internal/{catalog,inventory,order}`, `pkg/`.
- [ ] Write a basic HTTP server with `net/http` or chi/gin, with `/health` and CRUD endpoints for products kept in memory.
- [ ] Write table-driven tests for one handler.

**Interview prep (1h)**

- [ ] DSA: 3 array/hashmap problems, solved in Go so you get used to the syntax.

## Day 2: Concurrency in Go (they will ask about this)

**Learn (2.5h)**

- [ ] Goroutines and channels, both buffered and unbuffered.
- [ ] `select`, `sync.WaitGroup`, `sync.Mutex` / `RWMutex`, `sync.Once`.
- [ ] `context.Context` for cancellation, timeouts and deadlines, and passing it through HTTP handlers.
- [ ] Patterns to practice:
  - **Worker pool:** a fixed number of goroutines pull jobs from a channel.
  - **Fan-out / fan-in:** split work across goroutines, then merge the results.
  - **Rate limiting:** cap how fast requests or jobs run.
  - **errgroup:** run goroutines together and stop on the first error.
  - **Graceful shutdown:** let in-flight work finish before the server exits.
- [ ] Race conditions: run `go test -race` and learn the classic mistake of capturing a loop variable inside a goroutine.

**Build (1.5h)**

- [ ] Write a worker pool that processes "order events" from a channel with retries and exponential backoff.
- [ ] Add graceful shutdown to the server using `signal.NotifyContext` and `server.Shutdown(ctx)`.

**Interview prep (1h)**

- [ ] Be ready to explain:
  - goroutine vs OS thread (the Go scheduler's G-M-P model, at a high level)
  - channel vs mutex: when to use each
  - what a deadlock is and how to spot one
- [ ] DSA: 3 sliding-window or two-pointer problems.

## Day 3: MongoDB

**Learn (2.5h)**

- [ ] Data modeling: when to embed vs reference. Example: should order line items live inside the order document?
- [ ] CRUD operations with the official Go driver (`go.mongodb.org/mongo-driver`).
- [ ] **Indexes:**
  - Single-field, compound and multikey indexes.
  - The ESR rule for compound indexes: equality fields first, then sort fields, then range fields.
  - Read `explain()` output and know COLLSCAN (full scan) vs IXSCAN (index scan).
- [ ] **Aggregation pipelines** (listed in their bonus points): `$match`, `$group`, `$lookup`, `$unwind`, `$project`, `$facet`, `$sort`. Put `$match` early so later stages handle fewer documents.
- [ ] Transactions: multi-document transactions require a replica set. Know when single-document atomicity is enough.
- [ ] Concurrency control: atomic operators (`$inc`, `findOneAndUpdate` with a condition) and optimistic locking with a version field.

**Build (2h)**

- [ ] Replace the in-memory store with MongoDB running in Docker Compose.
- [ ] Write an inventory reservation that can't oversell, as one atomic update:
  ```js
  updateOne({ _id, stock: { $gte: qty } }, { $inc: { stock: -qty } })
  ```
- [ ] Add a catalog search endpoint with filters (shape, carat range, price range), a compound index and cursor-based pagination.
- [ ] Add a sales report endpoint built on an aggregation pipeline, for example revenue per category per day.

**Interview prep (0.5h)**

- [ ] Be ready to compare SQL and NoSQL. Your PostgreSQL experience from the Reporting Engine gives you a good answer.

## Day 4: Redis for caching and queues

**Learn (2.5h)**

- [ ] Data types and what each is used for:
  - string, hash, list, set, sorted set (leaderboards, delayed jobs)
  - streams (event logs)
- [ ] **Caching:**
  - Cache-aside pattern, write-through vs write-behind.
  - Expiry times (TTL) and invalidation strategies.
  - Cache stampede, when many requests hit an expired key at once. Fixes: a lock, randomised TTLs, or `singleflight` in Go.
- [ ] **Queues (their core use of Redis):**
  - Simple queues with `LPUSH` + `BRPOP`, and the reliable-queue pattern with `LMOVE` so a crashed worker doesn't lose a job.
  - Redis Streams with consumer groups, `XACK` and `XPENDING`.
  - Delayed jobs with sorted sets.
  - Libraries: **[asynq](https://github.com/hibiken/asynq)**, the popular Go job queue.
  - Concepts: retries, dead-letter queues, idempotent jobs, and at-least-once vs exactly-once delivery.
- [ ] Distributed locks: `SET NX PX` and their limits.
- [ ] Rate limiting with fixed-window or sliding-window counters.

**Build (2h)**

- [ ] Cache price lookups in Redis with cache-aside and a TTL. Invalidate the cache when a price changes.
- [ ] **Asynq checkout flow:**
  - On checkout, queue an `order:confirm` job with retries.
  - Also queue a delayed `reservation:expire` job for 15 minutes later that returns stock if the order wasn't paid.
- [ ] Accept an `Idempotency-Key` header on `POST /orders` and store the key in Redis so a repeated request doesn't create a second order.

## Day 5: Deploy to GCP, then system design

**Build (2h)**

- [ ] Write a multi-stage Dockerfile that produces a tiny static Go binary in a distroless image.
- [ ] **GitHub Actions pipeline:**
  - Run `go vet`, `go test -race` and golangci-lint.
  - Build the image, push it to Artifact Registry, and deploy to **Cloud Run** with a separate worker service.
  - Use MongoDB Atlas free tier and Upstash or Memorystore for Redis.
- [ ] Write the project README:
  - an architecture diagram
  - API docs
  - the design decisions: why atomic stock updates, why idempotency keys, and the queue retry strategy

**System design (2.5h): practise these out loud**

- [ ] **Inventory and orders for an e-commerce site.** Prevent overselling during a flash sale, decide between reservations and direct decrements, and design the order state machine.
- [ ] **Pricing service.** Diamond prices depend on the 4Cs plus the gold rate, and the gold rate changes daily. Cover caching, bulk repricing as a background job, and price history.
- [ ] **Catalog search with filters.** Choose MongoDB indexes, and know when to move to Elasticsearch or Atlas Search.
- [ ] **Notification/CRM pipeline.** Event-driven with a queue: retries, a dead-letter queue, and making jobs idempotent.

For each one, use the same framework:

1. requirements, then traffic estimates
2. the API
3. the data model
4. high-level design
5. bottlenecks and failure modes

## Day 6: Backend fundamentals and your own work

**Fundamentals (3h): be ready for rapid-fire questions**

- [ ] **HTTP:**
  - methods and which are idempotent
  - status codes
  - headers, caching headers and ETags
  - HTTP/1.1 vs HTTP/2
  - REST design: versioning, pagination, error format
- [ ] **Auth:** sessions vs JWT, OAuth basics, RBAC. Use your Reporting Engine as the example.
- [ ] **Databases:** ACID, isolation levels, indexes (B-tree), N+1 queries, connection pooling.
- [ ] **Reliability:**
  - retries with backoff and jitter, timeouts
  - circuit breakers
  - the outbox pattern, saga pattern
  - idempotency
- [ ] **Networking:** what happens when you type a URL, TCP vs UDP, DNS, load balancers.

**Your stories (2h)**

- [ ] Write out STAR stories (Situation, Task, Action, Result) for:
  - **Indihood:** owning the codebase, the hardest bug you fixed, a design decision you made, how you set up CI/CD and GCP jobs. Lending ties in nicely: money flows need idempotency, state machines and audit trails, just like orders.
  - **Reporting Engine:** caching and indexing choices, multi-tenancy, and how you'd scale to 10× the data.
  - **Ethera-lite:** a walkthrough of the code, the trade-offs, and what you'd do next.
- [ ] Prepare for the question "Why Go / why Ethera / why leave Indihood?"

**Interview prep (2h)**

- [ ] DSA: 4 medium problems on heaps, graphs (BFS/DFS) and intervals.

## Day 7: Mock interviews and polish

**Morning**

- [ ] One timed 45-minute DSA mock: 2 medium problems.
- [ ] One 45-minute system design mock, recorded or with a friend. Use "design a jewellery store's order and inventory system".

**Afternoon**

- [ ] Run a 30-minute Go live-coding drill: build an LRU cache, a rate limiter and a worker pool with context cancellation, from scratch with no help.
- [ ] Fix gaps in the project, add tests and push.

**Resume update**

- [ ] Add Ethera-lite as the first project.
- [ ] Add Go, MongoDB and Redis queues (asynq) to Skills.

**Before applying**

- [ ] Add Ethera-lite to your LinkedIn Featured section.
- [ ] Pin the repo on GitHub.
- [ ] Prepare 3 questions for the founders:
  1. What's the hardest scaling problem right now?
  2. How are pricing updates handled today?
  3. What does ownership look like for a new engineer?

---

## Daily DSA focus

The problems most likely to come up in startup interviews:

| Day | Topics |
|---|---|
| 1 | Hashing, arrays |
| 2 | Sliding window, two pointers |
| 3 | Binary search, sorting |
| 4 | Stack, queue, LRU cache design |
| 5 | Linked lists, trees |
| 6 | Heaps, graphs, intervals |
| 7 | Mixed timed mock |

## Resources

- **Go:** [Tour of Go](https://go.dev/tour); [*Effective Go*](https://go.dev/doc/effective_go); *Concurrency in Go* (Katherine Cox-Buday), chapters 3–4; [gobyexample.com](https://gobyexample.com)
- **MongoDB:** MongoDB University "M001/M121" (aggregation) and the docs on indexing strategies
- **Redis:** [redis.io](https://redis.io/docs/latest/develop/data-types/) data types docs; the [asynq](https://github.com/hibiken/asynq) README and wiki
- **System design:** ByteByteGo / *System Design Interview* vol. 1 (rate limiter, unique ID generator chapters); Hussein Nasser on YouTube for backend fundamentals


A small project that works and that you can explain well is worth more than a big one you can't explain.
