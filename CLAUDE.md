# Indonesian trainer — agent notes

```
kaikki (Wiktionary id) ─┐
Tatoeba id/eng ─────────┼─> tools/build_pack.py (packbuilder, langs/id.py)
FrequencyWords + wordfreq┘        │
Stanza (id gsd, build-time only)  │  gloss_overrides.json, forced_a1.txt,
Sastrawi stemmer (build-time only)│  passages_src.json, id_map_v1.json
                                   v
                          pack/*.json --jsonify_pack.py--> pack/*.js
                                   v
              build.sh (engine/build.sh: engine/app.html + engine/core.js + pack js)
                                   v
                    index.html + sw.js -> GitHub Pages
                    https://bannerless-studio.github.io/indonesian/
progress lives in localStorage key vocab_id on the shared origin
```

Why it is built this way: single-file site + service worker for offline; engine as a
git submodule so every language ships the same drills; pack ids frozen
(`tools/id_map_v1.json`) so learner progress survives rebuilds. Indonesian-specific
linking rules (verb-form folding, enclitics, fixed compounds, classifiers,
reduplication) live in `engine/tools/packbuilder/langs/id.py`, not here — see
README.md "Indonesian rules" for the summary and TODO.md for open residuals.

## Commands (pinned)

- Rebuild pack: `python3 tools/build_pack.py` (equivalent to
  `PYTHONPATH=engine/tools python3 -m packbuilder build --lang id --repo .`), then
  `python3 engine/tools/jsonify_pack.py pack`
- Rebuild reading passages: `PYTHONPATH=engine/tools python3 -m packbuilder passages --lang id .`
  then `python3 engine/tools/jsonify_pack.py pack`
- Build site: `./build.sh`
- Check (must pass before every commit of index.html): `./check.sh`
- Against a non-submodule vocab-engine checkout: set
  `PACKBUILDER_PATH=../vocab-engine/tools` for both `tools/build_pack.py` and `./check.sh`
- Engine tests live in vocab-engine (see its CLAUDE.md)

## Always

- Commit index.html and sw.js together; check.sh's stale-build guard runs post-commit.
- Bump the engine submodule only to a vocab-engine main sha; rebuild after every bump.
- Keep ids append-only; never renumber (`tools/id_map_v1.json`).
- Path-limited commits: engine, index.html, sw.js, pack/, tools/, README.md, TODO.md;
  never .venv or .cache.
- Sources download once into `.cache/` (gitignored); the build is deterministic, so
  re-running from cache reproduces byte-identical `pack/*.json`.

## Never

- Edit pack/*.json by hand; change tools/gloss_overrides.json, tools/forced_a1.txt or
  tools/gloss_display.json and rebuild instead.
- Edit pack/*.js, index.html or sw.js by hand (generated).
- Delete sw.js (use engine/sw.disable.js).
- Add comments that say what the code does; only why, or an external reference.
- Push to main without `git merge-base --is-ancestor origin/main HEAD`.

## Generated files

pack/*.js, pack/*.json, index.html, sw.js, tools/REPORT.md, tools/REPORT_passages.md,
tools/id_map_v1.json (frozen, hand-edit never), tools/generated_sentences.tsv is
hand-authored input (not generated) but feeds the build like a source.

## Where things are

README.md (end users), tools/README.md (builder inputs, file by file), TODO.md
(residuals + v2 candidates), engine/ (submodule, read-only here).
