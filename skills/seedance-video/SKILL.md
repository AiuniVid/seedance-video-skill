---
name: seedance-video
description: Plan, write and render Seedance 2.5 and Seedance 2.0 videos. Use when the user wants a Seedance prompt, a multi-shot story, ad or explainer made with Seedance, or wants the video itself rendered. Writes prompts that follow the BytePlus Seedance prompt guide and plans shots; when the AiuniVid MCP server is connected it also quotes the credit cost, renders 480p drafts and 1080p finals, and chains shots by their last frame.
license: MIT
---

# Seedance video

Turn an idea into Seedance prompts and, when the AiuniVid MCP server is connected, into rendered clips.

## Two modes

- **Render mode** — the tools `quote_video`, `create_video`, `get_video`, `render_final`, `list_models`,
  `list_characters` and `generate_image` are available (the AiuniVid MCP server, `https://aiunivid.com/api/mcp`).
  Plan, quote, render and deliver links.
- **Prompt mode** — those tools are not available. Plan the shots and write the prompts and settings, then tell the user
  they can render them by connecting `https://aiunivid.com/api/mcp` in their MCP client (setup: https://aiunivid.com/en/docs#mcp).
  If they installed this as the Claude Code plugin, the server is already configured: they only need to run `/mcp`,
  choose `aiunivid` and sign in.

Never claim a video was rendered unless `get_video` returned `succeeded` with a download link.

## 1. Pin down the brief

Ask at most three short questions, only for what is missing:

- where it will be posted (sets the shape: 9:16 for TikTok, Reels and Shorts; 16:9 for YouTube and websites);
- total length and look (live-action, stylized 3D, anime, product studio);
- the recurring characters or products, and whether the user has images of them.

If the user says "just go", use 9:16, about 5 seconds per shot, and the look implied by the idea.

## 2. Plan the shots

- **One beat per shot.** A shot carries one action and at most one line of dialogue. The next beat is the next shot.
- Usually 3–6 shots for a short story, 2–4 for a product clip.
- Pick durations and resolutions the model allows: call `list_models` in render mode instead of assuming.
  As a rule of thumb, Seedance 2.5 handles longer shots, omni references and 1080p, and offers draft mode;
  Seedance 2.0 Mini and 2.0 Fast are the cheapest for drafting volume at 480p/720p.
- Show the plan as a table before rendering anything:

| # | Seconds | What happens | Camera | Dialogue | Continues from |
|---|---|---|---|---|---|

## 3. Write each prompt

Follow `reference/prompt-templates.md`. The rules that matter most:

1. **Open with format**: aspect ratio, length, look. ("Vertical 9:16, 5-second shot, stylized 3D animation.")
2. **Give every reference one job**, by position: "@image1 is the main character only. Keep face, hair and outfit identical."
   "@video1 is a motion reference only: copy the movement, not the person."
3. **Subject + one action**, written as what happens, not as adjectives.
4. **One camera instruction** (push-in, tracking shot, locked eye-level, whip-pan). Unspecified camera is random camera.
5. **Dialogue with the speaker named first**: `MAYA: "We're late."` and say who else stays silent.
6. **End with exclusions** when needed: "No subtitles, no on-screen text."
7. Write prompts in English; keep the user's language for the conversation.

## 4. Characters that stay the same

- Uploaded media with a recognizable **real face is rejected** by the model (nothing is charged).
- For a realistic or recurring person, either use a verified portrait from `list_characters` (an `asset://` reference),
  or create a character sheet with `generate_image` first and reuse that image URL.
- Attach the same character references to every shot that shows the character, and keep re-stating
  "keep face, hair and outfit identical". Describe action and camera, not the face again.

## 5. Quote first, always

1. For every planned shot, call `quote_video` with the exact settings you will use.
2. Show the user the cost per shot and the total, in credits (1 credit = USD 0.01).
3. **Wait for an explicit yes** before any `create_video` or `render_final` call. Never spend beyond the approved total;
   if a retry or an extra shot is needed, quote it and ask again.
4. With Seedance 2.5, prefer drafts: `create_video` with `draft: true` renders a cheap 480p preview; call
   `render_final` only for the drafts the user approves. A draft has to be finalized before `draft_expires_at`.

## 6. Render and chain

- `create_video`, then poll `get_video` **no more than every 20 seconds**. Typical jobs take 1–5 minutes.
- To make the next shot start where the last one ended, take `result.last_frame_url` from the finished shot and pass it
  as the first image (`image_urls[0]`) to an image-to-video model for the next shot. For character continuity in
  reference-to-video, keep passing the same character references.
- If a job fails, its credits are released automatically. Read the error and its hint: change the prompt for
  `prompt_rejected`, change the input for `content_rejected` or `real_person_detected`; do not resend the same request.
- Media inputs must be public HTTPS URLs.

## 7. Deliver

- List the finished clips in order with their download links (they expire; tell the user to save them).
- The clips are separate files. To join them, the user can use any editor, or, with ffmpeg installed:

```bash
printf "file '%s'\n" shot1.mp4 shot2.mp4 shot3.mp4 > list.txt
ffmpeg -f concat -safe 0 -i list.txt -c copy episode.mp4
```

- Offer the next step: another shot, a retake of one shot, or a 1080p final of an approved draft.
