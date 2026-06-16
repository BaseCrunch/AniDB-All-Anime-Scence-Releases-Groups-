# AniDB All Anime Scence Releases Groups

> A local AniDB release-group import pack for anime torrent matching, group recognition, alias detection, and audio/subtitle hinting.

![Data](https://img.shields.io/badge/groups-17%2C654-blue)
![Format](https://img.shields.io/badge/formats-JSON%20%2B%20CSV-green)
![Source](https://img.shields.io/badge/source-AniDB%20HTML%20import-purple)
![Project Nava](https://img.shields.io/badge/built%20for-Project%20Nava-ff69b4)

## What is this?

**AniDB All Anime Scence Releases Groups** is a cleaned local import of AniDB anime release groups. It was built from user-provided AniDB HTML group-list pages and converted into import-friendly JSON and CSV files.

The main goal is simple:

**Give Project Nava a local release-group brain so it can understand anime torrent names better without constantly depending on AniDB lookups.**

This pack helps Nava recognize groups like `Vodes`, `A&C Fansub`, `SubsPlease`, `Judas`, `EMBER`, `LostYears`, and thousands more. It can also help with group aliases, short tags, likely subtitle languages, likely audio languages, and release matching confidence.

> Note: The project name keeps the original spelling, **Scence**, but this README also uses **scene** where it means anime release/fansub/encode groups.

## Package stats

| Item | Count |
|---|---:|
| HTML files parsed | 589 |
| Total rows parsed | 17,654 |
| Expected page math | 588 pages × 30 + 14 final-page rows = 17,654 |
| Unique AniDB group IDs | 17,654 |
| Unique group names | 17,627 |
| Entries with language hints | 16,354 |
| Entries with more than one alias | 10,001 |
| Entries with short tags | 17,654 |

## Files included

| File | Purpose |
|---|---|
| `release_groups.import.json` | Main Nava-ready import file. Use this for app integration. |
| `release_groups.import.csv` | Spreadsheet-friendly copy for review, filtering, manual cleanup, or debugging. |
| `ANIDB_GROUP_IMPORT_SUMMARY.txt` | Small summary of the import run and expected counts. |

## Recommended Nava install path

Place:

```text
release_groups.import.json
```

inside Nava's release-groups folder opened by the **Open Groups** button.

The import was designed so Nava can load the file locally and use it as a release-group registry for anime torrent matching.

## Why this matters for torrent matching

Anime torrent titles are messy. The group can appear as a prefix, suffix, short tag, alias, or partial name:

```text
[Vodes] Tensei Shitara Slime Datta Ken S01 [BD 1080p HEVC] [Dual-Audio]
[A&C] One Piece E001-E130 [DVD Dual-Audio]
[SubsPlease] Some Anime - 01 (1080p) [ABC12345]
[Judas] Anime Title S02 [BD 1080p][HEVC x265][10bit]
```

This import gives Nava a way to answer questions like:

- Is `[Vodes]` a known anime release group?
- Is `A&C` an alias for `A&C Fansub`?
- Does this group usually appear with Japanese audio, English subtitles, dual audio, or other language hints?
- Should this torrent receive a group-recognition bonus?
- Should this group be treated as known, unknown, favorite, blocked, or manually reviewed?

## Example JSON record

```json
{
  "name": "Vodes",
  "normalizedKey": "",
  "aniDbGroupId": 16852,
  "aniDbUrl": "https://anidb.net/group/16852",
  "groupType": "release-group",
  "shortTags": [
    "Vodes"
  ],
  "aliases": [
    "Vodes"
  ],
  "languages": [
    "audio: Japanese",
    "audio: English",
    "audio: German",
    "audio: Korean",
    "sub: English",
    "sub: German",
    "sub: French",
    "sub: Italian",
    "sub: Spanish",
    "sub: Russian",
    "sub: Arabic",
    "sub: Portuguese"
  ],
  "audioProfile": "dual-audio-candidate",
  "subtitleProfile": "english-sub-candidate",
  "sourceHints": [],
  "knownFrom": [
    "AniDBHtmlUserImport"
  ],
  "confidence": "imported-html",
  "favorite": false,
  "blocked": false,
  "notes": "AniDB user-provided HTML group list import; last release 03.08.2021; rating N/A (8)",
  "importedAtUtc": "2026-06-16T00:38:50.241296+00:00"
}
```

## JSON schema overview

Each entry contains:

| Field | Meaning |
|---|---|
| `name` | Main AniDB group name. |
| `normalizedKey` | Reserved normalization key. Currently empty in this import. Nava can generate its own key at load time. |
| `aniDbGroupId` | AniDB numeric group ID. |
| `aniDbUrl` | Direct AniDB group URL. |
| `groupType` | Usually `release-group`. |
| `shortTags` | Short tags commonly used in release names. Example: `A&C`. |
| `aliases` | Known names/tags for the group. Use this for matching torrent group tags. |
| `languages` | Audio/subtitle hints found from the AniDB group list. |
| `audioProfile` | Nava-friendly audio hint such as `dual-audio-candidate` or `japanese-audio / english-sub-candidate`. |
| `subtitleProfile` | Nava-friendly subtitle hint such as `english-sub-candidate` or `subtitle-candidate`. |
| `sourceHints` | Reserved source-quality hints. Empty in this import. |
| `knownFrom` | Import provenance. Current value: `AniDBHtmlUserImport`. |
| `confidence` | Import confidence label. Current value: `imported-html`. |
| `favorite` | Manual Nava flag. Defaults to `false`. |
| `blocked` | Manual Nava flag. Defaults to `false`. |
| `notes` | Human-readable AniDB import notes, including last-release date and rating when available. |
| `importedAtUtc` | UTC timestamp for when the entry was generated. |

## CSV columns

The CSV version contains these columns:

```text
name, shortTags, aliases, languages, audioProfile, subtitleProfile, sourceHints, favorite, blocked, notes, aniDbGroupId, aniDbUrl
```

The CSV is best for manual review. The JSON is best for Nava runtime/import use.

## Suggested Nava matching behavior

Recommended matching order:

1. Extract bracket/group tags from torrent names.
2. Compare the extracted tag against `shortTags` first.
3. Compare against `aliases` second.
4. Compare against `name` third.
5. Generate a local normalized key at load time, because `normalizedKey` is intentionally empty in this raw import.
6. Treat `confidence: imported-html` as a known-group signal, not as a perfect quality guarantee.
7. Use `audioProfile` and `subtitleProfile` as hints, not hard truth.
8. Allow Nava's stronger evidence to override this import, especially exact info-hash matches, SeaDex matches, user overrides, and manual group rules.

## Recommended scoring use

This data should improve scoring, but it should not completely decide scoring by itself.

Good uses:

- Add a small-to-medium bonus when a torrent group tag matches a known AniDB group.
- Add a stronger bonus when the group also matches SeaDex or exact BTIH evidence.
- Detect known aliases like `A&C` → `A&C Fansub`.
- Help explain why Nava trusts a candidate.
- Pre-fill group metadata in the release quality explanation panel.
- Show language/audio/subtitle hints before playback.

Avoid using this data to:

- Automatically block a torrent just because the group is unknown.
- Assume every listed group is high quality.
- Assume `dual-audio-candidate` means every single release from that group is dual audio.
- Replace exact hash checks, episode mapping, torrent file inspection, or user overrides.

## Suggested merge rules

When importing into an existing Nava group registry:

- Do **not** overwrite user-edited `favorite` flags.
- Do **not** overwrite user-edited `blocked` flags.
- Do **not** downgrade manually curated notes.
- Merge aliases instead of replacing aliases.
- Keep AniDB ID as the stable external ID.
- If two records share the same display name, prefer AniDB group ID as the unique key.

## Example use cases in Project Nava

### 1. Better candidate normalization

Nava can recognize the release group before scoring:

```text
[Vodes] Tensei Shitara Slime Datta Ken S01 [BD 1080p HEVC] [Dual-Audio]
```

Possible normalized metadata:

```text
Group: Vodes
Known group: yes
Audio hint: dual-audio-candidate
Subtitle hint: english-sub-candidate
AniDB group: https://anidb.net/group/16852
```

### 2. Alias matching

```text
[A&C] One Piece E001-E130 [DVD Dual-Audio]
```

Can resolve to:

```text
Group: A&C Fansub
Matched alias/tag: A&C
AniDB group ID: 1283
```

### 3. Better user-facing explanations

Instead of saying only:

```text
Matched group tag.
```

Nava can say:

```text
Matched known AniDB release group: Vodes.
AniDB language hints suggest this group has Japanese/English audio entries and English subtitle entries.
This is a group-recognition hint only; exact hash and SeaDex evidence still rank higher.
```

## Important limitations

This import is useful, but it is not magic.

- AniDB group metadata can be old or incomplete.
- Some groups changed naming over time.
- Some groups released different audio/subtitle combinations depending on the anime.
- Some aliases are ambiguous.
- `normalizedKey` is empty in every record and should be generated by Nava.
- `sourceHints` is empty in every record and can be filled later by Nava-specific rules.
- Language hints should be treated as candidates, not guaranteed release properties.

## Data freshness

The import was generated from local AniDB HTML files and marked with `importedAtUtc` timestamps on **2026-06-16 UTC**.

Because this is a local snapshot, future AniDB changes will not appear unless the import is regenerated.

## Suggested future upgrades

Useful next steps for Project Nava:

- Generate `normalizedKey` during import.
- Add a conflict detector for duplicate or ambiguous short tags.
- Add a manual review screen for high-value groups.
- Add source-quality hints such as `BD`, `WEB`, `DVD`, `remux`, `mini-encode`, or `scene` when known.
- Add user-maintained overrides for trusted groups, blocked groups, and preferred groups.
- Add group history, such as active years and last-known release date.
- Add a small SQLite cache for fast runtime lookup.
- Add a one-click backup/export for edited group rules.

## Quick validation commands

Count JSON entries:

```bash
python - <<'PY'
import json
with open('release_groups.import.json', 'r', encoding='utf-8') as f:
    data = json.load(f)
print(len(data))
PY
```

Check for a group by name:

```bash
python - <<'PY'
import json
needle = 'Vodes'.lower()
with open('release_groups.import.json', 'r', encoding='utf-8') as f:
    data = json.load(f)
for g in data:
    names = [g.get('name', '')] + g.get('aliases', []) + g.get('shortTags', [])
    if any(needle == n.lower() for n in names):
        print(g)
PY
```

Preview top-level fields:

```bash
python - <<'PY'
import json
with open('release_groups.import.json', 'r', encoding='utf-8') as f:
    data = json.load(f)
print(data[0].keys())
PY
```

## Recommended folder layout

```text
AniDB-All-Anime-Scence-Releases-Groups/
├─ README.md
├─ release_groups.import.json
├─ release_groups.import.csv
└─ ANIDB_GROUP_IMPORT_SUMMARY.txt
```

## Credits

- Source data: AniDB group-list HTML pages provided by the user.
- Import target: Project Nava release-group registry.
- Purpose: local anime release-group recognition for better torrent normalization, scoring, and explanation.

## Disclaimer

This project is an unofficial local data import. It is not affiliated with AniDB. Use it as metadata and matching assistance only. Always allow user overrides and stronger source evidence to take priority.
