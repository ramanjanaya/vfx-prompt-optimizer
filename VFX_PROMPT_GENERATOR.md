# VFX Prompt Generator (Versatile, Configurable)

## Overview
This document provides a configurable prompt generator tailored for VFX workloads. It includes:
- A master generator that selects a domain based on user input.
- Domain-specific prompt templates with clear roles, objectives, inputs, constraints, and evaluation criteria.
- Configuration options (tools, output formats, complexity).
- Prompt examples at three difficulty levels.
- A quick-start pseudo-code snippet to generate prompts programmatically.

---

## Global Configuration (applies to all domains)
**Core inputs**
- **Project name / shot ID**
- **Domain**: video generation concept | matchmoving | animation | 3D modeling | FX simulation | compositing
- **Complexity level**: concept | mid | production
- **Target tools**: Blender | Maya | Nuke | Houdini | 3ds Max | Cinema 4D | Unreal | Katana | Clarisse | PFTrack | SynthEyes | After Effects
- **Output formats**: scene files (.blend, .ma/.mb, .hip) | caches (Alembic, USD) | renders (EXR, OpenEXR multi-pass) | plates (ProRes, DPX)
- **Resolution**: e.g., 1920x1080, 3840x2160
- **Frame rate**: e.g., 23.976, 24, 25, 30
- **Frame range**: e.g., 1001–1100
- **Color pipeline**: ACEScg / sRGB / linear
- **Render budget**: per-frame time or total render hours
- **Delivery deadline**

**Evaluation rubric (global defaults)**
- **Accuracy**: technical correctness (matchmove, animation timing, topology fidelity)
- **Realism**: lighting/physics/material coherence
- **Consistency**: continuity across frames/shots
- **Efficiency**: render performance and optimization
- **Deliverable compliance**: formats, naming, and passes

---

# Master Generator (Domain Selector)
Use this master template to route the request to the correct domain-specific prompt.

**Routing keywords**
- **Video generation concept**: “storyboard,” “concept,” “pitch,” “look-dev,” “style frames”
- **Matchmoving**: “camera track,” “solve,” “tracking,” “lens,” “distortion,” “survey”
- **Animation**: “rig,” “performance,” “blocking,” “motion,” “character”
- **3D modeling**: “model,” “topology,” “sculpt,” “retopo,” “UV”
- **FX simulation**: “fluid,” “smoke,” “explosion,” “destruction,” “pyro,” “particles”
- **Compositing**: “comp,” “plates,” “keying,” “roto,” “integration”

**Master generator template**
```
MASTER_INPUT:
  user_goal:
  shot_id:
  domain_hint:
  complexity:
  tools:
  output_formats:
  resolution:
  fps:
  frame_range:
  color_pipeline:
  budget:

ROUTE:
  if domain_hint contains matchmove OR keywords in user_goal -> MATCHMOVING_TEMPLATE
  else if domain_hint contains animation -> ANIMATION_TEMPLATE
  else if domain_hint contains modeling -> MODELING_TEMPLATE
  else if domain_hint contains fx -> FX_TEMPLATE
  else if domain_hint contains comp -> COMPOSITING_TEMPLATE
  else -> VIDEO_GEN_CONCEPT_TEMPLATE
```

---

# Domain Templates

## 1) Video Generation Concept (Look-Dev / Pitch)
**Role & context**: VFX supervisor + concept artist + director

**Prompt template**
- **Objective & deliverables**: Define the visual concept and key beats. Deliver 3–5 style frames + a brief look-dev spec.
- **Input assets & constraints**: Story outline, reference images, target resolution/fps, delivery format (PNG/JPG style frames + PDF spec).
- **Technical focus**: Color pipeline, visual continuity, exposure consistency.
- **Creative guidance**: Mood, style references (films, art), camera language, time of day.
- **Evaluation rubric**: Concept clarity, visual coherence, readability, alignment with narrative.

**Examples**
- **Basic**: “Generate 3 style frames for a neon rainy city chase, 1080p, 24fps references, deliver PNGs and a 1-page PDF look spec.”
- **Intermediate**: “Provide 5 frames showing progression from dusk to night in a cyberpunk alley. Include lighting notes, palette, and lens choices. 4K, ACEScg.”
- **Advanced**: “Create a cohesive look-dev pitch for a 60-second trailer: 5 frames, 1 lighting key diagram, and a LUT suggestion. Match Blade Runner 2049 mood with a colder palette.”

---

## 2) Matchmoving
**Role & context**: Matchmove artist + VFX supervisor

**Prompt template**
- **Objective & deliverables**: Solve camera and object tracks for shot(s). Deliver camera solve + lens data + QC notes.
- **Input assets & constraints**: Plates (EXR/DPX), lens grid, survey data, frame range, resolution, fps.
- **Technical focus**: Tracking accuracy, lens distortion modeling, reprojection error limits.
- **Creative guidance**: Maintain physical camera feel, match on-set camera behavior.
- **Evaluation rubric**: Reprojection error, parallax fidelity, stability across frames.

