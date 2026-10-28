# Installing the `/watch` skill in Claude Code

Step-by-step setup for a Claude Code instance to install and verify the `watch` plugin (lets Claude download a YouTube video/local file, extract frames, pull the transcript, and answer questions about it or take notes from it) — plus this repo's note-taking conventions and its `/verify` command.

**Windows only.** Run each step in order. Don't skip the verification checks — the Windows setup this was written on hit real gotchas (PATH not updating in an already-open terminal, Windows' fake `python` Store-alias stub) and each check below exists because of one of those.

---

## Step 1 — Install the Claude Code CLI

If `claude` is already available in a terminal, skip to Step 2.

```powershell
irm https://claude.ai/install.ps1 | iex
```

The installer may report that its install directory (typically `C:\Users\<you>\.local\bin`) isn't on PATH yet. If so, add it — either use System Properties → Environment Variables → Edit User PATH → New → add the path, **or** run:
```powershell
[Environment]::SetEnvironmentVariable('Path', [Environment]::GetEnvironmentVariable('Path','User') + ';C:\Users\<you>\.local\bin', 'User')
```

**⚠️ Gotcha:** any terminal/tool session already running (including the one you used to install) will **not** see the updated PATH — it was inherited at process start. Open a **brand new** terminal window before continuing.

**Verify:**
```powershell
claude --version
```

---

## Step 2 — Log in

```powershell
claude
```
Then inside the interactive session, run:
```
/login
```
and follow the prompts.

---

## Step 3 — Add the plugin marketplace

This registers the GitHub repo that hosts the `watch` plugin.

```powershell
claude plugin marketplace add bradautomates/claude-video
```

**Verify:** should print `Successfully added marketplace: claude-video`.

---

## Step 4 — Install the `watch` plugin

```powershell
claude plugin install watch@claude-video
```

**Verify:** should print `Successfully installed plugin: watch@claude-video (scope: user)`. This adds a `/watch` skill and a `SessionStart` hook (a one-line status message that appears when a new session starts, telling you what's still missing).

---

## Step 5 — Install the underlying dependencies

The skill itself needs three things on the system: a real Python (not a stub), `ffmpeg`/`ffprobe`, and `yt-dlp`. It ships its own installer for this.

**Locate the skill's install location first** — typically:
```
C:\Users\<you>\.claude\plugins\cache\claude-video\watch\<version>\skills\watch\
```

**Run its bundled installer:**
```powershell
python "<SKILL_DIR>\scripts\setup.py"
```

It prints the exact commands to run if anything's missing. On Windows those were:
```powershell
winget install Python.Python.3.12          # if python itself is missing/fake
winget install Gyan.FFmpeg
pip install --user yt-dlp
```

**⚠️ Gotcha:** Windows ships fake `python`/`python3` commands under `AppData\Local\Microsoft\WindowsApps` that just open the Microsoft Store when run — they are *not* a real Python and will silently fail. If `python --version` doesn't return a real version number, install one properly (`winget install Python.Python.3.12`) rather than trusting the pre-existing `python` command.

**⚠️ Gotcha:** freshly-installed tools (Python, ffmpeg, yt-dlp) won't be on PATH in a terminal session that was already open when you installed them — open a new terminal (or add their install directories to PATH manually for the current session) before the next step.

It also scaffolds a config file at `~/.config/watch/.env` (`0600` permissions) for optional Whisper API keys (Groq or OpenAI) — needed only as a transcript fallback for videos with no native captions. Skip this if you're fine with captions-only for now; the skill still works without it.

**Verify — run the structured preflight check:**
```powershell
python "<SKILL_DIR>\scripts\setup.py" --json
```
Look for `"can_proceed": true` and `"missing_binaries": []`. If binaries are still missing, it means PATH hasn't picked up the new installs yet — open a fresh terminal and re-check.

---

## Step 6 — Tighten the config file's permissions

The `.env` file can hold API keys, so lock it to your account only. `chmod` doesn't reliably work on NTFS — use `icacls` instead:
```powershell
$path = "$env:USERPROFILE\.config\watch\.env"
icacls $path /inheritance:r
icacls $path /grant:r "${env:USERNAME}:RW"
```

---

## Step 7 — Test it

Open a **fresh terminal** (important — see the PATH gotcha above), start Claude Code in a project folder, then:
```
/watch https://www.youtube.com/watch?v=<any short video id>
```
On first run it may ask a couple of setup preference questions (detail level, Whisper key). Confirm it downloads, extracts frames/captions, and returns a report with frame paths and a transcript.

---

## Step 8 — Add the note-taking conventions

Copy this repo's [CLAUDE.md](CLAUDE.md) into the root of the project folder you'll be running `/watch` from. It tells Claude Code to:
- Break videos into their real steps/sections and quote every prompt spoken to Claude word-for-word (reconstructed into one clean quote if it was spoken in fragments).
- Say explicitly when part of a prompt isn't fully readable/audible, rather than inventing wording.
- Note any follow-up corrections or standing rules given mid-video.
- Save notes into a `watch notes/` subfolder, named for the video's topic.
- Delete each video's temp working directory once its note is saved — per video, right after, not batched at the end.
- Process a list of video URLs one at a time, not in parallel.

This file is picked up automatically by Claude Code whenever a session's working directory is that project (or a subfolder of it) — no extra setup needed beyond placing the file there.

---

## Step 9 — Add the `/verify` command

Copy this repo's [.claude/commands/verify.md](.claude/commands/verify.md) into the same relative path (`.claude/commands/verify.md`) inside the project folder.

`/verify` is an **optional, on-demand** command — not something that runs automatically. Use it when a source (a video, PDF, ebook, academic paper, article) is long or dense enough that a single extraction pass could plausibly drop or misquote something, and getting it exactly right matters. It:
1. Reads the source once.
2. Produces 3 independent extraction passes from scratch, each without referencing the others.
3. Compares all 3 for missing quotes, wording mismatches, or invented structure the source never actually states.
4. Reconciles them into one corrected final note, re-checking the raw source to resolve any disagreement.
5. Reports briefly what the cross-check found (or confirms nothing was caught), then cleans up the 3 scratch drafts.

Skip it for short or low-stakes sources — it triples the read/write cost, so it's meant for the cases where that's worth it, not as a default.

---

## Done

At this point your Claude Code setup has: `/watch` installed and verified, the verbatim-note-taking conventions in `CLAUDE.md`, and `/verify` available for spot-checking high-stakes notes.
