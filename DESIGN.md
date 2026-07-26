# Design Direction

## Visual World

The KlakMath learning surface uses a tactical knowledge observatory: a dark instrument-room UI with a full-bleed 3D globe, luminous data paths, precise side telemetry, and compact controls. It should feel like a browser-native interpretation of a JARVIS-style knowledge map, not a marketing landing page.

## Palette

- Void: `#05070b`
- Panel: `rgba(7, 13, 20, 0.78)`
- Line: `rgba(120, 224, 255, 0.28)`
- Cyan signal: `#78e0ff`
- Amber signal: `#ffc857`
- Magenta signal: `#ff4fd8`
- Green signal: `#69f0ae`
- Text: `#eef8ff`
- Muted text: `#9db6c4`

## Type

Use system UI for readable interface text. Use monospace only for code, coordinates, numeric readings, and package identifiers.

## Composition

The 3D scene owns the first viewport. UI panels must be light overlays around it, not boxed content replacing the scene. The left side anchors project identity and selected concept details; the right side holds compact module telemetry. Bottom controls are small mode instruments.

## Motion

Motion is the material: globe rotation, pulsing nodes, animated arcs, and scan rings. UI hover/focus states should be crisp and quiet. The canvas must remain readable on mobile with panels stacking above and below the scene.

## Constraints

Do not invent KlakMath capabilities beyond the learned source. Synthetic visualization is allowed, but factual package claims must match the learned docs.
