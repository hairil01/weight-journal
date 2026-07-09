# Weight Journal — Product Requirements Document

Status: Draft v1
Source of truth for UI: [design/Weight Journal Enhanced.dc.html](../design/Weight%20Journal%20Enhanced.dc.html)
Date: 2026-07-09

---

## 1. App Overview

Weight Journal is a mobile journaling app for tracking body weight alongside the daily context that actually explains why weight moves — meals, activity, mood, and notes. The product thesis, taken directly from the design copy ("log less, learn more"), is that a lightweight, low-friction daily entry produces better long-term insight than a heavyweight calorie tracker.

Core loop:
1. Log morning weight (+ optional before/after-meal weight) and breakfast/morning notes.
2. Later in the day, log evening weight, lunch/dinner/snacks, quick-tap activities, mood, and notes.
3. Review trends (Progress), browse history (Timeline), and get pattern-based feedback (Insights).

Key product properties:
- **Time-of-day aware**: the Journal screen automatically shows the Morning section before ~3pm and unlocks the Evening section afterward (with a manual preview override for demo/testing).
- **Multilingual**: English, Bahasa Malaysia, and Chinese (Simplified) are first-class, not an afterthought — all copy is translated, including locale-aware date formatting.
- **Unit-flexible**: kg/lbs toggle available everywhere weight is shown; all weight is stored canonically in kg and converted for display.
- **Local-first**: no accounts, no login. All data lives on-device. (See §7 Offline Storage Strategy.)
- **Free**: no paywalls in v1. The "AI Summary" card is a visible teaser but explicitly out of scope for this version (see §9 Future Enhancements).

## 2. Target Users

- **Primary**: Individuals on a weight-loss or weight-management journey who want a simple daily ritual, not a calorie-counting spreadsheet. They care more about "what habits move the needle" than macro-level nutrition data.
- **Secondary**: Malaysians/Southeast Asian users specifically — evidenced by MY-locale copy examples ("ayam gepuk", "nasi lemak", "roti", OMAD as a named pattern) and trilingual support (EN/MY/ZH). The app should feel native to this audience's food vocabulary and language mix.
- **Usage pattern**: twice-daily check-in (morning + evening), short session (under a minute per entry), occasional deeper session to review Progress/Timeline/Insights (weekly reflection).
- **Technical comfort**: general consumer, not power users — hence quick-tap activity chips and emoji moods rather than free-form data entry as the primary input method.

## 3. Screen-by-Screen Functionality

The app has 4 tabs, always visible via a bottom tab bar: **Journal, Progress, Timeline, Insights**. A language switcher (EN/MY/中文) appears in the header of every tab.

### 3.1 Journal (home tab)
Primary daily entry screen. Content changes based on time-of-day mode (`auto` | `morning` | `evening`), with a preview toggle for testing/demo.

- **Header**: "New Entry" label, today's date (locale-formatted), day subtitle, language switcher.
- **Preview toggle** (dev/demo aid): Auto / Morning / Evening — forces which section renders regardless of clock.
- **Completion card**: shows % of today's entry completed, computed from 4 checklist items: Morning weight logged, Meals logged (any of breakfast/lunch/dinner/snacks), Activities logged, Notes/mood logged.
- **Morning section** (shown when mode = morning, or always visible as the "already logged" summary once entered):
  - Morning weight input (decimal, kg/lbs toggle).
  - Optional expandable "before/after meal weight" sub-inputs.
  - Breakfast text field.
  - Morning notes text field (optional).
- **Evening reminder banner**: shown when in evening mode but evening entry is incomplete (no evening weight, no lunch/dinner, no mood).
- **Evening section** (shown when mode = evening):
  - Evening weight input.
  - Lunch, Dinner, Snacks (optional) text fields.
  - **Activities**: 6 quick-tap chips (Walking, Running, Gym, Cycling, Yoga, Swimming) that toggle on/off, plus a free-text "+ Custom" input for anything else. Selected/custom activities render as removable chips.
  - **Mood**: 5-option emoji picker (Great, Good, Okay, Difficult, Very hard), single-select.
  - Evening notes (optional, multiline).
- **Save Entry button**: persists the day's entry; shows a transient "✓ Saved" state (~1.8s) then reverts.
- One entry per calendar day — morning and evening data merge into a single day record.

