# Indonesian A1-B1 vocab trainer

A free vocabulary trainer for Indonesian, A1 through B1: 2000 words with short
English glosses and example sentences, plus 60 short reading passages with
comprehension questions.

**Live:** https://bannerless-studio.github.io/indonesian/

**Scope note:** this app gives the vocabulary base for B1. A B1 exam (for
example UKBI or a BIPA level test) also needs grammar, writing and speaking
practice, which this app does not teach.

## Using the trainer

- **Today** runs one daily session: review, learn new words, listen, recall,
  sentence practice, and a reading passage when one is due. Each stage skips
  itself when there is not enough material for it yet.
- **Words** lets you browse and search the word list, and drill any set on
  demand.
- **Test** has a placement test (to skip words you already know) plus free
  tests.
- **Progress** shows your stats and lets you export, import, or reset your
  progress.
- Question types: hearing a word and picking its meaning, reading a word and
  picking its meaning, seeing a meaning and picking the word, typing the word
  from its meaning, and filling a gap in a sentence.
- **Reading passages (Read tab, inside Today):** 20 short texts each at A1,
  A2 and B1, with comprehension questions. A level's passages unlock once
  you've learned 70% of that level's words. Tap any word in a passage for
  its gloss, including inflected and reduplicated forms and multiword
  compounds. Missed comprehension questions feed the words back into review.
  A passage's spaced re-read (after 7 days) becomes a listening pass once
  every sentence has audio: the text stays hidden and about half the
  questions are audio-only.
- **Offline:** the app is a single page with a service worker, so once
  loaded it keeps working offline and loads instantly on repeat visits.
- **Speech:** there is no recorded audio for Indonesian. The trainer speaks
  every word and sentence with the browser's `id-ID` voice. Apple devices
  ship no Indonesian voice, so the speaker buttons are silent there.
- **Progress export/import:** the Progress tab can export your progress as
  text and import it back (for example, to move to a new device). Progress
  is otherwise kept only in this browser's local storage.

## Data

This is a static data pack for a language-agnostic vocab trainer (`key:
"id"`). The word list is a frequency-ranked selection, glossed from
Wiktionary, with example sentences and levels assigned by rule (see
"Sources and licences" and "Level bands" below). It's built from the shared
[`vocab-engine`](https://github.com/Bannerless-Studio/vocab-engine) (the UI
and drill logic, included here as a git submodule at `engine/`) plus this
repo's Indonesian data and Indonesian-specific pack-builder rules.

**Data quality.** Hand QA used a stratified sample of 180 words (60 per
level, seed 51) and 90 sentences (seed 52). 177 of 180 words had the right
primary sense, and the three misses now have gloss overrides. 498 of 507
sentence links were right. A synonym linked in place of the word (tiba "to
arrive" counted as datang) is gone: an audit of all 17,393 links finds no
synonym links. The frequency list is built from film subtitles and is
colloquial (gue, lo, nggak, banget, udah); colloquial spellings count
toward their formal word (udah toward sudah), and words like nggak,
gimana, sih, nih, tuh, kok and dong are glossed "(colloquial)" and kept to
A2 or higher. Sentences with the Jakarta pronouns gue/lo are left out, and
A1 examples prefer formal or neutral sentences. Tatoeba had fewer than two
usable sentences for several hundred words, so **461 simple sentences were
written for this pack** (marked `"src": "gen"` in `pack/sentences.json`,
exact count in `pack/attribution.json`); they are machine-written and
reviewed, but not by a native Indonesian speaker. Sexual content and
violence are kept out of A1/A2 sentences, and rape or abuse sentences are
left out at every level. Levels are frequency bands, not CEFR. Rules,
counts, seeds and further QA detail are in `tools/REPORT.md`; residual
known issues (wrong-sense links, unlinked compounds, sensitive-content
edge cases, and more) are tracked in `TODO.md`.

The 60 reading passages (`pack/passages.json`) were written for this pack
(`"src": "gen"`) from `tools/passages_src.json`, checked by an automated QA
pass but not by a native speaker. They enforce in-pack word coverage of at
least 95% at A1/A2 and 93% at B1, with a level budget on how many
higher-level words each passage may use. Per-passage numbers are in
`tools/REPORT_passages.md`.

### Sources and licences

| Data | Source | Licence | Used for |
|---|---|---|---|
| Spoken/subtitle frequency | [hermitdave/FrequencyWords](https://github.com/hermitdave/FrequencyWords) (`id_full.txt`, 2018 OpenSubtitles) | CC-BY-SA 4.0 | word ranking |
| Written/general frequency | [`wordfreq`](https://github.com/rspeer/wordfreq) Python package (`small_id`) | CC-BY-SA 4.0 | word ranking |
| Glosses, part of speech, affix and plural links | [kaikki.org](https://kaikki.org) Indonesian Wiktionary extract | CC-BY-SA 3.0 / GFDL (Wiktionary) | English glosses, POS, inflection map |
| POS tagging / lemmatisation (build time only) | [Stanza](https://stanfordnlp.github.io/stanza/) (Apache-2.0), Indonesian `gsd` model, trained on UD_Indonesian-GSD | CC BY-SA 4.0 (model training data) | corpus POS, lemma and sense choice; clitic splitting; sentence word links. The pack ships no model files. |
| Affix roots (build time only) | [Sastrawi](https://github.com/har07/PySastrawi) stemmer | MIT | root of me-/di- verbs |
| Example sentences | [Tatoeba](https://tatoeba.org) `ind_sentences_detailed.tsv` | CC-BY 2.0 FR | sentence text (contributor usernames in `pack/attribution.json`) |
| Sentence translations | Tatoeba `eng_sentences.tsv` + `ind-eng_links.tsv` | CC-BY 2.0 FR | English translations |
| Generated sentences | written for this pack, `tools/generated_sentences.tsv` | CC-BY-SA 4.0 | sentences for words Tatoeba covers with fewer than 2 usable sentences, marked `"src": "gen"` |

Licence: code MIT, pack data CC BY-SA 4.0, see LICENSE.

Tatoeba has only 18 permissively licensed Indonesian audio clips, so the pack
links none and relies on TTS. No licence is non-commercial. No graded
Indonesian word list is used or shipped.

### Level bands

Words are ranked A1/A2/B1 by a blended frequency score across the subtitle
list, `wordfreq`, and the tagged Tatoeba corpus (weighted toward Tatoeba,
since the subtitle list is colloquial), with a forced A1 core (days, months,
numbers, colours, pronouns, question words, core function words and set
phrases). This is a reproducible proxy for CEFR level, not an official
classification. Full detail is in the README's "Indonesian rules" content
that moved to `tools/README.md` and `engine/tools/packbuilder/langs/id.py`.

## Rebuild and publish

See `CLAUDE.md` for the pinned rebuild/check commands and `tools/README.md`
for what each file under `tools/` is and how it feeds the build. In short:
`python3 tools/build_pack.py` rebuilds the pack, `./build.sh` builds
`index.html`, and `./check.sh` must pass before every commit that touches
`index.html`.
