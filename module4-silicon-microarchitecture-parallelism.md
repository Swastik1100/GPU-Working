# Module 4 — Silicon Micro-architecture and Parallelism

## 1) Why GPU Hardware Is Different from CPU Hardware

CPU design is optimized for low-latency execution of a few complex instruction streams.  
GPU design is optimized for high-throughput execution of massive numbers of similar operations.

In graphics, millions of vertices/fragments often run the same shader logic on different data, so GPU parallel structure wins.

---

## 2) SIMT/SIMD Execution Intuition

GPUs group threads (warp/wavefront style groups) and issue instructions together.

Conceptually:

\[
U = \frac{\text{active lanes}}{\text{warp width}}
\]

If all lanes are active, utilization is high.  
If branches split threads, some lanes go idle, utilization drops.

---

## 3) Occupancy and Latency Hiding

Memory operations are slow compared with arithmetic.
GPUs hide this by keeping many warps resident, so scheduler can switch when one waits.

\[
\text{occupancy}=\frac{\text{resident warps per SM}}{\text{max warps per SM}}
\]

Occupancy is influenced by:
- Registers per thread
- Shared memory per block
- Hardware limits per SM/CU

Higher occupancy often helps latency hiding, but it is not the only performance factor.

---

## 4) Throughput Bottlenecks

Kernel speed is bounded by the slowest limiting subsystem:

\[
T_{kernel} \approx \min(T_{compute},\,T_{memory},\,T_{issue})
\]

Meaning:
- If ALUs are saturated, compute bound.
- If memory traffic dominates, memory bound.
- If instruction dispatch is constrained, issue bound.

Optimization starts by identifying the true bound, not guessing.

---

## 5) Memory Hierarchy in Plain Terms

Typical hierarchy idea:
- Registers: fastest, private, tiny
- Shared/LDS: very fast, block-local
- Cache levels: intermediate reuse
- Global memory: largest, slowest

Good GPU code improves locality and access patterns so fast levels are used effectively.

---

## 6) Branch Divergence Problem

When lanes in one warp take different branches:
- Hardware serializes paths
- Some lanes wait idle while others execute

Result: reduced effective parallel efficiency.

So many GPU optimizations aim to keep neighboring threads following similar control flow and memory behavior.

---

## 7) Class-12 Intuition Summary

- GPUs are built for massive parallel throughput.
- Warp/wave execution works best when lanes stay aligned.
- Occupancy helps hide waiting time.
- Real performance is limited by compute, memory, or issue bottlenecks.
- Memory hierarchy and divergence strongly shape speed.

This module explains why shader code structure and hardware architecture are deeply connected.