### 3.2 Progress
Trend and milestone visualization for the whole journal history.

- **Header**: current weight (large, kg/lbs), subtitle ("Started at X kg · N days logged").
- **Stat tiles** (3-up): Lost (total), Started (starting weight), Streak (days logged).
- **Trend chart**: line chart (SVG polyline) of weight over the logged period (design uses a 21-day series), with kg/lbs toggle and min/max labels.
- **Averages**: Weekly average, Monthly average.
- **Milestone ladder**: vertical list of weight thresholds between start weight and goal weight (e.g., every 5kg step), each marked achieved/not-achieved, plus a goal line and % progress to goal.
- **Weekly Reflection**: tappable card opening a bottom-sheet modal with 4 free-text reflection prompts (How was your week? / What went well? / Biggest challenge? / What will you improve?) and a Save action. One reflection per week.

### 3.3 Timeline
Reverse-chronological history of all logged days, with filtering.

- **Header**: "Timeline" title + language switcher.
- **Filter chips**: Walking, Gym, OMAD (single-meal day), Weight Gain, Weight Loss (single-select toggle), Date Range (reveals From/To date pickers).
- **Mood filter**: row of 5 mood emoji, toggle to filter by mood.
- **Entry cards** (one per day, newest first): weekday + date header, then a vertical timeline of sub-events for that day, each with an icon and only rendered if data exists:
  - Morning weight (M badge)
  - Meals (🍽️ — breakfast/lunch/dinner/snacks, only non-empty ones)
  - Activities (🏃 — chips of logged activities)
  - Mood (emoji + label)
  - Notes (📝 — quoted italic text)
  - Evening weight (E badge)
- **Empty state**: "No entries match these filters" when filters exclude everything.

### 3.4 Insights
Pattern-based feedback derived from logged history (rule-based in v1, not ML — see §10).

- **Header**: "Insights" title, "Habit Correlation" heading, subtitle showing days-of-data count.
- **Habits Helping** (loss habits): cards showing habits correlated with weight loss (e.g., Walking, No supper, OMAD) with average kg lost and a confidence label (High/Medium).
- **Habits Increasing Weight** (gain habits): cards for habits correlated with weight gain (e.g., Late-night meals, Soft drinks, No exercise) with average kg gained.
- **Personal Insights**: bulleted list of plain-language, rule-generated observations (e.g., "You tend to lose more weight on days you walk more than 20 minutes").
- **AI Summary teaser**: de-emphasized card advertising a locked "AI Summary" feature — **out of scope for v1**, tapping it shows a "coming soon" toast. Retained in the UI as a forward-looking hook (§9).
- **Export Journal**: export all data as TXT, PDF, or CSV, described as "take your data to any AI assistant for deeper analysis" — this is the actual v1 mechanism for AI-assisted analysis, done externally by the user.

### 3.5 Global elements
- **Bottom tab bar**: Journal / Progress / Timeline / Insights, persistent, active tab indicated by filled/bold icon + label.
- **Toast notifications**: transient bottom-of-screen messages (save confirmations, export confirmations, "coming soon" notices).
- **Language switcher**: EN / MY (Bahasa Malaysia) / 中文, present in every screen's header, applies instantly and globally.
- **Unit switcher**: kg / lbs, present anywhere weight is displayed or entered, applies instantly and globally.

## 4. Navigation Flow

```
App Launch
   │
   ▼
Journal (default tab) ──────────────┐
   │                                 │
   │ time-of-day auto-detected       │
   │ (before 3pm → Morning view,     │
   │  after 3pm → Evening view)      │
   │                                 │
   ▼                                 ▼
[Bottom Tab Bar — always visible, persists across app]
   │           │            │            │
Journal    Progress     Timeline     Insights
   │           │            │            │
   │           ▼            │            │
   │   Weekly Reflection    │            │
   │   (modal bottom sheet, │            │
   │    dismissable)        │            │
   │                        ▼            │
   │              Filter/date-range      │
   │              refine in place        │
   │              (no navigation)        │
   │                                     ▼
   │                          Export (TXT/PDF/CSV)
   │                          triggers OS share/save
   ▼
Save Entry → toast confirmation → stays on Journal
```

