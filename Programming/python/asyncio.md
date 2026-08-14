# asyncio

`asyncio` is Python's **cooperative concurrency** for I/O — an event loop that multiplexes many "concurrent" tasks on one thread. It's the GIL-era answer for I/O-bound work without threads (see [[gil-threading]]), and the model behind modern network/serving code.

## The mental model

- One **event loop** (in `main`) drives everything. `async def` functions are **coroutines**: they pause at `await`, handing control back to the loop, which runs *other* ready coroutines. No preemption — a coroutine runs until it awaits.
- `asyncio.gather(a(), b())` — run several concurrently on the loop; `asyncio.create_task()` — fire-and-forget a coroutine; `asyncio.sleep(0)` — yield the CPU.
- **The cardinal rule**: never block the loop. A `time.sleep(2)` or a synchronous HTTP call *freezes all other tasks* because nothing else gets a turn. The async libraries exist precisely to avoid this: `aiohttp`, `httpx.AsyncClient`, `asyncpg`.

## Why asyncio (not threads) for I/O

- I/O-bound: the wait is the cost, and an event loop waits on *all* connections at once (no thread per request). Scale = thousands of concurrent connections on one process.
- CPU-bound: **wrong tool** — it serializes (GIL anyway, see [[gil-threading]]). Use threads/processes, or offload the compute (NumPy/torch, [[python-performance]]).

## The integration patterns

- **asyncio + threads**: `loop.run_in_executor(None, sync_fn)` — run a blocking/CPU call in a threadpool and await its result from async code (the standard bridge).
- **asyncio + subprocess**: `asyncio.create_subprocess_exec` — manage subprocesses concurrently.
- **asyncio + FastAPI**: the server is `async`; a sync route runs in a threadpool automatically (same `run_in_executor` under the hood).
- **Generators↔async**: `async for` (async iterables), `async with` (async context managers) — the same protocols from [[data-model]], but `__anext__`/`__aenter__`.

## The failure modes

- **The blocking leak**: some transitive call blocks the loop (a `requests.get` inside an `async` handler) → everything stalls. Rule: audit every `await` path for sync calls.
- **Tasks never awaited** → "Task was destroyed but it is pending" warnings — always `gather`/`asyncio.wait`/`create_task` + hold the handle.
- **Deadlocks with threads**: `run_in_executor` from within a thread that holds the loop — the classic "why did my FastAPI hang".

## Related

- [[gil-threading]] — the threading contrast.
- [[python-performance]] — when asyncio is the right concurrency fix.
- [[data-model]] — the async protocol dunders.
- [[Packaging]] — the tooling to run these apps.
