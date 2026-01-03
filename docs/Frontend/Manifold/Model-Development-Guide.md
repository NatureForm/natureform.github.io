# NatureForm Model Plugin Guide

This guide explains how to create and structure a 3D model file so that it is automatically compatible with the NatureForm Playground. Our architecture uses a **decoupled plugin system**, meaning each model is self-contained and manages its own parameters, geometry logic, and rendering style.

## 1. File Location
All model files must be placed in:
`src/utils/Objects/[YourModelName].js`

## 2. Required Structure
Every model file **must** export exactly three items:
1. `config`: Defines the UI sliders and inputs.
2. `generator`: Handles the heavy math and Manifold (Boolean) operations.
3. `render`: Bridges the Manifold data to Three.js meshes and materials.

---

## 3. Detailed Export Requirements

### A. The `config` Object
The config object tells the GUI (lil-gui) which sliders and folders to create.

```javascript
export const config = {
  type: 'My Unique Model Name', // Used as the ID in the dropdown
  label: 'User Friendly Label', 
  params: [
    { 
      name: 'width', 
      type: 'number', 
      default: 1000, 
      min: 100, 
      max: 5000, 
      label: 'Overall Width', 
      folder: 'Dimensions' 
    },
    { 
      name: 'materialType', 
      type: 'select', 
      options: ['Wood', 'Metal'], 
      default: 'Wood', 
      folder: 'Visuals' 
    }
  ]
};
```

### B. The generator Function
This function should perform the geometry calculations. It must return an object containing named Manifold parts.

Input: manifoldLib (The WASM library) and inputParams (Current slider values).

Output: An object where keys are part names (e.g., { seat: manifoldObj, legs: manifoldObj }).

```javascript
export function generator(manifoldLib, inputParams) {
  const { Manifold } = manifoldLib;
  const { cube } = Manifold;
  
  // Logic here
  const myCube = cube([inputParams.width, 400, 450]);

  return {
    mainPart: myCube
  };
}
```

### C. The render Function
The render function is called by the Playground to turn Manifold geometry into visible Three.js objects.

- result: The object returned by your generator.

- group: The Three.js Group where you should add your meshes.
 
- helpers: An API provided by the Playground containing:
 
- createThreeMesh: Function to convert Manifold to Three.js Mesh.

- applyBoxUV: Standardized UV mapping.

- textureLoader: For loading local images.

- THREE: Access to the Three.js constant library.

```javascript
export function render(result, group, helpers, appState) {
  const { createThreeMesh, applyBoxUV, textureLoader, THREE } = helpers;

  // 1. Create Materials (and load textures)
  const mat = new THREE.MeshStandardMaterial({ color: 0x888888 });

  // 2. Convert and Add to scene
  if (result.mainPart) {
    const mesh = createThreeMesh(result.mainPart, mat);
    applyBoxUV(mesh.geometry, 0.0005); // Standard scale
    group.add(mesh);
  }
}
```

## 4. Best Practices
Self-Contained Textures
To keep the Playground clean, import your textures directly at the top of your model file. This ensures that when you move the file, the textures move with it.

```javascript
import diffUrl from '../../assets/my_textures/diffuse.jpg';
```

### Performance

Boolean operations are expensive.

Try to keep the number of Manifold operations (subtract, union) to a minimum.

In the render function, always check if a part exists (if (result.partName)) before creating a mesh.

Dispose of old geometries? No need—the Playground handles the cleanup of the modelGroup automatically before every regeneration.

Shaders and Grooves
If your model requires custom logic (like the procedural wood grooves), implement the onBeforeCompile logic inside your render function. This keeps specialized shader code isolated to the specific model that needs it.

### Troubleshooting
Model not showing up? Check the Browser Console (F12). The Playground logs a warning if a file is missing one of the three required exports (config, generator, render).

UI not updating? Ensure your config.params names match the keys you are accessing in the generator via inputParams.
