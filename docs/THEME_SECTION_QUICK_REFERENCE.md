# Theme Section - Quick Reference

## 📍 Where to Find Theme Sections

### For Users
- **Menu Location:** Options > Visual Tweaks > Theme
- **Documentation:** [README.md](../README.md#themes) (Lines 229-233)
- **Predefined Themes:** https://github.com/kerichdev/themes-for-ytmdesktop-player

### For Developers

| Component | File | Lines | Purpose |
|-----------|------|-------|---------|
| **Menu UI** | `src/menu.ts` | 225-283 | Theme menu implementation |
| **Config** | `src/config/defaults.ts` | 36, 71 | Theme storage configuration |
| **i18n (English)** | `src/i18n/resources/en.json` | 160-174 | Theme UI text translations |
| **Console Messages** | `src/i18n/resources/en.json` | 32-34 | Theme error messages |
| **Album Color Plugin** | `src/plugins/album-color-theme/index.ts` | Full file | Dynamic theme based on album art |

## 🎨 Theme Types

### 1. Static CSS Themes
- User-imported CSS files
- Stored as file paths in config
- Applied globally to the app
- Multiple themes can be active simultaneously

### 2. Dynamic Album Color Theme (Plugin)
- Extracts colors from album artwork
- Applies color mixing to UI elements
- Configurable intensity (0-100%)
- Located in `src/plugins/album-color-theme/`

## ⚙️ Configuration

```typescript
// config.options.themes
themes: string[]  // Array of CSS file paths

// Default value
themes: []  // Empty array (no themes)
```

## 🔧 Key Functions

### In `src/menu.ts`
```typescript
// Check if themes exist
config.get('options.themes')?.length ?? 0

// Get theme list
config.get('options.themes')

// Set themes
config.set('options.themes', filePaths)

// Remove a theme
config.set('options.themes', 
  config.get('options.themes')?.filter((t) => t !== theme) ?? []
)
```

## 📝 Translation Keys

```
main.menu.options.submenu.visual-tweaks.submenu.theme.label
main.menu.options.submenu.visual-tweaks.submenu.theme.submenu.no-theme
main.menu.options.submenu.visual-tweaks.submenu.theme.submenu.import-css-file
main.menu.options.submenu.visual-tweaks.submenu.theme.dialog.remove-theme
main.menu.options.submenu.visual-tweaks.submenu.theme.dialog.remove-theme-message
main.menu.options.submenu.visual-tweaks.submenu.theme.dialog.button.cancel
main.menu.options.submenu.visual-tweaks.submenu.theme.dialog.button.remove
main.console.theme.css-file-not-found
```

## 🚀 Quick Code Navigation

```bash
# View theme menu implementation
cat src/menu.ts | sed -n '225,283p'

# View theme configuration
cat src/config/defaults.ts | grep -A 2 -B 2 "themes"

# View theme translations (English)
cat src/i18n/resources/en.json | jq '.main.menu.options.submenu."visual-tweaks".submenu.theme'

# View album color theme plugin
cat src/plugins/album-color-theme/index.ts
```

## 📚 Related Documentation

- [Full Theme Section Guide](./THEME_SECTION_GUIDE.md) - Comprehensive documentation
- [README - Themes Section](../README.md#themes) - User guide
- [Plugin Development Guide](../README.md#build-your-own-plugins) - Creating theme plugins
