# Handoff: ClosetMind — Full App (iOS)

## Overview
ClosetMind is an iOS wardrobe-archive app with an editorial, print-magazine aesthetic: warm off-white "paper", a near-black header/tab bar, serif display type, and a restrained monochrome UI with sparing accent color. This package covers the **complete app flow** — seven screens connected by a five-tab bottom navigation plus an auth entry:

1. **Sign In / Create Account** — auth entry (logo, login, inline account-creation flow)
2. **Home** — daily outfit suggestion, saved outfits, rediscover
3. **Wardrobe** — filterable garment grid (lives in `Wardrobe/` subfolder)
4. **Mira** — AI stylist chat
5. **Inspo** — inspiration photo board with add-photo bottom sheet
6. **Inspo Match** — single-inspo detail with wardrobe match + save/delete actions
7. **Profile** — account identity, preferences, settings, log out

## About the Design Files
The files in this bundle are **design references created in HTML** — prototypes showing intended look and behavior, **not production code to copy directly**. The task is to **recreate these designs in the target codebase's existing environment** (SwiftUI / React Native / Flutter / React, etc.) using its established components, navigation, and styling patterns. If no environment exists yet, choose the most appropriate framework and implement there.

Each screen is a self-contained HTML file inside a fixed 402×874 "device" frame. They use CSS custom properties for all themeable values (see Design Tokens) and inline SVG for every icon and image placeholder. Garment/photo imagery is represented by solid-tone placeholder tiles — real images replace these in implementation.

**Ignore the Tweaks scaffolding.** Every file loads React + Babel + `tweaks-panel.jsx` at the bottom and an inline `<script type="text/babel">` block — this is a design-time theme explorer (surround color, paper, card, serif font, accent), **not part of the app**. Skip it when porting.

**Ignore the page-transition shim.** Each file has a small `nav(url)` function, `<link rel="prefetch">` tags, and a `#scaler`/`fit()` block — these only exist to make the standalone HTML prototypes scale to the browser and navigate between files smoothly. In a real app, use the platform's native navigation.

## Fidelity
**High-fidelity.** Final colors, typography, spacing, and layout are specified below and should be reproduced faithfully. The only intentional placeholders are garment/product/inspo images (flat tone tiles) and AI-generated outfit thumbnails.

---

## Shared Chrome (all screens)
- **Device frame**: 402×874 screen (iPhone 16 Pro logical size), 54px-radius outer body in `#0C0B0A` with 11px bezel, 43px inner screen radius, dynamic island (124×35, centered, 9px from top).
- **Status bar**: 54px tall, black bg, white "9:41" (16px, 600) left; signal/wifi/battery SVGs right.
- **App header**: black (`--black`) bar. Either a serif screen title (30px, white) or the ClosetMind wordmark. Padding 14–24px.
- **Tab bar** (`.tabbar`): `--paper` bg, 1px top hairline, 5 tabs — HOME, WARDROBE, MIRA, INSPO, PROFILE. Each tab: 24px line icon + 9.5px / 1.3px-tracking label. Active tab uses `--accent` with a 3px accent dot beneath the label; inactive tabs `--muted`. Side padding 26px so HOME/PROFILE sit in from the edges.
- **Home indicator**: 138×5 rounded bar, `rgba(20,18,15,.32)`, centered above bottom edge.
- The body scrolls vertically; status bar, header, tab bar, and home indicator stay fixed.

---

## Screens / Views

