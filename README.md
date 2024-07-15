# vite-vue-plugin-custom-export

A custom Vite plugin that transforms the output of your build to correctly handle both default and named exports, removing unnecessary patterns and preserving the desired directory structure. This plugin is particularly useful for component libraries built with Vue 3 and TypeScript.

## Features

- Transforms export patterns to handle both default and named exports.
- Preserves directory structure during the build process.
- Removes unnecessary patterns such as `Object.freeze` and `Object.defineProperty`.

## Installation

```bash
npm install vite-vue-plugin-custom-export --save-dev

Sure, here are the remaining instructions converted to markdown format:

---

### 1. Update `vite.config.ts`

Include the plugin in your Vite configuration file:

```ts
import vue from '@vitejs/plugin-vue';
import { defineConfig } from 'vite';
import { resolve } from 'path';
import customExportPlugin from 'vite-vue-plugin-custom-export';

export default defineConfig({
  plugins: [
    vue(),
    customExportPlugin(),
  ],
  build: {
    lib: {
      entry: resolve(__dirname, 'src/index.ts'),
      name: 'MyComponentLibrary',
      formats: ['es', 'cjs'],
      fileName: (format) => `my-component-library.${format}.js`,
    },
    rollupOptions: {
      external: ['vue'],
      output: {
        globals: {
          vue: 'Vue',
        },
        entryFileNames: '[name].js',
        chunkFileNames: '[name].js',
        assetFileNames: 'assets/[name].[ext]',
        manualChunks(id) {
          if (id.includes('src/components/')) {
            const parts = id.split('/');
            const name = parts.slice(parts.indexOf('components') + 1, -1).join('/');
            return name ? `components/${name}` : undefined;
          }
          return undefined;
        },
      },
    },
  },
  optimizeDeps: {
    include: ['vue'],
  },
});
```

### 2. Ensure Correct Exports in `index.ts`

Ensure that each component exports default and named exports correctly. For example:

**Example: `src/components/Alert/index.ts`**:
```ts
import Alert from './Alert.vue';

export { Alert };
export default Alert;
```

### 3. Build the Library

Run the build command to generate the library:

```bash
npm run build
```

### Example

Here's an example of the transformation:

**Original Output:**
```js
import { _ as e } from "./Alertue_vue_type_script_setup_true_lang.js";
const o = /* @__PURE__ */ Object.freeze(/* @__PURE__ */ Object.defineProperty({
  __proto__: null,
  Alert: e,
  default: e
}, Symbol.toStringTag, { value: "Module" }));
export {
  o as i
};
```

**Transformed Output:**
```js
import Alert from './Alert.js';

export { Alert };
export default Alert;
```
