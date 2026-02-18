# Complete Feature Flags Reference

All available flags for `features` in `unlayer.init()`:

```javascript
features: {
  // --- CORE ---
  audit: true,
  preview: true,                      // or object:
  // preview: {
  //   enabled: true,
  //   cleanup: true,
  //   deviceResolutions: {
  //     showDefaultResolutions: true,
  //     customResolutions: {
  //       desktop: [{ value: 1440, name: 'Large Desktop' }],
  //       tablet: [{ value: 768, name: 'iPad' }],
  //       mobile: [{ value: 375, name: 'iPhone X' }],
  //     },
  //   },
  // },
  blocks: true,
  undoRedo: true,                     // or { enabled: true, autoSelect: true, autoFocus: true }
  stockImages: true,                  // or { enabled: true, safeSearch: true, defaultSearchTerm: 'business' }
  userUploads: true,                  // or { enabled: true, search: true }
  preheaderText: true,
  headersAndFooters: false,
  sendTestEmail: false,

  // --- TEXT EDITOR ---
  textEditor: {
    spellChecker: true,
    tables: false,                    // Default: false
    cleanPaste: 'confirm',            // true | false | 'basic' | 'confirm'
    emojis: true,
    textDirection: true,              // null to hide controls
    inlineFontControls: true,
    customButtons: [],                // Custom toolbar buttons
  },

  // --- AI ---
  ai: true,                           // or object:
  // ai: {
  //   enabled: true,
  //   copilot: false,                // BETA — requires entitlement
  //   models: false,
  //   magicImage: false,
  //   smartButtons: false,
  //   smartHeadings: false,
  //   smartImageAltText: false,
  //   smartText: false,
  //   smartParagraph: false,
  // },

  // --- IMAGE EDITOR ---
  imageEditor: true,                  // or { enabled: true, tools: { resize: true } }

  // --- COLOR PICKER ---
  colorPicker: {
    colors: ['#FF0000'],              // string[] or ColorGroup[]
    // ColorGroup: { id: 'brand_colors', label: 'Brand', colors: [...], default: true }
    // Built-in IDs: 'brand_colors', 'common_colors', 'recent_colors', 'template_colors'
    limit: 50,
    recentColors: true,               // boolean | null
    // DEPRECATED: presets, brandColors — use colors instead
  },

  // --- COLLABORATION ---
  collaboration: false,

  // --- LEGACY ---
  legacy: {
    disableHoverButtonColors: false,  // Disable hover effects
  },
}
```