### 1. Sign In / Create Account  (`ClosetMind Sign In.html`)
- **Purpose**: App entry. Existing users sign in; new users create an account inline.
- **Header** (black, centered column, ~36px vertical padding): 74px dark-circle (`#1F1D1A`) holding the ClosetMind monogram (`closetmind-mark.png`, 40px), serif "ClosetMind" wordmark (34px, white), tracking-2.6px tagline "YOUR WARDROBE, ARCHIVED" (11px, `#8C887E`).
- **Body** (`--paper`, 28px padding) holds two swappable views:
  - **Sign in view**: serif "Sign in" (25px) + sub "Enter your details to continue" (14px `--muted`). Two fields (EMAIL prefilled `martha@smu.edu`, PASSWORD). Field = 1px `--hair` border, `--card` fill, 16px padding; filled field gets `--ink` border. "Forgot password?" underlined link, right-aligned. Black **SIGN IN** button (full-width, 19px padding, 12px/2px-tracking white label) → navigates to Home. Divider "NEW HERE?" (hairline both sides). Outlined ghost button **CREATE AN ACCOUNT** → swaps to create view.
  - **Create account view**: "‹ Back to sign in" link, serif "Create account" (25px) + sub. Three fields (FULL NAME, EMAIL, PASSWORD — empty placeholders). Black **CREATE ACCOUNT** button. Footnote about Terms/Privacy.
- **Inline collapse → success**: tapping CREATE ACCOUNT collapses the form (max-height→0, fade) and reveals a **success card** in place: 62px black circle with white check, serif "Account created!" (24px), sub copy, outlined **BACK TO SIGN IN** button → returns to sign-in view.
- No tab bar on this screen.

### 2. Home  (`ClosetMind Home.html`)
- **Purpose**: Daily landing — today's suggested outfit, saved outfits, rediscover neglected pieces.
- **Header** (black): serif wordmark "ClosetMind" (30px) with tracking-2.4px label "YOUR WARDROBE, ARCHIVED" (10.5px, `#8C887E`) beneath; right side has search + add (plus) icons (21px, white, 1.6 stroke). A tap on search expands an in-page search state.
- **Day strip**: row, space-between, 1px bottom hairline. Left "TUESDAY, 10 JUNE" (12px, 1.6px tracking, `--ink`, 600); right cloud icon + "22°C · partly cloudy" (13px, `--muted`).
- **Today's Suggestion**: section label row ("TODAY'S SUGGESTION" / "REGENERATE"). Card (`--card`, 1px `--hair`, 2px radius, 14px pad) with a 4-column thumbnail grid (gap 9px, tiles `aspect-ratio:1/1.18`, `--placeholder` fill, centered 34px garment glyph), then a meta row: serif title "Casual Tuesday" (20px) left, outline heart (22px, `--muted-2`) right.
- **Saved Outfits**: label row ("SAVED OUTFITS" / "VIEW ALL"). **Two-card grid** (2 columns, gap 14px — not a scroller). Each card: `--card` + `--hair` border; a 2×2 mini-grid of tone thumbnails, then caption with serif outfit name + uppercase folder tag (e.g. "NIGHT OUT", "WORK"). **Folder names are dynamic** — populated from folders the user created via Inspo Match's "Save as outfit" flow (see State); falls back to defaults when none exist. Shows the two most recent; the rest are behind VIEW ALL.
- **Rediscover**: label row ("REDISCOVER" / "VIEW ALL 6"). 2-column grid (gap 14px). Each card: `--card` + `--hair`; image placeholder `aspect-ratio:1/1.26` with 14px inset; caption serif name (15.5px, one line) + meta sub (9.5px, e.g. "UNWORN · 4 MONTHS").
- **Section label pattern** (reused everywhere): `.sec-head` = space-between row; `.sec-title` = 12px / 1.8px tracking / `--muted` / 600; `.sec-link` = 12px / `--ink` / 600 / underlined, 3px offset.

