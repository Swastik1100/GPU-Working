# How a 3D Object is Represented and Positioned in a Virtual World


## 1) The 3D Grid (Model Space)

Imagine a **connect-the-dots puzzle**, but in 3D instead of on flat paper.

A 3D object (like a game character, sword, or car) is built from:

- **Vertices**: points in space
- **Edges**: lines connecting points
- **Faces**: surfaces (usually triangles) filling those edges

So a “solid” character is actually a huge network of tiny triangles connected together.

### Model Space = the object’s own private coordinate system

Before placing it in a game world, the object lives in its own local grid called **Model Space**.

Think of it like this:

- In school graph paper, a point might be \((x, y)\).
- In 3D, a point is \((x, y, z)\).

A vertex can be written as:

\[
\mathbf{v} =
\begin{bmatrix}
x\\
y\\
z
\end{bmatrix}
\]

Where:

- \(x\): left-right
- \(y\): up-down
- \(z\): forward-back (depth)

If a model’s center is at \((0,0,0)\), that center is called its **local origin**.
All other vertices are measured relative to that center.

So if a character’s hand vertex is at \((0.5, 1.2, 0.3)\), that means:
“From the character’s own center, move right, up, and slightly forward.”

---

## 2) Moving Things into the World (World Space)

Now imagine the game map is huge: mountains, roads, buildings, players.
Your object must move from its private model grid into this global map grid: **World Space**.

### What does a matrix do (conceptually)?

A **matrix** is like a transformation machine.
Feed in a point, and it outputs a changed point.

Different transformations include:

- **Scale** (make bigger/smaller)
- **Rotate** (turn)
- **Translate** (move position)

So matrices are geometric operators that reshape or reposition all vertices consistently.

### Translation with vector addition (high-school vector math)

If a vertex is \(\mathbf{v}_{old}\), and we want to move the whole object by translation vector \(\mathbf{t}\), then:

\[
\mathbf{v}_{new} = \mathbf{v}_{old} + \mathbf{t}
\]

If

\[
\mathbf{v}_{old} =
\begin{bmatrix}
1\\
2\\
-1
\end{bmatrix},
\quad
\mathbf{t} =
\begin{bmatrix}
10\\
0\\
5
\end{bmatrix}
\]

then

\[
\mathbf{v}_{new} =
\begin{bmatrix}
11\\
2\\
4
\end{bmatrix}
\]

That means every vertex in the model shifts by the same offset, so the object moves without changing shape.

> Intuition: Translation is like picking up a statue and placing it elsewhere—same statue, new location.

---

## 3) The Math of Perspective (How Things Shrink with Distance)

Your eyes and camera lenses do not see the world in “raw 3D coordinates.”
They project 3D onto a 2D image.

That is what the **Projection Matrix** helps simulate.

### Why distant things look smaller

If two objects have the same real size, the farther one takes less visual space on your retina (or screen).
This is perspective.

A simple idea (not full pipeline math, but core logic):

\[
x_{screen} \propto \frac{x}{z}, \qquad
y_{screen} \propto \frac{y}{z}
\]

So as depth \(z\) increases, \(\frac{x}{z}\) and \(\frac{y}{z}\) shrink.
Hence distant objects appear smaller.

### Train tracks meeting at the horizon

In reality, train tracks are parallel.
In perspective projection, their projected lines converge to a **vanishing point**.

Why?
Because equal spacing in depth gets compressed more and more as distance increases.
The projection matrix encodes this compression mathematically.

### Camera-like view volume

The projection matrix also defines what the camera can see:

- **Near plane**: closest visible distance
- **Far plane**: farthest visible distance
- **Field of View (FOV)**: wide-angle vs zoomed-in

So projection is not just shrinking—it maps visible 3D space into normalized screen-ready coordinates.


---

If you want, next we can continue to **Module 1.2: Homogeneous Coordinates (\(w\))**—the key trick that lets translation, rotation, and scaling all be handled in one matrix framework.
