# Theme Architecture

## System Overview

```
┌─────────────────────────────────────────────────────────────────┐
│                    YouTube Music Desktop App                     │
│                      Theme System Architecture                   │
└─────────────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────────────┐
│                         User Interface                           │
├─────────────────────────────────────────────────────────────────┤
│  Menu Bar                                                        │
│    └─ Options                                                    │
│        └─ Visual Tweaks                                          │
│            └─ Theme ◄────────────────┐                          │
│                 ├─ [Theme 1.css]     │  User clicks to remove   │
│                 ├─ [Theme 2.css]     │                          │
│                 ├─ ─────────────     │                          │
│                 └─ Import CSS File   │  User selects files      │
└─────────────────────────────────────────────────────────────────┘
                           │                    │
                           │                    │
                           ▼                    ▼
┌─────────────────────────────────────────────────────────────────┐
│                    Menu Controller                               │
│                    (src/menu.ts:225-283)                         │
├─────────────────────────────────────────────────────────────────┤
│  • Read config.options.themes                                    │
│  • Display theme list or "No theme"                              │
│  • Handle file dialog for import                                 │
│  • Handle removal confirmation dialog                            │
│  • Update config and refresh menu                                │
└─────────────────────────────────────────────────────────────────┘
                           │
                           │
                           ▼
┌─────────────────────────────────────────────────────────────────┐
│                   Configuration Store                            │
│               (src/config/defaults.ts:36,71)                     │
├─────────────────────────────────────────────────────────────────┤
│  {                                                               │
│    options: {                                                    │
│      themes: [                                                   │
│        "/path/to/theme1.css",                                    │
│        "/path/to/theme2.css"                                     │
│      ]                                                           │
│    }                                                             │
│  }                                                               │
└─────────────────────────────────────────────────────────────────┘
                           │
                           │
                           ▼
┌─────────────────────────────────────────────────────────────────┐
│                    Theme Loader                                  │
│                   (Electron Main Process)                        │
├─────────────────────────────────────────────────────────────────┤
│  • Read CSS file from disk                                       │
│  • Inject CSS into renderer                                      │
│  • Log errors for missing files                                  │
│    (main.console.theme.css-file-not-found)                       │
└─────────────────────────────────────────────────────────────────┘
                           │
                           │
                           ▼
┌─────────────────────────────────────────────────────────────────┐
│                   Renderer Process                               │
│                 (YouTube Music Web UI)                           │
├─────────────────────────────────────────────────────────────────┤
│  • Apply CSS styles to DOM                                       │
│  • Override default styles                                       │
│  • Work with Album Color Theme plugin                            │
└─────────────────────────────────────────────────────────────────┘
```

## Theme Types

### 1. Static CSS Themes (User-Imported)

```
┌──────────────────────────────────────────────┐
│         User's File System                   │
│                                              │
│  ~/themes/dark-theme.css ───────┐           │
│  ~/themes/custom-colors.css ────┤           │
│  ~/.themes/minimal.css ─────────┤           │
└──────────────────────────────────────────────┘
                                   │
                                   │ File paths stored
                                   │
                                   ▼
┌──────────────────────────────────────────────┐
│         config.options.themes                │
│                                              │
│  [                                           │
│    "/home/user/themes/dark-theme.css",       │
│    "/home/user/themes/custom-colors.css",    │
│    "/home/user/.themes/minimal.css"          │
│  ]                                           │
└──────────────────────────────────────────────┘
                                   │
                                   │ Loaded at runtime
                                   │
                                   ▼
┌──────────────────────────────────────────────┐
│         Applied to App                       │
│                                              │
│  • All CSS rules merged                      │
│  • Last rule wins (CSS cascade)              │
│  • Styles applied globally                   │
└──────────────────────────────────────────────┘
```

### 2. Dynamic Album Color Theme (Plugin)

```
┌──────────────────────────────────────────────┐
│      Album Artwork (Currently Playing)       │
│                                              │
│           🎨 Album Cover Image               │
└──────────────────────────────────────────────┘
                    │
                    │ Extract colors
                    │
                    ▼
┌──────────────────────────────────────────────┐
│      Fast Average Color Algorithm            │
│    (src/plugins/album-color-theme/)          │
│                                              │
│  • Analyze image pixels                      │
│  • Calculate dominant colors                 │
│  • Apply color darken/lighten                │
└──────────────────────────────────────────────┘
                    │
                    │ Generate CSS variables
                    │
                    ▼
┌──────────────────────────────────────────────┐
│         CSS Custom Properties                │
│                                              │
│  --ytmusic-album-color: rgb(r, g, b)         │
│  --ytmusic-album-color-dark: rgb(r, g, b)    │
│  --ytmusic-album-color-ratio: 50%            │
└──────────────────────────────────────────────┘
                    │
                    │ Mix with base colors
                    │
                    ▼
┌──────────────────────────────────────────────┐
│      Applied to UI Elements                  │
│                                              │
│  • Background                                │
│  • Navigation bar                            │
│  • Player bar                                │
│  • Sidebars                                  │
│  • Various UI components                     │
└──────────────────────────────────────────────┘
```

