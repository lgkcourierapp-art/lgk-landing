# Changelog

All notable changes to LGK Courier are documented here.
Format: `[version/date] — description`

---

## [unreleased] — 2026-06-30 → 2026-07-01 — Food-courier wedge close + BRAMA polish + APK unblock

Full session handoff: [`docs/HANDOFF_2026-07-01.md`](docs/HANDOFF_2026-07-01.md)
([Google Doc](https://docs.google.com/document/d/1taG0xRk_u3SLd8eR9T0tOBuzb1GEvCgTIl7nih9M-Gs/edit)).
Commit range: `de1094d..44da3cd` — 10 commits, all on `origin/main`.

### Added — Food-courier "personal OS" wedge

- **Map-first food home** (`src/screens/FoodCourierHomeScreen.js`,
  `2978b6e`) — full parity with `HomeWithDeliveriesV2`: MapCanvas
  canvas + restaurant pins coloured by wait time + bottom sheet with
  `TipJarPill`, `WeatherStrip`, `LiveEarningsTicker`,
  `KarmaProgressBar`, "Log a restaurant" CTA, recent visits list,
  floating end-shift pill.
- **End-of-Day Story** (`src/screens/EndOfDayStoryScreen.js`, `2978b6e`) —
  6-scene cinematic recap (greeting → earnings count-up → tips/external
  split → pickup spots → karma + tier promotion pulse → farewell).
  Auto-advance 2.8s, tap to skip, long-press to pause. Wired between
  end-shift Alert and EOD route in `App.js`.
- **Accountant PDF** (`src/components/TaxPdfExportSheet.js` + extended
  `src/lib/taxStatement.js`, `6d91a96`) — period + ZUS tier + tax mode
  chips → live preview → A4 PDF via `expo-print` → native share. New
  ZUS / PIT estimate block in the PDF with legal citations.
- **EarningsRingChart** (`src/components/EarningsRingChart.js`,
  `6368a5d`) — SVG donut on EOD: platform / tips / cross-platform with
  tappable segment focus.
- **Long-press TipJarPill quick-add** (`src/components/TipJarPill.js`,
  `6368a5d`) — +5 / +10 / +20 / +50 preset row, last-platform memory
  via AsyncStorage.
- **`KarmaProgressBar`** (`src/components/KarmaProgressBar.js`,
  `6368a5d`) — tier icon + progress bar + tap-to-reveal help card.
- **🔑 BRAMA badge on parcel home rows** (`HomeWithDeliveriesV2`,
  `6368a5d`) — lime pill replacing plain "BRAMA" text.
- **Tier badge on food home header** — replaces generic profile icon
  with the courier's current tier icon (🌱/🔍/🗺️/🛡️/👑) +
  colour dot.

### Added — BRAMA intel polish (the moat)

- **Cinematic `BramaRevealCard`** (`src/components/BramaRevealCard.js`,
  `44da3cd`) — smooth distance interpolation, ●●● masked pre-reveal,
  lime ring 3-beat pulse + distinctive haptic on reveal, confidence
  sub-line (GREEN ≥3 verifications + <30d / YELLOW 30-60d / RED
  otherwise).
- **`BramaWorkedSheet`** (`src/components/BramaWorkedSheet.js`,
  `44da3cd`) — 2.7s after delivery, asks "Did the gate code work?
  👎 / 👍". 8s auto-dismiss, +1 karma per answer, distinctive haptic
  per choice.
- **`src/lib/bramaIntel.js → submitBramaVote`** — wraps
  `increment_worked_votes` / `increment_failed_votes` RPCs.
- **`getIntelNearLatLng`** now surfaces `bramaId` + `bramaAgeDays`
  for the confidence tier and vote target.

### Changed — Polish tax estimator accuracy

- `src/lib/polishTaxHelpers.js` — placeholder ZUS/PIT numbers replaced
  with real Q4 2024 rates derived from component-level ZUS percentages
  (`2978b6e`). Health insurance now scales correctly by tax mode
  (4.9% liniowy, 9% skala, bracket-fixed for ryczałt). Skala progressive
  bracket math + Ryczałt 12% transport added. Each tier + mode carries
  a legal citation.

### Changed — Design system unification

- **`src/lib/karmaTiers.js`** is now the single source of truth — three
  duplicate karma tier systems collapsed (`fdd37a3`):
  Profile (had legacy Newcomer/Member/Pro/Elite), KarmaDashboard (had
  Newcomer/Trusted/Pro/God Mode), and the new unified Rookie/Scout/
  Mapper/Sentinel/Legend system. `perksPl` + `perksEn` carried on each
  tier so wording is identical everywhere.
- **Design rhythm locked** across the 6 top-traffic screens:
  `PAGE_PAD: 16`, `CARD_RADIUS: 14`, `PILL_RADIUS: 999`,
  `BORDER: '#1A1A1A'`, sheet radius 18.
  - ProfileScreen (`6ea8d85`) — 10 documented imbalance fixes.
  - KarmaDashboardScreen + VaultScreen (`fdd37a3`).
  - ClusterScreen + SettingsScreen (`3af6f48`) — mixed-radii chips
    (4/5/8/20) unified to true pills (999).
  - EditProfileSheet (`44da3cd`).

### Fixed — APK build pipeline

- **Sentry source-map upload failure** — four-step fix
  (`b94521e` → `727c713` → `f6659d8`). Final: `npm uninstall
  @sentry/react-native`. Root cause: Expo auto-discovers any package
  with `app.plugin.js` at its root, so plugin-array removal wasn't
  enough. `src/lib/sentry.js` replaced with a no-op shim — consumers
  (`telemetry.js`, `safeWrite.js`) continue compiling. Re-enable path
  in `DEFERRED_BUGS.md`.
- **Adaptive icon** — repadded to Android's 66% safe area (676×676
  inner centred on 1024×1024 `#0A0A0A` canvas) via `sips`. Stop-gap
  until the real Lmark PNG is dropped.

### Documentation

- **`docs/HANDOFF_2026-07-01.md`** — comprehensive session handoff.
- **[Google Doc copy](https://docs.google.com/document/d/1taG0xRk_u3SLd8eR9T0tOBuzb1GEvCgTIl7nih9M-Gs/edit)**
  in the LGK Drive folder.
- **`DEFERRED_BUGS.md`** registry seeded — Sentry config, Lmark PNG,
  rhythm pass tail, cold-start recalibration.

---

## [0.10.0] — 2026-06-29 — Slice 0/1 + Slice 2 foundation

### Added — Design system foundation (`src/design/`, 27 modules)
- Token system (`tokens.js`): semantic color palette (dark + light),
  spacing scale, type variants with locked `bramaCode` signature,
  tap density, motion, cross-platform elevation, z-index, deviceScale
  × textSizeScale × gloveMode
- Responsive primitives: 5 device classes (`phone-sm`/`md`/`lg`,
  `tablet-sm`/`lg`), `useBreakpoint`, `responsive()` value picker,
  `scaledTypeSize` / `scaledLineHeight` / `edgeFor`
- Text primitives: `LText` (only Text wrapper allowed in screens —
  `allowFontScaling=false` baked in; Inter weight-family swap fixes
  Android bold), `MoneyText` ("PLN XX.XX"), `TimeText` (24h HH:mm
  locked), `AddressText` (handles missing apt/floor), `TrackingText`
  (mono + tap-to-copy via expo-clipboard)
- Layout primitives: `Screen` (safe area + header/footer zones +
  keyboard avoidance), `Stack`/`Inline` (gap-based), `Card` (surface
  raise + elevation)
- Action primitives: `Pressable` (haptic + opacity feedback baked),
  `Button` (4 variants × 4 sizes, loading state disables tap),
  `IconButton` (a11y label required at API)
- State primitives: `Sheet`, `Skeleton`, `Empty`, `ErrorState`,
  `StatusPill`
- Field primitives with validation: `PhoneField` (E.164),
  `IBANField` (full ISO 13616 MOD-97), `MoneyField` (locale-correct)
- Sound system: `useSounds()` haptic-first + dynamic `expo-av`
  audio (CC0 sounds drop-in), 8 named events catalogued
- Accessibility context: TextSize S/M/L/XL, Glove mode, HighContrast,
  ReduceMotion — discrete modes instead of OS scaling
- `MapCanvas` (Slice 2): WebView + Leaflet + OSM tiles, zero API keys
- `DesignPreviewScreen` (dev): exercises every primitive at every
  breakpoint; includes Feature Flags toggle panel

### Added — Infrastructure (`src/lib/`)
- `safeWrite(table, payload, opts)`: every Supabase write with retry +
  offline AsyncStorage queue + Sentry breadcrumb on final failure
- `featureFlags.js`: 8 flags (USE_NEW_SCAN_FLOW, USE_MAP_HOME,
  USE_TSP_ROUTING, USE_PACKING_GATES, USE_LEARNING_LAYER,
  USE_SOUND_AUDIO, ADMIN_DEBUG_OVERLAY, USE_MANUAL_FIRST), all
  default OFF in shipped APKs, toggle via DesignPreviewScreen
- `telemetry.js`: `track(event, props)` → buffered → bulk-flushed to
  `shift_events` table; 35 named events catalogued, business_id
  context for lgk-business platform queries
- `imageQuality.js`: Layer 1 blur + lighting detection via JPEG-size
  proxy (Phase 6 ML Kit replaces later)
- `imageCrop.js`: auto-crop label + barcode region heuristic;
  `resizeForVision()` shrinks image to 1024px before Edge Function
  (kills 150s timeouts)
- `regionalPatterns.js`: per-country postcode + address conventions
  for PL/GB/US/DE/FR/NL/IT/ES/IE with multi-signal country detection
- `ocrExtractor.js`: country-aware address fragment extraction from
  ML Kit OCR text (when barcode unreadable)
- `postcodeValidator.js`: postcodeOk + postcodePrefixOk + cityOk
  three-signal cross-check; country-aware region-prefix logic
- `deliveryEvents.js`: `recordAndNotify()` writes immutable event +
  outbox webhook with idempotency key

### Added — Database (`supabase/migrations/`)
- `shift_events` table (telemetry) + RLS (courier reads own; admin
  via trusted backend)
- `delivery_events` table (immutable event log) — UPDATE/DELETE
  policies deny; CHECK constraint on event_type
- `delivery_outbox` table (pending webhooks) — FK to businesses,
  status CHECK constraint, idempotency_key UNIQUE
- `businesses` table — owner, webhook_url, webhook_secret for
  signed outbox POSTs
- `parcels.barcode_crop_url` + `label_crop_url` columns

### Added — Scan pipeline
- 7-layer pipeline per SESSION_16 plan: L1 quality + L2 barcode/regex +
  L3 confidence engine + L4 country-aware geocode validation +
  L5 Claude Vision (opt-in via USE_MANUAL_FIRST flag)
- Confirm-card UX: `USE_MANUAL_FIRST` skips Vision and pre-fills
  partial stop with barcode + carrier + OCR-extracted address;
  courier confirms or escalates with "🤖 Use AI" button

### Added — Map-first home (S2 foundation)
- `HomeWithDeliveriesV2` — flag-gated `USE_MAP_HOME`. Full-screen
  Leaflet map, route polyline, status-coloured pins, bottom sheet
  with NextStopHero + ordered stop list + primary CTA. Existing
  HomeWithDeliveries stays default.

### Added — Business platform integration
- `business_id` propagated end-to-end (handleJobAccepted, recordAndNotify,
  outbox row). PICKED_UP event fires on job accept → outbox row
  queued for business webhook.

### Fixed
- TDZ crash in HomeWithDeliveries (`pending` referenced in dep arrays
  before declaration; Hermes returned undefined silently)
- `AbortSignal.timeout` polyfilled (Hermes lacks this 2022 API);
  killed false "HERE Maps unavailable" banner from useServiceStatus
- Scan-label Edge Function 150s timeout: added 22s fetch timeout +
  image-size logging + 1024px resize before upload (was 2-3 MB)
- CourierIdentityScreen keyboard avoidance — name input was hidden
  behind Android soft keyboard
- Anthropic API key replaced (old key returned 401 → 502 cascade);
  Edge Function deployment via npx supabase
- Paywall bypass rules: admin / pro / first-30-days never see it
- Pre-commit hook allowlist matched only `*/supabase/functions/*`,
  failed on repo-root paths — added `supabase/functions/*` variant
  and self-exempt for `scripts/pre-commit`

### Removed
- Mock data fully removed (`src/lib/mockData.js`) — Supabase only
- Stale users + anonymous sessions cleared from auth.users

### Operational
- Anonymous sign-in temporarily enabled at project level for testing
  (flip OFF before public launch)
- Supabase CLI deployment workflow established (npx supabase
  functions deploy / db push)
- Migration naming standardised on `YYYYMMDDHHMMSS_*.sql`

---

## [0.9.1] — 2026-05-26

### Changed — Job Feed improvements
- Order number displayed in yellow monospace above address on every job card
- BRAMA INTEL preview box (black card, lime left border) when building has gate code intel
- 15-min countdown timer per card: orange → red when under 2 min, disables Accept when expired
- Expired card: dimmed to 50% opacity, "Expired — claimed by another courier" message, button shows "Expired"
- "EARNINGS" label in grey above payout number (was unlabelled — now explicit for new couriers)

---

## [0.9.0] — 2026-05-26

### Added
- ClusterScreen 3-level visual hierarchy (full rewrite)
  - Level 1 NEXT STOP card: `#1A1A1A` bg, 3px lime left border, dominant size
    - Navigate (lime, flex 2, 44px) + Detail (#1E1E1E, flex 1, 44px) buttons
    - BRAMA ✓ pill, COD/Fragile/Express package pills, time-aware fine zone pill
  - Level 2 medium cards: opacity 0.85, BRAMA pill, package pills
  - Level 3 compact cards: opacity 0.55, yellow 4px dot if has BRAMA intel
  - Zone header with street name derived from pending stops (frequency-ranked)
  - Swipe hint banner (← fail · swipe to act · deliver →) — shown once,
    dismissed to AsyncStorage key `lgk_swipe_hint_dismissed`
  - `getStopLevel`, `getZoneDisplayName`, `isFineZoneActive` helper functions
  - `StyleSheet.create()` replacing all inline styles
  - `stops` prop added to App.js ClusterScreen render for zone hierarchy
- JobFeedScreen restaurant data
  - Source badge: "Via portal" (lime) or "Manual entry" (grey)
  - Restaurant data strip (bags count · divider · ready time · divider · pickup notes)
    — only shown when `time_window && time_window !== 'any_time' && order_item_count`
  - Ready-time arrival guidance orange pill: calculates travel time from distance_km,
    shows arrive time + wait / timing / order ready
  - Distance moved to source row (was a chip in metadata)

### Fixed
- `.id` dot-notation violations in ClusterScreen (4× stop.id, 1× pkg.id → bracket notation)
- `allowFontScaling:false` was inside style objects — moved to JSX prop on all Text in
  ClusterScreen and JobFeedScreen edited sections

---

## [0.8.1] — 2026-05-26

### Added
- ShiftReturnBanner component (src/components/ShiftReturnBanner.js)
  - Slim lime-tinted banner shown at top of any sub-screen while shift is active
  - Shows pending stop count + one-tap "↩ Resume shift" button
  - Renders null when isShiftActive === false — invisible overhead
  - Wired into all 7 profile sub-screens: Profile, Settings, Karma,
    Vault, Earnings, PickupInfo, Payment
- useBackHandler on all screens with onBack:
  - Profile, Settings, Karma, Vault, Earnings, PickupInfo, Payment,
    Cluster, PackageDetail — Android hardware back now works everywhere
  - HomeWithDeliveries: back exits app (correct — home screen behaviour)
- Header icon buttons on HomeWithDeliveries
  - 👤 Profile · ⚙️ Settings · END shift button replaces old earnings card
  - All 44px touch targets with hitSlop

### Fixed
- Android hardware back button had no handler on most screens
- No way to return to active shift from profile sub-screens without
  manually navigating back through multiple screens

---

## [0.8.0] — 2026-05-25

### Added
- navHistory array in App.js — gives go() system memory
  - Max 10 entries, auto-capped, never grows infinitely
  - Cleared on shift start and shift end
- goBack() — pops history, safe default fallback
- onEndShift confirmation dialog with remaining stop count
- isShiftActive, onReturnToShift, pendingStopCount props
  passed to all 7 profile sub-screens (banner next session)

### Fixed
- Settings/Karma/Vault/Earnings/PickupInfo/Payment → back
  now returns to correct previous screen via goBack()
- Previously all went to startshift regardless of origin

### Unchanged (deliberately)
- ClusterScreen onBack → deliveries (correct, kept)
- PackageDetailScreen onBack → cluster (correct, kept)
- ProofPhotoScreen onBack → prevScreen (correct, kept)
- All 17 screen files — zero changes in this session

---

## [0.7.1] — 2026-05-25

### Added
- FloatingNavButton on active cluster/delivery screens
  - Floating ≡ button bottom-right, always visible during shift
  - Tap to reveal tab overlay (slides up with backdrop)
  - Tabs: Deliveries, Profile, Karma, Vault, Settings
  - Active tab highlighted in primary yellow (#D4FF00)
  - Tap tab to navigate · Tap backdrop to dismiss
  - Button animates ≡ to ✕ when open
  - Safe area aware · 48px touch target · Android elevation

### Fixed
- No way to reach Settings/Profile during active shift

---

## [2026-05-25] — v0.5.0 · Progressive profiling bug fixes

### 5 bugs fixed across 5 files

**BUG 1 — EditProfileSheet z-order (App.js):**
`<EditProfileSheet>` was nested inside the `{screen === 'profile'}` block, rendering below `<BottomTabBar>`. The tab bar drew on top of the sheet backdrop. Moved `<EditProfileSheet>` to after `<BottomTabBar>` at root render level so it overlays everything.

**BUG 2 — PickupInfoScreen: no save feedback (src/screens/PickupInfoScreen.js):**
Save button called `onBack()` immediately after Supabase write — no visual confirmation. Added `saved` state: button flips to `✓ Saved` green (`#00C853`) for 1 second, then navigates back. Button disabled while saved to prevent double-tap.

**BUG 3 — PaymentAccountsScreen: no tab isolation, wrong IBAN fields (src/screens/PaymentAccountsScreen.js):**
Both Revolut and bank fields were always visible regardless of tab. Rewrote form with `isRevolut` boolean controlling which section renders. Bank transfer tab now shows: account holder name, IBAN (auto-formatted with spaces every 4 chars, validated for 2-letter country code + min 15 chars), and BIC/SWIFT (optional). Save button shows `✓ Saved` without navigating away. Added `bank_account_holder` and `bank_swift` columns (required in Supabase `profiles` table).

**BUG 4 — EarningsLedgerScreen: infinite spinner on no session (src/screens/EarningsLedgerScreen.js):**
`if (!session) return` exited `loadData()` without calling `setLoading(false)` → permanent spinner. Fixed with `{ setLoading(false); return; }`. Also wrapped `Promise.all` in try/catch/finally to prevent spinner lockup if `earnings` or `payouts` tables don't exist yet. Added `console.log('EarningsLedger mounted')` for debugging.

**BUG 5 — Identity gate timing (App.js):**
`restoreSession()` returns the auth user object; `name` lives in `profiles` table and was not reliably populated on it. Gate was checking `restoredUser?.name` which was always falsy for returning users, routing them back to `CourierIdentityScreen` every cold start. Fixed: after session restore, explicitly fetch `profiles.name` from Supabase before deciding route. Added `console.log('[routing]')` to confirm decision.

---

## [2026-05-09] — Tablet layout fixes (Blackview Tab 8)

### Global tablet fixes — 5 passes across 17 screens

**Target device:** Blackview Tab 8 · 10.1" · 1280×800 · ~800 logical px portrait

**FIX 1 — Tablet utilities added to `src/constants/index.js`:**
- `IS_TABLET = SCREEN_WIDTH >= 768`
- `CONTENT_WIDTH = IS_TABLET ? Math.min(SCREEN_WIDTH, 600) : SCREEN_WIDTH` — max 600px content column
- `H_PAD = IS_TABLET ? (SCREEN_WIDTH - CONTENT_WIDTH) / 2 : 0` — centering offset for absolute-positioned bars

**FIX 2 — ScrollView content centered on tablet (12 screens):**
`LoginScreen`, `HomeWithDeliveries`, `FoodHomeScreen`, `JobFeedScreen`, `KarmaDashboardScreen`, `ImportScreen` (both phases), `VaultScreen`, `EndOfDayScreen`, `ContributeIntelScreen`, `ModerationQueueScreen`, `SettingsScreen`.
Pattern: removed `paddingHorizontal` from `contentContainerStyle`; added `<View style={{ width:CONTENT_WIDTH, alignSelf:'center', paddingHorizontal:X }}>` wrapper inside each ScrollView. On phone: zero visual change (`CONTENT_WIDTH = SCREEN_WIDTH`). On tablet: content capped at 600px, centered.
`StartShiftScreen` (no ScrollView): changed `paddingHorizontal:28/36` → `paddingHorizontal:H_PAD+28/36` on all 3 phase containers.

**FIX 3 — Absolute bottom bars and sheets centered on tablet:**
`ClusterScreen`, `PackageDetailScreen`, `EndOfDayScreen`, `ContributeIntelScreen` — `left:0, right:0` → `left:H_PAD, right:H_PAD` on bottom action bars and fail/safe-place sheets.

**FIX 4 — CompassNavigator scales on tablet (`src/components/index.js`):**
`COMPASS_SIZE` 84→110, `COMPASS_INNER` 72→96, `COMPASS_FONT` 32→42, `DISTANCE_FONT` 24→32.
Also: `PaywallModal` inner sheet width changed from `width:'100%'` to `width:CONTENT_WIDTH, alignSelf:'center'`.

**FIX 5 — ProofPhotoScreen camera overlays repositioned:**
Watermark preview: `bottom:160` → `bottom:insets.bottom+(IS_TABLET?180:120)`.
Comment field: `bottom:150` → `bottom:insets.bottom+(IS_TABLET?170:110)`.

**Bundle confirmed clean:** 754 modules, no errors (`npx expo export --platform android`).

---

## [2026-05-03]

### Fix 1 — Food courier crash

**Root cause A — `useSmartPaste` missing from hooks:**
`FoodHomeScreen` imported `useSmartPaste` from `src/hooks/index.js` but the function was never defined there. Calling an undefined hook throws immediately when `FoodHomeScreen` first mounts (after the user completes food courier onboarding). Added `useSmartPaste()` to `src/hooks/index.js` — parses addresses from Glovo / Uber Eats clipboard text, returns `{ parsed, handlePaste, clear }`.

**Root cause B — expo-notifications incompatible with Expo Go SDK 53+:**
`notificationService.js` called `Notifications.setNotificationHandler()` at module-load time (outside any function). Expo Go SDK 53+ throws the moment any `expo-notifications` API is touched. Since `useBramaReveal` imports `notificationService`, and `FoodHomeScreen` calls `useBramaReveal`, loading the food home screen crashed the app. Fixed with an `isExpoGo` guard (`Constants.appOwnership === 'expo'`) around the module-level handler and all three exports. Notifications are fully active in EAS dev/preview/production builds.

**Additional fixes bundled with Fix 1:**
- `.id` notation fixed in `App.js` (3 places), `FoodHomeScreen` (2 places), `HomeWithDeliveries` (1 place) — CLAUDE.md rule requires `['id']` bracket notation.
- `key={i}` replaced with `key={f}` (stable feature string) in `OnboardingDeliveryTypeScreen` — CLAUDE.md rule requires stable list keys.

---

### Fix 2 — UI improvements

**ClusterScreen:**
- **Brama card** — lime 4px accent bar at top (clipped to border radius via `overflow:'hidden'`), pill status badge (green UNLOCKED / amber distance), 3 metadata chips: code type (keypad / concierge / open), verified count, days until expiry (amber when <10 days). Expiry computed from `stop.intel.lastVerifiedAt` as `90 - daysSinceVerification`.
- **Intel cards** — location + parking render as a 2-column grid (`flexDirection:'row'`, each in a `flex:1` wrapper) when both values are present; falls back to full-width stacked when only one has a value.
- **Risk alert** — distinct red-tinted card: `backgroundColor:'#FF3B3014'`, full `borderWidth:1.5` red border, red icon circle (`borderRadius:14`). Visually separates risk from neutral intel cards.

**HomeWithDeliveries:**
- **Stats card** — unified card: progress bar with `%` label at top, then 4 metrics in one row each with an emoji icon. Progress bar replaces the standalone 3px bar that was above the scroll area.
- **Next stop hero** — zone badge circle (32×32, coloured by zone priority score, shows zone letter A–Z) in the top-right of the card header. Carrier name replaced with `CarrierPill` component. Compact `🔐 BRAMA / UNLOCKED` pill added to the metadata row alongside carrier and package count.

---

### Fix 3 — CHANGELOG

Created `CHANGELOG.md` at project root. Updated after each fix in this session. Going forward all changes are appended here before moving to the next task.

---

### Fix 4 — Google OAuth SHA-1 registration

**What was missing:**
Google OAuth requires the production keystore SHA-1 fingerprint to be registered in Google Cloud Console. Without it, Google Sign-In silently fails in EAS builds (returns no credential / error 10).

**SHA-1 fingerprint retrieved from EAS-managed keystore (`m3iEwexg5_`):**
```
03:6F:D0:62:E5:B6:C8:75:7B:3E:9C:3C:45:3E:62:7B:B7:A9:03:41
```

**Full keystore fingerprints (for reference):**
- MD5: `6D:DB:FC:93:6E:7C:0F:05:28:39:E1:76:1C:E8:F4:1B`
- SHA-1: `03:6F:D0:62:E5:B6:C8:75:7B:3E:9C:3C:45:3E:62:7B:B7:A9:03:41`
- SHA-256: `14:44:AE:F5:C6:87:57:36:E1:2F:DE:EE:DE:D9:5D:AF:77:6E:58:60:FC:A4:63:F0:16:65:EB:E4:76:2C:97:F7`

**Manual step required (cannot be automated):**
1. Go to [console.cloud.google.com](https://console.cloud.google.com) → Credentials → OAuth 2.0 Client IDs → Android client
2. Add SHA-1: `03:6F:D0:62:E5:B6:C8:75:7B:3E:9C:3C:45:3E:62:7B:B7:A9:03:41`
3. Confirm package name is `com.lgkcourier.app`
4. Save

---

## [2026-05-04] — Google OAuth callback fix

### Fix — OAuth flow looped back to login instead of completing

**Root cause:** `WebBrowser.openAuthSessionAsync` returns the callback URL directly in its result object (`result.url`) on Android — it does NOT fire `Linking.addEventListener`. The previous `handleGoogle` discarded the return value entirely, so the auth code in the callback URL was never exchanged for a session.

**Change (`LoginScreen.js` only):**
- Added `handleGoogleCallback` to authService import
- `handleGoogle` now captures `result` from `openAuthSessionAsync`
- When `result.type === 'success'`, passes `result.url` to `handleGoogleCallback()`
- `handleGoogleCallback` calls `supabase.auth.exchangeCodeForSession(url)`, builds the user object, and returns it
- On success: `updateUser(googleUser)` + `onLogin()` — proceeds to onboarding
- On failure: sets error message "Sign-in failed. Try again."
- App.js `Linking.addEventListener` untouched — remains valid for cold-start deep link edge cases

---

## [2026-05-05] — OAuth auth lock fix + EAS build

### Fix — onAuthStateChange SIGNED_OUT during exchangeCodeForSession

**Root cause:** Supabase fires a `SIGNED_OUT` event internally during `exchangeCodeForSession` as part of the token exchange. The `onAuthStateChange` listener in `App.js` caught this event and reset state to `MOCK_USER` + navigated to login — before `handleGoogleCallback` had a chance to complete and call `updateUser` + `onLogin`.

**Fix (3 files):**
- `authService.js` — added `export const authLock = { inProgress: false }` (mutable object — live binding readable by all importers)
- `LoginScreen.js` — sets `authLock.inProgress = true` before `openAuthSessionAsync`, clears it to `false` on every exit path (success, failure, URL error). Debug console.logs removed.
- `App.js` — added `if (authLock.inProgress) return;` as first line of `onAuthStateChange` callback, suppressing all events while an OAuth flow is active.

### Bundle error resolved

Previous EAS build failed with "Unknown error. See logs of the Bundle JavaScript build phase." Root cause was a transitive bundler issue. Confirmed clean with `npx expo export --platform android` — 754 modules, no errors.

### EAS build triggered

Preview APK build: `81241727-0333-4f92-a7ce-aa39b2975ed3`
Logs: https://expo.dev/accounts/lgkcourier/projects/lgk-courier/builds/81241727-0333-4f92-a7ce-aa39b2975ed3

---

## [2026-05-05] — expo-notifications lazy-load fix

The `isExpoGo` guard in `notificationService.js` was not sufficient. The static import `import { ... } from '../lib/notificationService'` at the top of `src/hooks/index.js` caused the entire module to execute at load time — before any guard could run — triggering `warnOfExpoGoPushUsage` and `addPushTokenListener` and crashing Expo Go.

**Fix (`src/hooks/index.js`):**
- Removed static import of `notificationService` entirely
- Added `import Constants from 'expo-constants'` and `const isExpoGo = Constants.appOwnership === 'expo'` at module level (safe — no notifications touched)
- All three call sites (`setupNotifications`, `sendBramaNotification`, `dismissBramaNotification`) replaced with `if (!isExpoGo) { import('../lib/notificationService').then(...) }` dynamic imports
- `notificationService.js` is now never loaded in Expo Go — not even the module header

---

## [2026-05-04] — Dead dependency cleanup

Removed 3 unused packages (26 transitive packages removed):

- `expo-auth-session` — installed but never imported. `expo-web-browser` (which is used) is a separate package.
- `@react-navigation/native` — routing is a manual string switch in `App.js` (`go(screenName)`). React Navigation was never used.
- `@react-navigation/native-stack` — same reason.

Run: `npm uninstall expo-auth-session @react-navigation/native @react-navigation/native-stack`

---

## [2026-05-04] — Google Sign-In wired up

### Fix — Google OAuth button was stubbed out

`LoginScreen.handleGoogle` was a no-op that set a hardcoded error string. The Supabase OAuth implementation in `authService.getGoogleOAuthUrl()` existed but was never called from the UI.

**Change (`LoginScreen.js` only):**
- Added `getGoogleOAuthUrl` to the authService import
- `handleGoogle` now calls `getGoogleOAuthUrl()` → opens result URL with `WebBrowser.openAuthSessionAsync(url, 'lgkcourier://auth/callback')`
- Loading spinner shown during the flow; error shown if Supabase fails to return a URL
- Callback handling in App.js (`Linking.addEventListener`) was already wired — untouched

**Expo Go behaviour:** browser opens but redirect back does not complete (`lgkcourier://` scheme not registered in Expo Go). Full round-trip only works in the EAS APK.

---

## [2026-05-04]

### Supabase schema — pivot tables

Ran SQL to create: `deliveries`, `earnings`, `clients`, `payouts` tables; `pending_deliveries` view; added `role`, `verified`, `jobs_completed`, `supervised_mode`, `pending_balance_pln` columns to `profiles`. All with RLS policies and indexes.

---

### JobFeedScreen + Available Jobs section

**New file: `src/screens/JobFeedScreen.js`**
- Fetches from `pending_deliveries` Supabase view on mount
- Supabase realtime subscription — job list refreshes on any deliveries table change
- Job card shows: payout (most prominent, in user currency), pickup→delivery address connector, distance from courier GPS to pickup, package type, weight, time window, COD badge, brama badge
- Accept button updates `deliveries` row: `courier_id`, `status='accepted'`, `accepted_at`; row disappears from list immediately
- Empty state ("No jobs available in your area right now") and 3-skeleton loading state
- LIVE indicator badge in header

**Currency formatting:**
- `formatMoney(amount, currency)` — dynamic per `user.currency`
- PLN → `13.00 PLN` (suffix convention); prefix symbols for EUR/GBP/USD etc.
- Reads from `CURRENCY_SYMBOL` in `currencyMap.js`; defaults to PLN if unset

**`src/lib/supabase.js`** — added:
- `fetchPendingJobs()` — queries `pending_deliveries` view, limit 50
- `acceptJob(deliveryId, courierId)` — atomic update with `.is('courier_id', null)` guard (prevents double-accept race)
- `subscribeToJobs(callback)` — Supabase realtime channel on `deliveries` table; returns cleanup function
- `checkBramaForDeliveries(jobs)` — batch brama check: two queries (buildings bounding box + location_intel join); returns `{ [jobId]: true }` map

**`src/screens/HomeWithDeliveries.js`** — modified:
- `onJobFeed` prop added (optional; section hidden when absent or `jobCount === 0`)
- `jobCount` state: fetched on mount via `fetchPendingJobs`, updated by `subscribeToJobs` subscription
- "Available jobs" banner above the route zones: lime border, briefcase icon, job count, "Platform deliveries · Tap to view", chevron

**`App.js`** — modified:
- Imported `JobFeedScreen`
- `handleJobAccepted(delivery)` — converts accepted delivery record to a stop-shaped object (`carrier:'platform'`, `isPlatformJob:true`, preserves pickup coords) and appends to stops array; navigates to homeScreen
- `{screen === 'job_feed'}` entry added after `food_home`
- `onJobFeed={() => go('job_feed')}` passed to `HomeWithDeliveries`

---

## [2026-04-xx] — Earlier session

### Features
- Full cargo bike mode (`user.vehicle === 'cargo_bike'`): 3-layer bike routing (ORS → OSRM → Maps deeplink), 30-min AsyncStorage cache, degraded amber dot in CompassNavigator, elevation warnings, bike time estimate, fine zone filtering (`bikeRelevantFine`), package weight bar, 1.5× karma multiplier.
- Lock screen brama notification via `expo-notifications` — fires once when courier enters 100m radius, dismissed on screen exit.
- Real GPS wiring in `useBramaReveal` — `watchPositionAsync` with 5m / 3s interval, Haversine distance, falls back to mock countdown if permission denied.
- `app.config.js` created — exposes `ORS_API_KEY` from `.env` via `Constants.expoConfig.extra.orsApiKey`.
- `.env.example` created with `ORS_API_KEY` placeholder.

### Bug fixes
- App logo not showing on loading screen: replaced hardcoded yellow box with `<Image source={require('./assets/icon.png')}/>`.
- Splash screen blank on Android: pointed `app.json` splash to `icon.png` (proper RGB PNG, not 8-bit indexed).

### New files
- `src/lib/notificationService.js` — `setupNotifications`, `sendBramaNotification`, `dismissBramaNotification`.
- `src/lib/bikeRouting.js` — `getBikeRoute`, `getElevationWarnings`, `estimateBikeTime`, `getWeightStatus`, `getBikeKarmaMultiplier`, `openBikeMapsFallback`.

### Data
- `MOCK_STOPS` in `mockData.js`: added `lat`/`lng` fields (Warsaw coords), `bikeRelevantFine` flag, `weightKg` per package.
- `MOCK_FOOD_STOPS`: first food stop added.
