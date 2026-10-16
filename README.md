# Handoff: Alaska Pulse — mobile data-story app

## Overview
Alaska Pulse is a mobile app/site that tells the year-by-year story of Alaska (2015–2024) across people, economy, housing, resources, culture, health and climate. Users scrub through years, explore 17 indicators and 18 regions, compare years or regions, run an oil/dividend "what-if" simulator, pin insights into an exportable report, and ask **Aurora**, an AI analyst, to explain any number.

## About the design files
`reference/Alaska Pulse v2.dc.html` is a **design reference built in HTML**: a prototype that shows the intended look and behavior. It is not production code. Rebuild it in your target stack. **Recommended stack:** Next.js (App Router) + React + TypeScript + Tailwind, or Vite + React if you don't need server routes. To view the prototype, open the file in a browser; `support.js` must sit next to it.

All content (numbers, notes, narratives, regions, model constants) has been extracted to **`data/alaska-data.json`**. Make that file (or a CMS) the single source of truth, so content can be edited without touching components.

## Fidelity
**High-fidelity.** Colors, type, spacing, radii and interactions are final for **dark mode**. Light mode is currently a CSS `invert(1) hue-rotate(180deg)` filter on the whole screen. That is a placeholder: build a real light palette instead.

**Data is approximate** and needs to be verified against primary sources before anything is published. The softest numbers are the age mix, revenue ribbon, regional salmon, food costs, wild-food harvest and road-vs-rural gaps. The climate stripes before 2015 are synthetic.

---

## App shell
- Designed at a **390 × 844** phone viewport. On the web, center the app in a max-width column of 430px; desktop can use the same layout.
- **Status-bar area:** 47px top inset. In real builds use `env(safe-area-inset-top)`.
- **Scrollable content area:** between top inset and tab bar. It resets scroll to top on tab or topic change. In the Ask view it auto-scrolls to the bottom when new messages arrive.
- **Bottom tab bar:** 84px tall (includes 22px home-indicator padding). Background `oklch(0.15 0.03 255 / 0.96)`, 1px top border `oklch(0.26 0.03 255)`. Five equal tabs: **Story · Pulse · Explore · Map · Ask AI**. Each tab has a 22×3px indicator bar (accent `s` when active, transparent otherwise) above a 12px/600 label (`t` when active, `m` otherwise). Sub-screens highlight the tab they came from: Topic/Compare use `from`, What-if/Saved use `back`.
- **Floating "◎ Point & ask" button** (every tab except Ask, hidden during onboarding): fixed right 14px, bottom 96px, 44px tall pill, Plex Mono 12/600, bg `oklch(0.21 0.04 200)`, text `g`, 1px border `g/0.5`, shadow `0 8px 24px rgba(0,0,0,.45)`. When active it turns into "✕ Cancel" (bg `t`, text `bg`), and a banner appears at the top (bg `g`, text `bg`, radius 14): "Point mode · tap any number, bar or tile [Cancel]".
- **Toast:** centered, bottom 150px, bg `t`, text `bg`, 13/600, radius 14. Auto-hides after 1.6s.

## Design tokens

### Colors (oklch; convert to hex with a tool if needed)
| token | value | use |
|---|---|---|
| `bg` | `oklch(0.17 0.03 255)` | app background |
| `bg-deep` | `oklch(0.15 0.03 255)` | tab bar, onboarding |
| `surface` / `sf` | `oklch(0.22 0.03 255)` | cards |
| `surface-inset` | `oklch(0.19 0.03 255)` | chart wells inside cards |
| `surface-3d` | `oklch(0.20 0.03 255)` | 3D chart cards |
| `border` | `oklch(0.30 0.03 255)` | card borders |
| `line` | `oklch(0.34 0.03 255)` | outline buttons |
| `divider` | `oklch(0.27–0.28 0.03 255)` | table rows, bar tracks |
| `dim` | `oklch(0.36 0.03 255)` | inactive bars |
| `t` (text) | `oklch(0.96 0.01 240)` | primary text |
| `text-2` | `oklch(0.80–0.88 0.01 245)` | body secondary |
| `m` (muted) | `oklch(0.72 0.02 245)` | labels, captions |
| `faint` | `oklch(0.55–0.60 0.02 245)` | footnotes |
| `s` (salmon/accent) | `oklch(0.78 0.12 40)` | primary accent, CTAs, "bad" deltas |
| `g` (aurora green) | `oklch(0.82 0.14 165)` | AI/Aurora, "good" deltas |
| `b` (glacier blue) | `oklch(0.80 0.11 230)` | links/back, secondary series |
| `aurora-card` | bg `oklch(0.21 0.04 200)`, border `g / 0.35` | all Aurora (AI) cards |
| `violet` | `oklch(0.72 0.10 290)` | revenue "Savings draw" |
| `other-gray` | `oklch(0.60 0.02 245)` | revenue "Other" |

