# Module 5 — Ultra-High Refresh Rate Synchronization

## 1) Refresh Rate and Time Budget

A display refreshing at \(f_{refresh}\) Hz has frame interval:

\[
\Delta t = \frac{1}{f_{refresh}}
\]

Examples:
- 60 Hz \(\rightarrow\) 16.67 ms per frame
- 144 Hz \(\rightarrow\) 6.94 ms per frame
- 240 Hz \(\rightarrow\) 4.17 ms per frame

As refresh rate rises, timing margins become much tighter.

---

## 2) What Frame Pacing Means

Average FPS alone is not enough.
If frame times fluctuate, motion feels uneven (micro-stutter), even when average FPS is high.

Frame pacing = making frame delivery intervals consistent.

Good pacing improves perceived smoothness more than raw average FPS numbers.

---

## 3) End-to-End Latency Pipeline

Input-to-photon latency can be approximated as:

\[
L_{total}=L_{input}+L_{CPU}+L_{GPU}+L_{queue}+L_{scanout}+L_{pixel}
\]

Each stage adds delay:
- Input sampling
- Game simulation and CPU submission
- GPU rendering
- Queues/buffers waiting
- Display scanout
- Pixel response time

Reducing latency means managing all stages, not only GPU render time.

---

## 4) Scanout Timing Intuition

Displays are often scanned line by line (top to bottom).  
For row \(r\) in total \(R\) rows:

\[
t_{row}(r)=\frac{r}{R}\Delta t
\]

So bottom pixels of the same frame are shown later than top pixels.
This matters for tearing position and perceived responsiveness.

---

## 5) V-Sync, Tearing, and VRR

- **V-Sync OFF**: lower queue delay possible, but tearing can appear.
- **V-Sync ON**: reduces tearing, but can add latency and stutter when misses happen.
- **VRR (G-Sync/FreeSync)**: display refresh adapts to frame completion timing, reducing stutter/tearing trade-offs.

VRR works best when frame times stay within panel VRR range.

---

## 6) Why 240 Hz+ Is Hard

At 240 Hz, each frame has ~4.17 ms budget.
Small spikes become visible quickly.

This requires:
- Stable render workload
- Careful queue management
- Fast input and presentation paths

High refresh is not just “more FPS”; it is strict real-time systems engineering.

---

## 7) Class-12 Intuition Summary

- Higher refresh means less time per frame.
- Smoothness needs consistent frame times, not only high average FPS.
- Total latency is a chain of delays from input to pixel light output.
- Scanout and sync method strongly affect visual quality and responsiveness.
- VRR is a practical method to improve smoothness under variable workloads.

This module connects rendering performance with what the player actually feels.

