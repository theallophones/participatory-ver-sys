# Interaction Logging: Google Sheets Pipeline

The active contributor interface logs all interactions client-side (JavaScript/localStorage)  
and submits them to a Google Apps Script webhook on session completion.

## Data Flow

```
Listener action (feature switch, play, pause, seek)
        ↓
window.logInteraction() / window.logPlayPause() / window.logSeek()
        ↓
localStorage['interaction_log']  (accumulated during session)
        ↓
Submit button → POST to Google Sheets webhook
        ↓
Google Sheet row: participant_id | song_id | timestamp | interaction_log | final_config
```

## Webhook

The webhook URL is set in `app/active_contributor.py` as `GOOGLE_SHEET_WEBHOOK`.  
It points to a Google Apps Script web app that accepts POST requests and appends rows.

## Payload Schema

```json
{
  "participant_id": "string (from Qualtrics URL param)",
  "song_id":        "string (from Qualtrics URL param)",
  "timestamp":      "ISO-8601 string",
  "interaction_log": "[{timestamp, control, from, to}, ...]  (JSON string)",
  "final_lyrics":   "A | B | C",
  "final_groove":   "A | B | C",
  "final_solo":     "A | B | C",
  "final_spatialize": "narrow | wide",
  "final_backing":  "on | off"
}
```

## interaction_log Event Types

| control | from / to values | description |
|---|---|---|
| `lyrics` | A, B, C | Listener switched lyric version |
| `groove` | A, B, C | Listener switched groove version |
| `solo` | A, B, C | Listener switched solo take |
| `spatialize` | narrow, wide | Listener toggled spatial rendering |
| `backVocals` | off, on | Listener toggled backing vocals |
| `playback` | — | Play or pause event (has `action` and `time` fields) |
| `seek` | seconds (float) | Waveform navigation (has `direction` field) |

## Known Gap

Feature switch events currently do not include `playback_pos_s` (audio position in seconds  
at the moment of the switch). This limits the ability to compute time-spent-per-configuration  
from the interaction log alone. Timestamps can be used as a proxy but are less precise.

To fix: add `grooveAWS.getCurrentTime()` to each `logInteraction()` call in the JavaScript.

## Canonical Schema

See `data/event_schema.json` for the full documented schema including all field definitions  
and derived variable descriptions used in the analysis plan (Chapter 6).
