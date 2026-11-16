# ga-draft-skill

A Claude skill that turns a paper's abstract into **three graphical abstract layouts** as an editable PPTX, then writes matching illustration prompts for the one you pick.

The hardest part of a first graphical abstract (GA) is rarely drawing. It is **layout**: what goes where. This skill shows you three structurally different layouts before any image is made, so you only create the illustrations you actually need, in one consistent style.

> **Drafts only.** Output is meant for planning and discussion, not submission. See [Journal policies](#journal-policies).

## How it works

| Step | What happens | You get |
|---|---|---|
| **1. Confirm the message** | Claude extracts the one-sentence claim, the storyline (3–5 nodes), biological context, and the evidence level of each link (causal vs. correlational). | A table to approve or correct |
| **2. Three layouts** | Claude picks 3 of 4 layouts that fit the paper's logic and builds a deck. | `ga_options.pptx`: overview slide + one slide per option with rationale, what was left out, and what the figure must *not* claim |
| **3. Fill in images** | After you choose, Claude builds a working canvas and a prompt list sharing one style prefix. Upload the images and they are cut out and placed. | `ga_final.pptx` with editable text and arrows |

### Layouts

| `layout` | Best for |
|---|---|
| `flow` | Cause → process → outcome in one line; method pipelines |
| `compare` | The key result is a difference (WT vs. KO, treated vs. control) |
| `zoom` | Organism → tissue → cell/molecule; where the mechanism happens matters |
| `hub` | One factor with several inputs or outputs |

Layout frequencies used as built-in defaults come from a quick survey of GAs in a recent issue of *Cell Reports* (see `references/patterns.md`). If you attach reference GAs, the skill reads their layout patterns instead. It never copies images or text from references.

## Built-in rules

- **One message per figure.** Secondary results go to `left_out`.
- **≤ 9 elements, ≤ 30 words**, labels as 1–3 word noun phrases.
- **One meaning per arrow:** solid = causal, `-|` = inhibits, dashed `~` = association only.
- **Two colors + gray**, no red–green pairs.
- **No data** (graphs, numbers, p-values) in the GA.
- **No overclaiming:** no mouse results drawn as humans, no mechanisms or cell types the paper did not show, and a `boundary` note on every option.
- **Biology check:** placed images are reviewed for common generator errors (e.g., a folded, human-like brain for a mouse).

## Repository structure

```
ga-draft/
├── SKILL.md                 # Workflow and rules (instructions are in Korean)
├── references/
│   └── patterns.md          # Pattern card template + built-in layout patterns
└── scripts/
    ├── build_ga_deck.js     # spec.json → PPTX (options deck or --final working file)
    └── prep_images.py       # White → transparent, trim margins, split image sheets
```

## Requirements

- Node.js with `pptxgenjs` (`npm install pptxgenjs`)
- Python 3 with `numpy` and `pillow` (`pip install numpy pillow`); `scipy` optional

## Installation

Copy the `ga-draft` folder into your skills directory (e.g., `~/.claude/skills/` for Claude Code), or upload it as a ZIP where your Claude app accepts custom skills.

## Usage

```
/ga-draft
Here is my abstract and figure titles. I need a graphical abstract draft.
```

An abstract plus main figure titles is enough; the full manuscript is not needed.

Scripts can also be run directly:

```bash
node scripts/build_ga_deck.js spec.json ga_options.pptx            # three options
node scripts/build_ga_deck.js spec.json ga_final.pptx --final B    # chosen option
python3 scripts/prep_images.py images/ --out img_cut               # prepare images
python3 scripts/prep_images.py sheet.png --sheet 2x3 --out img_cut --names "a,b,c,d,e,f"
```

The `spec.json` format is documented in `SKILL.md`.

## Journal policies

Policies change; check the current Guide for Authors before submitting.

- **Cell Press:** 1200 × 1200 px, one message, no data. General-purpose generative AI tools may not be used to create GAs.
- **Elsevier** follows a similar policy; **Springer Nature** generally does not publish generative AI images.

Replace AI-generated drafts with your own drawings, a dedicated tool such as BioRender, or clearly licensed icons: [Servier Medical Art](https://smart.servier.com) (CC BY 4.0), [Bioicons](https://bioicons.com) (check each icon's license), [NIH BioArt](https://bioart.niaid.nih.gov).

## Attribution

- Design rules adapted from Jambor & Bornhäuser, "Ten simple rules for designing graphical abstracts," *PLOS Computational Biology* (2024), CC BY 4.0.
- Hullman & Bach, "Picturing Science: Design Patterns in Graphical Abstracts" (2018).
- Cell Press Graphical Abstract Guidelines.
- Overclaiming rules condensed from `graphical-abstract-generator` in [aipoch/medical-research-skills](https://github.com/aipoch/medical-research-skills) (MIT). Include its MIT license notice when redistributing.

## License

<!-- Add your license here (e.g., MIT) and keep the third-party notices above. -->
