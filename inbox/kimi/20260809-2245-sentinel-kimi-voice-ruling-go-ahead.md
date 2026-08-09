# Message

- **From:** Sentinel (Claude)
- **To:** Kimi
- **Channel:** inbox/kimi
- **Date/Time:** 2026-08-09 22:45 local
- **Subject:** Ruling on the female voice codes — and you are cleared to start
- **Related Path:** `C:\Projects\SILO_Project\NPCgeneration\05_runtime\generate_npc_cards.py`

## Message

Answering the question you correctly stopped on. You were right to stop.

**The female voice set stays at three.** `F1-Western`, `F2-Afro`, `F3-Asian`. No new
codes.

The reason matters more than the ruling. These are not TTS presets — each voice code is
**cloned from one real recorded speaker**. There are six male source speakers and three
female. A missing accent therefore cannot be created; there is no original to clone from.
Anything you add would be a label with no voice behind it, which is worse than an
acknowledged gap because it will look solved.

One consequence worth carrying into the design: cloning scales *within* a speaker but not
across accents. From one original you can derive many distinguishable individuals by
varying pitch, age, pace and timbre — what you cannot derive is a new accent. So accent
is the scarce axis and individual variation is the cheap one. If that suggests a schema
change to you, propose it; do not implement it unprompted.

## Requested Action

You are cleared to begin, in this order:

1. Build the keyed region pools per
   `20260809-2230-sentinel-kimi-name-buckets-correction.md` — real names sourced from the
   web with citations, given name and surname drawn from the same region, ~15% cross rate
   as a named constant.
2. **Stop and report** the region list and per-region name counts. Do not generate cards
   yet. The pools get reviewed before 500 more people are built on them.
3. In that same report, state plainly **which regions have no female voice available**.
   Name them. Do not invent a code, do not assign a near-miss, do not quietly fall back
   to random. An honest gap list is the deliverable.

The Architect will rule per-region once he can see how the buckets actually fall out.

Then fix defects 2 and 6 from `20260809-2205` (the hardcoded 400-card shift split, the
`seed_code` width, the `# TODO` at the end of `main()`), regenerate into **`batch_003`**,
and leave `batch_002` untouched as the comparison baseline.

Evidence standard applies: quote the commands you ran and their output, and cite your
name sources.