- Navigation is flat (tab-based), no deep stacks. The only modal/overlay is the Weekly Reflection sheet, opened from Progress and dismissed via X or Save.
- No onboarding, login, or settings screens are defined in the current design — first-run experience and settings are open questions (see §11 Open Questions).
- Language and unit preferences are global state, not per-screen — changing either anywhere updates the whole app.

## 5. Database Schema

Local-only, single-user, single-device (see §7). Proposed relational-style schema (implementable in SQLite via e.g. `expo-sqlite` or `op-sqlite`):

```sql
-- One row per calendar day; morning and evening data share a row.
CREATE TABLE journal_entries (
  id                TEXT PRIMARY KEY,          -- UUID
  entry_date        TEXT NOT NULL UNIQUE,      -- ISO 8601 date, e.g. "2026-07-09"

  morning_weight_kg REAL,                      -- nullable until logged
  before_meal_kg    REAL,                      -- optional
  after_meal_kg     REAL,                      -- optional
  breakfast         TEXT,
  morning_notes     TEXT,

  evening_weight_kg REAL,
  lunch             TEXT,
  dinner            TEXT,
  snacks            TEXT,
  mood              TEXT,                      -- 'great'|'good'|'okay'|'difficult'|'veryhard'
  evening_notes     TEXT,

  created_at        TEXT NOT NULL,             -- ISO 8601 timestamp
  updated_at        TEXT NOT NULL
);

-- Many-to-many: activities logged per entry (quick-tap + custom, both stored as text label)
CREATE TABLE entry_activities (
  id          TEXT PRIMARY KEY,
  entry_id    TEXT NOT NULL REFERENCES journal_entries(id) ON DELETE CASCADE,
  activity    TEXT NOT NULL,                   -- e.g. "Walking", "Gym", or free-text custom
  is_custom   INTEGER NOT NULL DEFAULT 0        -- 0/1 boolean
);

-- One row per ISO week
CREATE TABLE weekly_reflections (
  id          TEXT PRIMARY KEY,
  week_start  TEXT NOT NULL UNIQUE,             -- ISO date of Monday (or locale week start)
  q1_how_week TEXT,
  q2_went_well TEXT,
  q3_challenge TEXT,
  q4_improve  TEXT,
  created_at  TEXT NOT NULL,
  updated_at  TEXT NOT NULL
);

-- Single-row table for global user preferences
CREATE TABLE user_settings (
  id                  INTEGER PRIMARY KEY CHECK (id = 1),  -- singleton
  unit                TEXT NOT NULL DEFAULT 'kg',           -- 'kg'|'lbs'
  language            TEXT NOT NULL DEFAULT 'en',           -- 'en'|'ms'|'zh'
  goal_weight_kg       REAL,
  show_meal_weights_default INTEGER NOT NULL DEFAULT 0
);
```

