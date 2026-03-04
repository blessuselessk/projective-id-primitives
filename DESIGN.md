---
title: "EPF Kernel"
version: "0.1.0-draft"
status: "pre-RFC"
---

# EPF Kernel

<!--raw-typst
// ── Page & typography setup ───────────────────────────────────────────────────
#set page(paper: "a4", margin: (top: 2.5cm, bottom: 2.5cm, left: 3cm, right: 3cm))
#set text(font: "Linux Libertine", size: 11pt, lang: "en")
#set heading(numbering: "1.1.")
#set par(justify: true, leading: 0.65em)
#show link: underline
#show raw.where(block: true): it => block(
  fill: luma(245),
  inset: (x: 1em, y: 0.8em),
  radius: 4pt,
  width: 100%,
  it
)
#show quote.where(block: true): it => pad(left: 1em, block(
  stroke: (left: 3pt + luma(180)),
  inset: (left: 1em, y: 0.4em),
  it.body
))

// ── Status badges ─────────────────────────────────────────────────────────────
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
> The kernel of the Entropy Projection Framework — a deterministic entropy → cross-domain design primitive engine.
>
> **Rendering this document**
>
> This file is authored in CommonMark Markdown and renders via
> [Typst](https://typst.app) +
> [`cmarker`](https://typst.app/universe/package/cmarker) (v0.1.8+).
>
> ```bash
> brew install typst          # macOS
> cargo install typst-cli     # Linux/Windows
> typst compile render.typ
> ```
>
> **render.typ** (place alongside this file):
> ```typst
> #import "@preview/cmarker:0.1.8"
> #cmarker.render(read("DESIGN.md"), h1-level: 1, smart-punctuation: true, raw-typst: true)
> ```
>
> `<!--raw-typst-->` blocks inject native Typst (page setup, styled callouts,
> the pipeline diagram). `<!--typst-begin-exclude-->` sections are visible here
> but stripped from the PDF.
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
── kernel ───────────────────────────────────────────────────────
Entropy Source          # Arbitrary bytes — hash, signature, ID, seed, timestamp
        ↓
Entropy Expansion       # HKDF-SHA256 stretch with domain-separated info labels
        ↓
Canonical Normalization # Map to [0,1] scalars, bitfields, angles — clamped
        ↓
Primitive Field         # Abstract design vocabulary: density, tension, phase…
── adapter territory ────────────────────────────────────────────
        ↓
Constraint Mapping      # Per-renderer precision boundaries and quantization
        ↓
Renderer Adapters       # Translate primitives → font axes, CSS vars, SVG, etc.
        ↓
Surface Projection      # Apply to medium: text, UI, document, diff, dashboard
        ↓
Cross-Medium Identity   # Coherent aesthetic fingerprint — reproducible
─────────────────────────────────────────────────────────────────
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
#let boundary(label) = block(
  width: 100%,
  inset: (x: 6pt, y: 5pt),
)[
  #line(length: 100%, stroke: (paint: luma(180), dash: "dashed"))
  #text(size: 7.5pt, fill: luma(150), style: "italic")[#label]
]
#v(6pt)
#boundary("── kernel ──────────────────────────────────────────────────")
#pipe-step("Entropy Source",          "hash · signature · ID · seed · timestamp",          rgb("#7b4fbf"))
#divider
#pipe-step("Entropy Expansion",       "HKDF-SHA256 · domain-separated info labels",        rgb("#2d6abf"))
#divider
#pipe-step("Canonical Normalization", "[0,1] scalars · bitfields · angles · clamped",      rgb("#1a8f7a"))
#divider
#pipe-step("Primitive Field",         "density · tension · phase · grain · curvature · …", rgb("#239dad"))
#boundary("── adapter territory ────────────────────────────────────────")
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

  /** Deterministic gaussian scalar — mean 0, std 1, clamped to [-1, 1] by default */
  normal(key: string, mean?: number, std?: number): number

  /** Deterministic permutation of a finite ordered set */
  shuffle<T>(key: string, items: readonly T[]): T[]

  /** Deterministic k-of-n selection without replacement */
  sample<T>(key: string, items: readonly T[], k: number): T[]

  /** Deterministic array of n independent scalars in [0, 1] */
  sequence(key: string, n: number): number[]

  /** Deterministic partition into n parts summing to 1 */
  partition(key: string, n: number): number[]

  /** Deterministic weighted selection from a finite ordered set */
  weighted<T>(key: string, options: readonly { value: T; weight: number }[]): T
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

### PrimitiveField

The concrete typed output of the kernel after canonical normalization. This is
the stable value passed to every adapter and `self.*` derivation.

```typescript
interface PrimitiveField {
  density:   number   // scalar [0, 1]
  expansion: number   // scalar [0, 1]
  curvature: number   // scalar [0, 1]
  contrast:  number   // scalar [0, 1]
  grain:     number   // scalar [0, 1]
  tension:   number   // scalar [0, 1]
  symmetry:  number   // scalar [0, 1]
  phase:     number   // angle  [0, 2π]
  [key: `feature-${string}`]: boolean   // all feature flags, named or sequenced
  [key: `normal-${string}`]:  number    // gaussian scalar, clamped [-1, 1]
  [key: `order-${string}`]:   unknown[] // shuffled or sampled sequence
  [key: `seq-${string}`]:     number[]  // n independent scalars in [0, 1]
  [key: `parts-${string}`]:   number[]  // n parts summing to 1
}
```

`PrimitiveField` is closed over raw entropy — it contains only normalized,
clamped values. No kernel method is accessible from within it.

### scalar · bits · angle · vec2 · pick · flag

The six primitive draw methods. Each produces a single deterministic value from
a keyed region of the expanded keystream.

| Method | Return | Range | Notes |
|--------|--------|-------|-------|
| `scalar(key)` | `number` | [0, 1] | Uniform draw — workhorse of the vocabulary |
| `bits(key, n)` | `number` | [0, 2ⁿ−1] | Raw integer — implementation escape hatch |
| `angle(key)` | `number` | [0, 2π] | Uniform on the circle |
| `vec2(key)` | `[number, number]` | unit square | Two independent scalar draws |
| `pick(key, options)` | `T` | — | Uniform categorical selection |
| `flag(key)` | `boolean` | — | Single bit draw |

`bits` is the only method that returns raw entropy without semantic normalization.
It exists for implementors who need access below the primitive layer. Adapters
that find themselves reaching for `bits` should consider whether a new named
method is the correct abstraction instead.

### normal

`normal(key, mean?, std?)` produces a gaussian-distributed scalar. The default
distribution is mean `0`, std `1`, clamped to `[-1, 1]`. Callers may supply
`mean` and `std` to shift and scale the distribution before clamping.

This is not derivable as a `self.*` primitive — gaussian output requires a
different draw from the expanded keystream, not a transformation of an existing
uniform scalar. A uniform-to-gaussian conversion (Box-Muller, etc.) applied to
a field value would distort the distribution and break domain separation.

```typescript
kernel.normal("offset")           // N(0, 1) clamped to [-1, 1]
kernel.normal("weight", 0, 0.5)   // N(0, 0.5) — tighter distribution
kernel.normal("shift", 0.2, 0.3)  // N(0.2, 0.3) — shifted center
```

Stored on `PrimitiveField` under `normal-${key}`.

### shuffle

`shuffle(key, items)` produces a deterministic permutation of the supplied
array. The input array is not mutated. The same `key` and `items` always
produce the same ordering.

This is not derivable as a `self.*` primitive — generating a permutation
requires as many independent draws as there are elements. Deriving it from
existing field values would require manual sub-key schemes and would not be
domain-separated by the spec.

```typescript
kernel.shuffle("palette", ["slate", "sand", "moss", "ember"])
// e.g. → ["moss", "ember", "slate", "sand"]  (deterministic for this entropy)
```

Stored on `PrimitiveField` under `order-${key}`. Element type is `unknown[]`
at the field level; adapters are responsible for casting to their expected type.

### sample

`sample(key, items, k)` produces a deterministic k-of-n selection without
replacement. The result is a sub-sequence of `items` of length `k`, drawn in a
single keyed operation.

This is distinct from `shuffle` + slice: `shuffle` draws entropy proportional to
`n` regardless of `k`. For large `items` with small `k`, that wastes entropy and
is semantically misleading — the intent is selection, not ordering.

```typescript
kernel.sample("palette", ["slate", "sand", "moss", "ember", "rust"], 3)
// e.g. → ["moss", "rust", "slate"]  (deterministic, k draws, no repeats)
```

Stored on `PrimitiveField` under `order-${key}`, sharing the same index type as
`shuffle`. The distinction between a shuffled and sampled sequence is not
preserved at the field level — both are ordered arrays of the supplied element type.

### sequence

`sequence(key, n)` produces `n` independent scalars in `[0, 1]`, each
domain-separated by their position within the keyed expansion.

The canonical use case is any adapter that needs multiple independent draws under
a shared semantic label — `n` column widths, `n` opacity stops, `n` offsets —
without inventing a sub-key scheme. Callers who write `scalar("x-0")`,
`scalar("x-1")`, `scalar("x-2")` are approximating this; the approximation is
fragile because sub-key naming is not part of the spec.

```typescript
kernel.sequence("column-widths", 4)
// e.g. → [0.61, 0.28, 0.74, 0.43]  (4 independent draws, domain-separated)
```

Stored on `PrimitiveField` under `seq-${key}`. Each element is a scalar in
`[0, 1]`; no element is derived from another.

### partition

`partition(key, n)` produces `n` non-negative values summing exactly to `1`.
The distribution is uniform over the n-simplex (equivalent to a symmetric
Dirichlet draw), implemented as n-1 independent draws + normalization.

This is not derivable from `sequence` without reintroducing the normalization
step at the adapter layer, which would require the adapter to reason about
entropy rather than primitives — a layering violation. The "sums to 1" guarantee
is a contract, not a convenience.

```typescript
kernel.partition("columns", 3)
// e.g. → [0.412, 0.231, 0.357]  (sum = 1.0, deterministic)
```

Stored on `PrimitiveField` under `parts-${key}`. Adapters may scale the parts
by a concrete total (e.g. container width in px) without any further entropy
reasoning.

### weighted

`weighted(key, options)` produces a deterministic selection from a finite set
where each option carries an explicit relative weight. Internally this is a
single scalar draw mapped against the cumulative weight distribution.

`pick` is uniform categorical selection — `weighted` is the non-uniform
generalization. Requiring callers to expand weights manually (repeating entries
to approximate a distribution) is a lossy encoding that loses precision and
obscures intent.

```typescript
kernel.weighted("style", [
  { value: "regular",   weight: 6 },
  { value: "medium",    weight: 3 },
  { value: "bold",      weight: 1 },
])
// e.g. → "regular"  (60% probability mass, deterministic for this entropy)
```

The result is a single selected value. It is not stored on `PrimitiveField`
under a named index — the output type is adapter-defined and does not fit a
uniform field slot. Adapters that use `weighted` consume its output directly
without field indirection.

### Feature Flag Naming

Feature flags produced by `flag(key)` follow a dual-form convention based on
semantic maturity:

```
feature-id  ::= "feature-" ( integer | kebab-slug )

integer     ::= [1-9][0-9]*        # sequenced, unspecified — planned/unknown
kebab-slug  ::= [a-z][a-z0-9-]*   # named, semantic — implemented/understood
```

**`feature-{n}`** (e.g. `feature-1`, `feature-2`) — a reserved slot whose
meaning is not yet specified. Numeric identity is the only contract. Adapters
may map these to OpenType stylistic sets, CSS flags, or any binary surface.
A sequenced feature matures into a named one; the number is retired, not reused.

**`feature-{slug}`** (e.g. `feature-italic`, `feature-condensed`) — a named
semantic flag. The slug is the meaning. Adapters that do not recognize a slug
must degrade gracefully and ignore it.

The two forms are syntactically disjoint (a slug must start with a letter) so
there is no parsing ambiguity. Third-party adapters may define their own named
feature flags without upstream registration.

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

### Density

`density` is a scalar in `[0, 1]` representing the mass and weight of visual
elements. Low density suggests lightness, openness, and restraint — thin strokes,
ample spacing, airy layout. High density suggests heaviness, concentration, and
presence — thick strokes, tight spacing, saturated surfaces.

Adapters most commonly map `density` to font weight axes (`wght`), stroke width,
or element mass. It is the single most load-bearing primitive for establishing
the overall visual character of a projection.

### Expansion

`expansion` is a scalar in `[0, 1]` representing lateral spread and spatial
openness. Low expansion suggests compression and narrow proportion. High
expansion suggests width, breadth, and generosity of space.

Adapters typically map `expansion` to font width axes (`wdth`), container
padding, or horizontal scale. It pairs naturally with `density` — a high-density,
high-expansion field reads differently than a high-density, low-expansion one.

### Curvature

`curvature` is a scalar in `[0, 1]` representing roundness and organic quality.
Low curvature produces angular, geometric, and constructed forms. High curvature
produces rounded, soft, and organic forms.

Adapters map `curvature` to border radius, glyph terminal rounding, corner
softening, or path smoothing. It is a strong signal of tone — engineered vs
humanist, cold vs approachable.

### Contrast

`contrast` is a scalar in `[0, 1]` representing the differential between figure
and ground. Low contrast produces flat, monolithic, minimal distinction between
elements. High contrast produces sharp differentiation — bold vs light, dark vs
bright, large vs small.

In typography, `contrast` maps naturally to stroke contrast (the ratio between
thick and thin strokes). In layout and color, it governs the perceptual pop of
foreground elements against their background.

### Grain

`grain` is a scalar in `[0, 1]` representing fine-grained micro-variation and
texture. Low grain is smooth, clean, and homogeneous. High grain is textured,
noisy, and variable at fine scale.

Grain operates at a different scale than the other primitives — it describes
character within elements rather than the overall structure of the projection.
Adapters may use it to control letter-spacing variation, sub-pixel noise,
texture overlays, or OpenType features like contextual alternates (`calt`).

### Tension

`tension` is a scalar in `[0, 1]` representing structural tightness and
compression. Low tension is relaxed, loose, and unhurried. High tension is
compressed, taut, and energetic.

Tension affects the perceived energy of a composition independently of density
or expansion. A sparse, lightweight layout can still carry high tension through
tight tracking, compressed spacing ratios, or sharp angular forms. Adapters
often combine `tension` with `grain` as a signal for micro-typographic
tightening.

### Symmetry

`symmetry` is a scalar in `[0, 1]` representing axial balance and alignment
bias. Low symmetry biases toward asymmetric, dynamic, and off-center
composition. High symmetry biases toward centered, balanced, and formally
structured arrangement.

Adapters use `symmetry` to govern text alignment (left-biased vs centered),
layout grid offset, or the degree to which paired elements mirror each other.
It is not a binary toggle — values between extremes produce graded asymmetric
tendencies rather than hard alignment rules.

### Phase

`phase` is an angle in `[0, 2π]` representing harmonic offset and rotational
position. It is the only non-scalar primitive in the named vocabulary, and
carries a fundamentally different semantic: rather than a linear spectrum between
two poles, it describes a position within a cycle.

`phase` is most naturally used to select between qualitative states — the
`self.voice` example in [Derived Primitives](#derived-primitives-self) divides
the full circle into three equal arcs. It also governs literal rotation (SVG
elements, gradient angles) and harmonic offset in periodic patterns.

Unlike the scalar primitives, `phase` has no "low is one thing, high is another"
reading — its meaning is always relational to the cycle being divided.

### Derived Primitives (`self.*`)

Any implementation may define additional primitives of the form `self.<semantic>`,
where `<semantic>` is a lowercase kebab-slug naming what the primitive means.

A `self.*` primitive must be a **pure function of `PrimitiveField`** — it may
not introduce new entropy, access external state, or produce side effects. Its
provenance is the field itself: same entropy, same field, same derived value.
This is the constraint that prevents arbitrary extension from dissolving into
noise.

```typescript
type SelfPrimitive = (field: PrimitiveField) => unknown

// examples — the function body is the proof of provenance:
self.warmth    = (f) => f.density * (1 - f.tension) + f.curvature * 0.3
self.agitation = (f) => f.tension * f.grain
self.voice     = (f) => f.phase < 2.09 ? "assertive"   // [0, 2π/3)
                      : f.phase < 4.19 ? "quiet"        // [2π/3, 4π/3)
                      : "considered"                     // [4π/3, 2π]
```

The name is a semantic claim. The derivation is the justification. Both are
required — a `self.*` primitive with no derivation is not a primitive, it is
an assertion.

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
  "--epf-font-style":     field["feature-1"] ? "italic" : "normal",
})
```

A named feature flag such as `field["feature-italic"]` is equally valid once
the vocabulary entry exists — named and sequenced flags share the same
`feature-${string}` index type on `PrimitiveField` and are populated by
`kernel.flag(name)` during field construction.

### Example: OpenType Variable Font

```typescript
const opentypeAdapter: Adapter<OpenTypeSettings> = (field) => ({
  variationSettings: {
    wght: lerp(200, 800, field.density),
    wdth: lerp(75, 125, field.expansion),
  },
  featureSettings: {
    "ss01": field["feature-1"],
    "ss02": field["feature-2"],
    "ss03": field["feature-3"],
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
| `"feature-1"` | `flag` | TBD |
| `"feature-2"` | `flag` | TBD |
| `"grain"` | `scalar` | TBD ±0.0001 |
| `"cols"` | `sequence(3)` | TBD[3] ±0.0001 each |
| `"layout"` | `partition(3)` | TBD[3], sum = 1.0 ±0.0001 |
| `"palette"` | `sample(items, 2)` | TBD[2] (deterministic pair) |

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

*EPF Kernel · Entropy Projection Framework · Pre-RFC Draft · © contributors*
