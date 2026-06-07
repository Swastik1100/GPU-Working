# Module 3 — Shading, Calculus, and Optics

## 1) What Shading Really Means

Rasterization tells us **which pixels belong to a triangle**.  
Shading tells us **what color each of those pixels should be**.

So shading is where image realism is created: light, material, and viewing direction all meet here.

---

## 2) Interpolation: Getting Pixel Data from Vertices

Vertex shader outputs values at triangle corners (color, normal, UV, etc.).
But we shade at pixel positions inside the triangle.

That means we need interpolation.

For perspective-correct interpolation:

\[
a = \frac{\sum_i \lambda_i \left(a_i / w_i\right)}{\sum_i \lambda_i \left(1 / w_i\right)}
\]

Why not simple linear interpolation in screen space?  
Because perspective projection distorts spacing. Without correction, textures “swim” and look wrong.

---

## 3) Light Transport Idea (Core Physics View)

The rendering equation describes outgoing light from a surface point:

\[
L_o(\mathbf{x},\omega_o)=\int_{\Omega} f_r(\mathbf{x},\omega_i,\omega_o)L_i(\mathbf{x},\omega_i)(\mathbf{n}\cdot\omega_i)\,d\omega_i
\]

Simple meaning:
- Add contributions of incoming light from all directions
- Weight by material response and angle
- Output reflected light toward camera

This is a calculus-based accumulation process.

---

## 4) BRDF and Material Behavior

A BRDF (\(f_r\)) answers:  
**Given incoming light direction and outgoing view direction, how much light is reflected?**

Cook-Torrance microfacet model:

\[
f_r=\frac{D(h)\,F(\omega_i,h)\,G(\omega_i,\omega_o,h)}{4(\mathbf{n}\cdot\omega_i)(\mathbf{n}\cdot\omega_o)}
\]

High-level parts:
- \(D\): how micro-surface orientations are distributed
- \(F\): Fresnel effect (angle-dependent reflectance)
- \(G\): geometry/masking-shadowing term

This gives realistic metal and rough-surface highlights in real-time graphics.

---

## 5) Why Optics Matters in Games

Even real-time engines use optics ideas:
- Roughness controls highlight spread
- Metallic controls whether reflection is colored
- Normals change micro-orientation for detail

So “game lighting” is not random art only; it is structured approximation of physical light behavior.

---

## 6) Class-12 Intuition Summary

- Shading decides pixel color after rasterization.
- Interpolation moves vertex data to pixel locations.
- Perspective-correct math keeps textures stable.
- BRDF models how materials reflect light.
- Calculus appears because total light is accumulated from many directions.

This module is the bridge from geometry to visual realism.