Notes:
- Weight is always stored canonically in **kg**; unit conversion happens only at the presentation layer. This matches the prototype's `toKg`/`toDisplay` logic.
- `journal_entries.entry_date` uniqueness enforces "one entry per day"; morning/evening are just nullable column groups on the same row, not separate records — this matches the merge behavior implied by the design (a day's entry accumulates through the day).
- Insights (habit correlations) are **derived at read-time** from `journal_entries` + `entry_activities`, not stored — no schema needed for them in v1 since the logic is simple aggregate/rule-based (see §10).
- Timeline filters (walking/gym/omad/gain/loss/mood/date-range) are all computed from existing columns; no additional schema needed.

## 6. State Management

Recommended for React Native: **Zustand** (or React Context + `useReducer` if avoiding a dependency) for global UI/app state, with SQLite as the source of truth for persisted data and the store as a synced cache/view over it.

Global state (mirrors the prototype's `state` object almost 1:1):

| Slice | Fields | Notes |
|---|---|---|
| **Preferences** | `unit`, `language` | Persisted to `user_settings`; changing either re-renders the whole app (all screens read from this slice). |
| **Draft entry** (today's in-progress entry) | `morningWeightKg`, `beforeMealKg`, `afterMealKg`, `breakfast`, `morningNotes`, `eveningWeightKg`, `lunch`, `dinner`, `snacks`, `activities[]`, `customActivityInput`, `mood`, `eveningNotes` | Loaded from DB on app open (today's row if it exists), autosaved or explicitly saved via "Save Entry". |
| **UI-only / ephemeral** | `activeTab`, `timeMode` (auto/morning/evening preview), `showMealWeights`, `showReflection`, `showDateFilter`, `toast`, `filterTag`, `filterMood`, `dateFrom`, `dateTo` | Never persisted; resets on app restart except `activeTab` (optionally remembered). |
| **Reflection draft** | `reflectionQ1-4` | Loaded/saved against the current ISO week's row. |
| **Derived/query results** | `timelineEntries`, `progressStats` (lost/started/streak/averages/milestones), `insights` (loss/gain habits, smart insight lines) | Computed via selectors from DB queries, not stored as separate mutable state — recompute on data change to avoid drift. |

Principles:
- Treat SQLite as the single source of truth; the in-memory store is a projection for rendering, not a competing source of state.
- Writes to the draft entry are debounced/autosaved to avoid data loss if the app is backgrounded mid-entry (the current design only writes on explicit "Save", which risks losing an entry — recommend upgrading to autosave-on-change in the real app, flagged as a deviation from the prototype for reliability).
- Unit and language conversions happen only in selectors/view layer, never mutate stored data.

## 7. Offline Storage Strategy

The app is **local-first and fully offline-capable by design** (per user decision — no accounts, no cloud sync in v1).

- **Primary store**: SQLite on-device (via `expo-sqlite` or similar), per §5 schema. Chosen over AsyncStorage/MMKV because the data is relational (entries ↔ activities) and needs filtering/aggregation (Timeline filters, Progress stats, Insights correlations) that key-value storage handles poorly.
- **No network dependency**: the app never requires connectivity to function. There is no server component in v1.
- **Backup/restore**: since there's no cloud sync, data loss on device loss/reinstall is a real risk. Recommend, even in v1:
  - Export already covers a manual "downloaded copy" (TXT/PDF/CSV) — but these are lossy, human-readable formats, not a re-importable backup.
  - Add a JSON export/import specifically for backup/restore purposes (distinct from the human-readable TXT/PDF/CSV export), so a user can move to a new phone manually via file share/iCloud/Google Drive.
- **Migrations**: use a versioned schema migration approach from day one (e.g., a `schema_version` row in `user_settings` or a dedicated migrations table) since local-first apps can't do server-side data fixes — the app must self-migrate on update.
- **Storage growth**: entries are small (a few hundred bytes each); even years of daily entries stay well within SQLite's comfortable range — no pruning/archival strategy needed for v1.

## 8. Technical Architecture

Per user decision: **React Native (Expo)**, local-only, no backend.

```
┌─────────────────────────────────────────────┐
│                 App (Expo/RN)                │
│                                               │
│  ┌───────────────┐   ┌─────────────────────┐ │
│  │  UI Layer      │   │  i18n layer         │ │
│  │  (4 tab        │   │  (en/ms/zh string   │ │
│  │  screens +     │   │  tables + locale-   │ │
│  │  reflection    │   │  aware date/number  │ │
│  │  modal)        │   │  formatting)        │ │
│  └──────┬────────┘   └─────────────────────┘ │
│         │                                     │
│  ┌──────▼────────┐                           │
│  │ State layer    │  (Zustand or Context)     │
│  │ (§6)           │                           │
│  └──────┬────────┘                           │
│         │                                     │
│  ┌──────▼────────┐   ┌─────────────────────┐ │
│  │ Data access    │   │ Insights engine      │ │
│  │ layer (repo    │◄──┤ (rule-based          │ │
│  │ pattern over   │   │ correlation logic,   │ │
│  │ SQLite)        │   │ runs on-device,      │ │
│  └──────┬────────┘   │ no ML/network)        │ │
│         │             └─────────────────────┘ │
│  ┌──────▼────────┐                            │
│  │ SQLite (on-    │                            │
│  │ device, §5/§7) │                            │
│  └───────────────┘                            │
│                                                │
│  ┌───────────────┐                            │
│  │ Export module  │  → OS share sheet          │
│  │ (TXT/PDF/CSV)  │                            │
│  └───────────────┘                            │
└─────────────────────────────────────────────┘
```

Suggested libraries (React Native/Expo ecosystem):
- **Navigation**: `@react-navigation/bottom-tabs` (matches the 4-tab structure exactly).
- **Storage**: `expo-sqlite`, with a thin repository layer (e.g., `drizzle-orm` or hand-written query functions) rather than raw SQL scattered through UI code.
- **State**: `zustand` for global preference/draft/UI state.
- **i18n**: `i18next` + `react-i18next`, or a lightweight custom table lookup mirroring the design's `translations` object structure (design already ships full EN/MS/ZH tables that can be lifted directly).
- **Charts**: `react-native-svg` (the design's trend chart is a hand-rolled SVG polyline — no charting library dependency needed for v1's single line chart).
- **Export**: `expo-file-system` + `expo-sharing` for TXT/CSV; a PDF-generation library (e.g., `expo-print`) for PDF export.
- **Date handling**: `date-fns` with locale packs for `en`, `ms` (fallback to `en` if unsupported), `zh-CN`.

No backend, no auth provider, no analytics/crash-reporting SDK is assumed in v1 — all out of scope per the local-only decision, though crash reporting (e.g., Sentry) is worth reconsidering before public release (see §11).

## 9. Future Enhancements

Explicitly deferred from v1, but visible as hooks in the current design:

1. **AI Summary** (shown as a locked teaser card in Insights): AI-generated personalized monthly recap from journal data. Requires either on-device LLM or a backend + API integration — a significant scope jump from local-only, so deliberately deferred.
2. **Cloud backup/sync & multi-device**: currently local-only by decision; a natural v2 if users request moving between devices or want durability beyond manual export.
3. **Reminders/notifications**: the design has an in-app "evening reminder" banner but no push notification to prompt users to log — local push notifications (e.g., "log your evening weight") would reinforce the twice-daily habit loop.
4. **Richer insights/ML**: v1 insights are simple rule-based correlations (fixed habit list: walking, OMAD, no supper, late meals, soft drinks, no exercise). A future version could generalize this to detect arbitrary user-specific patterns rather than a fixed set.
5. **Goal-setting UI**: goal weight currently appears to be a fixed/derived value in the Progress milestone ladder; a dedicated goal-setting flow (set/edit target weight, target date) is implied but not designed.
6. **Onboarding**: no first-run/onboarding screens exist in the current design (unit/language defaults, goal weight entry, explaining the morning/evening rhythm).
7. **Settings screen**: language and unit are changeable from any screen's header, but there's no dedicated settings screen for other preferences (e.g., default time-of-day cutoff, data export/backup, notification preferences).
8. **Photo progress tracking**: common in weight-journal apps; not present in this design but a plausible v2 addition alongside the weight chart.

## 10. Insights Engine Logic (v1, rule-based)

To avoid ambiguity when implementing the Insights tab, the correlation logic should be rule-based aggregate statistics over `journal_entries` + `entry_activities`, not ML:

- **Habit → weight delta**: for a fixed set of tracked habits (Walking, Gym, OMAD, No supper, Late-night meals, Soft drinks, No exercise), compute the average day-over-day weight change (`evening_weight - previous_evening_weight`, or `morning - previous morning`, tbd) on days where the habit occurred vs. days it didn't. Habits with an average delta ≤ 0 sort into "Habits Helping"; > 0 sort into "Habits Increasing Weight".
- **Confidence label**: High/Medium based on sample size (count of days matching the habit) — exact thresholds are an implementation decision, not yet specified by the design.
- **Smart insight lines**: templated plain-language sentences generated from the same aggregates (e.g., best-performing weekday, average weekly loss) — the design hardcodes example sentences; the real implementation should generate them from actual logged data.
- This entire engine runs synchronously on-device with no network call — consistent with the offline-first architecture.

## 11. Open Questions / Gaps in Current Design

These aren't answered by the design file and should be resolved before or during implementation:

- **Onboarding & first-run**: no goal-weight entry, unit/language default selection, or explanatory flow exists yet.
- **Settings screen**: not designed; language/unit switches are inline-only.
- **Editing past entries**: Timeline is read-only in the current design — can users edit/delete a past day's entry?
- **Morning/evening cutoff time**: hardcoded to 3pm (15:00) in the prototype logic — should this be configurable?
- **Backup/restore UX**: no JSON export/import UI exists yet (recommended in §7 but not designed).
- **Empty states**: only Timeline has a defined empty state; Progress/Insights behavior with zero or very little data (e.g., day 1, no chart history) is undefined.
- **Notifications**: no permission request or notification-scheduling flow is designed (relevant if reminders are pursued, §9.3).
