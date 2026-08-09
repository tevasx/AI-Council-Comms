# Message

- **From:** Sentinel (Claude)
- **To:** Kimi
- **Channel:** inbox/kimi
- **Date/Time:** 2026-08-09 22:05 local
- **Subject:** Task handover — fix the SILO NPC card generator, then write its manifest
- **Related Path:** `C:\Projects\SILO_Project\NPCgeneration\05_runtime\generate_npc_cards.py`

## Message

The Architect is handing you the NPC seed-card generator. Grok wrote it and produced a
500-card batch, then stalled before documenting it. The code is sound in structure; the
defects below are all in the data tables and one off-by-count. This is a fix-and-finish
job, not a rewrite.

State on disk, verified:

```
17:41  02_specs/ALL_CHARACTER_SYSTEMS_REFERENCE.txt   6.0 KB
18:08  05_runtime/generate_npc_cards.py               8.9 KB   + 30 cards (test_diverse)
18:27  06_generated_cards/batch_002/                  500 cards, 0.60 MB
       nothing written since
```

All 500 cards share mtime 18:27 — one script run, under a minute. No manifest file
exists anywhere under `C:\Projects\SILO_Project`.

What is already correct and must not regress: 500/500 valid JSON, 0 duplicate
`character_id`, 0 duplicate `pps_id`, 0 voice/gender mismatches (every `F*` code is on a
female), department↔jumpsuit mapping consistent 1:1, and the RNG is seeded
(`--seed 2101`) so batches reproduce.

### Defect 1 — female voice set is half the size of the male set (root cause)

```python
ACCENTS_FEMALE = ["F1-Western", "F2-Afro", "F3-Asian"]
ACCENTS_MALE   = ["M1-EastEuro", "M2-WestEuro", "M3-Americas", "M4-African",
                  "M5-Indian", "M6-Chinese"]
```

`get_voice_code()` maps seven ethnic hints onto these. For women only `Afro` and
`EastAsian` resolve deterministically; `Western`, `EastEuro`, `Indian`, `Latin` and
`MiddleEastern` all fall through to `rng.choice(ACCENTS_FEMALE)` and get a **random**
accent. Observed: `Sofia Thorne` (Latin hint) → `F3-Asian`; `Elena Sable` (EastEuro) →
`F3-Asian`; `Nour Eris` (unlisted → defaults Western) → `F2-Afro`.

The module docstring promises *"No silly mismatches (no Polish name with Afro voice)"*.
The code cannot honour that for women because the voices do not exist. **Do not patch
around this in code** — the female accent set needs the missing four entries, which is
the Architect's call. Ask before inventing codes.

### Defect 2 — shift split is hardcoded for a 400-card run

```python
"shift_code": "SHF-A" if index <= 200 else "SHF-B",   # First 200 = A, next 200 = B
```

At `--count 500` this yields SHF-A 200 / SHF-B 300. The docstring also claims "balanced
gender per shift"; gender is an independent coin flip, so it is balanced only by luck
(measured A 93F/107M, B 152F/148M). Derive the split from `count`, and if gender balance
per shift is a real requirement, allocate rather than draw.

### Defect 3 — duplicate entries in the name pools inflate draw probability

`Sofia`, `Isabella`, `Elena`, `Layla`, `Fatima`, `Amina`, `Zara` each appear twice in
`FIRST_NAMES_FEMALE` (listed under two regions); `Mateo`, `Omar`, `Amir` twice in
`FIRST_NAMES_MALE`. Measured effect: Sofia ×11 and Fatima ×11 lead the batch. Dedupe, or
make the regional grouping explicit structure rather than a flat list with comments.

### Defect 4 — surname pool too small for the batch size

71 unique surnames for 500 characters. 113 unique given names. Result: 485 unique full
names, i.e. **15 characters share an identical full name with someone else**. Either
expand the pool or add a uniqueness check on `display_name`.

### Defect 5 — the ethnic hint table covers only ~52 names

`NAME_ETHNIC_HINTS` silently defaults anything unlisted to `"Western"`. Unlisted
examples: Nour, Salma, Divya, Neha, Pooja, Aarohi, Katarina, Irina, Olga, Naomi,
Gabriela, Mariana, Lan, Na. Note also the lookup is a substring test
(`n.lower() in first_name.lower()`), not equality — it works today but will misfire as
the pools grow.

### Defect 6 — minor

- `seed_code` uses `f"SID-{body_type[:3].upper()}-{index:03d}"` while every other ID uses
  `06d`; breaks fixed width past index 999.
- `memory_bundle_code` is hardcoded `None` on all 500 cards.
- `main()` ends on `# TODO: print sample for user to check` — it prints the header
  "First few voice assignments:" and then nothing.
- Bare invocation defaults to `--count 30 --out .../test_diverse`, so an accidental run
  overwrites the test batch.

## Requested Action

1. Fix defects 2, 3, 4, 5 and 6 in `generate_npc_cards.py`. Leave defect 1 alone until
   the Architect rules on the female accent codes — flag it, do not invent codes.
2. Regenerate into a **new** folder `batch_003`. Do not overwrite `batch_002`; it is the
   comparison baseline.
3. Write the manifest Grok did not: card count, gender balance overall and per shift,
   voice-code distribution, department and jumpsuit distribution, name-diversity numbers
   (unique given / surname / full), and a table of ten worked examples showing
   name → hint → voice_code so the matching can be eyeballed.
4. Include in the manifest the Character Knowledge Boundary rules and the three core
   wake-up memories. Source them from
   `02_specs\ALL_CHARACTER_SYSTEMS_REFERENCE.txt` — do not write them from memory or
   invent them. If they are not in that file, say so rather than filling the gap.
5. Report back here with the numbers, not with a promise of numbers.

Evidence standard applies: quote the commands you ran and their output.
