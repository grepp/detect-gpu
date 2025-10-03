# Forked from [pmndrs/detect-gpu](https://github.com/pmndrs/detect-gpu)

Fixed the automatic update issue on detect-gpu

# Detect GPU

[![npm version](https://badge.fury.io/js/@grepp%2Fdetect-gpu.svg)](https://badge.fury.io/js/@grepp%2Fdetect-gpu)

Classifies GPUs based on their 3D rendering benchmark score allowing the developer to provide sensible default settings for graphically intensive applications. Think of it like a user-agent detection for the GPU but more powerful.

## Demo

[Live demo](https://gistpreview.github.io/?d86f1681c364f614bd47a4079fb99ac8/detect-gpu-demo.html)

## Installation

By default we use the [UNPKG](https://unpkg.com) CDN to host the benchmark data. If you would like to serve the benchmark data yourself download the required benchmarking data from [benchmarks.tar.gz](https://github.com/pmndrs/detect-gpu/raw/master/benchmarks.tar.gz) and serve it from a public directory.

Make sure you have [Node.js](http://nodejs.org/) installed.

```sh
 $ npm install @grepp/detect-gpu
```

## Usage

```ts
import { getGPUTier } from '@grepp/detect-gpu';

(async () => {
  const gpuTier = await getGPUTier();

  // Example output:
  // {
  //   "tier": 1,
  //   "isMobile": false,
  //   "type": "BENCHMARK",
  //   "fps": 21,
  //   "gpu": "intel iris graphics 6100"
  // }
})();
```

`detect-gpu` uses rendering benchmark scores (framerate, normalized by resolution) in order to determine what tier should be assigned to the user's GPU. If no `WebGLContext` can be created, the GPU is blocklisted or the GPU has reported to render on less than `15 fps` `tier: 0` is assigned. One should provide a fallback to a non-WebGL experience.

Based on the reported `fps` the GPU is then classified into either `tier: 1 (>= 15 fps)`, `tier: 2 (>= 30 fps)` or `tier: 3 (>= 60 fps)`. The higher the tier the more graphically intensive workload you can offer to the user.

## API

```ts
getGPUTier({
  /**
   * URL of directory where benchmark data is hosted.
   *
   * @default https://@grepp/unpkg.com/detect-gpu@{version}/dist/benchmarks
   */
  benchmarksURL?: string;
  /**
   * Optionally pass in a WebGL context to avoid creating a temporary one
   * internally.
   */
  glContext?: WebGLRenderingContext | WebGL2RenderingContext;
  /**
   * Whether to fail if the system performance is low or if no hardware GPU is
   * available.
   *
   * @default false
   */
  failIfMajorPerformanceCaveat?: boolean;
  /**
   * Framerate per tier for mobile devices.
   *
   * @defaultValue [0, 15, 30, 60]
   */
  mobileTiers?: number[];
  /**
   * Framerate per tier for desktop devices.
   *
   * @defaultValue [0, 15, 30, 60]
   */
  desktopTiers?: number[];
  /**
   * Optionally override specific parameters. Used mainly for testing.
   */
  override?: {
    renderer?: string;
    /**
     * Override whether device is an iPad.
     */
    isIpad?: boolean;
    /**
     * Override whether device is a mobile device.
     */
    isMobile?: boolean;
    /**
     * Override device screen size.
     */
    screenSize?: { width: number; height: number };
    /**
     * Override how benchmark data is loaded
     */
    loadBenchmarks?: (file: string) => Promise<ModelEntry[]>;
  };
})
```

## Support

Special care has been taken to make sure all browsers that support `WebGL` are also supported by `detect-gpu` including `IE 11`.

## Changelog

[Changelog](CHANGELOG.md)

## Licence

My work is released under the [MIT license](https://raw.githubusercontent.com/pmndrs/detect-gpu/master/LICENSE).

`detect-gpu` uses both mobile and desktop benchmarking scores from [https://gfxbench.com](https://gfxbench.com).
