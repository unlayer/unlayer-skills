# Configuration

Pass these options to `window.ImageEditor.createEditor()` or through the React wrapper's `options`
prop.

## Manual Tools

The editor includes Filter, Crop, Resize, Draw, Text, Shapes, Stickers, and Frame. All manual tools
are free. The published React types support `filter`, `crop`, `resize`, `draw`, `text`, `shapes`,
`stickers`, and `frame`.

Disable tools or override their icons with `features.imageEditor.tools`:

```javascript
const imageEditorOptions = {
  features: {
    imageEditor: {
      tools: {
        filter: {
          icon: '<svg viewBox="0 0 24 24"><circle cx="12" cy="12" r="9" /></svg>',
        },
        draw: { icon: 'https://example.com/icons/draw.svg' },
        resize: false,
        stickers: { enabled: false },
      },
    },
  },
};
```

Tool icons accept an HTTP(S) URL, data URL, absolute path, or inline SVG beginning with `<svg`.
Invalid icons fall back to the default.

### Dock and Rounded Corners

The current standalone runtime also supports `dock` (`'left'` or `'right'`) and a `corners` tool
configuration. `corners` controls rounded corners inside Crop; it is not a separate tab.

```javascript
await window.ImageEditor.createEditor({
  container: '#image-editor',
  image: 'https://example.com/photo.jpg',
  features: {
    imageEditor: {
      dock: 'left',
      tools: { corners: false },
    },
  },
});
```

Before using these two fields through React, check the installed `@unlayer/types` declarations.
They are supported at runtime but are absent from the `@unlayer/types@1.448.0` dependency shipped
with `@unlayer/react-image-editor@1.0.1`, so they do not currently type-check in React options.

## Theme and Localization

```javascript
const localizedOptions = {
  theme: 'dark',
  locale: 'fr',
  translations: {
    fr: {
      'image_editor.toolbar.save': 'Enregistrer',
      'image_editor.tools.filter': 'Effets',
    },
  },
};
```

Themes are `'light'` and `'dark'`. Bundled locales are `en`, `es`, `fr`, `de`, `it`, `pt`, `nl`,
`ja`, `ko`, and `zh`. Custom translation keys use the `image_editor.*` prefix.

## Paid AI Assistant

The AI Assistant is optional and paid. It requires `projectId`; without one, the assistant stays
hidden.

```javascript
const aiOptions = {
  projectId: 1234,
  features: {
    ai: {
      enabled: true,
      assistant: true,
    },
  },
  aiAssistantOpenState: 'open',
  defaultPrompt: 'Remove the background',
  autoSubmitPrompt: false,
};
```

`aiAssistantOpenState` accepts `'open'` or `'closed'`. Auto-submit only runs when the panel is open;
otherwise the default prompt is merely prefilled. AI edits share the same undo/redo history as
manual edits.
