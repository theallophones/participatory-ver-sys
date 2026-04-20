# Participatory Version Systems

A research prototype for studying how listeners navigate multiple realizations of the same musical work.  
Developed as part of a Master of Science thesis in Music Technology at Georgia Institute of Technology.

> Salimi, P. (2026). *Songs as Probability Clouds: Investigating Musical Preference Through Participatory Version Systems*. Georgia Institute of Technology.

---

## What This Is

This system is a browser-based interactive listening platform that lets participants control multiple simultaneous musical dimensions of a song — lyrics, groove, solo performance, spatial rendering, and backing vocals — while all interactions are logged in real time. The resulting behavioral traces form the primary dataset for studying how musical preferences emerge through active exploration rather than retrospective rating.

The repository contains three components:

| File | Role |
|---|---|
| `app/study_interface.py` | Main research instrument. Participants navigate the version space; all interactions are logged and submitted to a Google Sheets backend. |
| `app/passive_listener.py` | Probabilistic streaming interface. Versions are selected by inverse-rank weighting based on community preference scores. |
| `app/artist_dashboard.py` | Artist-facing upload and stem configuration interface. |

---

## Setup

**Requirements:** Python 3.9+

```bash
git clone https://github.com/theallophones/participatory-ver-sys
cd participatory-ver-sys
pip install -r requirements.txt
```

**Run the study interface:**
```bash
streamlit run app/study_interface.py
```

**Run the passive streaming interface:**
```bash
streamlit run app/passive_listener.py
```

**Run the artist dashboard:**
```bash
streamlit run app/artist_dashboard.py
```

Audio stems are served from a GitHub-hosted audio repository. The URL map is defined in `app/study_interface.py` under `audio_map`.

---

## Qualtrics Integration

The active contributor interface receives `pid` (participant ID) and `song` (song ID) as URL parameters, injected by Qualtrics:

```
https://[your-streamlit-url]?pid=RESPONDENT_ID&song=song1
```

On session completion, interaction data is posted to a Google Sheets webhook. See `logging/sheets_webhook.md` for the data schema.

---

## Interaction Logging

Every participant action generates a structured event. The canonical schema is in `data/event_schema.json`. An example session log is in `data/example_session.json`.

Logged events:
- `feature_switch` — listener changes a musical dimension (lyrics, groove, solo, spatial, backing vocals)
- `playback` — play and pause with track position
- `seek` — navigation within the waveform
- Session start, completion, and final configuration snapshot

---

## Thesis Context

This platform was developed alongside the *How Much Lyrics Matter* study (Chapter 3), which established that lyric-music congruence affects listener perception asymmetrically — happy songs showing significantly greater sensitivity to lyrical mismatch than sad songs. The interactive listening system extends this investigation by allowing multiple dimensions to vary simultaneously and observing how listeners navigate the resulting version space.

The probability cloud framework (Chapter 4) formalizes the version space as a probability distribution over possible realizations, with inverse-rank weighting determining which version a passive listener encounters on any given play.

---

## License

MIT — see LICENSE. Audio content belongs to the original artist and is used for research purposes only.
