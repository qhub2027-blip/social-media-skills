---
name: "web-3d-scenes"
description: "Build interactive 3D web scenes (react-three-fiber, rapier physics, postprocessing, Scenario/Blender assets) for videos, 3D hero sections and product viewers."
---

# Web 3D Scenes

For 3D product viewers, immersive hero sections, and animated 3D marketing visuals on the web.

## Stack
- `three` + `@react-three/fiber` (R3F) — declarative Three.js in React.
- `@react-three/drei` — helpers (`OrbitControls`, `Environment`, `useGLTF`, `Float`, `Text3D`, `ContactShadows`). Almost always wanted.
- `@react-three/rapier` — physics (rigid bodies, colliders, gravity) built on the Rapier engine.
- `@react-three/postprocessing` — effect passes (Bloom, DepthOfField, Vignette, Noise, ChromaticAberration) for a cinematic look.

Install: `npm i three @react-three/fiber @react-three/drei @react-three/rapier @react-three/postprocessing`.

## Scene skeleton
```jsx
<Canvas shadows camera={{ position:[0,2,6], fov:45 }}>
  <Environment preset="city" />
  <Physics gravity={[0,-9.8,0]}>
    <RigidBody colliders="hull"><MyModel/></RigidBody>
    <RigidBody type="fixed"><mesh rotation={[-Math.PI/2,0,0]}><planeGeometry args={[50,50]}/><shadowMaterial opacity={0.3}/></mesh></RigidBody>
  </Physics>
  <EffectComposer><Bloom intensity={0.6} luminanceThreshold={0.8}/><Vignette eskil={false} offset={0.2} darkness={0.8}/></EffectComposer>
</Canvas>
```

## Physics (rapier)
- Wrap dynamic objects in `<RigidBody>`; static ground = `type="fixed"`. Choose `colliders` (`cuboid`/`ball`/`hull`/`trimesh`) by shape vs cost.
- Trigger impulses on interaction (`api.applyImpulse`). Keep collider counts sane for perf; trimesh is expensive.

## Postprocessing
- Add atmosphere with Bloom (glow), DepthOfField (focus), subtle Noise/Vignette. Don't stack too many — each pass costs frame time.
- Tune `luminanceThreshold` so only highlights bloom.

## AI 3D assets & textures (Scenario / Blender)
- **Scenario** (app.scenario.com): train/use style-consistent generators for textures, skyboxes, and reference art; export maps (albedo/normal/roughness) and apply in Blender or directly as Three.js `meshStandardMaterial` maps.
- **Blender**: author/clean models, bake lighting/AO, and export **glTF/GLB** (Draco-compressed) for the web. Keep polycount low; bake detail into normal maps.
- Load in R3F with `useGLTF('/model.glb')`; preload with `useGLTF.preload`.

## Performance
- GLB + Draco/meshopt compression; lazy-load the Canvas (dynamic import, below the fold).
- Cap `dpr={[1,2]}`, use `frameloop="demand"` for static scenes, instance repeated meshes.
- Always provide a 2D fallback / poster image for low-end devices and reduced-motion.