### 3. Wardrobe  (`Wardrobe/ClosetMind Wardrobe.html`)
> Note: this screen lives in the `Wardrobe/` subfolder; its links reference siblings with `../`.
- **Purpose**: Browse and filter the full closet as a grid.
- **Header**: serif "Wardrobe" title (30px, white) left; search + a sliders/filter icon (2-line-with-knobs SVG) right. Padding 16px.
- **Category nav** (`.catnav`): horizontally-scrolling row (overflow-x:auto, scrollbar hidden, 24px gap, 22px pad, 1px bottom hairline). Items ALL / TOPS / BOTTOMS / SHOES / OUTERWEAR / BAGS / ACCESSORIES (11px, 1.4px tracking, 600, `--muted`). Active ("ALL") is `--ink` with a 2px `--ink` underline bar pinned to the bottom edge. **This row scrolls horizontally by design.**
- **Filter chips** (`.chips`): centered, single line, no-wrap, 8px gap, 18px pad. ALL (active = `--accent` fill, white), ♡ FAVOURITES (leading 13px heart), UNWORN, NEW. Inactive chip = `--card` fill, 1px `--hair`, `--ink` text, 10.5px / 0.8px tracking / 600, pad 9×12px.
- **Count bar**: "22 PIECES" (12px, 1.6px tracking, 600) left; view toggle right — filled 4-square grid (active `--ink`) + 2-row list (`--muted-2`), 20px, 16px gap.
- **Item grid** (`.grid`): 3 columns, 8px gap, 22px side pad, **22 tiles**. Each `.gitem`: `aspect-ratio:1/1.34`, flat tone fill. Top-right favourite heart (18px) — filled `--ink` when favourited, else outline `--muted-2`. Some tiles carry a NEW badge bottom-left (black bg, white 10px/1.4px text, 6×12px pad).
- Wardrobe tab active.

### 4. Mira  (`ClosetMind Mira.html`)
- **Purpose**: Conversational AI stylist — describe an occasion, get an outfit suggestion built from your wardrobe.
- **Header**: serif "Mira" title (white). 
- **Thread** (scrolling body):
  - **User message** (`.msg-user`): right-aligned bubble, darker fill, e.g. "Night out — birthday dinner, something elegant".
  - **AI message** (`.msg-ai`): left-aligned, small leading bullet/dot + serif bubble copy, e.g. "Found the perfect look — elegant but still you." Mira's outfit replies include a thumbnail set of suggested pieces. Closes with a follow-up prompt bubble "Want me to try a different vibe or swap any pieces?".
- **Composer** (pinned above tab bar): rounded input field with a leading chat/message SVG and placeholder "Tell Mira what you need…", plus a mic button.
- Mira tab active.

### 5. Inspo  (`ClosetMind Inspo.html`)
- **Purpose**: Save outfit inspiration photos to a personal board.
- **Header**: serif "Inspo" title (white); right side has a **+** icon that opens the add-photo bottom sheet.
- **Grid** (`.igrid`): 3 columns of `.itile` inspiration photos (flat tone placeholders, ~14 tiles), even gaps. Tapping any tile → **Inspo Match** detail.
- **Add-inspo bottom sheet** (`.sheet` + `.scrim`): slides up from bottom, `--paper`, 26px top radius, grab handle. Title "Add inspo photo", lead label "CHOOSE FROM YOUR LIBRARY", and option rows (`.addopt`): each is a 54px square badge (icon) + title/subtitle text + trailing arrow. First option "Upload from camera roll — Pick a photo from your library" (image icon). Scrim tap or CANCEL dismisses.
- Inspo tab active.

### 6. Inspo Match  (`ClosetMind Inspo Match.html`)
- **Purpose**: Detail view for one saved inspo photo, centered, with save/delete actions.
- **Header** (thin black banner, same height as other screen headers): back chevron ("‹") returning to Inspo, title "Inspo", and a trailing **⋯** (3-dot) button.
- **Body**: the selected inspo photo centered (large tone placeholder). 
- **Actions bottom sheet** (opened by ⋯): slides up over a dimmed scrim, grab handle, two option rows + CANCEL:
  - **Save as outfit** — heart badge (`#ECEAE3`), title + "Add this look to a folder", trailing arrow. Opens the **folder flow**.
  - **Delete inspo photo** — red trashcan in a pink badge (`#F6E7E5`), red title + "This can't be undone".
