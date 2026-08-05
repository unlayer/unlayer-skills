# JavaScript Integration

## Mount the Editor

```html
<script src="https://cdn.unlayer.com/image-editor/embed.js"></script>
<div id="image-editor" style="min-height: 700px"></div>

<script type="module">
  const editor = await window.ImageEditor.createEditor({
    container: '#image-editor',
    image: 'https://example.com/photo.jpg',
    onSave: async ({ blob, dataUrl }) => {
      const formData = new FormData();
      formData.append('file', blob, 'edited.png');
      await fetch('/api/images', { method: 'POST', body: formData });
    },
    onCancel: () => {
      // Close the editor view.
    },
    onLoadError: () => {
      // Handle CORS, missing image, or decode failures.
    },
  });
</script>
```

`container` accepts a CSS selector or `HTMLElement`. `image` accepts a URL or base64 data URL.
`createEditor()` always returns a Promise and rejects when the container is missing or already has
an editor mounted.

## Preload

The embed is a lightweight loader. Preload the full editor before opening it when startup latency
matters:

```javascript
await window.ImageEditor.load();
```

After the full bundle loads, the loader dispatches `image-editor-ready` on `window`.

## Manage the Instance

```javascript
const dataUrl = editor.getImage();
const dirty = editor.hasChanges();

await editor.reset('https://example.com/next-photo.jpg');
editor.updateOptions({ theme: 'dark', locale: 'fr' });

if (!editor.hasChanges()) {
  editor.destroy();
}
```

| Method | Purpose |
|--------|---------|
| `getImage()` | Return the flattened canvas as a data URL, or `null` |
| `hasChanges()` | Detect unsaved edits relative to the input image |
| `reset(imageUrl?)` | Clear canvas history and AI chat, optionally loading another image |
| `updateOptions(options)` | Update runtime options such as theme, locale, or translations |
| `destroy()` | Unmount the editor and release its resources |

Call `destroy()` before mounting another editor into the same container.
