# Simli agent skills

Give your coding agent [Simli](https://simli.com) in one command: real-time, lip-synced talking avatars for voice agents and apps.

```bash
npx skills add simliai/skills
```

Then ask your agent something like:

- "Add a Simli avatar to my LiveKit agent"
- "Give my Pipecat bot a face, 16:9"
- "Put a talking avatar on this Next.js page that speaks my ElevenLabs agent's replies"
- "Create a custom avatar from `me.jpg`"
- "The avatar is frozen / sounds like a chipmunk — fix it"

## What's inside

`skills/simli/` — one skill, loaded on demand:

| File | Covers |
|---|---|
| `SKILL.md` | Picking the integration path, keys and faces, rules for safe and good-looking sessions, definition of done |
| `references/livekit.md` | LiveKit Agents plugin, start order, Trinity-2 config workaround |
| `references/pipecat.md` | Pipecat `SimliVideoService`, pipeline placement, known issues |
| `references/browser-js.md` | `simli-client`: server token route, React component, ElevenLabs / OpenAI Realtime / Vapi / custom TTS |
| `references/python-sdk.md` | `simli-ai`: sessions, renderers, frames, errors |
| `references/trinity-2.md` | Video size, avatar position, camera, backgrounds, creating avatars from a photo |
| `references/audio.md` | PCM16 16 kHz mono, conversion and resampling |
| `references/troubleshooting.md` | Error codes and symptoms → fixes |
| `scripts/check_setup.py` | Checks the API key, face and an audio file without starting a session |

## Install without the CLI

Copy `skills/simli` into your agent's skills folder, for example `~/.claude/skills/simli` (Claude Code, personal) or `.claude/skills/simli` (Claude Code, project).

## Links

- Docs: https://docs.simli.com (agent index: https://docs.simli.com/llms.txt)
- Dashboard and API keys: https://app.simli.com
- Discord: https://discord.gg/yQx49zNF4d

## License

MIT