**Examples**
- **Basic**: “Track a static camera pan in Shot 010, 1001–1050, deliver Alembic camera + Nuke script.”
- **Intermediate**: “Solve handheld camera with lens distortion from grid. Provide undistort/redistort node setup. Error < 0.5 px.”
- **Advanced**: “Track a crane move with moving props. Include object tracks for two vehicles and deliver USD camera + lens metadata.”

---

## 3) Animation
**Role & context**: Animator + animation supervisor

**Prompt template**
- **Objective & deliverables**: Animate characters/props to match the shot brief. Deliver playblast + animation curves.
- **Input assets & constraints**: Rig files, audio/dialog, frame range, fps, output formats (playblast, FBX).
- **Technical focus**: Timing, weight, arcs, continuity, lip sync (if applicable).
- **Creative guidance**: Performance notes, acting beats, emotional tone.
- **Evaluation rubric**: Motion clarity, character believability, adherence to timing.

**Examples**
- **Basic**: “Animate a simple turn-and-wave for character A, 48 frames, 24fps. Deliver playblast MP4.”
- **Intermediate**: “Blocking pass for a 4-second run cycle with camera follow. Provide stepped keys + notes.”
- **Advanced**: “Final animation for a 12-second dialogue scene. Sync to audio, include facial performance, deliver spline curves + playblast.”

---

## 4) 3D Modeling
**Role & context**: CG modeler + asset supervisor

**Prompt template**
- **Objective & deliverables**: Build a production-ready asset. Deliver clean topology, UVs, and textures.
- **Input assets & constraints**: Concept art, scale references, triangle/quad budget, output formats (.fbx, .usd).
- **Technical focus**: Topology flow, UV layout efficiency, scale accuracy.
- **Creative guidance**: Material references, wear and tear, style.
- **Evaluation rubric**: Deformation readiness, texture quality, fidelity to concept.

**Examples**
- **Basic**: “Model a low-poly sci-fi crate, 2k tris, UV unwrapped, deliver FBX.”
- **Intermediate**: “Mid-poly hero prop with 4K texture set, clean quad topology, deliver USD + texture maps.”
- **Advanced**: “Production-ready creature head with sculpted detail, retopo for animation, UDIMs and displacement.”

---

## 5) FX Simulation
**Role & context**: FX artist + simulation lead

**Prompt template**
- **Objective & deliverables**: Simulate FX elements (fire, smoke, water, debris). Deliver caches + render passes.
- **Input assets & constraints**: Collision geometry, scale units, frame range, resolution, solver settings.
- **Technical focus**: Physical realism, scale fidelity, cache efficiency.
- **Creative guidance**: Intensity, timing, style (realistic vs stylized).
- **Evaluation rubric**: Realism, stability, render efficiency.

**Examples**
- **Basic**: “Create a small smoke puff on impact, 1001–1020, cache to Alembic.”
- **Intermediate**: “Simulate a controlled explosion with debris, 4K render passes (fire, smoke, embers).”
- **Advanced**: “Large-scale water splash with foam and spray, optimized caches, deliver VDB volumes + EXR renders.”

---

## 6) Compositing
**Role & context**: Compositor + VFX supervisor

**Prompt template**
- **Objective & deliverables**: Integrate CG into plates. Deliver final comp + breakdown.
- **Input assets & constraints**: Plates, CG renders/passes, color pipeline, frame range, output formats.
- **Technical focus**: Keying/roto quality, grain match, color consistency.
- **Creative guidance**: Overall look, light wrap, atmosphere.
- **Evaluation rubric**: Seamlessness, color match, edge quality.

**Examples**
- **Basic**: “Composite CG object into plate, match lighting and grain, deliver EXR sequence.”
- **Intermediate**: “Key greenscreen actor, integrate with CG background, add atmospheric haze.”
- **Advanced**: “Full comp with multi-pass integration (diffuse/spec/AO), lens effects, and ACES color management.”

---

# Quick-Start Generator Snippet (Pseudocode)
```
function buildPrompt(input):
  domain = routeDomain(input.user_goal, input.domain_hint)
  template = loadTemplate(domain)
  prompt = fill(template, {
    objective: input.objective,
    deliverables: input.deliverables,
    assets: input.assets,
    constraints: {
      resolution: input.resolution,
      fps: input.fps,
      frame_range: input.frame_range,
      tools: input.tools,
      output_formats: input.output_formats,
      color_pipeline: input.color_pipeline
    },
    technical_focus: input.technical_focus,
    creative_guidance: input.creative_guidance,
    evaluation: input.evaluation
  })
  return prompt
```

---

## Optional: User Input Schema (High-Level)
```
user_goal:
shot_id:
domain_hint:
complexity:
tools:
output_formats:
resolution:
fps:
frame_range:
color_pipeline:
assets:
technical_focus:
creative_guidance:
evaluation:
```

---

## Confirmation
Do you prefer a single unified generator (one master selector + templates), or separate domain-specific generators? Also share any preferred tools or output formats to tailor future prompt builds.
