# CSDK: Reliverse CanvasSDK

[![Build Status](https://img.shields.io/badge/build-passing-brightgreen)](https://github.com/reliverse/csdk-canvas-sdk)
[![npm version](https://img.shields.io/npm/v/@reliverse/csdk.svg)](https://npmjs.com/package/@reliverse/csdk)
[![License](https://img.shields.io/npm/l/@reliverse/csdk.svg)](LICENSE)
[![Releases](https://img.shields.io/npm/dm/@reliverse/csdk.svg)](https://npmjs.com/package/@reliverse/csdk)

[💖 GitHub Sponsors](https://github.com/sponsors/blefnk) • [💬 Discord](https://discord.gg/Pb8uKbwpsJ) • [📦 NPM](https://npmjs.com/package/@reliverse/csdk) • [✨ Repo](https://github.com/reliverse/csdk-canvas-sdk)

> - 🎧 **Prefer listening than reading?** Reliverse Deep Dive on CanvasSDK is live! [▶️ Listen here](./docs/podcast-csdk.mp3).
> - 🤖 **Want to discuss this repo with AI?** Reliverse AI will be happy to chat with you! [💬 Talk here](https://reliverse.org/repo/reliverse/csdk-canvas-sdk).

**@reliverse/csdk** is a TypeScript-first Canvas 2D rendering engine. It is a modern, lightweight, and modular 2D rendering engine for the HTML Canvas, built entirely with TypeScript. It provides a flexible and performant way to draw and animate shapes, text, and images across different JavaScript environments, including browsers, Web Workers, and Node.js.

## Key Features

- **🚀 TypeScript-First:** Built from the ground up with TypeScript for robust type safety and excellent developer experience (IntelliSense, compile-time checks).
- **💡 Lightweight & Modular:** A minimal core engine with features like shapes, animation, and interaction available as opt-in, tree-shakable plugins. Only include what you need!
- **🌐 Cross-Environment:** Write your drawing logic once and run it in the browser (main thread), Web Workers (`OffscreenCanvas` / `WebGL` / `WebGPU`), or Node.js (headless rendering using `@reliverse/csdk-node`).
- **🔌 Extensible Plugin Architecture:** Easily add new shapes, behaviors (like drag-and-drop), or integrations without modifying the core.
- **⚡ Performance Focused:** Explicit rendering control, optional dirty-checking to skip unnecessary redraws, and support for caching complex drawables. Designed to be efficient by default.
- **🧩 Simple Core API:** Easy-to-learn concepts based on `Renderer`, `CanvasSurface`, `Drawable`, and an optional `Scheduler`.
- **🏗️ Framework Agnostic:** Works seamlessly with vanilla JS/TS or integrates easily into frameworks like React, Vue, or Svelte.

## Heads Up

- **Most of the things** mentioned in this doc **aren’t implemented yet** — they’re part of the vision for `v1.0.0`.
- Got thoughts? Ideas? Complaints? Drop your feedback in [Discord](https://discord.gg/Pb8uKbwpsJ) or use [GitHub Issues](https://github.com/reliverse/csdk-canvas-sdk/issues).
- Your feedback means the world and helps shape where this project goes next. Thank you!

## Installation

```bash
# bun / pnpm / yarn / npm
bun add @reliverse/csdk
```

## Why CanvasSDK

The modern web demands flexible, performant rendering tools that work across various environments without compromise. **CanvasSDK** aims to be a lightweight, modular, and runtime-flexible graphics engine that decouples Canvas 2D rendering capabilities from the DOM, enabling consistent rendering in browsers, headless environments, and even Node.js. Designed with developer ergonomics and performance in mind, CanvasSDK offers a simple core API that is easy to extend through an opt-in plugin system.

**@reliverse/csdk** is not just another canvas lib—it’s a modern TypeScript-native 2D engine that treats rendering like it actually matters. Built for speed, crafted for flexibility, and designed to work wherever JavaScript runs: browsers, Web Workers, Node.js—you name it. This isn’t a bloated abstraction or a half-baked wrapper. **CanvasSDK** gives you **raw control** with a modular core, blazing-fast rendering paths (including OffscreenCanvas, WebGL, and WebGPU), and a plugin system that doesn’t get in your way. You extend what you want, skip what you don’t.

We decouple rendering from the DOM so you can draw _wherever_ the runtime lets you, without sacrificing performance or dev experience. Whether you’re building a visual editor, a game, a dashboard, or a full-on 2D engine—**CSDK is the surface you paint on**. Designed for people who think in frames. Powered by a minimal, flexible API. No fluff. No magic. No lock-in. The modern web doesn't need another canvas toy. It needs something that _feels right_ in 2025.

## Quick Start

Here's a basic example of drawing a red rectangle and a blue circle on a canvas in the browser:

```typescript
import { CanvasSurface, Renderer } from "@reliverse/csdk/core";
import { Rect, Circle } from "@reliverse/csdk/plugins/shapes";

// Assume you have <canvas id="myCanvas" width="400" height="300"></canvas> in your HTML
const canvasElement = document.getElementById("myCanvas") as HTMLCanvasElement;

// 1. Create a CanvasSurface from your canvas element
const surface = new CanvasSurface(canvasElement);

// 2. Create a Renderer for the surface
const renderer = new Renderer(surface);

// 3. Create Drawable shapes
const redRect = new Rect({
  x: 50, y: 40,
  width: 120, height: 80,
  fill: "red"
});

const blueCircle = new Circle({
  x: 220, y: 80,
  radius: 50,
  stroke: "blue", strokeWidth: 5
});

// 4. Add drawables to the renderer
renderer.add(redRect).add(blueCircle);

// 5. Render the scene
renderer.render();
```

This will render the shapes onto your canvas element.

## Core Concepts

CanvasSDK's architecture is built around four main components:

1. **`CanvasSurface`**: Abstracts the rendering target (`<canvas>`, `OffscreenCanvas`, or `@reliverse/csdk-node`). It provides a unified way to get a `CanvasRenderingContext2D` regardless of the environment.
2. **`Drawable`**: An interface or base class for anything that can be drawn. Shapes (`Rect`, `Circle`), `Text`, and `Image` are common drawables. You can easily create your own custom drawables. Each drawable knows how to render itself using the canvas context.
3. **`Renderer`**: Orchestrates the drawing process. It holds a list of drawables and, when `render()` is called, it iterates through them, calling their `draw()` method on the `CanvasSurface`'s context. It typically handles clearing the canvas and managing the draw order.
4. **`Scheduler`**: A utility for managing animation loops. It uses `requestAnimationFrame` (in browsers) or timers to trigger updates at regular intervals (e.g., 60 FPS), providing a `deltaTime` for smooth, time-based animations.

<p align="left">
  <img src="./docs/core.png" alt="The core modules of CanvasSDK and their interactions." width="300" />
</p>

This separation of concerns makes the engine flexible and easy to reason about. The `Renderer` decides _what_ and _when_ (in order) to draw, the `Drawable` knows _how_ to draw itself, the `CanvasSurface` handles _where_ to draw, and the `Scheduler` helps automate _when_ for animations.

## Key Features In-Depth

### Cross-Environment Rendering

CanvasSDK works consistently across different JavaScript environments:

- **Browser (Main Thread):** Uses standard `HTMLCanvasElement` with an optional CanvasSDK's improved API.
- **Web Workers:** Uses `OffscreenCanvas` to perform rendering off the main thread, improving UI responsiveness.
- **Node.js:** Uses the `@reliverse/csdk-node` for headless rendering on the server (e.g., generating images, SSR).

The `CanvasSurface` handles the environment detection and context acquisition, so your rendering code (`Renderer` and `Drawable` logic) remains the same everywhere.

```typescript
// Example: Node.js usage
import { createCanvas } from "@reliverse/csdk-node";
import { CanvasSurface, Renderer, Text } from "@reliverse/csdk";
import * as fs from "fs";

const nodeCanvas = createCanvas(400, 200);
const surface = new CanvasSurface(nodeCanvas);
const renderer = new Renderer(surface);

const helloText = new Text({ x: 50, y: 100, text: "Rendered in Node!", font: "30px sans-serif", fill: "blue" });
renderer.add(helloText).render();

// Output to a file
fs.writeFileSync("output.png", nodeCanvas.toBuffer("image/png"));
console.log("Saved output.png");
```

### Performance Strategies

CanvasSDK employs several strategies for efficient rendering:

- **Explicit Rendering:** By default, the canvas only updates when you explicitly call `renderer.render()`. No wasted cycles rendering static content.
- **Dirty Checking (Opt-in):** The engine can track changes (`dirty` flags) and skip rendering frames if nothing has changed, saving CPU and battery.
- **Controlled Updates:** Use the `Scheduler` for smooth animations, or trigger manual renders on events for interactive applications. `deltaTime` is provided for frame-rate independent movement.
- **Caching:** Complex `Drawable` objects can potentially cache their rendering to an offscreen canvas, allowing faster redraws (blitting) if the object itself hasn't changed. (This might be provided via a plugin or utility).
- **Layering:** While not built into the core, the architecture supports using multiple canvases or managing layers via plugins to optimize redraws for complex scenes (e.g., static background, dynamic foreground).

### Extensible Plugin System

CanvasSDK's core is minimal. Additional features are added via plugins:

- **Shapes Plugin (`@reliverse/csdk/plugins/shapes`):** Provides common drawables like `Rect`, `Circle`, `Path`, `Text`, `Image`.
- **Animation Plugin (`@reliverse/csdk/plugins/animation`):** Offers tools for tweening properties over time, potentially using the `Scheduler`.
- **Interaction Plugin (`@reliverse/csdk/plugins/interaction`):** Adds event handling (click, hover, drag) to drawables, including hit-testing.
- **Debug Plugin (`@reliverse/csdk/plugins/debug`):** Utilities for development, like drawing bounding boxes or displaying FPS.
- **SceneGraph Plugin (`@reliverse/csdk/plugins/scenegraph`):** An optional layer for retained-mode rendering with node hierarchy and transformations.

Plugins are **opt-in** and **tree-shakable**. If you don't import and use a plugin, its code won't be included in your final bundle.

```typescript
// Example: Using the Interaction Plugin
import { InteractionPlugin } from "@reliverse/csdk/plugins/interaction";

const interaction = new InteractionPlugin();
renderer.use(interaction); // Register the plugin with the renderer

interaction.on(myCircle, "pointerover", () => {
  myCircle.fill = "lightblue";
  renderer.render(); // Re-render to show hover effect
});
interaction.on(myCircle, "pointerout", () => {
  myCircle.fill = "blue";
  renderer.render();
});
interaction.on(myCircle, "click", () => {
  console.log("Circle clicked!");
});
```

You can also easily create your own custom `Drawable` implementations or plugins.

## More Examples

### Animation Loop

```typescript
import { Scheduler } from "@reliverse/csdk/core";
import { Circle } from "@reliverse/csdk/plugins/shapes";

const circle = new Circle({ x: 50, y: 150, radius: 20, fill: "green" });
renderer.add(circle);

const scheduler = new Scheduler();
let direction = 1;

scheduler.onTick((deltaTime) => {
  // Move the circle back and forth
  circle.x += 0.1 * deltaTime * direction;
  if (circle.x > surface.width - circle.radius || circle.x < circle.radius) {
    direction *= -1; // Reverse direction at edges
  }

  renderer.render(); // Render the updated position
});

scheduler.start(); // Start the animation loop

// To stop later: scheduler.stop();
```

### Manual Rendering on Event

```typescript
import { Rect } from "@reliverse/csdk/plugins/shapes";

// Create a rectangle and add it to the renderer
const rect = new Rect({ x: 10, y: 10, width: 50, height: 50, fill: "purple" });
renderer.add(rect).render();

// Change color on button click
const button = document.getElementById("changeColorBtn");
button.addEventListener("click", () => {
  rect.fill = `rgb(${Math.random() * 255}, ${Math.random() * 255}, ${Math.random() * 255})`;
  renderer.render(); // Only render when the button is clicked
});
```

## Why Choose CanvasSDK?

Compared to other canvas libraries, CanvasSDK aims for a sweet spot:

- **More Modern & Ergonomic than Older Libs:** Leverages TypeScript, ES Modules, and modern JS features. Avoids global namespace pollution and ensures tree-shakability.
- **Simpler & Lighter than Feature-Heavy Engines:** Less complex than libraries like Pixi.js if you don't need WebGL or advanced game engine features. Lower bundle size footprint.
- **More Flexible than Opinionated Frameworks:** Doesn't force a specific paradigm like a mandatory scene graph (unlike Paper.js or Konva's default structure). You choose the level of abstraction.
- **Better Cross-Environment Focus:** Designed from the start to work reliably in browsers, workers, and Node.js, unlike libraries where this was an afterthought.
- **Emphasis on Developer Control:** You control the rendering loop and performance optimizations explicitly.

CanvasSDK is ideal for applications needing custom 2D graphics, interactive visualizations, simple games, or server-side image generation, where performance, bundle size, and modern development practices are important.

## Future Plans

- [ ] Official bindings for React, Vue, and Svelte
- [ ] Continuous improvements to performance and API ergonomics
- [ ] Advanced plugins (e.g., SVG export, physics integration, enhanced text layout)
- [ ] Implement WebGL/WebGPU rendering backend abstraction layer for high-performance scenarios (as an optional extension). Potential example:

```typescript
const sdk = new CanvasSDK({
  renderer: "webgl", // Use WebGL instead of Canvas 2D
  fallback: "canvas" // Fallback if WebGL isn't available
});
```

- [ ] In the `csdk` implementation use the `class`/`this` approach as rarely as possible
- [ ] Integrate with [@reliverse/rengine](https://github.com/reliverse/rengine) (into web module)
- [ ] Implement `@reliverse/csdk-node`
- [ ] Integrate with [Anime.js](https://animejs.com/documentation/getting-started)
- [ ] Improve [RESEARCH.md](RESEARCH.md)

## Who Uses?

- 🔜 [reliverse.org](https://reliverse.org)
- 🔜 [Bleverse Games](https://games.bleverse.com)
- 🔜 [Rengine](https://github.com/reliverse/rengine)
- 🔜 [MFPiano](https://mfpiano.org)

## API Documentation

- (🔜 Soon) Full API documentation can be found here.

## Learning Resources

- [CanvasSDK Research](RESEARCH.md)
- [WebGL How It Works](https://webglfundamentals.org/webgl/lessons/webgl-how-it-works.html)
- [p5.js WebGL Mode Architecture](https://p5js.org/contribute/webgl_mode_architecture)

## Contributing

Contributions are welcome! Please read our _Contributing Guidelines (🔜 Soon)_ for details on how to submit pull requests, report issues, and suggest features.

See our [Deep Research](RESEARCH.md) to learn more about what we plan to implement.

## Shoutouts

- [konva](https://github.com/konvajs/konva#readme)
- [canvas](https://github.com/Automattic/node-canvas#readme)
- [pixijs](https://github.com/pixijs/pixijs#readme)
- [p5.js](https://github.com/processing/p5.js#readme)
- [three.js](https://github.com/mrdoob/three.js#readme)
- [anime.js](https://github.com/juliangarnier/anime#readme)

## License

💖 [MIT](LICENSE) 2025 © [blefnk Nazar Kornienko](https://github.com/blefnk) & [Reliverse](https://github.com/reliverse)
