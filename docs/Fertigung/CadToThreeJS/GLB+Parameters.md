# The "glTF + JSON" Parametric Theory (And Why It's a Dead End)

This document outlines the common theoretical approach for creating a web-based CAD configurator by mapping a JSON parameter file to a static `glTF`/`glb` model. It then explains the fundamental reasons why this method fails in practice.

---

## The "Dream" Workflow: How It *Should* Work

The idea is to decouple the *logic* (parameters) from the *geometry* (the mesh), which seems clean and efficient.

The theoretical process would look like this:

1.  **Export from CAD:**
    * **`model.glb`:** The base 3D geometry. This is the visual "mesh" of the model.
    * **`params.json`:** A custom-exported file containing all parameters and rules (e.g., `{"width": 100, "height": 200, "hole_count": 3}`).

2.  **Load in Three.js:**
    * The `GLTFLoader` loads the `model.glb` and creates a standard `THREE.Mesh` with a `BufferGeometry`.
    * A `fetch` request loads the `params.json` into a JavaScript object.

3.  **The "Magic" Mapping:**
    * This is the core assumption. The theory assumes you can find a **stable link** between the parameters and the geometry.
    * For example, your code would *know* that the parameter `"width"` corresponds to a specific group of vertices on the side of the mesh.

4.  **Execute Parameter Change:**
    * A user moves a slider, changing `"width"` from 100 to 110.
    * Your JavaScript code would:
        1.  Get all vertices "tagged" with `"width"`.
        2.  Loop through them and update their X-position in the `geometry.attributes.position` buffer (e.g., `vertex.x += 10`).
        3.  Call `geometry.attributes.position.needsUpdate = true` to tell Three.js to re-draw the modified shape.

> **The Core Assumption:** This entire process hinges on the belief that changing a parameter is a simple **translation** (moving) of existing vertices, and that those vertices can be reliably identified.

---

## The Dead End: Why This Fails

This "modify-in-place" approach fails because a CAD model's geometry is far more complex than a simple "bag of vertices." The relationship between parameters and geometry is **not** a simple 1-to-1 mapping.

### 1. The "Dumb Mesh" Problem

A `glTF` or `glb` file is a "dumb" mesh format. It is an *optimized* and "flattened" list of triangles. It has **no concept** of:
* Faces
* Edges
* Holes
* Fillets
* Or "width"

It only contains a giant array of `(x, y, z)` coordinates for its vertices. When you export from Inventor, all the "intelligence" that defined the part is **completely discarded**.

### 2. The Topological Naming Problem

This is the most critical failure point. Even if you *could* "tag" vertices in your CAD program (e.g., "Vertex #501" = "part_of_width_face"), this information is **lost during export**.

* CAD programs perform **mesh optimization** when exporting.
* Vertices are merged (e.g., if two edges meet, their shared vertices become one).
* The entire vertex buffer is re-indexed.

The "Vertex #501" in your Inventor file might become "Vertex #1204" in the `.glb` file, or it might be optimized away entirely. The link is broken instantly.

### 3. The "Non-Linear" Problem (The Real Killer)

This is the fatal flaw in the theory. Changing a parameter is **not a simple translation** of vertices.

Changing a parameter causes the CAD software to **re-calculate the entire model's topology**.

Consider these examples:
* **A Simple Fillet (Rounded Edge):** You increase the `width` of a box. The flat face's vertices *do* just move. But the fillet that connects it to the top face must be **completely re-calculated**. The old fillet's vertices are *deleted*, and a new set of vertices with a new curve is *created*.
* **A Pattern Feature:** Your model has a `hole_count` parameter. Changing it from `3` to `4` doesn't *move* anything. It **creates new geometry** (a new cylinder to cut) and **adds new vertices** to the mesh.
* **A Boolean Feature:** You decrease the `width` of a part. The new width is now *smaller* than a hole that was cut into it. The CAD program's logic says "this hole feature no longer touches the part," so the feature is suppressed. The hole (and all its vertices) **disappears entirely**.

The vertex count, face count, and triangle order are **not stable**. They change *dramatically* with almost every parameter update.
