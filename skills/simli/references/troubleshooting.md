# Troubleshooting Simli integrations

Start with `python3 scripts/check_setup.py`: it confirms the key, the face and the number of sessions running, without starting a session.

## Session won't start

| Message / status | Meaning | Fix |
|---|---|---|
| `401`, "Unauthorized", invalid API key | `SIMLI_API_KEY` wrong, revoked or missing in that process | Check the env var is loaded where the code runs (server, worker, container), and that there is no stray whitespace or quotes |
| `400`, `INVALID_FACE_ID`, face not found | Face ID doesn't exist | Use a preset (`bf13113a-355a-4d97-8b90-4edccd1da7b2`) or copy the ID from the dashboard |
| `403` `FACE_NOT_ALLOWED` | Face is private to another account | Use one of the account's own faces, or a preset |
| `403` on face creation | No Trinity-2 generations left on the plan | Use a preset, or upgrade the plan |
| `422` | Invalid framing: not 1:1 / 16:9 / 9:16, a side under 512, or a position outside 0–1 | Fix the `streaming_*` / `avatar_position_*` values |
| `429`, `RATE...` | The account is already running its maximum concurrent sessions | Stop leaked sessions (set `maxIdleTime`, stop on unload), or upgrade the plan |
| No minutes left / plan error | Out of streaming minutes | Top up or upgrade at https://app.simli.com |
| Token expired | The browser used an old session token | Fetch a new token right before creating each `SimliClient` |
| JS: `Too Many Retry Attempts Failed to connect` | Network: the browser can't reach Simli's media servers | Check firewall/VPN/corporate proxy; don't use `"p2p"` mode on restrictive networks |
| LiveKit: avatar never joins | Simli can't reach the LiveKit server | `LIVEKIT_URL` must be public (LiveKit Cloud or public self-hosted), not `localhost` |

## Session starts, but…

| Symptom | Likely cause | Fix |
|---|---|---|
| Black or no video | Video element not attached / not `autoPlay playsInline`; Pipecat before first speech; transport video disabled | Check the element; greet immediately; `video_out_enabled=True` |
| Avatar visible but doesn't move | No audio reaching Simli, or wrong format | Log bytes sent; check format ([audio.md](audio.md)); in Pipecat put `simli` after the TTS |
| Avatar freezes between sentences | `handleSilence: false` with bursty audio | Use `handleSilence: true` with `sendAudioData` |
| Crackles / artifacts with a live track | `handleSilence: true` with `listenToMediastreamTrack` | Use `handleSilence: false` for continuous tracks |
| Chipmunk / slow / robotic voice | Sample rate not 16 kHz (often OpenAI's 24 kHz) | Resample to 16 kHz |
| Static or loud noise | Float32 or big-endian data sent as PCM16, or a WAV header | Convert to `s16le`; strip headers |
| Voice heard twice or echo | Original agent audio played alongside Simli's | Play only Simli's audio element / track |
| No sound at all in the browser | Autoplay blocked | Start the session from a click; keep `<audio autoPlay>` |
| First words cut off | Agent spoke before the avatar was ready | Start speaking only after `start()` resolves / `wait_for_join()` |
| Avatar keeps talking after the user interrupts | Buffer not cleared | Call `ClearBuffer()` / `clearBuffer()` on the interruption event |
| Stretched or cropped video | Player aspect ratio ≠ stream size | Match CSS `aspect-ratio` / transport size to `streaming_width/height` |
| Session ends after a few minutes | `maxIdleTime` reached (no audio) or `maxSessionLength` reached | Raise them, or handle the `stop` event and offer a restart |
| Lip-sync drifts | Audio sent faster than real time from a non-buffered source, or mixed sources | Send through one path; let the SDK queue; don't mix `sendAudioData` and track listening |

## Before asking for help

Collect: SDK and framework versions, the face ID (not the API key), the exact error text, and the `session_id` (JS: the `destination` event; logs in Python and plugins). Then ask on Discord: https://discord.gg/yQx49zNF4d. Never paste the API key into issues, chats or logs.
