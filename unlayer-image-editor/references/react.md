# React Integration

Use React 18 or later and Node.js 18 or later.

```bash
npm view @unlayer/react-image-editor version
npm install @unlayer/react-image-editor
```

```tsx
import { useRef } from 'react';
import ImageEditor, {
  type ImageEditorRef,
  type ImageEditorSaveResult,
} from '@unlayer/react-image-editor';

export function PhotoEditor() {
  const editorRef = useRef<ImageEditorRef>(null);

  const saveImage = async ({ blob }: ImageEditorSaveResult) => {
    const formData = new FormData();
    formData.append('file', blob, 'edited.png');
    await fetch('/api/images', { method: 'POST', body: formData });
  };

  return (
    <ImageEditor
      ref={editorRef}
      image="https://example.com/photo.jpg"
      minHeight="700px"
      options={{ projectId: 1234, theme: 'light' }}
      onSave={saveImage}
      onCancel={() => {
        // Close the editor view.
      }}
      onLoadError={() => {
        // Handle an image fetch or decode failure.
      }}
      onError={(error) => {
        console.error('Image Editor failed:', error);
      }}
    />
  );
}
```

The forwarded ref exposes `{ editor }` after mount. The instance supports `getImage()`,
`hasChanges()`, `reset()`, `updateOptions()`, and `destroy()`.

## Prop Behavior

| Change | Behavior |
|--------|----------|
| `image` | Reset to the new image and clear undo/redo history and AI chat |
| `options.theme`, `locale`, or `translations` | Update without remounting |
| Other `options` values | Recreate the editor and discard unsaved changes |
| `scriptUrl` | Remount; use one script URL across all instances on a page |
| Callback props | Use the latest callback without remounting |

The wrapper is a client component and can be imported directly in Next.js App Router and other
React Server Components environments. It loads the script from an effect and destroys the editor
when the component unmounts.
