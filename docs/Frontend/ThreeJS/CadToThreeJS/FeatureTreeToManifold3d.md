### The "Transpiler" Architecture: A Viable Path for Parametric CAD

This document explains the "Compiler/Transpiler" model for creating a parametric 3D web application. This is the correct, robust, and scalable alternative to the "glTF + JSON" approach.

Instead of modifying existing geometry, this architecture **rebuilds the entire 3D model from scratch** on every parameter change. This is made possible by a fast, client-side geometry kernel.

-----

### 1\. The Core Concept: A Compiler Pipeline

Think of this entire system as a compiler, which has three distinct parts. This analogy is the key to understanding the workflow.

1.  **The Frontend (The CAD Plugin):**

      * **What it is:** A custom plugin you write for your "source" CAD software (e.g., Inventor, SolidWorks).
      * **Job:** To "parse" the proprietary, native feature tree and translate it into a universal, intermediate language.

2.  **The Intermediate Representation (IR):**

      * **What it is:** Your custom **"JSON Contract"** or "Feature Tree."
      * **Job:** To represent the *design intent* (the step-by-step recipe) in a simple, standardized, text-based format. This is your "Abstract Syntax Tree" (AST).

3.  **The Backend (The Web App):**

      * **What it is:** Your JavaScript/TypeScript code running in the browser.
      * **Job:** To "compile" or "interpret" the JSON (the IR) into "machine code"—a series of low-level commands that a geometry kernel can execute.

-----

### 2\. The Core Components in Detail

#### Component A: The JSON "Recipe" (The Intermediate Representation)

This is the file you custom-export from your CAD software. It is **not** just a list of parameters. It is a **step-by-step history of operations** that reference those parameters.

**Example `recipe.json`:**

```json
{
  "parameters": {
    "width": 100,
    "length": 150,
    "thickness": 20,
    "hole_diam": 15
  },
  "history": [
    {
      "id": "base_sketch",
      "type": "SKETCH_2D",
      "plane": "XY",
      "shapes": [
        { "type": "rect", "x": 0, "y": 0, "w": "width", "h": "length" }
      ]
    },
    {
      "id": "base_solid",
      "type": "EXTRUDE",
      "target": "base_sketch",
      "depth": "thickness"
    },
    {
      "id": "hole_cutout",
      "type": "CYLINDER",
      "radius": "hole_diam / 2",
      "height": "thickness + 10",
      "position": ["width / 2", "length / 2", 0]
    },
    {
      "id": "final_part",
      "type": "BOOLEAN_CUT",
      "target": "base_solid",
      "tool": "hole_cutout"
    }
  ]
}
```

#### Component B: The JavaScript "Translator" (The Interpreter)

This is the core of your web application. It is a **tree walker** that uses the "Visitor" pattern. Its job is to read the `history` array from the JSON and call the Manifold3D API.

**Example `translator.js` (Simplified):**

