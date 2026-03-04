---
title: "Entropy Projection Framework"
version: "0.1.0-draft"
status: "pre-RFC"
---

# Entropy Projection Framework

<!--raw-typst
#let badge(label, fill-color) = box(
  fill: fill-color,
  radius: 3pt,
  inset: (x: 7pt, y: 4pt),
  text(size: 7.5pt, weight: "bold", fill: white, label)
)
#align(right)[
  #badge("PRE-RFC", rgb("#5b3cc4"))
  #h(4pt)
  #badge("v0.1.0-draft", rgb("#888888"))
]
#v(4pt)
-->

<!--typst-begin-exclude-->
> **Status:** Pre-RFC Draft · v0.1.0
> A deterministic entropy → cross-domain design primitive projection engine.
<!--typst-end-exclude-->

A deterministic framework for projecting arbitrary entropy into a stable, cross-domain
field of design primitives — rendering consistently across typography, layout, CSS,
SVG, and any design surface.

Crypto is one entropy source. It is not a requirement.

---

## Problem Statement

Agent-generated and deterministically-authored artifacts lack a unified identity
surface. Cryptographic provenance exists in metadata and audit logs — but not in
the artifact itself. Meanwhile, design systems are static: they don't vary
deterministically with content or authorship.

This framework bridges both gaps:

- **Human-visible:** a consistent aesthetic fingerprint embedded in the artifact
- **Machine-verifiable:** derived deterministically from a canonical entropy source
- **Cross-medium:** the same seed produces coherent variation across all renderers

---

## Core Invariants

These five promises must hold across all conforming implementations:

1. **Determinism** — identical entropy always produces an identical primitive field
2. **Domain separation** — `kernel.scalar("density")` and `kernel.scalar("tension")`
   draw from independent, non-overlapping regions of the expanded keystream
3. **Platform stability** — all outputs are clamped and quantized to defined precision
   boundaries *before* reaching any renderer
4. **Entropy agnosticism** — the kernel accepts any byte sequence; cryptographic
   provenance is optional
5. **Renderer independence** — the primitive field is defined without reference to
   any specific rendering target

<!--raw-typst
#v(8pt)
#block(
  width: 100%,
  fill: rgb("#edf6fb"),
  stroke: (left: 3.5pt + rgb("#239dad")),
  radius: (right: 4pt),
  inset: (left: 14pt, right: 12pt, top: 10pt, bottom: 10pt),
)[
  #text(weight: "bold", fill: rgb("#239dad"))[Stability Contract] \
  #v(5pt)
  The canonical normalization step is the stability boundary of the framework.
  Everything _above_ it is raw entropy. Everything _below_ it is a guarantee. \
  #v(3pt)
  #text(style: "italic", fill: rgb("#555555"))[Renderer adapters must never consume raw entropy — only normalized primitives.]
]
#v(8pt)
-->

---

## Architecture

The framework is composed of eight ordered layers. Each layer has a single
responsibility and a defined interface to the layer below it.

<!--typst-begin-exclude-->
```
Entropy Source          # Arbitrary bytes — hash, signature, ID, seed, timestamp
        ↓
Entropy Expansion       # HKDF-SHA256 stretch with domain-separated info labels
        ↓
Canonical Normalization # Map to [0,1] scalars, bitfields, angles — clamped
        ↓
Primitive Field         # Abstract design vocabulary: density, tension, phase…
        ↓
Constraint Mapping      # Per-renderer precision boundaries and quantization
        ↓
Renderer Adapters       # Translate primitives → font axes, CSS vars, SVG, etc.
        ↓
Surface Projection      # Apply to medium: text, UI, document, diff, dashboard
        ↓
Cross-Medium Identity   # Coherent aesthetic fingerprint — reproducible
```
<!--typst-end-exclude-->

