# ScrollWeaver

**Online sequential authoring of game worlds from player trajectories.**

[Project page](https://scrollweaver.sunhaoxuan.org) · [Paper (coming soon)]() · [Demo (coming soon)]()

> **Work in progress**Everything here is subject to change.

---

## TL;DR

Most procedural content generation (PCG) systems treat world generation as a **one-shot** problem: given a designer spec, output a full level. ScrollWeaver reframes it as **online sequential authoring** — the world is generated *area-by-area*, and each new area is conditioned on (i) the player's behavioral trajectory through previously generated regions and (ii) high-level designer constraints.

The result is a world that **adapts to how you actually play it**, while staying coherent and faithful to designer intent.

## Why this framing

| | Static one-shot PCG (PCGRL, Moonshine, ...) | **ScrollWeaver (this work)** |
|---|---|---|
| When is content generated? | Before play | During play, area by area |
| What conditions the output? | Designer spec only | Designer spec **+ player trajectory** |
| Adaptation to play style | None | Built in |

This makes the problem closer to how game worlds will need to be generated in the era of long-horizon, agent-driven play.

## Method (sketch)

## Room Generation: Generate-then-Edit
 
Each room is produced in two stages:
 
1. **Background generation.** While the player explores Room *n*, the system uses the previous trajectory (trace₀) to generate a full draft of Room *n+1*. The cost of generation is hidden behind gameplay time.
 
2. **Transition-time edit.** When the player exits Room *n*, a lightweight editor applies bounded modifications to the draft using trace₁ (the player's most recent behavior), keeping the final room up-to-date without blocking gameplay.
 
Since the edited room has never been seen by the player, there is no risk of modifying already-observed content.


## Architecture
 
**World Model (V / D).** V encodes a room's full structured state (tiles, entities, player position) into discrete latents from a god's-eye view — it serves the generator/editor, not the agent. In this section, we can use self-generated maps and agents operating in preset modes to collect data for training.
 
**Trajectory Representation.** A single latent history [z₀, z₁, ..., zₜ] where each zᵢ is a global snapshot. Player behavior is implicit in each snapshot; trace₀/trace₁ are slices of this sequence, pooled via a small transformer for conditioning.
 
**Transition Model (M).** A transformer world model using Block Teacher Forcing and Dyna with Warmup, serving double duty as both transition predictor and trajectory encoder.

**Generator.** Produces a full room draft from scratch, conditioned on trace₀ and designer constraints (pacing, difficulty). Runs in the background during gameplay.

**Editor.** Applies bounded modifications to the draft at room transition, conditioned on trace₁. Edit budget is capped to keep latency minimal.

A full system diagram will be on the [project page](https://scrollweaver.sunhaoxuan.org).

## Repository layout

```
scrollweaver/
├── scrollweaver/        # core models (encoders, generator, coherence)
├── envs/                # roguelike environments (pygame-based)
├── scripts/             # train / eval / demo entry points
├── configs/             # experiment configs
├── notebooks/           # analysis & visualizations
├── requirements.txt
└── README.md
```

## Roadmap
 
**Phase 1 — Environment & Foundations** (Apr–May 2026)
- Custom Pygame roguelike environment with Gymnasium wrapper
- PCGRL-JAX reproduction as baseline
- Collect 10k episodes via hand-crafted policy
- World model V/D: field-wise tokenizer + discrete latent encoder, sanity-check reconstruction
 
**Phase 2 — Core Pipeline** (Jun–Jul 2026)
- Transition model M (transformer, Block Teacher Forcing + Dyna with Warmup)
- Generator + Editor with trajectory conditioning via latent slicing (trace₀ / trace₁)
- End-to-end generate-then-edit loop: background generation + transition-time edit
- Three-way comparison: unconditional / static condition / trajectory-conditioned
- Evaluation: playability, boundary continuity, style coherence; surrogate RL agents as experiential proxy
 
**Phase 3 — Demo & Report** (Aug 2026)
- Interactive Gradio demo (player-controlled roguelike with live ScrollWeaver generation)
- Technical report (arXiv-style, 8–12 pages)
- Project page, 1-min video, public repo with reproduction instructions

## Getting started

## Related work

- **PCGRL** (Khalifa et al., 2020) — RL for level generation; ScrollWeaver uses a related action formulation but at the *region* level and conditioned on player traces.
- **Moonshine / one-shot LLM PCG** — generates whole maps at once; ScrollWeaver is online and player-aware.
- **DIAMOND** (Alonso et al., ICLR 2025) — diffusion world models for RL; we draw on its world-model machinery for the coherence module.
- **Path of Destruction** (Siper, Earle, Togelius) — autoregressive content generation; the closest conceptual neighbor to ScrollWeaver's per-region authoring loop.


## Author

**Haoxuan Sun** — M.S. in Computer Science, Vanderbilt University
[Homepage](https://sunhaoxuan.org) · [GitHub](https://github.com/guwangtu)

## License

MIT License