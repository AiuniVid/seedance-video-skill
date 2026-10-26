# Seedance video skill for Claude

An [Agent Skill](https://agentskills.io) that teaches Claude to plan and write **Seedance 2.5 / 2.0** video prompts —
and, when Claude is connected to the [AiuniVid MCP server](https://aiunivid.com/en/blog/seedance-mcp-server),
to **render them too**: quote the credit cost, wait for your yes, render a 480p draft, turn approved shots into
1080p finals and chain shots by their last frame.

Most Seedance skills stop at the prompt. This one closes the loop inside the chat. In Claude Code, the plugin in this
repository installs the skill and the MCP server together.

## What it does

| | Skill alone | Skill + AiuniVid MCP |
|---|---|---|
| Plans shots (one beat per shot) | ✅ | ✅ |
| Writes prompts following the BytePlus Seedance prompt guide | ✅ | ✅ |
| Keeps characters consistent (reference roles, character sheets) | ✅ | ✅ |
| Quotes the exact credit cost before anything is spent | — | ✅ |
| Renders drafts and 1080p finals, chains shots by last frame | — | ✅ |

## Install

### Claude Code: plugin (skill + MCP server)

```bash
claude plugin marketplace add AiuniVid/seedance-video-skill
claude plugin install seedance-video@aiunivid
```

On Claude Code 2.1.275 or later, one command does both. Inside a session:

```
/plugin install seedance-video --marketplace AiuniVid/seedance-video-skill
```

or in the terminal, `claude plugin install seedance-video --marketplace AiuniVid/seedance-video-skill`.

Then run `/mcp`, choose `aiunivid` and sign in once in your browser (OAuth, no API key to copy). Claude loads the
skill whenever you ask for a Seedance video; you can also call it as `/seedance-video:seedance-video`.
If you already added the server yourself with `claude mcp add`, Claude Code keeps your entry and skips the plugin's copy.

### Claude Code: skill only

```bash
git clone --depth 1 https://github.com/AiuniVid/seedance-video-skill
cp -r seedance-video-skill/skills/seedance-video ~/.claude/skills/
```

That installs it for all your projects. To share it with a team, copy the folder to `.claude/skills/seedance-video/`
inside the repository instead.

### Claude apps (paid plans)

Download `seedance-video.zip` from the [latest release](https://github.com/AiuniVid/seedance-video-skill/releases/latest)
and upload it as a custom skill in Settings. To render, also add `https://aiunivid.com/api/mcp` as a custom connector.

### Other agents

Agents that read the Agent Skills format (`SKILL.md`) can load `skills/seedance-video/`.

## Connect rendering in other MCP clients

The server is `https://aiunivid.com/api/mcp` (Streamable HTTP). Every client below signs in with OAuth in your
browser; full steps for each one are at https://aiunivid.com/en/docs#mcp.

**Claude, ChatGPT** — add the URL as a custom connector.

**Claude Code** (without the plugin) — then run `/mcp` and sign in:

```bash
claude mcp add --transport http --scope user aiunivid https://aiunivid.com/api/mcp
```

**Codex**

```bash
codex mcp add aiunivid --url https://aiunivid.com/api/mcp
codex mcp login aiunivid
```

**Cursor** — `~/.cursor/mcp.json`, then click "Needs login" in Cursor Settings › MCP:

```json
{
  "mcpServers": {
    "aiunivid": { "url": "https://aiunivid.com/api/mcp" }
  }
}
```

**VS Code (Copilot)** — `.vscode/mcp.json`, then start the server and sign in:

```json
{
  "servers": {
    "aiunivid": { "type": "http", "url": "https://aiunivid.com/api/mcp" }
  }
}
```

**Gemini CLI** — then restart it and run `/mcp auth aiunivid`:

```bash
gemini mcp add --transport http --scope user aiunivid https://aiunivid.com/api/mcp
```

**OpenCode** — `~/.config/opencode/opencode.json`, then run `opencode mcp auth aiunivid`:

```json
{
  "mcp": {
    "aiunivid": { "type": "remote", "url": "https://aiunivid.com/api/mcp", "enabled": true }
  }
}
```

Rendering uses pay-as-you-go AiuniVid credits (1 credit = USD 0.01). Every job is quoted before it is created,
and credits for a failed job are released automatically.

## Try it

> Make a three-shot vertical story: a courier delivers a parcel to the wrong door, the neighbour opens it, it's a cake.
> Stylized 3D. Quote it first.

## What's in this repository

```
.claude-plugin/
  plugin.json        Claude Code plugin manifest
  marketplace.json   lets `claude plugin marketplace add AiuniVid/seedance-video-skill` find the plugin
  icon.png
.mcp.json            the AiuniVid MCP server the plugin adds (no keys; you sign in with OAuth)
skills/seedance-video/
  SKILL.md           the workflow Claude follows
  reference/prompt-templates.md
                     fill-in templates: story shot, hook, motion reference, storyboard,
                     product clip, next shot from last frame, character sheet
```

AiuniVid is an independent service, not made or endorsed by BytePlus or ByteDance.
[Privacy policy](https://aiunivid.com/en/privacy-policy) · [Terms](https://aiunivid.com/en/terms-of-service)

## License

MIT
