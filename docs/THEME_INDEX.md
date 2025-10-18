# Theme Documentation Index

Welcome to the YouTube Music Desktop App theme documentation. This index helps you find the right documentation for your needs.

## 📖 Documentation Files

### For All Users

| Document | Purpose | Best For |
|----------|---------|----------|
| [README - Themes Section](../README.md#themes) | Basic user guide | End users who want to use themes |

### For Developers

| Document | Purpose | Best For |
|----------|---------|----------|
| [Theme Section Quick Reference](./THEME_SECTION_QUICK_REFERENCE.md) | Fast lookup of locations, APIs, and commands | Developers making quick changes |
| [Theme Section Guide](./THEME_SECTION_GUIDE.md) | Comprehensive feature documentation | Understanding how themes work |
| [Theme Architecture](./THEME_ARCHITECTURE.md) | System design and data flows | Understanding the architecture |

## 🎯 Quick Navigation by Task

### I want to...

#### Use Themes (End Users)
→ Read [README - Themes Section](../README.md#themes)
- How to import CSS files
- Where to find predefined themes
- Access menu: Options > Visual Tweaks > Themes

#### Find Where Theme Code Is Located (Developers)
→ Read [Theme Section Quick Reference](./THEME_SECTION_QUICK_REFERENCE.md)
- File locations table
- Line numbers for each component
- Quick code navigation commands

#### Understand How Themes Work (Developers)
→ Read [Theme Section Guide](./THEME_SECTION_GUIDE.md)
- Feature overview
- Technical implementation details
- Configuration structure
- Plugin integration

#### Understand Theme Architecture (Developers)
→ Read [Theme Architecture](./THEME_ARCHITECTURE.md)
- System overview diagrams
- Data flow diagrams
- Component relationships
- File organization

#### Modify Theme Menu (Developers)
1. Read [Theme Section Quick Reference](./THEME_SECTION_QUICK_REFERENCE.md) for file locations
2. Edit `src/menu.ts` lines 225-283
3. Update translations in `src/i18n/resources/*.json`
4. Test changes with `pnpm dev`

#### Add New Theme Features (Developers)
1. Read [Theme Section Guide](./THEME_SECTION_GUIDE.md) for feature overview
2. Read [Theme Architecture](./THEME_ARCHITECTURE.md) for system design
3. Modify relevant components:
   - Menu: `src/menu.ts`
   - Config: `src/config/defaults.ts`
   - Translations: `src/i18n/resources/*.json`

#### Create a Theme Plugin (Developers)
1. Read [README - Build your own plugins](../README.md#build-your-own-plugins)
2. Study example: `src/plugins/album-color-theme/index.ts`
3. Follow the plugin creation guide in README

#### Translate Theme UI (Translators)
1. Find your language file in `src/i18n/resources/`
2. Translate keys under `main.menu.options.submenu.visual-tweaks.submenu.theme.*`
3. See [Theme Section Guide](./THEME_SECTION_GUIDE.md) for complete key list

## 📁 Source Code Locations

Quick links to actual source code files:

### Core Theme System
- **Menu Implementation:** [`src/menu.ts`](../src/menu.ts) (lines 225-283)
- **Configuration:** [`src/config/defaults.ts`](../src/config/defaults.ts) (lines 36, 71)
- **English Translations:** [`src/i18n/resources/en.json`](../src/i18n/resources/en.json) (lines 160-174)

### Related Features
- **Album Color Theme Plugin:** [`src/plugins/album-color-theme/index.ts`](../src/plugins/album-color-theme/index.ts)
- **All Plugins:** [`src/plugins/`](../src/plugins/)

## 🔍 Search Tips

### Finding Theme-Related Code
```bash
# Search for theme references
grep -r "theme" src/ --include="*.ts" --include="*.json"

# Find theme translations
cat src/i18n/resources/en.json | jq '.main.menu.options.submenu."visual-tweaks".submenu.theme'

# View theme menu code
sed -n '225,283p' src/menu.ts
```

### Common Search Terms
- `options.themes` - Theme configuration array
- `theme.label` - Theme menu label
- `import-css-file` - Import functionality
- `album-color-theme` - Dynamic theme plugin
- `visual-tweaks` - Parent menu section

## 🏗️ Architecture Overview

```
Theme System
├── Static CSS Themes (User-imported)
│   ├── Menu (src/menu.ts)
│   ├── Config (src/config/defaults.ts)
│   └── i18n (src/i18n/resources/*.json)
│
└── Dynamic Themes (Plugins)
    └── Album Color Theme (src/plugins/album-color-theme/)
```

For detailed architecture diagrams, see [Theme Architecture](./THEME_ARCHITECTURE.md).

## 📝 Related Documentation

- [Main README](../README.md) - Project overview
- [Plugin Development Guide](../README.md#build-your-own-plugins) - Creating plugins
- [Build Instructions](../README.md#build) - Building the app

## 🤝 Contributing

When contributing to theme functionality:

1. ✅ **Read** relevant documentation first
2. ✅ **Update** documentation if you change behavior
3. ✅ **Test** with multiple themes
4. ✅ **Localize** any new UI strings
5. ✅ **Follow** existing code patterns

## 📮 Getting Help

- **Issues:** [GitHub Issues](https://github.com/th-ch/youtube-music/issues)
- **Discussions:** [GitHub Discussions](https://github.com/th-ch/youtube-music/discussions)
- **External Themes:** [themes-for-ytmdesktop-player](https://github.com/kerichdev/themes-for-ytmdesktop-player)

---

*Documentation created for YouTube Music Desktop App v3.5.1*