<!--raw-typst
#let pipe-step(name, annotation, accent) = block(
  width: 100%,
  fill: accent.lighten(87%),
  stroke: (left: 2.5pt + accent),
  radius: (right: 3pt),
  inset: (x: 12pt, y: 8pt),
)[
  #text(weight: "bold", size: 9.5pt)[#name]
  #text(fill: luma(120), size: 8.5pt, style: "italic")[ — #annotation]
]
#let divider = pad(left: 20pt)[#text(fill: luma(190), size: 11pt)[↓]]
#v(6pt)
#pipe-step("Entropy Source",          "hash · signature · ID · seed · timestamp",          rgb("#7b4fbf"))
#divider
#pipe-step("Entropy Expansion",       "HKDF-SHA256 · domain-separated info labels",        rgb("#2d6abf"))
#divider
#pipe-step("Canonical Normalization", "[0,1] scalars · bitfields · angles · clamped",      rgb("#1a8f7a"))
#divider
#pipe-step("Primitive Field",         "density · tension · phase · grain · curvature · …", rgb("#239dad"))
#divider
#pipe-step("Constraint Mapping",      "per-renderer quantization · precision bounds",       rgb("#7a7a00"))
#divider
#pipe-step("Renderer Adapters",       "CSS vars · OpenType axes · SVG attrs · layout",      rgb("#cc6600"))
#divider
#pipe-step("Surface Projection",      "text · UI · document · diff · dashboard",            rgb("#cc3300"))
#divider
#pipe-step("Cross-Medium Identity",   "coherent · reproducible · human-glanceable",         rgb("#880033"))
#v(6pt)
-->

---

## Kernel API

The kernel is the sole abstraction boundary. All other components are adapters.
The interface is intentionally minimal.

```typescript
interface EntropyKernel {
  /** Deterministic scalar in [0, 1] */
  scalar(key: string): number

  /** Deterministic integer in [0, 2^n - 1] */
  bits(key: string, n: number): number

  /** Deterministic angle in [0, 2π] */
  angle(key: string): number

  /** Deterministic 2D point in unit square */
  vec2(key: string): [number, number]

  /** Deterministic selection from a finite ordered set */
  pick<T>(key: string, options: readonly T[]): T

  /** Deterministic boolean */
  flag(key: string): boolean
}

/** Primary constructor */
function createKernel(entropy: Uint8Array | string): EntropyKernel
```

### Domain Separation

Each `key` string is passed as the HKDF `info` parameter during expansion.
This guarantees any two distinct keys produce statistically independent output
streams regardless of their string relationship. The primitive vocabulary can
grow freely without risk of cross-field correlation.

### String Entropy

When `entropy` is a string, it is UTF-8 encoded and used as HKDF input keying
material with an empty salt. Non-cryptographic seeds like `"acme-corp"` or
`user.id` are valid first-class inputs. Callers requiring cryptographic strength
are responsible for providing high-entropy bytes upstream.

---

## Primitive Vocabulary

Abstract names that carry no rendering assumptions. Adapters are solely responsible
for interpretation. Names are lowercase kebab-case. The vocabulary is versioned
alongside the kernel spec.

| Primitive | Type | Range | Semantic Intent |
|-----------|------|-------|----------------|
| `density` | scalar | [0, 1] | Mass and weight of visual elements |
| `expansion` | scalar | [0, 1] | Lateral spread and spatial openness |
| `curvature` | scalar | [0, 1] | Roundness and organic quality |
| `contrast` | scalar | [0, 1] | Differential between figure and ground |
| `grain` | scalar | [0, 1] | Fine-grained micro-variation and texture |
| `tension` | scalar | [0, 1] | Structural tightness and compression |
| `symmetry` | scalar | [0, 1] | Axial balance and alignment bias |
| `phase` | angle | [0, 2π] | Harmonic offset and rotational position |
| `feature-a` | flag | bool | Binary stylistic toggle — slot A |
| `feature-b` | flag | bool | Binary stylistic toggle — slot B |
| `feature-c` | flag | bool | Binary stylistic toggle — slot C |

Adapters must degrade gracefully when a primitive is absent or when the vocabulary
version predates a primitive's introduction.

---

