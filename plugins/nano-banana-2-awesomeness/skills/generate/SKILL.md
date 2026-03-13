---
name: generate
description: "Generate AI images using the Gemini image generation API. Zero external dependencies — Claude Code calls the API via curl and decodes with python3. Triggers on: 'generate an image', 'create an image', 'make an image', 'generate artwork', 'create a sprite', 'make an asset', 'design a logo', 'generate a screenshot', 'create a mockup', or any image generation request."
---

# Nano Banana 2 Awesomeness — Image Generation Skill for Claude Code

You are Claude Code. When the user asks you to generate an image, you call the Gemini image generation API directly using `curl` and decode the result with `python3`. The user never runs commands — you do everything.

## Prerequisite

`GEMINI_API_KEY` must be available. Resolve it in this order:
1. `$GEMINI_API_KEY` environment variable
2. `~/.nano-banana/.env` file (parse `GEMINI_API_KEY=...`)
3. `.env` in the current working directory

If not found, ask the user for their key and save it immediately:
```bash
mkdir -p ~/.nano-banana
echo "GEMINI_API_KEY=their_key" > ~/.nano-banana/.env
```

Get a free key at https://aistudio.google.com/apikey

## How You Generate an Image

When the user asks for an image, you:

1. **Enhance the prompt** (see Prompt Engineering section below)
2. **Select the right parameters** (size, aspect, model)
3. **Execute the curl + python3 pipeline** using the Bash tool
4. **Report the result** — tell the user the file path and size

### The Generation Command

This is the exact command you run via the Bash tool for every generation:

```bash
GEMINI_API_KEY="${GEMINI_API_KEY:-$(grep -s GEMINI_API_KEY ~/.nano-banana/.env .env 2>/dev/null | head -1 | sed 's/.*=//' | tr -d \"\'\ )}" && \
curl -s "https://generativelanguage.googleapis.com/v1beta/models/MODEL_ID:generateContent?key=$GEMINI_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "contents": [{"parts": [{"text": "ENHANCED_PROMPT"}]}],
    "generationConfig": {"responseModalities": ["TEXT", "IMAGE"], "imageConfig": {IMAGE_CONFIG}}
  }' -o /tmp/nb-response.json && \
python3 -c "
import json, base64, sys
with open('/tmp/nb-response.json') as f: data = json.load(f)
if 'error' in data: print(f'ERROR: {data[\"error\"][\"message\"]}'); sys.exit(1)
for p in data['candidates'][0]['content']['parts']:
    if 'inlineData' in p:
        ext = p['inlineData'].get('mimeType', 'image/png').split('/')[-1]
        img = base64.b64decode(p['inlineData']['data'])
        with open('OUTPUT_NAME.' + ext, 'wb') as out: out.write(img)
        print(f'Saved: OUTPUT_NAME.{ext} ({len(img)} bytes)')
    elif 'text' in p:
        print(f'Note: {p[\"text\"]}')
"
```

Replace these placeholders:
- `MODEL_ID` — the Gemini model (see Models below)
- `ENHANCED_PROMPT` — the prompt you enhanced (escape single quotes as `'\''`)
- `IMAGE_CONFIG` — the imageConfig JSON (see Sizes and Aspect Ratios below)
- `OUTPUT_NAME` — a descriptive kebab-case name you choose

### IMAGE_CONFIG Examples

Square 1K (default):
```json
"imageSize": "1024x1024"
```

Widescreen 2K:
```json
"imageSize": "2048x2048", "aspectRatio": "16:9"
```

Mobile portrait:
```json
"imageSize": "2048x2048", "aspectRatio": "9:16"
```

### Reference Images (Editing / Style Transfer)

When the user provides a reference image, encode it and add it as an `inlineData` part before the text:

```bash
REF_B64=$(base64 -i "PATH_TO_IMAGE" | tr -d '\n') && \
GEMINI_API_KEY="${GEMINI_API_KEY:-$(grep -s GEMINI_API_KEY ~/.nano-banana/.env .env 2>/dev/null | head -1 | sed 's/.*=//' | tr -d \"\'\ )}" && \
curl -s "https://generativelanguage.googleapis.com/v1beta/models/MODEL_ID:generateContent?key=$GEMINI_API_KEY" \
  -H "Content-Type: application/json" \
  -d "{
    \"contents\": [{\"parts\": [
      {\"inlineData\": {\"mimeType\": \"MIME_TYPE\", \"data\": \"$REF_B64\"}},
      {\"text\": \"ENHANCED_PROMPT\"}
    ]}],
    \"generationConfig\": {\"responseModalities\": [\"TEXT\", \"IMAGE\"], \"imageConfig\": {IMAGE_CONFIG}}
  }" -o /tmp/nb-response.json && \
python3 -c "
import json, base64, sys
with open('/tmp/nb-response.json') as f: data = json.load(f)
if 'error' in data: print(f'ERROR: {data[\"error\"][\"message\"]}'); sys.exit(1)
for p in data['candidates'][0]['content']['parts']:
    if 'inlineData' in p:
        ext = p['inlineData'].get('mimeType', 'image/png').split('/')[-1]
        img = base64.b64decode(p['inlineData']['data'])
        with open('OUTPUT_NAME.' + ext, 'wb') as out: out.write(img)
        print(f'Saved: OUTPUT_NAME.{ext} ({len(img)} bytes)')
"
```

MIME types: `.png` → `image/png`, `.jpg`/`.jpeg` → `image/jpeg`, `.webp` → `image/webp`

