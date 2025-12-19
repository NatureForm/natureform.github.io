# Architectures for Parametric CAD in Three.js

This document provides a high-level overview of the different architectural patterns for bringing parametric CAD (Computer-Aided Design) models into a real-time 3D web application like Three.js.

## The Core Problem: Logic vs. Geometry

The fundamental challenge is that a native CAD file (e.g., from Inventor or SolidWorks) is a **"recipe,"** not a "model." It contains a **feature tree**—a step-by-step history of operations like `extrude`, `cut`, and `fillet`, along with mathematical parameters and constraints.

Web formats like `glTF`/`glb` are **"dumb" meshes.** They are the final, flattened geometry (like a JPG) and contain no information about the recipe used to create them.

The goal, therefore, is to **re-create the parametric "recipe" logic** in the browser. We have discussed three primary architectures to achieve this, ranging from a common "dead end" to highly robust, professional solutions.

## 1\. The "glTF + JSON" Mapping Approach (The Dead End)

This is the most common first idea. It's an intuitive approach that unfortunately fails in practice.

  * **The Theory:** Export a `model.glb` (the geometry) and a `params.json` (the parameters) from your CAD program. In Three.js, load the `.glb` and then try to "map" the JSON parameters to the mesh vertices. When a parameter changes (e.g., `width = 110`), you find all the vertices associated with "width" and move them in JavaScript.

  * **The Verdict:** This is a **dead end**. A CAD model's topology (its vertex and triangle structure) is not stable. Changing one parameter (like a fillet radius or a hole count) causes the CAD engine to **re-calculate and generate a completely new mesh**—it doesn't just "move" vertices. The vertex IDs, count, and order all change, making any static mapping impossible.

  * **Learn more:** **[In-depth: Why the "glTF + JSON" Approach Fails](./GLB%2BParameters.md)**

## 2\. The "Feature Tree Transpiler" Approach (The "Compiler")

This is the most powerful, complex, and "pure" solution. It treats the entire process as a compiler pipeline.

  * **The Theory:** You invent your own "Intermediate Representation" (a custom JSON schema) that describes the *entire feature tree* as a list of operations (e.g., `{"type": "SKETCH"}`, `{"type": "EXTRUDE"}`).

    1.  **Frontend (Plugin):** You write a custom plugin for each CAD program (Inventor, SolidWorks) to "transpile" their proprietary feature tree into your standard JSON "recipe."
    2.  **Backend (Web App):** Your JavaScript code acts as an "interpreter" or "compiler" that reads this JSON recipe. It walks the feature tree step-by-step and issues low-level commands to a geometry kernel (like Manifold3D) to build the model from scratch.

  * **The Verdict:** This is the most flexible and scalable solution, as it creates a true, generic CAD program in the browser. It is also by far the most difficult, requiring you to build and maintain multiple complex CAD plugins and a sophisticated JavaScript interpreter.

  * **Learn more:** **[In-depth: The "Feature Tree to Manifold" (Transpiler) Architecture](FeatureTreeToManifold3d.md)**

## 3\. The "Domain-Specific" Generator Approach (The "Emulator")

This is the pragmatic and highly effective solution you are implementing. It "fakes" the CAD logic by specializing in a specific domain (e.g., furniture).

  * **The Theory:** Instead of translating a *generic* feature tree, you write *specific* JavaScript functions (e.g., `buildCabinet(params)`, `buildTable(params)`). This code doesn't read a recipe; it *is* the recipe. It knows how to build a cabinet from scratch using simple math (e.g., `shelfWidth = totalWidth - (2 * boardThickness)`). It generates simple primitives (like boxes for boards) and "assembles" them using a kernel like Manifold3D.

  * **The Verdict:** This is the **fastest and most robust** solution for product configurators. It avoids 99% of the complexity of "real" CAD. It's decoupled, meaning the web visual can be a simple "wrapper" of boards, while the "real" CAD model for manufacturing can be far more detailed.

  * **Learn more:** **[In-depth: The "Domain-Specific" (Wrapping) Architecture](Wrapping.md)**

## Comparison Summary

| Approach | How it Works | Key Challenge | Verdict |
| :--- | :--- | :--- | :--- |
| **glTF + JSON** | "Modify" an existing `.glb` mesh by moving vertices. | Unstable topology. Fails on any real-world model. | **Dead End** |
| **Transpiler** | "Re-builds" a model by interpreting a JSON *feature tree*. | Extreme complexity. Requires custom plugins for each CAD program. | **The "Pure" (Hardest) Way** |
| **Domain-Specific** | "Re-builds" a model using hard-coded *generative logic* (e.g., `buildCabinet()`). | Limited to a specific domain (e.g., "board furniture"). | **The Pragmatic (Best) Way** |