# Claude Watch setup (Windows)

Step-by-step guide to set up the `/watch` skill in Claude Code on Windows, plus a note-taking convention and an optional `/verify` command.

- `/watch` downloads a YouTube video or local file, pulls frames and the transcript, and lets Claude answer questions or write notes from it.
- Notes are verbatim: real steps, every spoken prompt quoted word for word, gaps flagged instead of invented.
- `/verify` is an optional triple-pass check for long or dense sources.

## What is in here

- `install-watch-skill.md`: the guide. Start here.
- `CLAUDE.md`: the note-taking rules. Copy it into the project folder you run `/watch` from.
- `.claude/commands/verify.md`: the `/verify` command. Copy it to the same path in your project.

## Credit and limits

- The `watch` plugin is not mine. It comes from [bradautomates/claude-video](https://github.com/bradautomates/claude-video). This repo only documents setting it up.
- Written October 2026 on Windows 11. Commands and plugin versions may have changed since.
- Windows only. The guide covers the PATH and fake-Python gotchas that cost time.

## Licence

MIT. See `LICENSE`.