**Delta color rule:** use `g` if the change is good (`higherIsGood` matches the direction), `s` if bad, and `m` if the indicator is `neutral` or there is no change.

### Typography
- **IBM Plex Sans** 400/500/600/700: body and headings. **IBM Plex Mono** 400/500/600: labels, numbers, eyebrows, chips.
- Hero year: 60px/600, letter-spacing −0.04em, line-height 0.95.
- Screen titles: 34px/600 (tab roots), 30px/600 (sub-screens), −0.03em.
- Topic big value: 52px/600, −0.04em.
- Metric values: 40px (population), 30px (who lives here), 24px (tiles), 22px (stat cards), 18px (what-if outputs).
- Body: 15px/1.55 for story text, 14px/1.5 for notes, 12–13px for secondary text.
- Eyebrow: Plex Mono 10–11px, weight 500–600, letter-spacing 0.12–0.14em, uppercase, color `m` (or `s`/`g` for chapter and Aurora labels).
- Use `text-wrap: pretty` on paragraphs.

### Radii, spacing, effects
- Radius: 54 (device frame), 22 (cards), 20 (tile cards/pills), 18 (tiles, chat bubbles), 16 (buttons, inner wells), 14 (small cards, segmented control), 10–12 (year chips), 9 (region chips), 5–6 (bar tracks/bars), 2–3 (mini bars).
- Screen padding: 20px horizontal (Story uses 16px). Vertical gap between sections: 12–16px. Card padding: 16px (18px on Pulse).
- Aurora "live" dot: 8px circle `g` with `box-shadow: 0 0 10px g`.
- Minimum hit target: 40–44px for primary controls.

---

## Screens

### 0. Onboarding (first launch; can be reopened with the "?" button)
- Full-screen overlay on `bg-deep`, padding 60/24/36. Header shows "ALASKA PULSE" (Mono 12/600, `s`) and a **Skip** button.
- Three steps (text in `onboarding` in the JSON):
  1. Mini bar chart of 10 years (height scaled to PFD).
  2. Glowing Aurora orb with a sample answer bubble.
  3. Live 3D field chart.
- Below the visual: title 30/600 and body 15px. Footer shows dots (active dot 22px wide `s`, inactive 8px `dim`) and a CTA pill (52px tall, bg `s`) that reads "Next" or "Start exploring".
- Save `localStorage['akpulse-onb']='1'` when the user finishes or skips.