- **Save-to-folder flow** (inside the sheet): a folder list view (existing folders + a "Create new folder" row) and a "new folder" view with a single **Folder name** text field and a **CREATE FOLDER & SAVE** button (plus CANCEL / back). No name is auto-populated; no suggestion chips. On save, the folder is persisted and surfaces on Home's Saved Outfits.
- Inspo tab active.

### 7. Profile  (`ClosetMind Profile.html`)
- **Purpose**: Account identity and app settings.
- **Header**: serif "Profile" title (white).
- **Identity card**: 64px black circle avatar with serif monogram "M" (white) + name "Martha" (serif 24px) + "martha@smu.edu" (13px `--muted`) + outlined **EDIT** button (11px / 1.6px tracking).
- **PREFERENCES group**: rows (each = 42px `#ECEAE3` icon badge + serif label 18px + right-aligned value + chevron): Location · "Plano, TX"; Notifications · "On"; Weather unit · "°C".
- **ACCOUNT group** (separated by an 8px `--paper-2` divider): Change password; Help & support; Privacy policy (icon + label + chevron each).
- **Log out**: row with pink badge (`#F6E7E5`) + red door/arrow icon + red "Log out" label → navigates to Sign In. Footer "CLOSETMIND · V1.0.0" (11px / 1.8px tracking, `--muted-2`).
- Profile tab active.

---

## Interactions & Behavior
- **Tab bar**: switches between the five top-level destinations; active = accent color + dot.
- **Auth**: SIGN IN → Home. CREATE AN ACCOUNT → create view. CREATE ACCOUNT → inline collapse to "Account created!" success → BACK TO SIGN IN. Profile "Log out" → Sign In.
- **Home**: REGENERATE re-rolls today's suggestion. Saved Outfits cards reflect folders created in Inspo Match (most-recent first, max two shown, rest behind VIEW ALL). Search icon expands an in-page search state.
- **Wardrobe**: category nav scrolls horizontally; tapping a category filters the grid and slides the underline. Filter chips toggle (single active, filled). View toggle = grid ↔ list. Favourite heart toggles filled/outline.
- **Mira**: typing in the composer + send appends a user bubble and an AI reply with a suggested-outfit thumbnail set; follow-up prompt offered.
- **Inspo**: **+** opens the add-photo bottom sheet (slide-up, scrim dim, dismiss on scrim/cancel). Tapping a tile → Inspo Match.
- **Inspo Match**: ⋯ opens the actions bottom sheet. Save as outfit → folder list → create/choose folder → CREATE FOLDER & SAVE persists and dismisses. Delete is destructive.
- **Transitions**: bottom sheets slide up `cubic-bezier(.32,.72,0,1)` ~.32s with a fading scrim. The success collapse animates `max-height`/opacity ~.5s. Respect reduced-motion (no entrance animation required).

## State Management
- `authView` — `signin | create | created` (Sign In screen).
- `activeTab` — `home | wardrobe | mira | inspo | profile`.
- `activeCategory` — selected wardrobe category (default "ALL"); `activeFilter` — chip (default "ALL"); `viewMode` — `grid | list`.
- `favourites` — set of garment IDs.
- `garments` — wardrobe items (id, name, brand, category, imageURL, isNew, isFavourite, lastWorn, dateAdded).
- `todaysSuggestion` — generated outfit (garment IDs + title); REGENERATE re-rolls.
- `savedOutfits` — array of `{ name, folder }`; created via Inspo Match's Save-as-outfit flow, **read by Home's Saved Outfits** (most-recent first). In the prototype this is persisted to `localStorage` under `cm_saved_outfits` — in-app, use real persistence.
- `inspoPhotos` — saved inspiration images; `miraThread` — conversation messages (role + content + optional outfit).
- `pieceCount` — derived from filtered garments ("N PIECES").

