# ScrollWeaver

**Online sequential authoring of game worlds from player trajectories.**

[Project page](https://scrollweaver.sunhaoxuan.org) · [Paper (coming soon)]() · [Demo (coming soon)]()

> ⚠️ **Status: active early-stage research.** Code, results, and documentation are evolving rapidly. Expect breaking changes.

---

## TL;DR

Most procedural content generation (PCG) systems treat world generation as a **one-shot** problem: given a designer spec, output a full level. ScrollWeaver reframes it as **online sequential authoring** — the world is generated *area-by-area*, and each new area is conditioned on (i) the player's behavioral trajectory through previously generated regions and (ii) high-level designer constraints.

The result is a world that **adapts to how you actually play it**, while staying coherent and faithful to designer intent.

## Why this framing

| | Static one-shot PCG (PCGRL, Moonshine, ...) | **ScrollWeaver (this work)** |
|---|---|---|
| When is content generated? | Before play | During play, area by area |
| What conditions the output? | Designer spec only | Designer spec **+ player trajectory so far** |
| Adaptation to play style | None | Built in |
| Global coherence | Trivial (whole map at once) | Enforced by an explicit coherence module |
| Authoring loop | Open loop | Closed loop with the player |

This makes the problem closer to how human designers actually work in tools like Spelunky's level editor or Dwarf Fortress — and closer to how game worlds will need to be generated in the era of long-horizon, agent-driven play.

## Method (sketch)

ScrollWeaver decomposes generation into a sequence of local authoring decisions:

1. **Trajectory encoder** — compresses the player's recent behavior into a fixed-size context vector.
2. **Constraint encoder** — encodes the designer's high-level intent (difficulty curve, themes, beats).
3. **Regional generator** — proposes the next area conditioned on both encoders' outputs.
4. **Coherence module** — guarantees the proposed area is consistent with previously committed geometry, items, and narrative state.

A full system diagram is on the [project page](https://scrollweaver.sunhaoxuan.org).

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

This repo follows a 14-week development plan. Current phase is marked **▶**.

- **▶ Phase 1 — Foundations (weeks 1–5)**
  Reproduce PCGRL and DIAMOND (ICLR 2025) as baselines and sanity checks.
- **Phase 2 — Online authoring system (weeks 6–11)**
  Build the trajectory-conditioned regional generator and coherence module in a pygame roguelike testbed.
- **Phase 3 — Demo & writeup (weeks 12–14)**
  Gradio demo, technical report, target submission to IEEE Conference on Games or an AAAI/NeurIPS workshop.

## Getting started

> Setup instructions will be finalized once Phase 1 reproductions land. Tentative:

```bash
git clone https://github.com/guwangtu/scrollweaver.git
cd scrollweaver
pip install -r requirements.txt
python scripts/train.py --config configs/baseline_pcgrl.yaml
```

## Related work

- **PCGRL** (Khalifa et al., 2020) — RL for level generation; ScrollWeaver uses a related action formulation but at the *region* level and conditioned on player traces.
- **Moonshine / one-shot LLM PCG** — generates whole maps at once; ScrollWeaver is online and player-aware.
- **DIAMOND** (Alonso et al., ICLR 2025) — diffusion world models for RL; we draw on its world-model machinery for the coherence module.
- **Path of Destruction** (Siper, Earle, Togelius) — autoregressive content generation; the closest conceptual neighbor to ScrollWeaver's per-region authoring loop.

A full reading list lives in [`docs/related_work.md`](docs/related_work.md) (TODO).

## Citing

If ScrollWeaver is useful to your work, please cite:

```bibtex
@misc{sun2026scrollweaver,
  title  = {ScrollWeaver: Online Sequential Authoring of Game Worlds from Player Trajectories},
  author = {Sun, Haoxuan},
  year   = {2026},
  note   = {Work in progress},
  url    = {https://scrollweaver.sunhaoxuan.org}
}
```

## Author

**Haoxuan Sun** — M.S. in Computer Science, Vanderbilt University
Advised by [Prof. Soheil Kolouri](https://skolouri.github.io/) (MINT Lab)
[Homepage](https://sunhaoxuan.org) · [GitHub](https://github.com/guwangtu)

## License

TBD (likely MIT). Until a `LICENSE` file is added, all rights reserved.