For multiple references, add multiple `inlineData` parts before the text part.

## Models

| Model ID | Use |
|----------|-----|
| `gemini-2.5-flash-image` | Default — fast, cheap, good quality |
| `gemini-3.1-flash-image-preview` | Fallback if default is unavailable |

## Sizes

| Label | imageSize Value | Cost |
|-------|----------------|------|
| 512 | `512x512` | ~$0.045 |
| 1K | `1024x1024` | ~$0.067 |
| 2K | `2048x2048` | ~$0.101 |
| 4K | `4096x4096` | ~$0.151 |

## Aspect Ratios

Supported: `1:1`, `16:9`, `9:16`, `4:3`, `3:4`, `3:2`, `2:3`, `4:5`, `5:4`, `21:9`

---

## Your Job: Be the Brain

You handle everything. The user just says what they want. You:

1. **Enhance their prompt** — transform vague requests into detailed, effective prompts
2. **Select parameters** — pick the right size, aspect ratio, and model
3. **Run the command** — execute via the Bash tool
4. **Handle errors** — retry with different models/sizes if it fails
5. **Organize output** — use good filenames, create subdirectories for batches
6. **Report results** — tell the user the file path

---

## Prompt Engineering

This is the most important part. A mediocre prompt gives a mediocre image. You MUST enhance every prompt.

### Enhancement Strategy

**UI/Web mockups:**
```
User: "a dashboard"
You use: "Modern SaaS analytics dashboard, dark theme with #0f172a background, sidebar navigation with icons, main area showing line charts and KPI cards, clean typography, Inter font, subtle grid layout, professional data visualization"
```

**Illustrations/artwork:**
```
User: "a robot"
You use: "Friendly robot character in isometric 3/4 view, soft cel-shaded style, warm ambient lighting, expressive round eyes, compact rounded body, metallic blue and white color scheme, clean vector-like rendering"
```

**Logos:**
```
User: "a logo for my app Nexus"
You use: "Minimalist geometric logo mark for 'Nexus', abstract interconnected nodes forming an N shape, maximum 2 colors, works at 16x16 favicon size, clean vector style, no gradients, white background"
```

**Product/marketing:**
```
User: "hero image for landing page"
You use: "Premium SaaS landing page hero image, floating 3D UI elements with soft shadows, gradient background transitioning from deep purple to blue, glassmorphism cards showing analytics data, professional studio-quality rendering, 16:9 widescreen composition"
```

**Photos/realistic:**
```
User: "a coffee shop"
You use: "Cozy artisan coffee shop interior, shot with 35mm lens, soft natural window light, shallow depth of field, wooden tables and exposed brick, warm color grading, golden hour atmosphere, editorial photography style"
```

### Enhancement DON'Ts
- **Pixel art** → Don't add "highly detailed" or "8K". Add "clean pixel grid, limited color palette, retro game style"
- **Minimalist** → Don't add "complex" or "detailed". Emphasize "whitespace, clean lines, simple geometry"
- **User specified a style** → Don't override it. Enhance within their chosen style
- **Screenshot/exact UI** → Don't add artistic modifiers. Focus on layout, colors, typography, content

### Negative Prompt Technique
Add "avoid" clauses when needed:
```
"...clean design, avoid cluttered layouts, avoid text rendering artifacts, avoid watermarks"
```

---

## Automatic Parameter Selection

### Size
| Request Type | Size |
|-------------|------|
| Quick concept, sketch, test | `512x512` |
| Standard generation (default) | `1024x1024` |
| Detailed UI, product shots | `2048x2048` |
| Hero images, print, wallpapers | `4096x4096` |

### Aspect Ratio
| Request Type | Ratio |
|-------------|-------|
| Logo, icon, avatar, sprite | `1:1` |
| Hero image, YouTube thumbnail, wallpaper | `16:9` |
| Mobile mockup, story, TikTok, portrait | `9:16` |
| Standard photo, slide | `4:3` |
| Portrait photo | `3:4` or `2:3` |
| Instagram feed | `4:5` |
| Ultrawide banner | `21:9` |
| No specific need | omit — let model decide |

---

## Batch Workflows

When the user wants multiple variations, run multiple Bash commands. Vary ONE aspect per generation (pose, color, angle, style) while keeping the core consistent.

After a batch, if `magick` is available, create a grid:
```bash
magick montage v1.png v2.png v3.png v4.png -tile 2x2 -geometry 512x512+10+10 -background white grid.png
```

---

## Iterative Refinement

Generate → use output as reference → refine:
1. Generate a quick v1 at 512x512
2. Base64-encode v1 and pass it as a reference
3. Generate v2 at higher resolution with refinement instructions

---

## Error Recovery

If a generation fails:
1. **Rate limit (429)**: Wait 5 seconds, retry
2. **Server error (500/503)**: Retry once
3. **Model unavailable**: Switch to `gemini-3.1-flash-image-preview`
4. **Image too complex**: Simplify the prompt, reduce size
5. **Prompt rejected**: Remove problematic terms, rephrase
6. **JSON parse error**: The response may be HTML — report the error to the user

Always check for errors before extracting the image.

---

## Output Organization

### Naming Convention
Always use descriptive kebab-case names:
- `hero-landing-page.png` not `image.png`
- `logo-nexus-v1.png` not `output.png`
- `dashboard-dark-2k.png` not `temp.png`

### Directory Organization
For batches, create a subdirectory first:
```bash
mkdir -p ./generated/logos
```
