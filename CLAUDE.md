# Instructions for this project

## Taking notes from YouTube videos (via /watch)

When asked to watch a YouTube video and take notes / summarize it in detail, produce a **verbatim, detailed breakdown**, not a loose summary:

- Break the video into its actual steps/sections as presented (use the presenter's own step numbers/titles when they give them).
- For each step, write a short description of what's being done/shown.
- **Quote every prompt the person speaks or types to Claude word-for-word**, pulled directly from the transcript — not paraphrased. Use blockquotes so they're easy to copy-paste and run later.
- If a prompt is spoken in fragments across the transcript (interrupted by narration to camera), reconstruct it into one clean, continuous quote in the order it was actually said — don't paraphrase the content itself.
- If part of an on-screen prompt/instruction isn't fully readable from captions (video pans past it, audio unclear), say so explicitly rather than inventing wording.
- Note any follow-up corrections or standing rules the person gives Claude (e.g. "always do X from now on"), not just the initial prompt.
- Keep source metadata: video title, uploader, URL, duration.

Reference example of the expected depth/format: `watch notes/claude-cowork-setup-playbook.md`.

## Where to save notes

Save all notes generated from `/watch` (or any video-note-taking task) into the `watch notes/` folder in this project, as a `.md` file named for the video's topic.

## Cleanup

Once the note `.md` file for a video has been written and saved to `watch notes/`, delete that video's temp working directory (the download/frames folder printed at the end of the watch run, e.g. under the system temp dir) with `rm -rf`. Do this per video right after its note is done — don't leave it until the end of a batch.

## Processing multiple videos

When given a list of video URLs, process them one at a time (not in parallel): watch, write the note, clean up temp files, then move to the next.