## Data Flow: Theme Import

```
1. User Action
   └─► Click "Import CSS File"

2. File Dialog
   └─► User selects one or more .css files
       └─► Returns: ["/path/to/file1.css", "/path/to/file2.css"]

3. Config Update
   └─► config.set('options.themes', filePaths)

4. Menu Refresh
   └─► innerRefreshMenu()
       └─► Rebuild menu with new theme list

5. App Restart (if needed)
   └─► Load and apply new CSS files
```

## Data Flow: Theme Removal

```
1. User Action
   └─► Click on theme filename in menu

2. Confirmation Dialog
   └─► Show dialog: "Are you sure?"
       ├─► [Cancel] → Do nothing
       └─► [Remove] → Proceed

3. Config Update
   └─► Filter out removed theme from array
       config.set('options.themes', 
         config.get('options.themes')?.filter((t) => t !== theme)
       )

4. Menu Refresh
   └─► innerRefreshMenu()
       └─► Rebuild menu without removed theme

5. App Restart (if needed)
   └─► Stop applying removed CSS file
```

## Internationalization Flow

```
┌──────────────────────────────────────────────┐
│      Translation Key (Code)                  │
│                                              │
│  t('main.menu.options.submenu                │
│     .visual-tweaks.submenu.theme.label')     │
└──────────────────────────────────────────────┘
                    │
                    │ Lookup in i18n
                    │
                    ▼
┌──────────────────────────────────────────────┐
│      Language Files                          │
│   (src/i18n/resources/*.json)                │
│                                              │
│  en.json → "Theme"                           │
│  fr.json → "Thème"                           │
│  es.json → "Tema"                            │
│  de.json → "Design"                          │
│  ...                                         │
└──────────────────────────────────────────────┘
                    │
                    │ Return translated text
                    │
                    ▼
┌──────────────────────────────────────────────┐
│      Display in Menu                         │
│                                              │
│  Shows localized text based on user's        │
│  language preference                         │
└──────────────────────────────────────────────┘
```

## Component Relationships

```
              ┌─────────────────────────┐
              │   Main Process          │
              │   (index.ts)            │
              └───────────┬─────────────┘
                          │
                ┌─────────┴─────────┐
                │                   │
                ▼                   ▼
    ┌───────────────────┐   ┌──────────────────┐
    │   Menu System     │   │  Config System   │
    │   (menu.ts)       │◄──┤  (defaults.ts)   │
    └─────────┬─────────┘   └──────────────────┘
              │
              │ Creates menu items
              │ based on config
              │
              ▼
    ┌───────────────────┐
    │  Theme Submenu    │
    │  • List themes    │
    │  • Import option  │
    │  • Remove handler │
    └─────────┬─────────┘
              │
              │ Uses translations from
              │
              ▼
    ┌───────────────────┐
    │   i18n System     │
    │   (resources/)    │
    └───────────────────┘


              ┌─────────────────────────┐
              │   Renderer Process      │
              │   (renderer.ts)         │
              └───────────┬─────────────┘
                          │
                ┌─────────┴─────────┐
                │                   │
                ▼                   ▼
    ┌───────────────────┐   ┌──────────────────────┐
    │   Static Themes   │   │  Album Color Theme   │
    │   (CSS files)     │   │  (plugin)            │
    └───────────────────┘   └──────────────────────┘
                │                   │
                └─────────┬─────────┘
                          │
                          ▼
              ┌───────────────────────┐
              │   YouTube Music UI    │
              │   (Styled DOM)        │
              └───────────────────────┘
```

## File Organization

```
youtube-music-ad-free/
│
├── README.md
│   └── Lines 229-236: User documentation
│
├── src/
│   ├── menu.ts
│   │   └── Lines 225-283: Theme menu implementation
│   │
│   ├── config/
│   │   └── defaults.ts
│   │       └── Lines 36, 71: Theme configuration
│   │
│   ├── i18n/
│   │   └── resources/
│   │       ├── en.json
│   │       │   └── Lines 160-174: Theme translations
│   │       ├── es.json
│   │       ├── fr.json
│   │       └── ... (all languages)
│   │
│   └── plugins/
│       └── album-color-theme/
│           ├── index.ts (Plugin implementation)
│           └── style.css (Plugin styles)
│
└── docs/
    ├── THEME_SECTION_GUIDE.md
    │   └── Comprehensive documentation
    │
    ├── THEME_SECTION_QUICK_REFERENCE.md
    │   └── Quick reference guide
    │
    └── THEME_ARCHITECTURE.md (this file)
        └── Architecture diagrams and flows
```
