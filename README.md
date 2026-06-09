# SRTranslate

SRTranslate is a subtitle translation tool for `.srt` files. It reads an SRT file, translates the cues with Google Translate through `deep-translator`, preserves tags when possible, and writes a new translated subtitle file next to the original.

## Features

- Translate a single `.srt` file from the command line.
- Auto-detect the source language when `SOURCE_LANG=auto`.
- Group adjacent subtitle cues to improve translation quality and reduce network overhead.
- Cache translations in memory and, by default, on disk.
- Keep basic subtitle formatting and HTML-like tags intact.
- Offer a local download link when the translation finishes.

## Requirements

- Python 3.10+ is recommended.
- A virtual environment is strongly recommended.

## Installation

```bash
git clone git@github.com:yobns/SRTranslate.git
cd translate
python -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
```

If you already have the project open with a prepared virtual environment, you can reuse it directly.

## Usage

Translate a specific subtitle file:

```bash
python run_deep.py path/to/file.srt
```

If you do not pass a file, the script looks for the first `.srt` file in the current folder.

The program will ask for the target language when running in an interactive terminal. If no answer is provided, it defaults to French (`fr`).

The translated file is saved as:

```text
original_name_<target_lang>.srt
```

For example, `movie.srt` translated to French becomes `movie_fr.srt`.

## Helpful Scripts

Validate an SRT file:

```bash
python scripts/validate_srt.py path/to/file.srt
```

Format an SRT file in place:

```bash
python scripts/format_srt.py path/to/file.srt
```

## Environment Variables

These variables let you tune the translator without changing code:

- `INPUT_SRT`: explicit input file path.
- `TARGET_LANG`: target language code, for example `fr`, `en`, `es`, `ar`, `he`, or `zh-TW`.
- `SOURCE_LANG`: source language code, default `auto`.
- `GROUP_DEEP`: set to `0` to translate cue by cue instead of grouped translation.
- `FAST_MODE`: set to `1` to use larger groups and moderate concurrency.
- `TRANSLATE_CONCURRENCY`: number of concurrent translation requests.
- `GROUP_MAX_CHARS`: max characters per group.
- `GROUP_MAX_BLOCKS`: max subtitle cues per group.
- `GROUP_MAX_GAP_MS`: max gap in milliseconds between grouped cues.
- `TRANSLATE_CACHE`: set to `0` to disable the SQLite cache.
- `TRANSLATE_CACHE_PATH`: path to the SQLite cache file, default `.translate_cache.sqlite`.
- `OFFER_DOWNLOAD`: set to `0` to skip the local download prompt.
- `AUTO_DOWNLOAD`: set to `1` to open the download link automatically.

Example:

```bash
TARGET_LANG=fr SOURCE_LANG=auto FAST_MODE=1 python run_deep.py movie.srt
```

## How It Works

1. The script loads the SRT file with `pysrt`.
2. It detects the dominant language when needed.
3. It groups cues according to timing and length heuristics.
4. It translates the text through `GoogleTranslator`.
5. It restores protected tags, normalizes the text, and saves the translated SRT.

## Output And Cache

- Translations are written next to the source file by default.
- When download mode is accepted, the translated file is archived into an `archive/` folder beside the source file and the original input file is removed.
- Repeated translations are accelerated by a memory cache and an optional SQLite cache.

## Notes

- The old HTTP API is no longer part of the main workflow.
- If a command fails because a dependency is missing, make sure you are running inside `.venv`.