### 1. Story (default tab): scrolling narrative for the selected year
Top to bottom:
1. **Header row:** "ALASKA PULSE" eyebrow (`s`), then three 40px pill buttons: "★ {savedCount}" (opens Saved), "◐" (theme), "?" (onboarding).
2. **Year hero:** eyebrow "THE STORY OF ALASKA IN" + 60px year. On the right, a Play pill (44px, bg `s`): "▶ PLAY 10 YRS" or "❚❚ PAUSE".
3. **Year scrubber:** 10 equal chips (44px tall, radius 10, gap 3). Each chip has a 4px-wide mini bar whose height is scaled to population, plus a label like `'23`. The selected chip uses bg `t`, text `bg` and bar `s`.
4. **Ch. 1 · The Year** (Aurora card): year narrative at 17px/500, source line, and buttons "☆ Pin" and "Rewrite ↻". Rewrite asks the AI for a fresh 3-sentence lead.
5. **Aurora flagged · unusual in {year}** (Aurora card, only if anomalies exist): up to 4 indicators whose year-over-year change has |z| > 1.5σ. Each row shows the label, ▲/▼ change and σ value, and a "why" line. Tapping a row opens its Topic.
6. **3D · ten years × six signals:** a draggable isometric bar field (x = year, z = signal, height = value normalized to that signal's own range). The current year is lit and other years are dimmed. Signals and hues: pop 40, jobs 230, oil 280, salmon 165, visitors 95, pfd 330. A legend sits below.
7. **Ch. 2 · People:** templated paragraph, then a **3D age-profile × time** field (7 age bands, global scale) with a readout line.
8. **Ch. 3 · Money:** paragraph; **state revenue ribbon** (stacked area of Oil / Permanent Fund / Savings draw / Other, with a dashed marker and total label on the selected year) and a legend with values; a "⚙ What if oil hits $100? Run the simulator →" button; and a **4D trajectory** (3D wireframe cube with axes Oil $ / PFD / Jobs, a solid past path, dashed future path, floor shadow and glowing current point).
9. **Ch. 4 · Land, Sea & Sky:** paragraph; **salmon small multiples** (2-column grid, 6 regions, each with a value and a 10-bar mini chart); **climate stripes** 1975–2024 (64px tall, diverging blue→red, current year marked with an inset white bottom bar); **Arctic sea-ice** bars plus a readout.
10. **Ch. 5 · Home & Cost of Living:** paragraph; two stat cards (Avg home price, Median rent); **weekly food cost** horizontal bars for 7 places (bars over $400 use `s`, others use `b`).
11. **Ch. 6 · Culture & Subsistence:** paragraph; **wild-food harvest** bars for 6 regions (`g`); a "See living languages →" link.
12. **Ch. 7 · Learning & Health:** paragraph; two stat cards (Graduation, Life expectancy); 4 **road-system vs rural** paired bars (road `b`, rural `s`).
13. **Signal table:** all 17 indicators with columns Signal / Value / YoY / Rank (rank within 2015–24, highest = #1). Tapping a row opens its Topic.
14. Full-width CTA (bg `g`): "Discuss {year} with Aurora →".

All 3D charts rotate on horizontal drag (θ += dx × 0.012), share one rotation angle, and use the same projection code (see `proj()` in the reference). **Play** advances time at 0.8 years/sec, grows bars in progressively, slowly auto-rotates (0.25 rad/s) and loops back to 2015.

### 2. Pulse: dashboard for one year
- Header: eyebrow "ALASKA · YEAR IN DATA", 60px year, and ‹ › round buttons (40px).
- Year scrubber (same as Story).
- **Population card:** 40px value, delta, and a 326×120 SVG area chart (stroke `s` 2.5px, fill `s/0.16`) with a dashed vertical cursor and a dot on the current year.
- **Aurora paragraph card** with an "Ask why →" button.
- **Vital signs:** 2-column tiles for Unemployment, Oil production, Salmon, Visitors, PFD and Temp. Each tile (min-height 132px) has a label, 24px value, delta and a 10-bar sparkline. Values animate (ease toward the target at about 9×dt per frame).
- **Who lives here:** Alaska Native share (green) and net migration (salmon), each opening its Topic, plus a 12px share bar.

### 3. Explore: index of 17 indicators
- Title "Explore", eyebrow "17 INDICATORS · 2015–2024".
- **Compare** hero button (bg `s`), labeled e.g. "2015 vs 2024", which opens Compare.
- Two cards: What-if simulator; Saved ({n} pinned insights).
- "⇩ Export all 17 indicators (CSV)".
- Indicator rows grouped by People / Economy / Housing / Resources / Education & Health / Climate. Each row (min-height 56) shows the label, "value · delta" in delta color, a 90px sparkline and ›.
- **Living languages card:** horizontal bars with sqrt-scaled widths for 8 languages, plus "Ask Aurora about revitalization →".

### 4. Topic (detail for one indicator)
- "‹ {back}" link (`b`), group eyebrow, 30px title, 52px value in the indicator's color, unit · year, and delta.
- Actions: "☆ Pin chart" / "★ Pinned"; "⚙ What-if" (only for pfd, oil, oilprice); "SRC · {source}".
- **Bar chart** (190px tall, 10 bars, gap 5, radius 6): the selected bar uses the indicator color and the rest use `dim`. A value label appears above the selected bar only. A ◆ marks anomaly years (|z| > 1.5σ). Tapping a bar selects that year. Year labels sit below.
- Low / High / 10-yr change cards.
- **Aurora · Explain this chart:** a button "Explain {year} in context" that shows "Reading the numbers…" while loading, then the AI text (cached per topic+year).
- **Context** notes with ◆ bullets.
- **Raw data table:** columns Year / Value / YoY / Flag. Year and Value headers are sortable and toggle direction. "⇩ CSV" exports this indicator. Footer: source · updated · flag legend.
- "Ask Aurora a question about this →".

### 5. Compare
- Segmented control: **Years | Regions**.
- **Years mode:** FROM row (selected chip `s`) and TO row (selected chip `b`), 10 chips each. 16 indicators are shown as diverging bars from a center line. Bar width = % change normalized to the largest change. Indicators where the absolute change matters more than the % change (temp, netmig, unemp, native) are measured against their range instead. Delta color follows the good/bad rule.
- **Regions mode:** two wrapping 6-per-row chip grids of all 18 regions (A = `s`, B = `b`). Compares Population, Change since 2015, Native share and Income.
- CTA: "Ask Aurora what changed →".

### 6. What-if simulator
- Three range sliders (accent `s`): **ANS crude price** $40–130, **North Slope production** 300–600k bbl/d, and **Fund draw paid as dividends** 0–75%. Each shows a "2024: …" hint.
- Three output cards: Oil revenue, Dividend, and Surplus/Budget gap.
- **General Fund bar:** stacked segments (Oil / Permanent Fund / Other) with a white marker at $4.9B spending, plus an explanatory note.
- "☆ Pin" and "Ask Aurora to interpret".
- Model formulas and constants are in `whatIfModel` in the JSON. **This is a simplified model; label it as such.**

### 7. Saved insights (report builder)
- An empty state, or one card per pin showing kind (CHART / STORY / SCENARIO / AI ANSWER), title, detail, an optional mini chart, and "Remove ✕".
- "⇩ Export report" downloads a simple printable HTML file of all pins. "⇩ All data CSV".
- Pins should persist. The prototype keeps them in memory only; production should use `localStorage` or user accounts.

### 8. Map (Regions)
- Metric segmented control: **Change | Native share | Income**. View toggle: **Tile map | 3D · time**.
- **Tile map:** a 7-column grid of square tiles placed at each region's `tileRow`/`tileCol`. Each tile shows the abbreviation and value. Color: for change, green for growth and salmon for decline, with intensity up to ±15%; for the other metrics, a single-hue ramp (green for Native share, blue for income). The selected tile gets a 2px outline in `t`. Caption: "Tile map · not to scale".
- **3D · time:** extruded tiles where height = √population, which grows across years when playing, colored by the selected metric. Includes a Play button.
- **Region card:** name, 2024 population, change since 2015, Native %, median income, and "Ask Aurora about {region} →".
- **Ranked list:** all 18 regions sorted by the selected metric, each with a horizontal bar.

### 9. Ask AI (Aurora chat)
- Header: glowing dot, "AI ANALYST · LIVE", "Ask Aurora", subline.
- Empty state: "TRY ASKING" with 4 suggestion cards.
- Messages: user bubbles on the right (bg `b`, text `bg`); Aurora bubbles on the left (bg `surface`, border). Max width 86%, radius 18.
- Below each Aurora reply: action chips (open up to 2 detected indicators, "Compare YYYY–YYYY ›" or "{year} story ›", and "☆ Pin"), plus a mini chart card for the main indicator that opens its Topic.
- Sticky composer: a 46px pill input and a 46px round send button (bg `g`, "↑"). Enter sends.
- While loading, show "Aurora is reading the numbers…".

---

## Interactions and behavior
- **Global year state** is shared across every tab. Selecting a year anywhere updates everything.
- **Point & ask:** while point mode is on, tapping any number, bar, tile or row sends "Explain this number: {label} = {value}…" to Aurora and switches to the Ask tab. Otherwise the tap performs its normal action. Wrap every tappable data element in a shared `tap(label, value, normalAction)` helper.
- **Pin toggle:** pins are keyed by title. Pinning something already pinned removes it. Show the toasts "Pinned to report ★" and "Removed from report".
- **CSV export:** `year,"Label (unit)",…`, one row per year.
- **Transitions:** bar heights animate over 0.3–0.4s, backgrounds over 0.25s, and the theme filter over 0.3s. Compare bars use `all .35s`. The 3D charts render every frame with `requestAnimationFrame`.
- **Anomaly detection:** for each indicator, compute the year-over-year differences, then z = (diff − mean) / stdev. Flag a year when |z| > 1.5.

## AI integration (Aurora)
Put this behind a server route, e.g. `POST /api/aurora`, which calls Claude via the Anthropic API. Never expose the API key to the client.
- **System context:** the full dataset serialized as text (every indicator with year=value pairs, the regions table, and the year narratives). The reference builds this as `DATA`.
- **Prompts used in the prototype:**
  - *Chat:* "You are Aurora, a friendly data analyst inside a mobile app about Alaska. Answer in under 110 words of plain text (no markdown). Cite specific numbers and years, explain likely drivers, note uncertainty. The user is viewing year {Y}." Include the last 6 messages.
  - *Explain chart:* under 75 words covering the trend, what stands out about {year}, and one likely driver.
  - *Rewrite story lead:* 3 factual sentences, under 70 words, citing 3–4 numbers.
- **Offline fallback:** if the API fails, answer from the data deterministically: peak/low years and the 2024-vs-2015 change for the detected indicators. Indicators are detected with a keyword map (see `KW` in the reference).
- After each reply, detect which indicators and years it mentions so you can generate the action chips.

## State
`tab, year, from/back (navigation origin), topic, playing, t (fractional time), theta (3D rotation), compare {mode, yearA, yearB, regionA, regionB}, map {metric, region, is3d}, whatIf {price, prod, share}, saved[], msgs[], input, busy, explain{topic+year: text}, story{year: text}, sort{by, dir}, pointMode, toast, onboardingStep, theme`.
Suggested: keep URL routes per tab and topic (`/story?year=2023`, `/explore/pfd`), use Zustand or Context for global state, and persist `saved`, `theme` and `onboarding` to localStorage.

## Suggested project structure
```
/data/alaska-data.json        ← edit content here
/lib/format.ts                ← per-indicator formatters (see "format" hints)
/lib/stats.ts                 ← deltas, ranks, z-score anomalies
/lib/projection3d.ts          ← proj(), drawBoxes() ports
/components/charts/{BarChart,Sparkline,AreaLine,Field3D,Trajectory4D,Ribbon,Stripes,TileMap,Map3D}.tsx
/components/{YearScrubber,AuroraCard,TabBar,PointAsk,Toast,Onboarding}.tsx
/app/(tabs)/{story,pulse,explore,map,ask}/page.tsx
/app/explore/[topic]/page.tsx, /app/compare, /app/what-if, /app/saved
/app/api/aurora/route.ts
```

## Editing content (Cursor)
- **Numbers:** `indicators.<key>.values` (10 values, 2015→2024). The `format` field describes the units each value is stored in (for example, pop is stored in thousands).
- **Copy:** `indicators.<key>.notes`, `yearNarratives`, `onboarding`. Chapter paragraphs are templates in the reference's `storyVals()` and `extraVals()`; move them to the JSON as template strings like `"{pop} Alaskans lived in the state…"`.
- **Adding an indicator:** add an entry to `indicators`, add its key to an `indicatorGroups` entry, and add a formatter. It then appears in Explore, Topic, Compare, the signal table and CSV export automatically.
- **Sources** are listed per indicator; the "updated" dates are placeholders.

## Assets
None. There are no images or icon fonts: glyphs are Unicode characters (★ ☆ ◐ ◎ ◆ ▲ ▼ ‹ › → ↻ ⇩ ⚙ ✕). Fonts come from Google Fonts (IBM Plex Sans and IBM Plex Mono). All charts are hand-built SVG/CSS, so you can port them or rebuild them with visx or d3; 3D is plain math, so no three.js is needed.

## Files
- `reference/Alaska Pulse v2.dc.html`: the full prototype (markup and logic). The logic is in the `<script data-dc-script>` block at the bottom: constants (`M`, `REG`, `BUDGET`…), chart builders (`field2`, `traj`, `map3d`, `ribbon`), and `renderVals()`.
- `reference/support.js`: runtime needed only to open the prototype. Do not port it.
- `data/alaska-data.json`: all content and model constants, extracted.
