# Seedance video skill for Claude

An [Agent Skill](https://agentskills.io) that teaches Claude to plan and write **Seedance 2.5 / 2.0** video prompts —
and, when Claude is connected to the [AiuniVid MCP server](https://aiunivid.com/en/blog/seedance-mcp-server),
to **render them too**: quote the credit cost, wait for your yes, render a 480p draft, turn approved shots into
1080p finals and chain shots by their last frame.

Most Seedance skills stop at the prompt. This one closes the loop inside the chat.

## What it does

| | Skill alone | Skill + AiuniVid MCP |
|---|---|---|
| Plans shots (one beat per shot) | ✅ | ✅ |
| Writes prompts following the BytePlus Seedance prompt guide | ✅ | ✅ |
| Keeps characters consistent (reference roles, character sheets) | ✅ | ✅ |
| Quotes the exact credit cost before anything is spent | — | ✅ |
| Renders drafts and 1080p finals, chains shots by last frame | — | ✅ |

## Install

**Claude Code** — personal skill:

```bash
git clone https://github.com/AiuniVid/seedance-video-skill ~/.claude/skills/seedance-video
```

or put the folder in `.claude/skills/seedance-video/` inside a project.

**Claude apps** (paid plans) — download this repository as a ZIP and upload it as a custom skill in Settings.

**Other agents** that read the Agent Skills format (`SKILL.md`) can load the same folder.

## Connect rendering (optional)

Add `https://aiunivid.com/api/mcp` as a remote MCP server in Claude, ChatGPT, Claude Code, Cursor, VS Code, Codex or
Gemini CLI and sign in once (OAuth). Setup for each client: https://aiunivid.com/en/docs#mcp

Rendering uses pay-as-you-go AiuniVid credits (1 credit = USD 0.01). Every job is quoted before it is created,
and credits for a failed job are released automatically.

## Try it

> Make a three-shot vertical story: a courier delivers a parcel to the wrong door, the neighbour opens it, it's a cake.
> Stylized 3D. Quote it first.

## Files

- `SKILL.md` — the workflow Claude follows.
- `reference/prompt-templates.md` — fill-in prompt templates (story shot, hook, motion reference, storyboard,
  product clip, next shot from last frame, character sheet).

AiuniVid is an independent service, not made or endorsed by BytePlus or ByteDance.

## License

MIT
