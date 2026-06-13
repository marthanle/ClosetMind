# ClosetMind — Implementation Guide (React Native / Expo)

This guide turns the design references in this folder into a real app. It assumes **React Native + Expo** (best fit for a native iOS app). If you'd rather ship a **web** app, swap the stack notes for **Vite + React + React Router** — the screens, tokens, and structure map 1:1.

---

## Ready-to-paste Claude Code prompt

Open your `ClosetMind` repo in Claude Code and paste this:

```
Build ClosetMind as a React Native app using Expo + TypeScript.

The folder `design_handoff_closetmind/` is the SOURCE OF TRUTH for the design.
Read `design_handoff_closetmind/README.md` first — it documents every screen,
color, font, spacing value, interaction, and piece of state. The `.html` files
are design references showing the intended look/behavior; recreate them as native
React Native components, do NOT copy the HTML or the Tweaks/React+Babel/nav()
scaffolding (those are prototype-only).

Requirements:
- Expo Router for navigation. Bottom tab navigator with 5 tabs:
  Home, Wardrobe, Mira, Inspo, Profile. A separate auth stack for Sign In /
  Create Account shown when logged out.
- Centralize the design tokens from the README into a theme file
  (colors, spacing, radii, typography). Use the exact hex values.
- Load the "Newsreader" serif via expo-font for all display/serif text;
  use the system font for labels.
- Build each screen to match the README spec: Sign In (with inline
  create-account → success collapse), Home (today's suggestion, saved outfits
  from created folders, rediscover), Wardrobe (filterable 22-tile grid,
  horizontal category nav), Mira (chat thread + composer), Inspo (photo grid +
  add-photo bottom sheet), Inspo Match (detail + actions bottom sheet +
  save-to-folder flow), Profile (identity + settings + log out).
- Use placeholder tiles for garment/inspo images now; wire to real image
  sources later.
- Keep state local/in-memory for now (see "State Management" in the README),
  but structure it so it can move to a store or backend later. Persist
  savedOutfits so Inspo Match → Home stays in sync.

Start by scaffolding the project, the theme, the navigation, and the shared
chrome (status bar handled by the OS, app header, tab bar), then implement
screens one at a time starting with Sign In and Home.
```

---

## Recommended project structure

```
ClosetMind/
├─ app/                          # Expo Router routes
│  ├─ _layout.tsx                # root: auth gate → (auth) or (tabs)
│  ├─ (auth)/
│  │  └─ sign-in.tsx             # Sign In + inline Create Account
│  └─ (tabs)/
│     ├─ _layout.tsx             # bottom tab navigator (5 tabs)
│     ├─ index.tsx               # Home
│     ├─ wardrobe.tsx            # Wardrobe
│     ├─ mira.tsx                # Mira
│     ├─ inspo/
│     │  ├─ index.tsx            # Inspo board
│     │  └─ [id].tsx             # Inspo Match detail
│     └─ profile.tsx             # Profile
├─ src/
│  ├─ theme/
│  │  ├─ colors.ts               # --ink, --paper, --card, --accent, etc.
│  │  ├─ typography.ts           # serif/sans stacks + type scale
│  │  └─ spacing.ts              # padding, gaps, radii
│  ├─ components/
│  │  ├─ chrome/                 # AppHeader, TabBar icons, StatusBarSpacer
│  │  ├─ BottomSheet.tsx         # shared slide-up sheet + scrim
│  │  ├─ SectionHeader.tsx       # "LABEL" + link row
│  │  ├─ Chip.tsx, Tag.tsx, IconButton.tsx
│  │  └─ cards/                  # OutfitCard, GarmentTile, InspoTile, SettingRow
│  ├─ features/
│  │  ├─ wardrobe/               # filter/category/grid logic
│  │  ├─ mira/                   # thread + composer
│  │  └─ outfits/               # savedOutfits store (Inspo Match → Home)
│  └─ data/                      # mock garments, suggestions, inspo
├─ assets/
│  ├─ fonts/Newsreader-*.ttf
│  └─ images/                    # real garment/inspo images later
└─ app.json
```

## Setup commands

```bash
npx create-expo-app@latest ClosetMind -t expo-template-blank-typescript
cd ClosetMind
npx expo install expo-router expo-font react-native-reanimated react-native-gesture-handler
# fonts: download Newsreader from Google Fonts → assets/fonts/, load via useFonts()
```

## Build order (incremental, testable)
1. **Theme** — port the Design Tokens table from the README into `src/theme/`.
2. **Navigation + chrome** — auth gate, tab navigator, AppHeader, tab-bar icons.
3. **Sign In** — including the create-account → success collapse.
4. **Home** — static first, then wire `savedOutfits`.
5. **Wardrobe** — grid + category nav + chips + view toggle + favourites.
6. **Inspo + Inspo Match** — board, add-photo sheet, actions sheet, save-to-folder; persist folders so they appear on Home.
7. **Mira** — thread + composer.
8. **Profile** — identity + settings + log out → back to auth.

## Notes / gotchas
- **Don't port** the `tweaks-panel.jsx`, the React+Babel `<script>` blocks, the `nav()` function, `<link rel="prefetch">`, or the `#scaler`/`fit()` logic — all prototype-only.
- The device frame (402×874, bezel, island, status bar) is **prototype chrome** — on a real device the OS provides the status bar and safe areas. Use `react-native-safe-area-context`.
- Match exact hex values and the type scale from the README; the editorial look depends on the near-square 2px card radius, 1px hairlines, and serif headings — keep those precise.
- Tap targets ≥ 44px.

## Keeping the repo in sync (from your VS Code clone)
This design workspace can't push for you. After you pull new design updates here and drop them into your local clone:
```bash
git add .
git commit -m "Update designs / implementation"
git push
```
To automate, add a GitHub Action or a local commit/push alias in your clone.
