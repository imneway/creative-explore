---
name: creative-explore
description: Iterative creative exploration for generating brand-compliant marketing and operational images using AI. Use this skill whenever the user wants to create KV designs, marketing visuals, operational graphics, event posters, or any brand-compliant image assets. Also trigger when the user provides brand rules together with image generation needs, mentions "creative exploration", asks to iterate on generated visuals, or wants to brainstorm visual directions for a campaign. Works with any brand that has a rules document.
---

# Creative Exploration

Generate brand-compliant marketing and operational images through iterative creative exploration. Read the brand rules, understand the brief, brainstorm creative directions, generate quick drafts, iterate with the user, and archive results.

## Setup

On the first use in a session, establish the workspace:

1. **Workspace path** — Ask: "Is the current directory your project workspace? If not, please provide the path." The workspace is where brand rules, assets, and output archives live (often in a shared Dropbox).
2. **Brand rules** — Look for a brand rules markdown file in the workspace (e.g. `*-Brand-Rules.md`). Read it thoroughly — it defines colors, typography, visual language, and constraints that every generated image must follow. Pay special attention to any AI-specific prompt guidelines in the rules.
3. **Brand assets** — Scan the workspace `assets/` directory to understand available reference images, patterns, icons, and logos.
4. **Task requirements** — Prompt the user to provide:
   - The brief (what needs to be created, dimensions, format)
   - Task requirements doc if available (Feishu/Lark URL, or paste directly)
   - Any additional style reference images they want to incorporate
   - Text requirements or key messages

If the user provides a Feishu/Lark wiki URL, extract the `document_id` from the URL pattern `larksuite.com/wiki/{document_id}` and read it with `mcp__claude_ai_Feishu__read_document_content`.

## Workflow

### Round 1: Divergent Exploration

The first round is about **breadth and inspiration**. Think creatively and propose directions the user might not have considered.

1. **Brainstorm 5+ creative directions** as text first. Each direction needs:
   - A short bilingual name (e.g. "Tessellation Wave (镶嵌波)")
   - A 1-2 sentence concept description
   - How it connects to the brief's keywords/themes

   Push beyond the obvious — the goal is to spark ideas and expand the user's thinking. Consider metaphors, abstract interpretations, cultural references, and unexpected visual approaches.

2. **Generate quick drafts** for all directions using `nano-banana-2` (fast and cheap — ideal for divergent exploration). One image per direction is enough at this stage.

3. **Present in HTML preview** (see HTML Preview section below) and open in browser.

4. **Archive** the round (see Archive section below).

### Subsequent Rounds: Converge or Diverge

Based on user feedback, either:

- **Continue diverging**: Explore new directions, variations, or combinations of what worked
- **Converge**: Focus on selected directions, refine prompts, and suggest upgrading to higher-quality models:
  - `gpt-image-1.5` — better detail and composition control
  - `nano-banana-pro` — higher fidelity than nano-banana-2
  - `nano-banana-2/edit` — when the user wants to incorporate style reference images from brand assets

Each round builds on the previous one. Keep the creative momentum going.

## Image Generation

Depends on the `fal-generate` skill. Scripts are at `~/.claude/skills/fal-generate/scripts/`.

### FAL_KEY Setup

The `$FAL_KEY` environment variable should be set in the user's shell profile (e.g. `~/.zshrc`). If it's not set, guide the user:
```
To use image generation, you need a fal.ai API key:
1. Sign up at https://fal.ai and get your API key
2. Add to your ~/.zshrc: export FAL_KEY="your_key_here"
3. Run: source ~/.zshrc
```

### Text-to-Image (no reference images)

Use `generate.sh` with queue mode (the default).

```bash
bash ~/.claude/skills/fal-generate/scripts/generate.sh \
  --prompt "..." \
  --model "fal-ai/nano-banana-2" \
  --size landscape
```

Model-specific size values:
- `nano-banana-2`, `nano-banana-pro`: `landscape_4_3`, `square`, `portrait_4_3`
- `gpt-image-1.5`: must use exact pixels — `1536x1024`, `1024x1024`, `1024x1536`

### Edit Mode (with style reference images)

When the user wants to reference brand assets or mood boards for style, use the edit endpoint. This is a direct curl call because **the edit endpoint only supports sync mode** (`fal.run`), not the queue endpoint.

```bash
curl -s -X POST "https://fal.run/fal-ai/nano-banana-2/edit" \
  -H "Authorization: Key $FAL_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "prompt": "Use the attached images ONLY as style reference for color palette and visual language. Do NOT reproduce them. Create a new original composition: [actual prompt here]",
    "image_urls": ["https://...ref1.png", "https://...ref2.png"],
    "aspect_ratio": "16:9",
    "resolution": "1K",
    "num_images": 1
  }'
```

Critical rules for edit mode:
- **Style reference disclaimer**: Always start the prompt with "Use the attached images ONLY as style reference for [what to reference]. Do NOT reproduce them. Create a new original composition:" — without this, the model tends to copy the reference images rather than extracting style
- **Sync only**: Use `fal.run`, never `queue.fal.run` — the queue endpoint returns HTTP 405 for edit models
- **Set aspect_ratio explicitly**: Default is 1:1, so always specify (e.g. "16:9", "4:3", "1:1")
- Upload local files first with `upload.sh`, cache the returned CDN URLs for the session

### Uploading Local Files

```bash
bash ~/.claude/skills/fal-generate/scripts/upload.sh --file "/path/to/image.png"
```

Returns a fal CDN URL. Cache it — don't re-upload the same file within a session.

## HTML Preview

After generating images, create an HTML preview page. This is the primary way the user evaluates results.

Write the HTML to `/tmp/creative-explore/round{N}.html`.

Requirements:
- `<meta charset="UTF-8">` — without this, CJK characters and special symbols display as garbled text
- Include Figma capture script: `<script src="https://mcp.figma.com/mcp/html-to-design/capture.js" async></script>` (enables optional Figma import)
- Show reference images at top if any were used
- Each direction gets a card with: generated image, model name, and **the full prompt in a monospace box** — the user needs to see the exact prompt to know what to tweak
- Clean, readable layout with brand accent colors

See `references/html-template.md` for the template structure.

```bash
mkdir -p /tmp/creative-explore
# Write HTML file...
cd /tmp/creative-explore && python3 -m http.server 8899 &
open "http://localhost:8899/round{N}.html"
```

## Archive

After each round, save outputs to the workspace:

```
{workspace}/{brand or project name}/{yyyy-mm-dd}-{task title}/round{N}/
```

Copy both:
- Images: download from fal CDN URLs using `curl -o`
- The HTML preview file

## Prompt Writing Patterns

Adapt these patterns to the specific brand's visual language (always read the brand rules first):

- **Context first**: Start with what the image is for — "Abstract [concept] for a [context], [background description]"
- **Brand colors by hex**: AI models respond well to specific hex codes in prompts
- **Explicit constraints at the end**: "No text, no logos, no people. [mood adjectives]."
- **Layout guidance**: "Left side with generous whitespace for text placement" (if the design needs text overlay space)
- **Bilingual direction names**: Help multilingual teams communicate — "Gateway (门户)", "Constellation (星座)"

Common pitfalls:
- Relying on text description alone without referencing the brand's actual visual assets — use edit mode with reference images when available
- Forgetting to specify background lightness — many models default to dramatic dark backgrounds
- Over-describing — shorter, focused prompts often produce better results than exhaustive descriptions