## Adapter Contract

An adapter is a **pure function** from primitive field to renderer-specific output:

```typescript
type Adapter<T> = (field: PrimitiveField) => T
```

`PrimitiveField` is the stable, normalized output of the kernel after constraint
mapping. Adapters must not access raw entropy, produce side effects, or be
non-deterministic given the same field.

### Example: CSS Custom Properties

```typescript
const cssAdapter: Adapter<Record<string, string>> = (field) => ({
  "--epf-font-weight":    lerp(300, 700, field.density).toFixed(0),
  "--epf-font-width":     lerp(85, 115, field.expansion).toFixed(1) + "%",
  "--epf-border-radius":  lerp(0, 16, field.curvature).toFixed(1) + "px",
  "--epf-letter-spacing": lerp(-0.02, 0.08, field.grain).toFixed(3) + "em",
  "--epf-font-style":     field["feature-a"] ? "italic" : "normal",
})
```

### Example: OpenType Variable Font

```typescript
const opentypeAdapter: Adapter<OpenTypeSettings> = (field) => ({
  variationSettings: {
    wght: lerp(200, 800, field.density),
    wdth: lerp(75, 125, field.expansion),
  },
  featureSettings: {
    "ss01": field["feature-a"],
    "ss02": field["feature-b"],
    "ss03": field["feature-c"],
    "calt": field.tension > 0.5,
    "zero": field.grain > 0.5,
  },
})
```

---

## Test Vector

The following input/output pair is the canonical cross-platform conformance test.
Any conforming implementation must reproduce these values within stated tolerance.

**Input:** UTF-8 string `"epf-test-v0"`

| Key | Method | Expected Output |
|-----|--------|----------------|
| `"density"` | `scalar` | TBD ±0.0001 |
| `"phase"` | `angle` | TBD ±0.0001 |
| `"feature-a"` | `flag` | TBD |
| `"feature-b"` | `flag` | TBD |
| `"grain"` | `scalar` | TBD ±0.0001 |

<!--raw-typst
#v(6pt)
#block(
  width: 100%,
  fill: rgb("#fff8e6"),
  stroke: (left: 3pt + rgb("#cc8800")),
  radius: (right: 4pt),
  inset: (left: 14pt, right: 12pt, top: 10pt, bottom: 10pt),
)[
  #text(weight: "bold", fill: rgb("#996600"))[Implementation Note] \
  #v(5pt)
  Test vector values are populated from the first verified execution of `@epf/kernel`
  against the reference HKDF-SHA256 implementation. Until then, the structure and
  tolerance bounds are normative; the values are not. The test vector is immutable once published.
]
#v(6pt)
-->

---

## Non-Goals (v0)

The following are explicitly out of scope for the initial release:

- **Color systems** — color is a high-stakes perceptual primitive requiring its own
  gamut-aware model. Deferred to a dedicated `@epf/adapter-color` package.
- **Animation and temporal variation** — entropy maps to a static field; time-varying
  projection is a separate concern.
- **Per-glyph variation** — fine-grained per-character modification requires font
  forking at the OpenType source level. Deferred.
- **Cryptographic verification** — this framework projects entropy; it does not verify
  signatures. Signature verification is the caller's responsibility upstream.
- **Perceptual uniformity** — the framework makes no guarantee that two distant seeds
  produce visually distinguishable output. That is an application-layer concern.

---

## Roadmap

| Package | Description |
|---------|-------------|
| `@epf/kernel` | Reference TypeScript implementation |
| `@epf/adapter-css` | CSS custom properties adapter |
| `@epf/adapter-opentype` | Variable font / OpenType adapter |
| `@epf/adapter-svg` | SVG path and geometry adapter |
| `typst-epf` | Typst package for document-level entropy projection |

Following the reference implementation: lock the test vector, publish v0.1.0,
open the RFC process for primitive vocabulary versioning, and begin perceptual
distance research for seed distinguishability guarantees.

---

*Entropy Projection Framework · Pre-RFC Draft · © contributors*
