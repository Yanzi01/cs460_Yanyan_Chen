<!-- Copilot / AI agent guidance for this repository -->
# Copilot instructions — cs460 demo site

Purpose
- This repository is a small collection of WebGL / three.js demo pages (root HTML demos and subfolders such as `inkman/`). Each demo is a self-contained static HTML file that uses modern ES modules via `importmap` + `es-module-shims` and loads assets (GLTF/GLB, textures) at runtime.

Big picture (what to know quickly)
- The site is static — there is no build system or server-side code. Primary pages: `index.html`, `ar.html`, `webgpu.html`, and `inkman/index.html`.
- Each top-level demo uses an inline `<script type="module">` entrypoint and an `importmap` that aliases `three` and `three/addons/` to CDN modules (see `index.html` and `inkman/index.html`).
- Assets: some demos load local `.glb` files (e.g. `jellycat_cup.glb`, `orginal_jellycat_cup.glb` referenced from `index.html`). Others fetch textures/models from three.js CDN.

Developer workflows (how to run & debug)
- Run a local static server (import maps and module loading typically require HTTP). Example quick commands:
  - `python -m http.server 8000` (serves current directory)
  - `npx http-server -c-1 . -p 8000`
  - Use VS Code `Live Server` extension and open `http://localhost:5500/` as appropriate.
- Open the page in a modern browser (Chrome/Edge/Firefox). Use devtools console and Network tab to check for missing `.glb` or other asset 404s.
- For WebGPU/AR demos, ensure you use the correct browser flags or secure context if required (these demos are simple static pages but may rely on advanced APIs).

Project-specific coding patterns and conventions
- Module loading: every demo includes an `importmap` mapping `three` and `three/addons/` to the `unpkg` CDN and uses `es-module-shims`. Keep this pattern when adding demos to avoid bundling.
  - Example from `index.html`:
    ```html
    <script async src="https://unpkg.com/es-module-shims@.../dist/es-module-shims.js"></script>
    <script type="importmap"> { "imports": { "three": "https://unpkg.com/three@latest/build/three.module.js", "three/addons/": "https://unpkg.com/three@latest/examples/jsm/" } } </script>
    ```
- Global demo state: some pages attach state to `window` (e.g. `window.SCENE` in `index.html`) to expose toggles used by UI libraries like Tweakpane. If modifying code, search for `window.SCENE` or `SCENE` to find shared state.
- UI & tooling: these demos commonly use `Stats`, `Tweakpane` / `lil-gui`, and three.js helpers. Keep references to those CDNs consistent.
- Asset naming: the code expects specific filenames (e.g. `jellycat_cup.glb`). Ensure files live in the served directory root or update paths in HTML.

Integration points & external dependencies
- Heavily CDN-driven: `three` and official `three` examples are loaded from `unpkg`/`threejs.org`. Textures and some GLTF assets are loaded either locally or from `threejs.org` examples.
- If you add large assets, prefer to commit them to the repo near the HTML that references them, or update paths to a stable URL.

Editing & adding demos (how to contribute)
- Add a new demo HTML file (e.g. `my-demo.html`) following the pattern in `index.html` or `inkman/index.html`: include `es-module-shims`, an `importmap`, then a `type="module"` script.
- When referencing `three/addons` modules, import like:
  `import { GLTFLoader } from 'three/addons/loaders/GLTFLoader.js';`
- Check network/console for missing assets; update relative paths if you place assets in subfolders.

What I did not find / assumptions
- No build/test scripts present — repository appears to be intended for direct static hosting.
- Several `.blend1` files are present (Blender backups) but I did not find the `.glb` files referenced by `index.html` inside the repo; ensure the `.glb` files exist or are added.

If you want me to (next steps)
- Add a `README.md` documenting demo purposes and how to run the static server.
- Add a small `serve` script in `package.json` with `http-server` or GitHub Pages workflow.
- Search and add missing `.glb` assets found in HTML and either commit them or update the code to point to existing assets.

Questions for you
- Should `.glb` assets be checked into this repo or hosted externally? 
- Do you want a `README.md` and a simple `package.json`+`npm start` convenience script added now?

— End of instructions —
