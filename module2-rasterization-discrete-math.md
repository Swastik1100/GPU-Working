# Module 2 — Rasterization Stage and Discrete Mathematics

## 1) From Triangle to Pixels

After 3D objects are transformed and projected, the GPU gets triangles on a 2D screen plane.
Now it must answer a simple but huge question:

**Which pixels belong to each triangle?**

That conversion from geometric triangles to screen pixels is called **rasterization**.

---

## 2) Why Pixels Make This a Discrete Math Problem

A triangle is continuous (it can cover infinitely many points), but a screen is a fixed grid of square pixels.
So the GPU checks coverage at discrete sample points.

Think of this like coloring graph paper:
- The triangle is your drawn shape.
- The pixels are graph-paper boxes.
- You need a rule to decide which boxes count as “inside.”

---

## 3) Edge Function: Fast Inside/Outside Test

For an edge from vertex \(i\) to vertex \(j\), GPU hardware uses:

\[
E_{ij}(x,y) = (y_i - y_j)x + (x_j - x_i)y + (x_i y_j - x_j y_i)
\]

Interpretation:
- \(E_{ij}(x,y) > 0\): one side of the edge
- \(E_{ij}(x,y) < 0\): the other side
- \(E_{ij}(x,y) = 0\): exactly on the edge

For a consistently ordered triangle, a pixel is inside if all three edge tests have the same sign.

---

## 4) Barycentric Coordinates: Weighted Blending

Inside a triangle, any point can be written as a weighted mix of the three vertices:

\[
\lambda_0 + \lambda_1 + \lambda_2 = 1,\quad \lambda_k \ge 0
\]

Each \(\lambda\) tells “how close” the point is to a vertex.

Why this matters:
- Color interpolation
- Texture coordinate interpolation
- Normal interpolation

If vertex colors are \(C_0, C_1, C_2\), pixel color is:

\[
C = \lambda_0 C_0 + \lambda_1 C_1 + \lambda_2 C_2
\]

So barycentrics are the math bridge between geometry and shading.

---

## 5) Fill Rules and Shared Edges

Two neighboring triangles share an edge.  
If both fill that shared edge, pixels are double-drawn; if neither fills, cracks appear.

To avoid this, GPUs use a deterministic rule (commonly a **top-left style** rule) so exactly one triangle owns boundary pixels.

Result:
- No gaps (crack-free rendering)
- No accidental overlap artifacts

---

## 6) Tile-Based Thinking for Speed

Modern screens are large, so GPUs split them into tiles (small rectangular blocks).

\[
N_{tiles} = \left\lceil \frac{W}{T_x} \right\rceil \left\lceil \frac{H}{T_y} \right\rceil
\]

where:
- \(W,H\): screen width/height
- \(T_x,T_y\): tile width/height

Benefits:
- Better cache locality
- Less unnecessary memory traffic
- More parallel work distribution

---

## 7) Class-12 Intuition Summary

- Rasterization is “triangle to pixel” conversion.
- Edge equations decide inside/outside quickly.
- Barycentric weights blend vertex data smoothly.
- Fill rules prevent seams between triangles.
- Tiling helps GPUs process massive pixel workloads efficiently.

This is where geometry becomes image data.
