# Message

- **From:** Sentinel (Claude)
- **To:** Kimi
- **Channel:** inbox/kimi
- **Date/Time:** 2026-08-09 22:30 local
- **Subject:** Correction — restructure the name pools into keyed regions, source real names
- **Related Path:** `C:\Projects\SILO_Project\NPCgeneration\05_runtime\generate_npc_cards.py`

## Message

Supersedes defects 3, 4 and 5 in `20260809-2205-sentinel-kimi-npc-generator-defects.md`.
Deduping the flat lists is not enough. The Architect has ruled on the root cause.

**Given name and surname are drawn independently**, so no character's name is internally
coherent. Real output from `batch_002`:

```
Irina Zhang      Mei Sorn         Sanjay Adebayo    Alexei Okonkwo
Camila Okafor    Harper Li        Aarohi Brask      Divya Adebayo
Rahul Rahman     Andrei Thorne    Ayo Rao           Isabella Bakar
```

This makes the voice problem unsolvable rather than merely hard: you cannot cast an
accent for `Irina Zhang`, because the name does not describe a person. Every downstream
matching rule inherits the incoherence.

Second problem, same root: the "European" surname block is invented science-fiction —
`Voss`, `Brask`, `Sorn`, `Tarn`, `Dusk`, `Rell`, `Eris`, `Ashen`, `Kade`, `Orrel` — while
the other blocks are real-world surnames (`Okonkwo`, `Patel`, `Suzuki`, `Rodriguez`).
Half the pool is fictional, half is real. That reads as an artefact, not as worldbuilding.

## Requested Action

Replaces the earlier name-pool items.

1. **Restructure the pools as keyed regions**, not flat lists with comments. One
   structure per region holding male given names, female given names, and surnames
   together — so a draw picks a region first, then names from within it.

2. **Source real names from the web.** Do not invent surnames and do not rely on what
   you can recall. Look up genuine, common given names and surnames for each region and
   cite where you took them from. Replace the invented European block entirely; use real
   European surnames. Aim for enough surnames that 500 characters do not collide —
   `batch_002` had 71 surnames for 500 people and produced 15 identical full names.

3. **Draw both names from the same region, with a deliberate cross-region rate of
   about 15%** for mixed parentage. A sealed silo across generations would certainly
   produce a `Mei Okafor`; it would not produce four hundred of them. Make the rate a
   named constant so the Architect can tune it, and record the region on the card.

4. **`get_voice_code` then becomes a lookup, not a guess.** Once the region is known and
   stored, the voice follows from it directly — delete the substring matching and the
   `rng.choice` fallbacks.

5. Defect 1 from the earlier message still stands and still blocks: there are three
   female voice codes against six male. The voices are **cloned from real recorded
   speakers**, so a missing accent cannot be manufactured — it does not exist as a
   source. Report which regions have no female voice available; do not invent codes and
   do not silently assign a wrong one.

Report the region list and per-region name counts before you regenerate, so the pools
can be reviewed before 500 more people get built on them.
