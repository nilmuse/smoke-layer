# smoke-layer

Add animated smoke to an HTML container with a Canvas 2D layer and your own texture image.

[![npm](https://img.shields.io/npm/v/smoke-layer.svg?colorB=brightgreen)](https://www.npmjs.com/package/smoke-layer)
[![NPM Downloads](https://img.shields.io/npm/dm/smoke-layer.svg?style=flat)](https://www.npmjs.com/package/smoke-layer)

[Demo](https://codepen.io/ux-ui/pen/oNOaLyx)

## Installation

```bash
npm install smoke-layer
```

## Quick start

In a browser app with a bundler, serve a smoke texture with a transparent background at `/images/fog.png`. The texture is supplied by your app.

Add a container with a visible size:

```html
<div id="smoke-layer" class="smoke-preview" aria-hidden="true"></div>
```

```css
.smoke-preview {
  height: 320px;
  overflow: hidden;
  background: #101820;
}
```

Run this TypeScript module after the container exists:

```ts
import { SmokeLayer } from 'smoke-layer';

const container = document.getElementById('smoke-layer');

if (!container) {
  throw new Error('Smoke container was not found');
}

const smokeLayer = new SmokeLayer({
  container,
  texture: '/images/fog.png',
  tintColor: '#98B4C9',
  opacity: 0.45,
});

smokeLayer.start();
```

The constructor adds a canvas to the container and loads the texture. Smoke appears after the texture loads. Call `smokeLayer.dispose()` before removing the container.

## API

The package exports `SmokeLayer` and the `SmokeLayerOptions` type:

```ts
import { SmokeLayer } from 'smoke-layer';
import type { SmokeLayerOptions } from 'smoke-layer';
```

### Options

Pass these options to `new SmokeLayer(options)`:

| Option | Type | Default | Description |
| --- | --- | --- | --- |
| `container` | `HTMLElement` | Required | Element that receives the smoke canvas. |
| `texture` | `string` | Required | URL of the smoke texture image. |
| `tintColor` | `string` | `'#ffffff'` | Hex tint color, in `#rgb` or `#rrggbb` form. |
| `opacity` | `number` | `0.3` | Global smoke opacity, from `0` to `1`. |
| `smokeDensity` | `number` | `1` | Density multiplier for automatic particle count. |
| `particleScale` | `number` | `1` | Scale multiplier for automatic particle sizing. |
| `particleSize` | `number` | `undefined` | Fixed particle size in pixels. When omitted, size is calculated from the container. |
| `particleCount` | `number` | `undefined` | Fixed particle count. When omitted, count is calculated from the smoke area and density. |
| `maxParticleCount` | `number` | `420` | Upper limit for particle count. |
| `maxDpr` | `number` | `2` | Maximum device pixel ratio used for the canvas drawing buffer. |
| `windX` | `number` | `6` | Horizontal drift speed in pixels per second. |
| `windY` | `number` | `-8` | Vertical drift speed in pixels per second. |
| `turbulence` | `number` | `1` | Multiplier for sway, pulse, and depth movement. |
| `animationSpeed` | `number` | `1` | Time scale for animation updates. |

### Methods

- `start()` starts or resumes the animation loop.
- `stop()` pauses the animation loop and keeps the canvas and observers.
- `dispose()` stops animation, disconnects observers, and removes the canvas. Create a new instance if you need the effect again after disposal.

## Adjust smoke motion

As an alternative to the instance in Quick start, use the same `container` and texture with these options:

```ts
const smokeLayer = new SmokeLayer({
  container,
  texture: '/images/fog.png',
  tintColor: '#98B4C9',
  opacity: 0.45,
  particleSize: 280,
  windX: 5,
  windY: -9,
  turbulence: 1.5,
  animationSpeed: 1.25,
});

smokeLayer.start();
```

## Vue 3

This component uses the same texture URL as Quick start and disposes of the layer on unmount:

```vue
<script setup lang="ts">
import { onBeforeUnmount, onMounted, ref, shallowRef } from 'vue';
import { SmokeLayer } from 'smoke-layer';

const containerRef = ref<HTMLDivElement | null>(null);
const layerRef = shallowRef<SmokeLayer | null>(null);

onMounted(() => {
  if (!containerRef.value) return;

  layerRef.value = new SmokeLayer({
    container: containerRef.value,
    texture: '/images/fog.png',
  });
  layerRef.value.start();
});

onBeforeUnmount(() => {
  layerRef.value?.dispose();
  layerRef.value = null;
});
</script>

<template>
  <div ref="containerRef" class="smoke-preview" aria-hidden="true"></div>
</template>

<style scoped>
.smoke-preview {
  height: 320px;
  overflow: hidden;
  background: #101820;
}
</style>
```

## Browser support

Create the layer in the browser after its container exists. It needs Canvas 2D and `requestAnimationFrame`. The container must have a nonzero width and height, and the texture URL must be accessible to the browser. Cross-origin textures need a server that allows CORS requests.

The layer follows container size and device pixel ratio. It uses `ResizeObserver` when available and falls back to window resize events otherwise. Rendering pauses when the document is hidden and, with `IntersectionObserver`, when the container leaves the viewport.

## License

MIT
