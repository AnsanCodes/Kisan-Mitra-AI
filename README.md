# KisanMitraAI

**AI-Powered Crop Disease Identification** — a science-exhibition prototype for
Genesis 2026.

KisanMitraAI will identify crop diseases from photographs of leaves using an image
classification model that runs **entirely inside the browser**. No backend, no
account, no upload: the classifier covers the full 38-class colour PlantVillage
dataset across 14 crops, and the interface defaults to the **tomato** crop.

> **Prototype status.** This repository currently contains the project
> foundation only. The camera, the preprocessing pipeline, the ONNX model and
> the disease reference content are **not implemented**, and the app performs
> no image analysis. The homepage states this explicitly, and there is
> deliberately no mock data anywhere in the codebase.

## Running it

```bash
npm install
npm run dev      # http://localhost:3000
```

| Command               | Purpose                                    |
| --------------------- | ------------------------------------------ |
| `npm run dev`         | Development server with hot reload.        |
| `npm run build`       | Production build.                          |
| `npm run start`       | Serve the production build.                |
| `npm run lint`        | ESLint (`eslint-config-next`).             |
| `npm run typecheck`   | `tsc --noEmit`.                            |
| `npm run check`       | Typecheck + lint.                          |

Node 20+ is required. There is nothing to configure — every environment
variable is optional. Copy `.env.example` to `.env.local` only if you need to
override a model path or threshold.

## Architecture

```
src/
  app/                 Routes. page.tsx only composes sections; globals.css holds
                       the design tokens (colour, type, motion) and base rules.
  components/
    brand/             The KisanMitraAI mark, drawn as inline SVG. No image assets.
    layout/            Site header and footer, shared by every route.
    home/              One component per homepage section.
    ui/                Small reusable primitives: ButtonLink, SectionLabel, StatusBadge.
  types/               Domain types only — Crop, Disease, Prediction, PredictionResult.
  data/                Content. crops.ts is factual; diseases.ts is intentionally empty.
  lib/
    ai/                Model configuration: paths, input tensor spec, thresholds.
    camera/            Planned. See its README — no code yet, on purpose.
    disease/           Read-only access layer over src/data/diseases.ts.
    image/             Capture constraints shared by the camera and upload paths.
  hooks/               Planned. See its README for the hooks that will land here.
public/models/         Where the .onnx file and its label JSON will live.
```

Three rules keep the codebase honest as it grows:

1. **The UI reads from `lib`, never from `data` directly.** Components import
   `lib/disease/disease-repository`, not the raw array, so the storage format can
   change without touching the interface.
2. **Types describe shapes, not content.** `src/types` has no data in it, and
   `src/data/diseases.ts` has no invented plant pathology.
3. **One colour means one thing.** Amber is reserved for functionality that is
   planned but not working, so the interface can never overstate what the app
   does. See `StatusBadge`.

## Adding the model later

1. `npm install onnxruntime-web`
2. Copy the weights into `public/models/` (see the README there).
3. Confirm the input tensor spec in `src/lib/ai/model-config.ts` — size, layout
   and the per-channel `mean`/`std` — matches how the model was trained. A
   mismatch degrades accuracy silently.
4. Set `status: "ready"` in the same commit that adds the file.
5. Implement `src/lib/ai/session.ts` and wire it to `src/hooks/useModelRunner()`.

`CROSS_ORIGIN_ISOLATION=true` in the environment turns on the COOP/COEP headers
that multi-threaded WASM needs. Check `window.crossOriginIsolated` in the
console before relying on threads.

## Licence and data

The interface uses no paid or third-party assets; fonts are self-hosted by
`next/font`. Disease guidance must come from cited plant-pathology sources
before the exhibition build — record each one in the `references` field of the
`Disease` type and set `contentStatus: "verified"`.
# Kisan-Mitra-AI
# Kisan-Mitra-AI
