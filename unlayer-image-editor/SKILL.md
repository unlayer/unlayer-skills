---
name: unlayer-image-editor
description: Integrates and configures Unlayer's standalone Image Editor through the JavaScript embed or the official @unlayer/react-image-editor component. Use for mounting the standalone editor, editing or resetting images, handling save/cancel/error callbacks, configuring manual tools, icons, dock position, theme, localization, and enabling the paid AI Assistant. Do not use for the image editor built into Unlayer's email, page, popup, or document builders; route that configuration to unlayer-config.
---

# Integrate Unlayer Image Editor

## Overview

Embed Unlayer's standalone Image Editor with plain JavaScript or React. Keep it distinct from the
image editor inside Unlayer's builders, which is configured through `unlayer.init()`.

The standalone editor and all manual editing tools are free. The optional AI Assistant is paid.

## Choose the Integration

| Need | Read |
|------|------|
| Plain JavaScript embed, loader, callbacks, or instance lifecycle | [references/javascript.md](references/javascript.md) |
| React or Next.js with `@unlayer/react-image-editor` | [references/react.md](references/react.md) |
| Tools, icons, dock, theme, localization, or AI Assistant | [references/configuration.md](references/configuration.md) |
| Configure the image editor opened from an image inside a builder | Use `unlayer-config` instead |

## Core Workflow

1. Accept an image URL or base64 data URL.
2. Mount into a container at least 1024px wide and 700px high when possible.
3. Handle `onSave` and upload the returned `blob`; use `dataUrl` for previews or inline display.
4. Handle `onLoadError` separately from script or lifecycle failures.
5. Check `hasChanges()` before closing and call `destroy()` when the editor is no longer needed.

## Guardrails

- Use `https://cdn.unlayer.com/image-editor/embed.js` for the JavaScript loader.
- Use `@unlayer/react-image-editor` for React; do not substitute `react-email-editor`.
- Verify the package version with `npm view @unlayer/react-image-editor version` before pinning it.
- Wait for the Promise returned by `createEditor()` before calling instance methods.
- Treat `projectId` as required for AI features. Manual tools do not require a paid feature.
- Expect remote image URLs to require CORS access; use `onLoadError` for fetch, decode, or 404 failures.
- Preserve the distinction between `dataUrl` and `blob`: preview with the former, upload the latter.

## Official Resources

- [Image Editor overview](https://docs.unlayer.com/builder/latest/image-editor)
- [Image Editor quickstart](https://docs.unlayer.com/builder/latest/image-editor/quickstart)
- [Image Editor API reference](https://docs.unlayer.com/builder/latest/image-editor/api-reference)
- [React Image Editor](https://docs.unlayer.com/builder/latest/react-image-editor)
- [React wrapper on GitHub](https://github.com/unlayer/react-image-editor)
