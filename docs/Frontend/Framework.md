# Framework: Why We Use React as a framework

This document provides a high-level overview of why React was chosen as the framework for this application and how its core principles are used to manage our interactive tools.

## Why React?

React is a JavaScript library for building user interfaces. It's particularly well-suited for our project, which involves managing complex, interactive state for both a 2D editor and a 3D configurator.

  * **Component-Based Architecture:** Our entire application is built from small, independent, and reusable "components."

      * The `Floorplan` selector is a component.
      * The `FloorplanWorkspace` editor is another component.
      * The `Configurator` 3D scene is a component.
      * Even the UI sliders and buttons are individual components.
        This makes the code easy to manage, test, and update without one part breaking another.

  * **Declarative UI (State Management):** This is the most important concept.

      * **Traditional (Imperative):** In older code, you'd write step-by-step instructions like: "find the slider," "get its value," "find the 3D model," "update the model's X position." This is complex and error-prone.
      * **React (Declarative):** We simply *declare* the state we want. We have a state variable, `modelPositionX`. The slider's value is *bound* to this state, and the 3D model's position is *derived* from this state. When the user moves the slider, it updates the state, and React automatically ensures the 3D model's position updates to match. We just describe the "what" (the model's position should match this variable), and React handles the "how."

  * **Rich Ecosystem:** React is the most popular UI library, meaning it has a massive community and integrates well with other libraries. This is crucial for us, as it simplifies the integration of powerful tools like **`three.js`** (for 3D graphics) and **`react-router-dom`** (for navigation).

## How It All Works Together: Data Flow

React uses a **one-way data flow**, which makes the application predictable. Data flows "down" from parent components to children, and events (like user clicks) flow "up" via callback functions.

Here is a simplified diagram of our application's architecture:

```
[ App.js ]  <---------------------------------------+
   |                                                | 4. `onFinish` callback updates the
   | (State: floorplanPoints)                       |    `floorplanPoints` state.
   |                                                |
   +--> [ Floorplan.js ] --- (passes onFinish) ----> [ FloorplanWorkspace.js ]
   |      (Renders workspace)                         |
   |                                                  | 3. User clicks "Finish,"
   |                                                  |    and workspace calls `onFinish(points)`.
   |                                                  |
   +--> [ Configurator.js ] <------------------------+
        (Receives floorplanPoints as a "prop")        |
                                                      | 5. React sees state changed
                                                      |    and re-renders Configurator
                                                      |    with the *new* points.
```

### The Step-by-Step Flow

1.  **Top-Level State:** The main `App` component holds the most important piece of state: `floorplanPoints`.

2.  **Data Down (Props):**

      * The `App` component renders the `Floorplan` component, passing it a function prop called `onFinish`.
      * The `App` component also renders the `Configurator` component, passing it the *current* `floorplanPoints` array as a prop.

3.  **Events Up (Callbacks):**

      * When the user finishes drawing in the `FloorplanWorkspace`, they click the "Finish" button.
      * The workspace calls the `onFinish` function it received, passing the new array of points (e.g., `onFinish([...])`).

4.  **State Update:**

      * This `onFinish` function lives in the main `App` component. When called, it updates the `floorplanPoints` state inside `App` with the new points.

5.  **Automatic Re-render:**

      * React detects that the `floorplanPoints` state has changed.
      * It automatically re-renders any component that depends on that state. In this case, it re-renders the `Configurator` component.
      * The `Configurator` now receives the *new* `floorplanPoints` as its prop, triggering its internal `useEffect` hook to discard the old 3D floor and generate a new one.

### The Bridge to `three.js`: `useEffect`

React manages the DOM, but **`three.js`** manages a `<canvas>` element. We use the **`useEffect` hook** as the bridge between these two worlds.

In the `Configurator` component, the `useEffect` hook essentially says: "When the `floorplanPoints` prop changes, run this code to build (or re-build) the `three.js` scene."

This hook is also responsible for **cleanup**. When the component is removed, the `useEffect`'s return function runs, which safely disposes of all `three.js` geometries, materials, and the renderer. This prevents memory leaks and is a critical part of mixing React with external libraries.