# Theme Section Guide

This document provides a comprehensive guide to the theme functionality in YouTube Music Desktop App.

## Overview

The YouTube Music Desktop App supports custom themes through CSS files, allowing users to customize the look and feel of the application.

## Theme Section Locations

### 1. User Documentation
**File:** `README.md` (Lines 229-233)

The README contains user-facing documentation about themes:
- How to load CSS files (Options > Visual Tweaks > Themes)
- Reference to predefined themes repository: https://github.com/kerichdev/themes-for-ytmdesktop-player

### 2. Menu Implementation
**File:** `src/menu.ts` (Lines 225-283)

The theme menu is implemented in the Visual Tweaks submenu:
- Displays list of imported theme files
- Shows "No theme" message when no themes are imported
- Allows users to remove themes via dialog confirmation
- Provides "Import custom CSS file" option to add new themes
- Supports multiple CSS file selection

Key functionality:
```typescript
{
  label: t('main.menu.options.submenu.visual-tweaks.submenu.theme.label'),
  submenu: [
    // Shows "No theme" when themes array is empty
    // Lists all imported theme files with click-to-remove functionality
    // Separator
    // Import CSS file option with file dialog
  ]
}
```

### 3. Configuration
**File:** `src/config/defaults.ts` (Lines 36 and 71)

Theme configuration is stored in the app's config:
```typescript
interface DefaultConfig {
  options: {
    themes: string[];  // Array of file paths to CSS files
  };
}

const defaultConfig: DefaultConfig = {
  options: {
    themes: [],  // Default: no themes
  }
};
```

### 4. Internationalization
**File:** `src/i18n/resources/en.json` (Lines 160-174)

Theme-related translations:
```json
{
  "theme": {
    "label": "Theme",
    "submenu": {
      "import-css-file": "Import custom CSS file",
      "no-theme": "No theme"
    },
    "dialog": {
      "remove-theme": "Are you sure you want to remove the custom theme?",
      "remove-theme-message": "This will remove the custom theme",
      "button": {
        "cancel": "Cancel",
        "remove": "Remove"
      }
    }
  }
}
```

### 5. Album Color Theme Plugin
**File:** `src/plugins/album-color-theme/index.ts`

A related plugin that dynamically applies themes based on album artwork:
- Extracts dominant colors from album artwork
- Applies color mixing to UI elements
- Configurable color ratio (0-100%)
- Updates background, navigation bar, player bar, and sidebar colors

## How Themes Work

### User Workflow
1. Navigate to: **Options > Visual Tweaks > Theme**
2. Click: **Import custom CSS file**
3. Select one or multiple CSS files
4. The CSS files are stored in config as file paths
5. The app loads and applies these CSS files
6. To remove: Click on the theme name and confirm removal

### Technical Implementation
1. Theme file paths are stored in `config.options.themes[]`
2. The app loads CSS from these file paths
3. CSS is applied to override default styles
4. Multiple themes can be loaded simultaneously
5. Invalid file paths are logged and ignored (see console warning)

## Console Warnings
**File:** `src/i18n/resources/en.json` (Line 33)

When a CSS file is not found:
```json
"theme": {
  "css-file-not-found": "CSS file \"{{cssFile}}\" does not exist, ignoring"
}
```

## Related Features

### Album Color Theme Plugin
- Name: "Album Color Theme"
- Located: `src/plugins/album-color-theme/`
- Description: "Applies a dynamic theme and visual effects based on the album color palette"
- Feature: Automatically adjusts UI colors based on currently playing album artwork
- Configurable: Color mix ratio from 0% to 100%

## For Developers

### Adding Theme Support to New UI Elements
1. Use CSS custom properties (CSS variables) for themeable values
2. Ensure CSS selectors have appropriate specificity
3. Test with multiple theme files

### Testing Themes
1. Create a test CSS file
2. Import it via Options > Visual Tweaks > Theme
3. Verify styles are applied correctly
4. Check console for any warnings/errors

### Theme File Format
Standard CSS files with selectors targeting YouTube Music UI elements:
```css
/* Example theme file */
body {
  background-color: #1a1a1a;
}

ytmusic-player-bar {
  background-color: #2a2a2a;
}
```

## External Resources

- Predefined themes repository: https://github.com/kerichdev/themes-for-ytmdesktop-player
- Create your own CSS themes using browser DevTools to inspect elements

## Localization

Theme-related strings are localized in all supported languages under:
- `src/i18n/resources/{language}.json`
- Path: `main.menu.options.submenu.visual-tweaks.submenu.theme.*`

## Summary

The theme section is a comprehensive feature that:
- ✅ Supports custom CSS file imports
- ✅ Stores multiple theme file paths
- ✅ Provides user-friendly menu interface
- ✅ Includes removal confirmation dialogs
- ✅ Shows helpful messages when no themes are loaded
- ✅ Logs warnings for missing files
- ✅ Works alongside the Album Color Theme plugin
- ✅ Is fully localized for all supported languages
