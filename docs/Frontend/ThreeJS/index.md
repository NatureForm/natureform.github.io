# Framework: Why We Use Three.js

This document covers why Three.js was chosen as our 3D rendering library and the basic concepts of how it powers our `Configurator` component.

## Why Three.js?

Three.js is a 3D graphics library that renders complex 3D scenes in a web browser using **WebGL**. It's the engine that draws everything inside our `Configurator`'s `<canvas>` element.

* **It's an Abstraction, Not a Game Engine:**
    We don't need a heavy, all-in-one platform like Unity or Unreal Engine. We just need a powerful *library* that can draw 3D objects inside our *existing* React application. Three.js is the perfect fit. It gives us low-level control without forcing us to write raw, complex WebGL code.

* **It's Just JavaScript:**
    There are no plugins or external software. It's a JavaScript library that integrates directly into our React component. This allows us to pass data (like the `floorplanPoints`) from our 2D React components straight into the 3D scene.

* **Performance:**
    It's a very thin layer over WebGL, which means it runs directly on the computer's GPU. This allows us to render complex models, lighting, and shadows at a high frame rate (60fps).

* **Vast Ecosystem & Community:**
    Three.js is the most popular 3D library for the web. This means it has an enormous community and a vast collection of "helpers" and utilities. We use several of these:
    * **`GLTFLoader`:** To load our 3D sofa model.
    * **`OrbitControls`:** To provide the "click and drag" camera controls out of the box.
    * **`BufferGeometryUtils`:** To merge our dynamic "footprint" geometry into a single, efficient mesh.

---

## How It Works: The Core Concepts

Think of a Three.js application as a **movie set**. To film anything, you need three basic things:

1.  A **`Scene`**: This is the stage or the virtual world. It's an empty container where you place all your objects, models, and lights.

2.  A **`Camera`**: This is the "eye" that looks at the scene. We use a `PerspectiveCamera`, which mimics a real-world camera (objects farther away appear smaller).
    * (Note: Our `SilhouetteExtractor` utility uses an `OrthographicCamera`, which is a "flat" camera with no perspective, like a 2D blueprint.)

3.  A **`Renderer`**: This is the "artist" and crew. It takes the `Scene` and the `Camera`, calculates what the scene looks like from the camera's point of view, and draws the final 2D image onto the HTML `<canvas>` element. This "drawing" happens in a loop (using `requestAnimationFrame`) 60 times per second.

---

## What's in Our Scene?

Our scene is filled with a few key types of objects:

* **Meshes:** A `Mesh` is the main 3D object you see. Every `Mesh` is a combination of two things:
    1.  **Geometry:** The *shape* or 3D data (the vertices and faces).
        * This can be a primitive (like `BoxGeometry`).
        * It can be a loaded model (like our sofa).
        * It can be custom-generated (like our **`ShapeGeometry`**, which we create from the user's `floorplanPoints`!).
    2.  **Material:** The *skin* or surface (the color, texture, and shininess).
        * We use `MeshStandardMaterial` for the floor and sofa because it's a realistic material that reacts to light.
        * We use `MeshBasicMaterial` for the green "footprint," as it's a simple, flat color that is not affected by lights.

* **Lights:** Without lights, most materials would appear black.
    * **`AmbientLight`:** Provides a soft, general light to the whole scene so nothing is in pure black shadow.
    * **`DirectionalLight`:** Acts like the "sun." It shines from one direction and is the source that allows our sofa to cast realistic **shadows** onto the floor.

---

## How It Fits in Our React App

We don't use Three.js by itself; we wrap it entirely within our `Configurator` React component.

* **The `useEffect` Hook is the Bridge:**
    Our `Configurator` has a `useEffect` hook that runs when the component first appears. This hook is where we call our `init()` function to:
    1.  Create the `Scene`, `Camera`, and `Renderer`.
    2.  Create the `<canvas>` element and append it to the DOM.
    3.  Load the 3D model, create the lights, and start the `animate` loop.

* **State & Props as Triggers:**
    The `useEffect` hook is set to "watch" the `floorplanPoints` prop. If the user goes back and changes their floorplan, the `floorplanPoints` prop changes, and React re-runs the hook. This triggers our logic to throw away the old 3D floor and generate a new one from the new points.

* **Cleanup is Essential:**
    The `return` function inside the `useEffect` hook is our **cleanup crew**. When the user navigates away from the `Configurator`, this function runs and properly **disposes** of all the Three.js objects (geometries, materials, the renderer). This is *critical* for preventing memory leaks in a React application.