## Design Tokens
Colors (CSS custom properties → map to your theme):
- `--backdrop` `#F4F0E7` — surround behind device (prototype only)
- `--paper` `#FAF9F7` — app page background
- `--card` `#FFFFFF` — card / box fill
- `--paper-2` `#F3F1EA` — secondary field / group divider
- `--placeholder` `#E8E6DF` — image placeholder tiles; placeholder icon stroke `#9E9A8F`
- `--ink` `#1B1916` — primary text; `--ink-soft` `#2A2722`
- `--muted` `#9A968B` — labels/sub text; `--muted-2` `#B4B0A6` — lighter icons
- `--hair` `#E2DFD6`, `--hair-2` `#E7E4DB` — hairlines/borders
- `--black` `#0C0B0A` — header, tab chrome, device body
- `--accent` `#1B1916` — active nav/chip/dot/tag (mono default; alt options surfaced: terracotta `#A65A3C`, sage `#6E7355`, slate `#4A5A6E`, plum `#7C5A6B`)
- Status / destructive: success check circle `#0C0B0A`; danger red text `#B23B3B`, danger badge fill `#F6E7E5`; neutral badge fill `#ECEAE3`.

Typography:
- Serif display: **Newsreader** (Google) — wordmark, titles, item/outfit names, message bubbles. Weights 400–700. Alt options offered: Spectral, Source Serif 4.
- Sans / labels: system stack (`-apple-system, "Helvetica Neue", Helvetica, Arial`). All-caps labels use 1.3–2.6px letter-spacing.
- Scale in use: 34px auth wordmark · 30px screen title/Home wordmark · 25px auth heading · 24px profile name/success · 20px suggestion title · 18px settings row · 17px list row · 15.5px rediscover name · 14px field/sub · 13px weather/email · 12px section labels · 11px categories/sub · 10.5px chips · 9.5px tab labels.

Spacing & shape:
- Section side padding 22px; card padding 14px; grid gaps 8px (wardrobe) / 9–14px (home/inspo).
- Radius: cards/tiles ~2px (nearly square — editorial); bottom sheets 26px top; device 54px outer / 43px inner; avatars/auth logo full circle.
- Borders: 1px hairlines throughout. No drop shadows on in-app surfaces (device frame has an external shadow only).
- Tile aspect ratios: wardrobe `1/1.34`, home suggestion thumb `1/1.18`, rediscover `1/1.26`.
- Tap targets ≥ 44px.

## Assets
- **Icons**: all inline SVG (search, plus, sliders/filter, heart, hanger, weather cloud, signal/wifi/battery, tab icons, view-toggle grid/list, garment glyphs, chat/mic, back/forward chevrons, lock, bell, location, shield, log-out door, trash, check). Reproduce with the codebase's icon set (e.g. SF Symbols), stroke ~1.5–1.7.
- **Logo**: `closetmind-mark.png` — the ClosetMind monogram used in the Sign In header (white mark on a dark circle). Included in this bundle.
- **Fonts**: Newsreader via Google Fonts in the prototype; bundle or substitute an equivalent serif in-app.
- **Garment / outfit / inspo images**: placeholders only — supply real images at the listed aspect ratios.

## Files
All screens share the same token block, device frame, status bar, and tab-bar markup near the top/bottom — a good place to extract shared components.
- `ClosetMind Sign In.html` — Sign in + create account (auth entry).
- `ClosetMind Home.html` — Home.
- `Wardrobe/ClosetMind Wardrobe.html` — Wardrobe (in its own subfolder; links use `../`).
- `ClosetMind Mira.html` — Mira AI stylist chat.
- `ClosetMind Inspo.html` — Inspo board + add-photo sheet.
- `ClosetMind Inspo Match.html` — Inspo detail + save/delete + folder flow.
- `ClosetMind Profile.html` — Profile + settings + log out.
- `tweaks-panel.jsx` — design-time theme explorer dependency (NOT part of the app; ignore when porting).
- `closetmind-mark.png` — logo asset.
