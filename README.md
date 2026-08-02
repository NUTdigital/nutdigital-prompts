# NUTdigital · Automotive Blueprint Line Drawing

![schema](https://img.shields.io/badge/schema-1.0-blue)
![license](https://img.shields.io/badge/license-WTFPL-black)
![status](https://img.shields.io/badge/status-experimental-orange)

```
█▄ █ █ █ ▀█▀
█ ▀█ █▄█  █
```

> did you know: this JSON has stronger overlay-fidelity requirements than most factory homologation docs. no scaling. no warping. not even a little.

```
┌────────────────────────────────────────┐
│  GEN            2nd generation         │
│  BUILD          handbuilt in england   │
│  CHASSIS        BGT3-02/020            │
│  ENGINE         4.0L V8 BITURBO        │
│  DRIVETRAIN     RWD                    │
│  CLASS          GT3                    │
│  DIVISION       msport                 │
│  UNITS PRODUCED limited / homologated  │
│  REF            Continental GT3        │
└────────────────────────────────────────┘
```

> Image-to-image prompt spec: photo → 1:1 technical blueprint line drawing (b/w, zero-drift overlay)

```
in:  vehicle photo (any angle, static or panned)
out: pure black/white line drawing, 1:1 overlay, motion-suggested wheels
```

## TL;DR

```json
{
  "task": "convert source image to technical blueprint line drawing",
  "fidelity": { "overlay": "1:1 pixel-perfect match with source at 100% scale" },
  "palette": { "lines": "#000000", "background": "#ffffff", "fills": "none except car_shadow" }
}
```

Full spec → [`prompts/automotive/NUTdigital-automotive-blueprint-line-drawing.json`](prompts/automotive/NUTdigital-automotive-blueprint-line-drawing.json)

## Examples

| # | Input | Output | Notes |
|---|-------|--------|-------|
| 1 | ![in1](examples/automotive/01-input.jpg) | ![out1](examples/automotive/01-output.png) | 3/4 front, high-speed pan |
| 2 | ![in2](examples/automotive/02-input.jpg) | ![out2](examples/automotive/02-output.png) | side profile, static |
| 3 | ![in3](examples/automotive/03-input.jpg) | ![out3](examples/automotive/03-output.png) | rear 3/4, cornering |

## Spec highlights

```
fidelity      → preserve camera angle / foreshortening / vanishing points
              → prohibit scaling, warping, cropping, flipping, rotating
palette       → #000000 on #ffffff, zero fills except shadow
wheels        → hub_zone   : crisp solid circle, inner 30% radius
              → mid_zone   : 6-8 radial stubs (30-70%), NOT full spokes
              → outer_zone : tire outline only (70-100%)
              → motion_trail : 3-5 arcs, 0.5pt, max 2mm extension
shadow        → solid #000000 silhouette, gapless — only permitted fill
background    → none. everything above contact plane = white, no exceptions
global_prohibit → color · text/logos · gradients · fills (except shadow)
```

Every constraint above has a matching `render_check` baked into the JSON — the model is expected to self-verify against these before returning output (e.g. `"wheels read as spinning — not parked"`).

## Usage

```bash
python generate.py \
  --prompt prompts/automotive/NUTdigital-automotive-blueprint-line-drawing.json \
  --input  examples/automotive/01-input.jpg \
  --output examples/automotive/01-output.png
```

Model-agnostic. Written for structured JSON prompts on image-to-image models (tested against Nano-Banana-class models); should port to any model accepting structured JSON transform prompts.

## Versioning

The spec carries its own `schema_version` field (currently `1.0`), independent of repo tags. Bump it whenever the prompt logic changes, so any given output image can be traced back to the exact spec that generated it.

```
patch (1.0 → 1.1)  small fix, same core behavior
major (1.0 → 2.0)  behavior-changing rewrite (e.g. new wheel logic)
```

See [`CHANGELOG.md`](CHANGELOG.md) for history.

## Structure

```
prompts/
└── automotive/
    └── NUTdigital-automotive-blueprint-line-drawing.json
CHANGELOG.md
examples/
└── automotive/
    ├── 01-input.jpg   01-output.png
    ├── 02-input.jpg   02-output.png
    └── 03-input.jpg   03-output.png
```

## License

WTFPL — see [LICENSE](LICENSE).
