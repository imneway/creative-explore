# HTML Preview Template

Use this template structure for each round's preview page. Adapt the content (title, subtitle, directions, images) per round.

## Template

```html
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>{Brand} Creative Drafts - Round {N}</title>
<script src="https://mcp.figma.com/mcp/html-to-design/capture.js" async></script>
<style>
  * { box-sizing: border-box; margin: 0; padding: 0; }
  body { background: #f5f5f5; font-family: -apple-system, system-ui, 'Helvetica Neue', sans-serif; padding: 48px; color: #333; max-width: 1200px; margin: 0 auto; }
  h1 { font-size: 28px; font-weight: 700; margin-bottom: 6px; }
  .subtitle { font-size: 14px; color: #888; margin-bottom: 12px; }
  .ref-section { background: #f0ecff; border-radius: 10px; padding: 16px 20px; margin-bottom: 40px; }
  .ref-section h3 { font-size: 13px; font-weight: 600; color: {brand-accent}; margin-bottom: 8px; }
  .ref-images { display: flex; gap: 16px; }
  .ref-images img { height: 80px; border-radius: 6px; border: 1px solid #e0d8ff; }
  .ref-images span { font-size: 11px; color: #888; display: block; margin-top: 4px; }
  .color-ref { display: flex; gap: 8px; margin-bottom: 48px; align-items: center; font-size: 11px; color: #999; }
  .color-dot { width: 14px; height: 14px; border-radius: 50%; border: 1px solid rgba(0,0,0,0.08); }
  .direction { margin-bottom: 56px; }
  .direction-title { font-size: 20px; font-weight: 600; color: {brand-accent}; margin-bottom: 6px; }
  .direction-desc { font-size: 13px; color: #666; line-height: 1.6; max-width: 800px; margin-bottom: 16px; }
  .card { background: white; border-radius: 14px; padding: 20px; box-shadow: 0 2px 12px rgba(0,0,0,0.06); }
  .card img { width: 100%; border-radius: 10px; margin-bottom: 14px; background: #eee; }
  .model-name { font-size: 12px; font-weight: 600; color: {brand-accent}; margin-bottom: 10px; }
  .prompt-box { background: #f9f7ff; border: 1px solid #ece6ff; border-radius: 8px; padding: 14px; }
  .prompt-label { font-size: 10px; font-weight: 700; color: {brand-accent}; text-transform: uppercase; letter-spacing: 0.8px; margin-bottom: 4px; }
  .prompt-text { font-size: 11px; color: #555; line-height: 1.55; font-family: 'SF Mono', 'Menlo', 'Consolas', monospace; word-break: break-word; white-space: pre-wrap; }
</style>
</head>
<body>

<h1>{Brand} - {Task Title} Drafts</h1>
<div class="subtitle">Round {N} | Model: {model-name} | {brief context}</div>

<!-- Optional: Brand color reference (adapt colors from brand rules) -->
<div class="color-ref">
  <span>Brand colors:</span>
  <!-- Add color dots from the brand's palette -->
  <div class="color-dot" style="background: {color-1};"></div><span>{color-1}</span>
  <div class="color-dot" style="background: {color-2};"></div><span>{color-2}</span>
</div>

<!-- Optional: Reference images section (only when edit mode was used) -->
<div class="ref-section">
  <h3>Style Reference Images</h3>
  <div class="ref-images">
    <div>
      <img src="{ref-image-url}">
      <span>{ref-image-label}</span>
    </div>
  </div>
</div>

<!-- Repeat for each direction -->
<div class="direction">
  <div class="direction-title">{N}. {Direction Name} ({中文名})</div>
  <div class="direction-desc">{1-2 sentence concept description}</div>
  <div class="card">
    <img src="{generated-image-url}">
    <div class="model-name">{model-id}</div>
    <div class="prompt-box">
      <div class="prompt-label">Prompt</div>
      <div class="prompt-text">{full prompt used for generation}</div>
    </div>
  </div>
</div>

</body>
</html>
```

## Multi-model comparison layout

When comparing outputs from multiple models for the same direction, use a side-by-side layout:

```html
<div class="models" style="display: flex; gap: 28px;">
  <div class="card" style="flex: 1;">
    <img src="{model-a-image-url}">
    <div class="model-name">{model-a-id}</div>
    <div class="prompt-box">
      <div class="prompt-label">Prompt</div>
      <div class="prompt-text">{prompt}</div>
    </div>
  </div>
  <div class="card" style="flex: 1;">
    <img src="{model-b-image-url}">
    <div class="model-name">{model-b-id}</div>
    <div class="prompt-box">
      <div class="prompt-label">Prompt</div>
      <div class="prompt-text">{prompt}</div>
    </div>
  </div>
</div>
```
