# Nano Banana 2 Awesomeness

A zero-dependency AI image generation skill for [Claude Code](https://docs.anthropic.com/en/docs/claude-code). Calls the Gemini image generation API directly via `curl` — no CLI tools, no package managers, no runtimes to install.

Just tell Claude Code what you want and it handles everything: prompt engineering, parameter selection, API calls, error recovery, and file organization.

## What It Does

- **Generates images** from text descriptions using Google's Gemini models
- **Enhances your prompts** automatically — turns "a dashboard" into a detailed, effective prompt
- **Picks the right settings** — size, aspect ratio, and model based on what you're asking for
- **Handles batch generation** — multiple variations, style exploration, logo concepts
- **Edits existing images** — pass a reference image and describe the changes
- **Iterative refinement** — generate v1, then refine into v2 using the output as a reference
- **Error recovery** — retries with fallback models on failure

## Requirements

- [Claude Code](https://docs.anthropic.com/en/docs/claude-code)
- A Gemini API key (free at [Google AI Studio](https://aistudio.google.com/apikey))
- `curl` and `python3` (pre-installed on macOS and most Linux)

## Setup

1. Install the skill in Claude Code:
   ```
   claude install gh:WilliamJones/nano-banana-2-awesomeness
   ```

2. Set up your API key — just tell Claude Code:
   > "Set up my Gemini API key for image generation"

   It will create `~/.nano-banana/.env` and ask you to paste your key.

## Usage

Just talk to Claude Code. No commands to memorize.

### Basic Generation
> "Generate an image of a mountain landscape at sunset"

### UI Mockups
> "Generate a dark-themed SaaS dashboard with sidebar navigation and analytics charts"

### Logos
> "Generate 4 different logo concepts for an app called Bolt"

### Mobile Screens
> "Generate a fitness app home screen mockup, iOS style"

### Edit Existing Images
> "Edit this screenshot to have a dark theme" *(with a file path)*

### Batch Variations
> "Generate the same coffee shop logo in flat, 3D, sketch, and line art styles"

### Iterative Refinement
> "Generate a product page mockup"

Then after seeing v1:
> "Refine that — more whitespace, softer colors"

## How It Works

When you ask for an image, Claude Code:

1. **Enhances your prompt** with style-appropriate details (UI specifics for mockups, camera terms for photos, pixel art constraints for sprites, etc.)
2. **Selects parameters** — picks the right resolution (512px–4K), aspect ratio (1:1, 16:9, 9:16, etc.), and model
3. **Calls the Gemini API** via `curl` and decodes the base64 image with `python3`
4. **Saves the result** with a descriptive filename in your working directory
5. **Handles errors** — retries with fallback models, adjusts prompts if needed

## Supported Features

| Feature | Details |
|---------|---------|
| **Sizes** | 512px (~$0.045), 1K (~$0.067), 2K (~$0.101), 4K (~$0.151) |
| **Aspect Ratios** | 1:1, 16:9, 9:16, 4:3, 3:4, 3:2, 2:3, 4:5, 5:4, 21:9 |
| **Models** | gemini-2.5-flash-image (default), gemini-3.1-flash-image-preview (fallback) |
| **Reference Images** | Edit, style transfer, multi-reference combination |
| **Batch Generation** | Multiple variations with varied prompts |
| **Image Grids** | Combine batch outputs into contact sheets (if ImageMagick is installed) |

## Tutorial

See [TUTORIAL.md](TUTORIAL.md) for a walkthrough of everything the skill can do.

## License

MIT
