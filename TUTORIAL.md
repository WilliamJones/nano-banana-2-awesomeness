# Nano Banana 2 Awesomeness — Tutorial

Everything in this tutorial is done through Claude Code. You just type what you want — Claude handles the rest.

---

## Setup (One Time)

Tell Claude Code:

> "Set up my Gemini API key for image generation"

Claude will create `~/.nano-banana/.env` and ask you to paste your key. Get a free key at https://aistudio.google.com/apikey

---

## 1. Your First Image

> "Generate an image of a golden retriever puppy sitting in a field of sunflowers"

Claude will enhance your prompt, pick the right settings, call the Gemini API, and save the image to your current directory.

---

## 2. Sizes

> "Generate a quick sketch of a treehouse" → Claude picks 512px (fast, ~$0.045)

> "Generate a detailed treehouse illustration" → Claude picks 1K (default, ~$0.067)

> "Generate a high-res architectural rendering of a treehouse" → Claude picks 2K (~$0.101)

> "Generate a 4K treehouse wallpaper" → Claude picks 4K (~$0.151)

---

## 3. Aspect Ratios

> "Generate a widescreen hero image of a mountain landscape" → 16:9

> "Generate a mobile app login screen mockup" → 9:16

> "Generate an Instagram post of coffee on a marble table" → 4:5

> "Generate an ultrawide banner with abstract waves" → 21:9

---

## 4. UI Mockups

> "Generate a dark-themed SaaS analytics dashboard with sidebar navigation and charts"

> "Generate a fitness app home screen with activity rings and step counter, iOS style"

> "Generate a landing page hero section with glassmorphism cards and a blue gradient"

---

## 5. Logos

> "Generate 4 different logo concepts for an app called Bolt"

Claude will create 4 variations with different styles and optionally combine them into a grid.

---

## 6. Photos & Artwork

> "Generate a cyberpunk cityscape at sunset with neon reflections"

> "Generate a cozy coffee shop interior with natural light, editorial photography style"

> "Generate a friendly robot character, cel-shaded, isometric view"

---

## 7. Pixel Art & Game Assets

> "Generate a 16-bit pixel art warrior sprite with sword and shield"

> "Generate a pixel art grass tile, top-down, seamless"

> "Generate a pixel art treasure chest, isometric view"

---

## 8. Edit Existing Images

> "Edit this image to have a sunset background" *(with a file selected or path provided)*

> "Convert this photo to watercolor style" *(reference an existing image)*

> "Make this screenshot look more professional — better lighting and sharper details"

---

## 9. Iterative Refinement

> "Generate a product page mockup for headphones"

Then after seeing v1:

> "Refine that — add more whitespace, softer colors, and rounder corners"

Claude uses the first output as a reference to generate an improved v2.

---

## 10. Batch Generation

> "Generate 4 variations of a robot mascot — different poses, same blue color scheme"

> "Generate the same coffee shop logo in flat, 3D, sketch, and line art styles"

Claude runs multiple generations and can combine them into a grid.

---

## 11. Marketing Materials

> "Generate a product launch social media graphic with an iPhone mockup"

> "Generate an email newsletter header, coral and purple gradient, ultra-wide"

> "Generate a blog post hero image — developer workspace flat lay with keyboard and coffee"

---

## Quick Reference

Just tell Claude Code what you want. It handles:
- Prompt enhancement
- Size selection (512 / 1K / 2K / 4K)
- Aspect ratio (1:1, 16:9, 9:16, 4:3, 4:5, 21:9, etc.)
- Model selection
- Reference images for editing
- Batch generation and grids
- Error recovery and retries
- File naming and organization