```javascript
// (Assumes Manifold3D WASM module is loaded as 'manifold')

class ParametricEngine {
  constructor() {
    this.cache = new Map(); // Stores features by ID (e.g., 'base_solid')
  }

  // Main function to build the model
  build(contract) {
    this.cache.clear();
    const params = contract.parameters;

    // 1. Create a parameter evaluator
    // (This is a helper that safely evaluates "width / 2" to 50)
    const eval = (expr) => this.evaluate(expr, params);

    // 2. Walk the feature tree
    for (const step of contract.history) {
      switch (step.type) {
        
        case 'SKETCH_2D':
          // Creates a 2D Manifold.CrossSection object
          const poly = manifold.CrossSection.rectangle(
            eval(step.shapes[0].w), 
            eval(step.shapes[0].h)
          );
          this.cache.set(step.id, poly);
          break;

        case 'EXTRUDE':
          // Get the 2D sketch from the cache
          const sketch = this.cache.get(step.target);
          const depth = eval(step.depth);
          
          // Call Manifold API
          const solid = manifold.Manifold.extrude(sketch, depth);
          this.cache.set(step.id, solid);
          break;

        case 'CYLINDER':
          const r = eval(step.radius);
          const h = eval(step.height);
          const pos = [eval(step.position[0]), eval(step.position[1]), eval(step.position[2])];

          // Call Manifold API
          let cyl = manifold.Manifold.cylinder(h, r);
          cyl = cyl.translate(pos);
          this.cache.set(step.id, cyl);
          break;

        case 'BOOLEAN_CUT':
          // Get the two solids from the cache
          const target = this.cache.get(step.target);
          const tool = this.cache.get(step.tool);

          // Call Manifold API
          const result = manifold.Manifold.difference(target, tool);
          this.cache.set(step.id, result);
          break;
      }
    }
    
    // Return the final feature
    return this.cache.get(contract.history.at(-1).id);
  }

  evaluate(expr, params) {
    // In production, use a safe math parser, NOT eval()!
    if (typeof expr === 'number') return expr;
    let str = expr.toString();
    for (const [key, val] of Object.entries(params)) {
      str = str.replaceAll(key, val);
    }
    return new Function(`return ${str}`)();
  }
}
```

#### Component C: The Manifold3D Kernel (The "Machine")

This is the "dumb" but powerful engine. It doesn't know what a "feature tree" is. It only knows how to execute low-level commands passed to it by the Translator.

-----

### 3\. The Full Workflow (Step-by-Step)

Here is what happens when a user changes a parameter:

1.  **Initial Load:**

      * The browser loads your web app, the `Manifold3D.wasm` module, and the `recipe.json` for a default model.
      * The `ParametricEngine` runs `build(recipe)` for the *first time*.
      * It generates the final `Manifold` object.
      * You convert this object to a `THREE.BufferGeometry` and add it to your scene.

2.  **User Interaction:**

      * A user moves a UI slider, changing `parameters.width` from `100` to `110`.

3.  **The Re-Build:**

      * You call `engine.build(recipe)` again with the *new* parameters.
      * The engine **throws away all old results** in its cache.
      * It re-runs the *entire* history from top to bottom, but this time using `width = 110`.
      * `SKETCH_2D`: Creates a *wider* 2D rectangle.
      * `EXTRUDE`: Extrudes the *wider* sketch.
      * `CYLINDER`: Re-creates the cylinder at the *new* position (`width / 2` is now `55`).
      * `BOOLEAN_CUT`: Subtracts the cylinder from the wider block.
      * The whole process takes **5-10 milliseconds**.

4.  **The Render:**

      * The `build()` function returns the *new* `Manifold` object.
      * You **dispose of the old `BufferGeometry`** in Three.js (to prevent memory leaks).
      * You create a new `BufferGeometry` from the new Manifold object and put it in the scene.
      * To the user, the model appears to have "updated instantly."

-----

### 4\. The "Real" Work & Challenges

This architecture is robust, but the work is **significant**. The complexity is no longer in Three.js, but in building the translator.

1.  **The Exporter Plugin (The \#1 Hurdle):**
    This is the "Compiler Frontend." You must write a plugin for SolidWorks (C\#), Inventor (VBA/iLogic), or Fusion 360 (Python) that can read the *real* feature tree and export your *custom* JSON. This is a complex, software-specific task.

2.  **2D Sketch Engine:**
    My example used a simple rectangle. A real translator needs to read/execute complex 2D sketches with lines, arcs, and constraints. This is a major engineering task in itself.

3.  **Topological Referencing (The "Hard Problem"):**
    My JSON used math (`width / 2`). A real CAD model uses topology (`sketch on this face`, `fillet on this edge`). When the model rebuilds, "Face \#3" might become "Face \#5". You must build a system to reliably track these relationships. This is the hardest part of CAD development.

4.  **Fillets and Chamfers (The Kernel Limitation):**
    Manifold3D is a CSG kernel and does *not* have simple commands for fillets or chamfers. You must implement the math for these yourself (e.g., by creating a "negative" torus shape and subtracting it), which is extremely difficult.