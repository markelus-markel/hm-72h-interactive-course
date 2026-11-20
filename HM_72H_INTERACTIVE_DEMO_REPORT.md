# HM_72H Interactive Course Demo — Implementation Report v2.1

## Changelog

- **v2.1 (anglicism removal):** By explicit request, removed the remaining bare-English UI
  terms across both RU and KZ — these had been a deliberate "fixed-term" decision since v1.1/
  v1.2, now reversed. Three categories, all addressed: (1) gate/artifact status words
  PASS/REDO/BLOCKED/STOP, now fully localized (RU: Принято/На доработку/Заблокировано/Стоп;
  KZ: Қабылданды/Түзету/Бұғатталды/Тоқтатылды) wherever they appeared as display text — status
  tags, the header "Gate PASS N/11" chip (now wired to a proper localized string instead of a
  hardcoded English literal), the teacher-panel warning, the readiness text on SCR-16, button
  labels/captions referencing STOP, and the risk-level word BLOCKED; (2) the word "Gate" itself
  (RU "Контрольная точка", KZ "Бақылау нүктесі") and "Gate-log" (RU "Журнал контрольных точек",
  KZ "Бақылау нүктелерінен өту журналы" / "Бақылау нүктелерінің журналы"); (3) "Photo-ID" (→
  "Фотоидентификатор", RU and KZ, across ~10 locations: screen titles, gate G5 description,
  artifact name, SCR-07/08 prose, SCR-09 fields/table headers), "Reset demo" (→ "Сбросить
  демо"/"Демоны қалпына келтіру"), and "HTML demo" (→ "веб-демонстрация"/"веб-көрсетілім" on
  SCR-01 and in the footer responsibility text). Internal state/enum keys (`GATES_DEF`,
  `GATE_STATUSES`, `state.gates.G1 = "PASS"`, CSS classes like `.tag-PASS`, `data-status="PASS"`
  attributes) are unchanged — none of these are ever shown to the user, only the display text
  rendered through `t("status."+key)` was. `selfReport`/"Self-report" and `safetyLog`/"safety-
  log" were explicitly out of scope for this round (not selected) and remain untouched. See
  "What changed (v2.1)" below. All three QA scripts updated for the new localized display text
  and re-run clean: `smoke_check.js` (12/12), `smoke_check_v16.js` (20/20),
  `student_simulator.js` (106/106, 0 console/page errors).
- **v2.0 (gate-advance consistency + teacher field-practice review):** An independent QA pass
  (a Playwright "student simulator" playing the full course realistically, cross-checked
  against `app.js` source) surfaced three gate/artifact design gaps, all addressed here:
  1. **Gates G1–G5 now auto-advance to PASS** from the student action that completes them
     (G1 object chosen, G2 zone chosen, G3 component identified, G4 visible sign recorded, G5
     Photo-ID linked — both the gallery pick and the local-file upload path), matching the
     existing auto-suggestion pattern already used by G0/G6/G7/G8/G10. Manual
     PASS/REDO/BLOCKED/STOP buttons remain on every gate as before, unchanged.
  2. **"Дефектная ведомость" (SCR-12) now tracks real content.** Its artifact status used to
     be driven only by a manual dropdown on SCR-12, completely disconnected from the actual
     cards table shown on that same screen — meaning Gate G8 (SCR-13) could PASS from a single
     dropdown click regardless of whether any real defect cards existed. It now auto-advances
     to SUBMITTED once ≥3 cards are saved (same threshold as the `defectCards` artifact/Gate
     G6), set in `submitDefectCard()`. The dropdown stays as a manual override.
  3. **New "Полевая практика — проверка задания" card in the teacher panel** — shows the
     student's submitted field-task reference, the `fieldEvidence` artifact status, and Gate
     G9's own PASS/REDO/BLOCKED/STOP controls, all in one place, so a teacher reviewing the
     real-world field work doesn't need to navigate to the student's SCR-14. This does **not**
     remove the student's own ability to self-click Gate G9 (or any gate) — that broader
     "which gates should be teacher-only" question was explicitly scoped out as a separate,
     bigger decision (would mean role-based UI, not just this additive card).
  See "What changed (v2.0)" below. `student_simulator.js` and `qa_findings_report.md` (QA
  tooling/report, not shipped as course content) ran clean (106/106) after this round.
- **v1.9 (bugfixes: stop_now inconsistency, phone overflow):** Fixed two concrete bugs found
  by the same QA pass: choosing "Остановиться и не продолжать (STOP)" as a defect card's
  action (SCR-09) now correctly triggers the full STOP overlay, matching the identical choice
  on SCR-11 (previously it silently saved with no safety effect); and `.module-chip` no longer
  overflows the viewport by 30-38px at phone width (390px) on every module-labeled screen
  (added a `max-width:600px` wrap override) — the existing smoke tests only checked
  1440px/820px, which is why this went undetected until now.
- **v1.8 (STOP button clarity):** Added a short visible hint next to the header "Симулировать
  STOP" button (RU+KZ) clarifying it's a demo-only safety-stop simulation, not a real alert —
  previously this was explained only in a hover tooltip, easy to miss. Also reduced the
  button's vertical padding (~20% shorter) per request. No action/state logic changed.
- **v1.7 (dedicated field-practice screen + Gate):** Promotes the v1.6 field-task block from
  an inline block on the итоговый пакет screen into its own screen and Gate — the heavier
  option explicitly deferred in v1.6's "Scope decisions" ("it would renumber the course's
  screen count (15→16) and Gate count (10→11), which is a canon change, not a demo tweak"),
  now implemented on explicit request. New **SCR-14 "Полевая практика"** carries the field-task
  explanation, the protocol-reference input, the submit button, the `fieldEvidence` artifact
  status, and its own gate, **G9** ("Полевая практика подтверждена"). The old SCR-14 (итоговый
  пакет) shifts to **SCR-15**, the old SCR-15 (защита) shifts to **SCR-16**, and the old G9
  ("итоговая защита") is renumbered to **G10** so the field-practice gate can take the G9 slot
  right after G8 (SCR-13). The course is now 16 screens and 11 gates (G0–G10). No artifact ID
  changed, no new artifact was added (`fieldEvidence` already existed from v1.6), and no
  backend/export feature was added. See "What changed (v1.7 dedicated field-practice
  screen + Gate)" below.
- **v1.6 (zone-map diagram + field-task bridge):** Two linked additions, requested together
  to connect the digital demo to real fieldwork with the HouseMaster Inspector app
  (`insp.housemasters.kz`):
  1. **"Карта зон" is now a real visual artifact** (SCR-03/SCR-04), not just a status tag — a
     small clickable SVG house diagram that highlights the zones chosen in the existing
     SCR-04 chip list (and can be clicked directly, same `toggle-zone` action/state). The
     `zoneMap` artifact's own status now reflects both an object AND at least one zone being
     chosen (previously it went to DRAFT on object selection alone and never advanced).
  2. **A field-task block on SCR-14** makes the course's own "field/object contour: 7ч"
     (COURSE_CANON.md) concrete: explains that this part happens for real, in
     `insp.housemasters.kz`, on a real МКД, and collects a protocol reference/export filename
     as the student's claim that it's done (teacher verification stays manual, same as every
     other artifact). Adds one new artifact, `fieldEvidence` — **a genuine addition to the
     12-artifact canon in ARTIFACT_MODEL.md, flagged there for methodist confirmation**, same
     as the KZ-draft flag pattern used for translations.
  No screen/Gate was renumbered, no existing artifact's ID changed, and no backend was added
  — the diagram is pure SVG+CSS/state, and the field task is a text field + manual status,
  identical in spirit to the self-report/neutral-message pattern already in the course. See
  "What changed (v1.6 zone-map diagram + field-task bridge)" below.
- **v1.5 (real-app terminology bridge):** Added a short, visually distinct note ("🔗 ...")
  under the artifact status on 7 screens (SCR-03, SCR-04, SCR-05, SCR-06, SCR-07, SCR-08,
  SCR-09), naming the matching field/column in the real HouseMaster Inspector field app
  (`insp.housemasters.kz`) that students will use for actual field inspections — e.g. SCR-04's
  "Карта зон" status is now explicitly tied to the real app's "Место" column. Purely additive
  text (new `appBridge` string per screen, RU+KZ, plus one new CSS class); no artifact IDs,
  gate logic, or state model changed. See "What changed (v1.5 real-app terminology bridge)"
  below.
- **v1.4 (UI/UX polish):** A visual/readability-only pass over the v1.3 bilingual demo, aimed
  at making it easier to demonstrate to a director, methodist, teacher, or student group. No
  screen was added or removed, no Gate↔screen mapping changed, no artifact logic changed, and
  no course content changed — every edit is CSS, plus a handful of small HTML-structure/markup
  tweaks in `app.js`'s rendering functions (grouping, wrapper classes) needed to hang the new
  styles on. See "What changed (v1.4 UI/UX polish)" below for the full list.
- **v1.3 (RU/KZ bilingual mode):** Implemented a full RU/KZ bilingual interface on top of the
  v1.2 baseline. Added a "Рус / Қаз" language switcher in the header. Extracted essentially all
  visible UI strings — screen titles/explanations/tasks, module names, gate names, artifact
  names, zone/system/component/symptom/photo/risk/safe-action labels, status labels, footer
  safety/responsibility text, STOP overlay text, and the teacher panel — into an `STR.ru` /
  `STR.kz` dictionary accessed through a single `t(path)` lookup helper (falls back to Russian
  if a key is ever missing). Russian remains byte-for-byte the same reviewed content as v1.2
  and is the canonical baseline; Kazakh is a first draft translation, explicitly flagged as
  such in the running UI (see "KZ text status" below). Internal state keys and the six
  gate/artifact status enum values (NOT_STARTED, IN_PROGRESS, DRAFT, SUBMITTED, PASS, REDO,
  BLOCKED, STOP) were not touched — only their on-screen labels are looked up per language, and
  PASS/REDO/BLOCKED/STOP continue to render as the exact same English words in both languages,
  matching the fixed-term list from v1.1/v1.2. Reference data that used to store pre-resolved
  display text (selected component/symptom, and each saved defect card's zone/system/component/
  sign/Photo-ID) was changed to store stable IDs instead, with small resolver functions computing
  the display text at render time from the current language — so switching language re-localizes
  already-saved defect cards, not just the screen currently on view. Switching language never
  resets gates, artifacts, defect cards, the current screen, or any other state. See "What
  changed (v1.3 bilingual mode)" below for full detail, and
  `HM_72H_INTERACTIVE_BILINGUAL_QA_REPORT.md` for the test pass.
- **v1.2 (explanatory parentheses):** Added short Russian explanations in parentheses next to
  the canonical English UI terms (Gate, PASS, REDO, BLOCKED, STOP, Photo-ID, HTML demo, Reset
  demo) at the 8 locations requested: gate section titles, a new gate/artifact status legend
  line, the artifact tracker's Photo-ID row, the SCR-08 explanation, the STOP overlay title and
  the footer's STOP safety bullet, both Reset buttons (as a `title` tooltip plus visible helper
  text under the overlay button), the SCR-01 intro and footer's "HTML demo" mentions, and the
  teacher panel's "Gate 0–9" header plus its own status legend. Per the explicit UI-length
  rule, the six per-gate status buttons themselves were kept short (unchanged); the full
  explanations live in the new nearby legend line instead. Scope decision (confirmed with the
  requester): only the Russian explanatory form was wired into the live UI — the app has no
  Kazakh display mode, so the supplied Kazakh strings were not added as a live toggle; see
  "What changed (v1.2)" below for the full list and exact strings.
- **v1.1 (language cleanup):** Translated the remaining English field labels on the Defect
  Card screen (SCR-09) and the defect-cards table header into Russian, per review feedback.
  Standardized the two "Reset" buttons to the fixed term "Reset demo", and the STOP overlay
  title to use "STOP" (English) instead of the transliterated "СТОП", for consistency with the
  other fixed technical terms. Gate and artifact status tags for PASS / REDO / BLOCKED / STOP
  now display those exact English words (previously translated to Russian equivalents),
  matching the fixed-term list. No layout, structure, or course logic changed. See
  "What changed (v1.1)" below for the full list.
- **v1.0:** Initial implementation (see summary below).

## Implementation summary

A self-contained static HTML/CSS/JS prototype implementing all 15 required screens, Gate 0–9
control points, the artifact tracker, a sample defect-card form, a Photo-ID selector, a risk
selector, a STOP scenario, a demo reset, and a teacher summary view, for the course
«Хаузмастер МКД: технический осмотр, фиксация дефектов и сопровождение решений ОСИ / ПТ»
(72 academic hours, 8 modules). Built exactly to `TASK_FOR_CLAUDE.md` / `HANDOFF_TO_CLAUDE.md`
with no scope expansion.

## Files changed / created

- `index.html` — created (app shell: header, sidebar nav, screen container, artifacts panel,
  teacher panel, STOP overlay, safety/responsibility footer)
- `styles.css` — created (all styling; neutral palette with green/blue accents; responsive
  for tablet)
- `app.js` — created (all state, rendering, and interaction logic; vanilla JS, no dependencies)
- `README.md` — updated (added "how to run" / "how to demonstrate" / scope-confirmation
  sections; original source-materials description preserved)
- `HM_72H_INTERACTIVE_DEMO_REPORT.md` — created (this file)

No other files in the project were modified. `docs/`, `output/`, `src/` were left untouched
(they were empty). All source/spec `.md` files (`COURSE_CANON.md`, `SCREEN_MAP.md`,
`SAFETY_AND_RESPONSIBILITY.md`, `ARTIFACT_MODEL.md`, `DEMO_SCRIPT.md`, `TASK_FOR_CLAUDE.md`,
`HANDOFF_TO_CLAUDE.md`) were read but not modified.

## How to run

Open `index.html` in a desktop browser — no build step, no server, no installation. See
`README.md` for full instructions.

## How to demo

Follow `DEMO_SCRIPT.md` (7–10 minutes). See the "How to demonstrate" section of `README.md`
for a step-by-step walkthrough matching the demo script.

## Screens implemented (16/16)

SCR-01 Старт курса · SCR-02 Роль студента и safety · SCR-03 Карта МКД как объекта ·
SCR-04 Зоны дома · SCR-05 Инженерные системы · SCR-06 Система → компонент ·
SCR-07 Видимый признак / симптом · SCR-08 Фотофиксация / Photo-ID · SCR-09 Карточка дефекта ·
SCR-10 Риск · SCR-11 Безопасное действие · SCR-12 Дефектная ведомость ·
SCR-13 Учебный проект технического акта · **SCR-14 Полевая практика (v1.7)** ·
SCR-15 Итоговый пакет · SCR-16 Защита / результат.

Each screen shows: screen ID + title, current module (with hours), a short explanation, the
student task, the artifact(s) produced, PASS/REDO/BLOCKED/STOP gate controls (where a gate
applies to that screen), and a "Далее: …" next-action hint plus Prev/Next navigation. The
left sidebar lists all 16 screens grouped by module, highlights the current screen, and shows
a small color-coded dot per screen reflecting its linked gate's status. The header shows a
persistent course status panel: screens hours/modules, screen counter, gate PASS count,
overall progress bar, and a BLOCKED/STOP warning chip when relevant.

## Gates implemented (11/11)

G0–G10 are all present as visible control points, each tied to the screen named in
`TASK_FOR_CLAUDE.md` (G9/G10 as renumbered in v1.7 — see changelog):

- G0 (role/safety) — SCR-02, auto-set to PASS when the safety checkbox is confirmed (also
  settable manually)
- G1 (object/simulation selected) — SCR-03, auto-set to PASS when an object/case is chosen
  (**v2.0**)
- G2 (zone selected) — SCR-04, auto-set to PASS once at least one zone is chosen (**v2.0**)
- G3 (system + component identified) — SCR-06, auto-set to PASS when a component is chosen
  (**v2.0**)
- G4 (visible sign recorded) — SCR-07, auto-set to PASS when a visible sign is chosen (**v2.0**)
- G5 (Photo-ID linked) — SCR-08, auto-set to PASS when a Photo-ID is chosen or uploaded
  (**v2.0**)
- G6 (defect card completed) — SCR-09, auto-set to PASS on first saved card
- G7 (risk + safe action defined) — SCR-11, auto-set to PASS when a neutral message is saved
  with both a risk and an action chosen
- G8 (defect statement assembled) — SCR-13, auto-set to PASS when the act draft is saved
  (depends on the "дефектная ведомость" artifact no longer being NOT_STARTED, which as of
  **v2.0** itself auto-advances from ≥3 real saved cards — see Artifact tracker below)
- G9 (field practice confirmed) — **SCR-14 (v1.7)**, set to PASS/REDO/BLOCKED/STOP manually
  by the teacher/master after reviewing the student's submitted field-task reference; **v2.0**
  added a dedicated review card for this in the teacher panel (see "Teacher view" below)
- G10 (final package ready for defense) — SCR-16 (shifted from SCR-15 in v1.7, was G9 before
  v1.7), set to PASS when "защита пройдена" is confirmed

Every gate also exposes all six statuses (NOT STARTED / IN PROGRESS / PASS / REDO / BLOCKED /
STOP) as explicit buttons, so a teacher can demonstrate any gate behavior manually at any time,
independent of the auto-suggestions above.

## Artifact tracker behavior

All 13 required artifacts (карта зон, маршрутный чек-лист, таблица «система → компонент»,
карточки дефекта (мин. 3), Photo-ID, дефектная ведомость, учебный проект технического акта,
нейтральное сообщение ответственному лицу, safety-log, gate-log, **полевое задание
(подтверждение) — v1.6 addition, own screen/Gate since v1.7**, self-report, итоговая защита)
are listed in the always-visible right-hand panel, each with a status dropdown
(NOT STARTED / DRAFT / SUBMITTED / PASS / REDO / BLOCKED). Several artifacts auto-update their
status as the student progresses (e.g. selecting a zone moves the route checklist to DRAFT/
SUBMITTED, saving 3+ defect cards moves the defect-cards artifact to SUBMITTED, and — as of
**v2.0** — the same ≥3-card save also moves "дефектная ведомость" to SUBMITTED, since it is the
compiled table of those same cards); every status can also be changed manually at any time,
including on artifacts that now auto-advance.

## Defect card, Photo-ID, and risk demos

- **Defect card (SCR-09):** a form with all required fields (Defect Card ID, Zone, System,
  Component, Visible sign, Photo-ID, Fact, Hypothesis, Risk level, Safe next action, Status).
  Zone/System/Component/Sign/Photo-ID are pulled from earlier screens; Fact and Hypothesis are
  visually and functionally distinguished (Hypothesis is explicitly labeled "не диагноз").
  Saving appends the card to a table and auto-assigns the next Defect Card ID
  (DC-001, DC-002, …). The demo supports creating 3+ cards, matching the "minimum 3" artifact
  requirement.
- **Photo-ID selector (SCR-08):** a gallery of six neutral placeholder tiles (staircase, wall,
  roof, basement door, facade, utility-room door) representing non-personal scenes, plus an
  optional local file-upload preview (client-side `FileReader` only — no upload, no network
  call) for a more realistic demo if desired.
- **Risk selector (SCR-10):** LOW / MODERATE / HIGH / BLOCKED, with a visible safety callout
  recommending STOP when HIGH or BLOCKED is chosen.

## Safety / STOP / BLOCKED behavior

- The full safety checklist and the responsibility/rights-holder text are always visible in
  the page footer on every screen (not just SCR-02).
- SCR-02 additionally shows the student-role explanation and full safety list, with a
  confirmation checkbox.
- A "Симулировать STOP" button is available both in the header (every screen) and on SCR-02,
  with a short visible hint next to the header one (**v1.8**) clarifying it's a demo
  simulation, not a real alert. Choosing "Остановиться и не продолжать (STOP)" as a safe
  action on SCR-11, or as a defect card's action dropdown value on SCR-09 (fixed to match
  SCR-11 in **v1.9** — it previously silently saved with no safety effect from SCR-09), also
  triggers it. Triggering STOP shows a full-screen red overlay explaining the safety boundary
  and
  disables all navigation, gate/artifact controls, and form actions underneath it — the only
  working control is "Сбросить демо (Reset)".
- Setting any Gate's status to STOP (via its status buttons) also triggers the same overlay,
  so a teacher can demonstrate the STOP boundary from any gate, not only the dedicated button.
- REDO is visually distinct from BLOCKED/STOP (different tag colors; REDO does not block the
  app) to make clear REDO ≠ failure, while BLOCKED/STOP represent a safety/critical boundary.
- "Reset демо" (also always available in the header) clears all state back to the initial
  defaults and returns to SCR-01.

## Teacher view

A "Режим преподавателя" toggle in the header shows/hides a dashboard panel (visible above the
main content on every screen while active) summarizing: overall readiness %, defect-card
count, risk-level distribution, a BLOCKED/STOP alert when relevant, and full tables of all 11
gate statuses and all 13 artifact statuses. **v2.0** added a third, full-width card,
"Полевая практика — проверка задания": the student's submitted field-task reference, the
`fieldEvidence` artifact status, and Gate G9's own controls, all in one place — so reviewing
and acting on the field-practice step no longer requires leaving the teacher panel to visit
the student's SCR-14.

## Persistence

`localStorage` use is optional and off by default, controlled by a header checkbox
("Сохранять прогресс в браузере"), as required. With it off, the demo runs entirely in memory
and always starts fresh. This is documented in `README.md`.

## Known limitations

- This is a demonstration prototype, not a graded/production course tool: gate and artifact
  status transitions are simulated locally with buttons/selects, as explicitly permitted by
  the task; there is no server-side validation, scoring, or persistence beyond the optional
  browser `localStorage`.
- Photo-ID is simulated with placeholder gallery tiles (plus an optional local-only file
  preview); no camera capture or real media pipeline is implemented, matching the "no
  backend / no API calls" constraint.
- Zones, systems, components, symptoms, and safe actions are drawn from representative,
  typical MKD examples consistent with the course canon; they illustrate the model rather than
  constituting an exhaustive methodology reference.
- As of v1.1, the Defect Card field labels are in Russian (ID карточки дефекта, Зона, Система,
  Компонент, Видимый признак, Photo-ID, Факт / наблюдаемый признак, Гипотеза / возможное
  объяснение — не диагноз, Уровень риска, Безопасное следующее действие, Статус); "Photo-ID"
  is kept as the one intentionally fixed technical term.
- Tested with a headless Chromium smoke pass (navigation across all 15 screens, gate button
  transitions, defect-card creation ×3, Photo-ID selection, risk/action selection, STOP
  trigger + block + reset, teacher view toggle, and a tablet-width layout check) with zero
  console/page errors. Not tested on every real browser/device combination.

## What changed (v1.1 language cleanup)

- SCR-09 (Карточка дефекта) field labels translated to Russian per the requested mapping:
  ID карточки дефекта, Зона, Система, Компонент, Видимый признак, Photo-ID (kept),
  Факт / наблюдаемый признак, Гипотеза / возможное объяснение — не диагноз, Уровень риска,
  Безопасное следующее действие, Статус.
- The saved-cards table header (also shown on SCR-12, дефектная ведомость) translated:
  Зона, Система, Компонент, Признак, Photo-ID (kept), Факт, Гипотеза, Риск, Действие, Статус.
- The "Fact — … Hypothesis — …" explanatory callout on SCR-09 translated to
  "Факт — … Гипотеза — …".
- Gate and artifact status tags for PASS / REDO / BLOCKED / STOP now render those exact
  English words everywhere (previously localized to Пройдено / На доработку / Заблокировано /
  СТОП), matching the fixed-term list; NOT STARTED / IN PROGRESS / DRAFT / SUBMITTED remain in
  Russian since they were not on the kept-terms list.
- Both "Reset" buttons (header and STOP overlay) standardized to the exact label "Reset demo".
- The STOP overlay title changed from "СТОП" to "STOP" for consistency with the same rule.
- Artifact names that are canonical technical identifiers from `ARTIFACT_MODEL.md` itself
  (Safety-log, Gate-log, Self-report, Photo-ID) were intentionally left as-is — renaming them
  would mean redesigning the course's own artifact vocabulary, which is out of scope.
- No layout, screen structure, navigation, gate/artifact logic, or course content changed.

## What changed (v1.2 explanatory parentheses)

- **Gate section titles:** every gate box's title changed from "Gate N — …" to
  "Gate (контрольная точка) N — …" (SCR-02/03/04/06/07/08/09/11/13/15, plus the teacher panel's
  "Gate (контрольная точка) 0–9" card header).
- **Gate/artifact status legend (new):** a short legend line — "Обозначения статусов: PASS
  (принято) · REDO (доработать) · BLOCKED (заблокировано) · STOP (остановка по
  безопасности)" — now appears under every gate's status buttons, and once near the top of
  the teacher panel (covering both gate and artifact status tags, since they share the same
  PASS/REDO/BLOCKED vocabulary).
- **Gate status buttons:** left unchanged (still the short NOT STARTED / IN PROGRESS / PASS /
  REDO / BLOCKED / STOP labels) per the UI-length rule — the explanation lives in the new
  legend line next to them, not inside the buttons themselves.
- **Artifact tracker → Photo-ID:** the artifact's title changed from "Photo-ID" to "Photo-ID
  (идентификатор фото)" in the tracker panel and, by extension, in the teacher panel's
  artifact table. The SCR-09 defect-card field label and the saved-cards table header keep
  the bare "Photo-ID" (narrow form fields/table columns; also matches the exact field-label
  mapping fixed in v1.1).
- **SCR-08 explanation:** "Photo-ID подтверждает …" → "Photo-ID (идентификатор фото)
  подтверждает …".
- **STOP overlay title:** "STOP — небезопасная / нештатная ситуация" → "STOP (остановка по
  безопасности) — небезопасная / нештатная ситуация".
- **Footer safety list:** the STOP bullet now reads "…(STOP — остановка по безопасности)".
- **Reset demo buttons:** both buttons keep the exact label "Reset demo"; each now has a
  `title` tooltip "Reset demo (сброс демонстрации)", and the STOP overlay additionally shows
  that same text as a small caption directly under its button.
- **HTML demo references:** the SCR-01 intro callout and the footer's responsibility text now
  read "…HTML demo (интерактивная демонстрация)…" instead of the earlier Russian-only
  phrasing.
- Scope decision confirmed with the requester before implementing: the app has no Kazakh
  display mode today, so only the Russian explanatory parentheses were wired into the live
  UI. No language toggle was built, and the supplied Kazakh strings were not added anywhere in
  the running app (documented in the QA follow-up instead of hardcoded, so they're easy to use
  if a Kazakh mode is scoped as a separate task later).
- No layout, screen structure, navigation, Gate 0–9 mapping, artifact list, or course content
  changed; all edits are text-only.

## What changed (v1.3 bilingual mode)

- **Language switcher:** a "Рус / Қаз" button pair added to the header. The active language
  is visually highlighted. Clicking either button re-renders the entire UI in that language
  without changing the current screen, gate statuses, artifact statuses, saved defect cards,
  safety-checkbox state, teacher-mode visibility, or any other progress.
- **KZ draft notice:** whenever Kazakh mode is active, the header shows the required notice
  "Қазақша мәтін — бастапқы нұсқа. Тілдік және әдістемелік тексеруді қажет етеді." Whenever
  Russian mode is active, the header shows the Russian equivalent "Казахская версия — draft.
  Требуется языковая и методическая проверка." Neither notice can be dismissed or hidden.
- **Canonical English anchors preserved in both languages:** Gate, PASS, REDO, BLOCKED, STOP,
  Photo-ID, HTML demo, and Reset demo render identically in RU and KZ, each still paired with
  its own RU or KZ explanatory parenthetical (e.g. "Gate (контрольная точка)" in RU vs.
  "Gate (бақылау нүктесі)" in KZ), carrying forward the v1.2 explanatory-parentheses rule into
  both languages.
- **Translated content areas:** header/brand text, sidebar module labels, all 15 screens'
  titles/explanations/student tasks/artifact labels, gate names, all 12 artifact names, zone
  names, system and component names, symptom names, Photo-ID placeholder names, risk labels,
  safe-action labels, the SCR-09 defect-card field labels and saved-cards table headers, the
  SCR-13 educational-act disclaimer, the SCR-14/15 final-package and defense screens, the
  teacher panel (heading, summary line, legends, Gate/Artifact tables), the STOP overlay, and
  the footer safety list / responsibility-boundary text.
- **Not translated (by design):** free text the student types themselves — the SCR-07 symptom
  note, SCR-09 fact/hypothesis fields, SCR-11 neutral-message draft, SCR-13 act draft, and
  SCR-14 self-report — is preserved verbatim across a language switch rather than auto-
  translated, since it is the student's own authored content, not interface chrome.
- **Defect-card re-localization:** each saved defect card now stores zone/system/component/
  symptom/Photo-ID/risk/action as stable IDs rather than pre-resolved text, so a card saved in
  Russian correctly displays in Kazakh (and back) after a language switch — this slightly
  exceeds the literal "remain visible" requirement by also re-localizing correctly.
- **No structural change:** the 15-screen structure, the Gate 0–9 → screen mapping, the 12-item
  artifact list and its status logic, the defect-card data model's meaning, the STOP/Reset
  behavior, and the teacher-mode contents are unchanged from v1.2 — only how their labels are
  looked up (by language) changed.
- `index.html` was simplified so that the header brand/actions, the footer, and the STOP
  overlay are fully rendered by `app.js` (consistent with how the sidebar/artifacts/teacher
  panel already worked), so every visible string has exactly one source of truth in the new
  `STR` dictionary rather than being duplicated between static HTML and JS.
- Added a small `.lang-switch` / `.lang-btn` / `.kz-draft-note` block to `styles.css` for the
  new switcher and draft-notice UI; no other layout or styling changed.
- `README.md` updated with a "Bilingual mode (RU / KZ)" section documenting the switcher,
  the draft-status notice, and what does/doesn't translate.

## KZ text status

DRAFT / NEEDS HUMAN LANGUAGE REVIEW. All Kazakh strings are first-draft translations supplied
for this task; they have not been reviewed by a native Kazakh-speaking methodologist. This is
flagged directly in the running UI (see "KZ draft notice" above) and is not hidden or
suppressed anywhere.

## What changed (v1.4 UI/UX polish)

- **Header clarity:** the header status row is now split into a clear "where am I" group
  (current screen name + position, course hours/modules chip) and a "how ready am I" group
  (Gate PASS count, readiness %, a BLOCKED/STOP warning chip when relevant), with the progress
  bar spanning the full header width below both. The action row is split into a language
  cluster (switcher + draft-status note) and a session-controls cluster (persist toggle,
  teacher toggle, STOP, Reset), separated by a thin divider so the two purposes don't blur
  together. The teacher toggle now shows a small status dot and a highlighted "on" state so
  it's obvious at a glance whether teacher mode is active.
- **Language switcher:** unchanged in behavior; visual contrast of the active language button
  increased slightly and its minimum width fixed so RU/KZ labels don't shift the layout.
- **Sidebar navigation:** the active screen now also gets a left accent bar (in addition to
  its existing highlight) so it's unmistakable even at a glance; long module labels and screen
  titles now wrap gracefully instead of relying on the container to clip or overflow them.
- **Main content readability:** every screen's "student task" block now has a light green
  tint and left accent, visually distinct from plain explanatory blocks (previously all blocks
  looked identical regardless of role) — done via a new `taskBlock()` render helper alongside
  the existing `block()` helper, no content changed.
- **Artifact tracker:** artifact names now wrap gracefully instead of crowding their status
  tag; the tag is pinned to the top-right of its row so long names never push it out of line
  or make it look like a broken/overlapping badge; status dropdowns are full-width and aligned.
- **Gate area:** the "Gate (контрольная точка/бақылау нүктесі)" title, gate buttons, and
  PASS/REDO/BLOCKED/STOP tag colors are unchanged; the previously-repeated status legend under
  every single gate box is now visually de-emphasized (smaller, muted, prefixed with an "ⓘ")
  so it reads as a quiet reference rather than repeated noise, while remaining present and
  readable everywhere it was before.
- **STOP overlay:** unchanged in behavior and tone (still a serious, unmissable full-screen
  block, still cleared only by Reset demo) — no styling changes were needed here, since it was
  already scoped to appear only while STOP is actually active.
- **Defect card (SCR-09):** the Fact and Hypothesis textareas now carry a subtle green vs.
  amber tint (matching the existing fact/hypothesis callout colors used elsewhere), so the
  fact-vs-hypothesis distinction is visible in the form itself, not only in the explanatory
  text above it.
- **Teacher mode:** the panel now has a graduation-cap heading icon and a slightly stronger
  top border/inset shadow, reinforcing that it is a distinct "teacher dashboard" mode layered
  over the student flow rather than part of it — no data or layout structure changed.
- **Responsive layout:** re-verified at 1440px and 820px; a couple of small tablet-width fixes
  (nav-item title no longer wraps awkwardly in the horizontal tablet sidebar; the header
  actions divider hides itself when the header wraps to multiple lines; the KZ draft note
  takes full width on narrow screens instead of clipping).
- **Print CSS (new, low-risk):** printing now hides the header, sidebar, artifacts panel,
  teacher panel, STOP overlay, nav buttons, and the gate legend hint, leaving just the current
  screen's content and the footer — useful for a quick paper handout of one screen at a time.
  This is a CSS-only addition; there is no new export/PDF feature.
- No screen was added/removed, no Gate↔screen mapping changed, no artifact tracker logic
  changed, no internal state model changed, no course methodology or safety/responsibility
  semantics changed, and no backend/LMS/PWA/login/database/package/API was introduced.

## What changed (v1.5 real-app terminology bridge)

Students will eventually practice the same inspection in the field using the real
HouseMaster Inspector app (`insp.housemasters.kz`, prototype SEM-MCD-001), whose observation
cards use a fixed set of column names (Место, Компонент, Наблюдение, Фото, Измерение, Норма,
...). The course previously used its own, differently-shaped vocabulary (object → zone →
system → component → symptom → Photo-ID → defect card) with no stated link between the two,
which is what made "Карта зон" on SCR-03 read as an unexplained status tag rather than
something tied to a real skill.

- Added one new localized string, `appBridge` (RU canonical + KZ draft), to the screen-text
  data for SCR-03, SCR-04, SCR-05, SCR-06, SCR-07, SCR-08, and SCR-09. Each names the real
  app's corresponding field in one short sentence, e.g.:
  - SCR-03 (object/case selection) → "соответствует карточке объекта обследования"
  - SCR-04 (zone selection) → real app's **«Место»** column
  - SCR-05/SCR-06 (system → component) → real app's **«Компонент»** column (the real app has
    no separate "system" field; system+component are recorded together there)
  - SCR-07 (visible sign) → **«Наблюдение»** column
  - SCR-08 (Photo-ID) → **«Фото»** column
  - SCR-09 (defect card): Факт → «Наблюдение» / «Измерение» / «Норма»; the note is explicit
    that **Гипотеза has no real-app equivalent** — the field app only ever records a checked
    fact, never a hypothesis, which is deliberately surfaced rather than papered over.
- Added one new rendering helper, `appBridgeHtml(text)`, and one new CSS class,
  `.app-bridge-hint` (small, blue-tinted, left accent bar, with a 🔗 prefix — visually
  distinct from the grey `.status-legend-hint` gate legend so the two kinds of "hint" aren't
  confused), and wired both into the existing artifact-status line on the 7 screens above.
- Artifact IDs (`zoneMap`, `routeChecklist`, `sysCompTable`, ...), their on-screen labels,
  Gate↔screen mapping, and all state/persistence logic are unchanged. This is additive
  explanatory text only.
- Scope decision (confirmed with the requester): rename nothing, add a cross-reference layer
  instead — renaming "Карта зон" itself was considered and rejected as more likely to confuse
  than clarify, since the artifact ID and existing label are already referenced elsewhere
  (teacher panel, final package table).

## What changed (v1.6 zone-map diagram + field-task bridge)

Two scoped additions, chosen over heavier alternatives on purpose (see "Scope decisions"
below), implementing students practicing in a real МКД with HouseMaster Inspector and
drawing real zone maps there — the course needed to connect to that real-world step rather
than simulate it.

**1. "Карта зон" visual artifact (SCR-03 / SCR-04)**

- New `ZONE_SHAPES` config + `renderZoneMapDiagram()` in `app.js`: a small inline-SVG house
  schematic (roof / facade / entrance / basement / yard / a separate utility-room box) with
  decorative window rects (`pointer-events:none`, so they never block a click meant for the
  facade underneath).
- Each shape carries the *same* `data-action="toggle-zone" data-zone="..."` attributes as the
  existing SCR-04 chip list, so clicking the diagram and clicking a chip do the exact same
  thing to `state.zones` — no new event-handling logic, no parallel state.
- A selected zone gets a green fill + a checkmark badge (not color alone, for colorblind
  accessibility) and a `<title>` tooltip with the zone's localized name.
- Explicitly labeled **schematic, not a real floor plan** (`ui.zoneMapCaption`, shown under
  the diagram in both languages) — it teaches the *concept* of a zone, it is not a stand-in
  for the real building plan students will work with in the field.
- `zoneMap` artifact status logic changed: it now reaches DRAFT only once an object/case is
  chosen, and SUBMITTED once at least one zone is also chosen (previously it jumped to DRAFT
  on object selection alone and never moved past that, which is what made it read as a
  "broken" status tag before this change).
- Rendered on both SCR-03 (object screen, shows the diagram building toward completion) and
  SCR-04 (zone screen, the diagram is the primary visual, the chip list stays as the
  accessible/keyboard-equivalent control).

**2. Field-task block (SCR-14, new `fieldEvidence` artifact)**

- A new `taskBlock` on SCR-14 explains, in both languages, that the course's own "field/object
  contour: 7ч" (already in `COURSE_CANON.md`'s hour balance, previously not represented
  anywhere in the digital demo) happens for real: in `insp.housemasters.kz`, on a real МКД
  assigned by the teacher/master, repeating the same dом→зона→система→компонент chain —
  including drawing a real zone map there with the real tool.
- One text input (`fieldTaskRefInput`) collects a protocol reference or export filename; one
  button (`submit-field-task`) sets the new `fieldEvidence` artifact to SUBMITTED. This
  records the student's claim, not a verified fact — exactly like `neutralMessage` and
  `selfReport` already work in this course; the teacher/master still confirms the real thing
  separately and manually.
- A bridge hint (reusing the v1.5 `.app-bridge-hint` style) names the concrete evidence
  mechanism: the real app's own "Протокол Word" / "Протокол PDF" / "Сохранить CSV" export
  buttons.
- `fieldEvidence` was appended to `ARTIFACTS_DEF`, so it appears automatically in the
  existing SCR-14 artifact-package table and the sidebar artifact tracker — no new table/UI
  needed there.

**Scope decisions (both features)**

- Zone map: a clickable schematic (this) was chosen over (a) a literal per-floor plan editor
  or (b) a freehand/canvas drawing tool. (b) was rejected specifically because it would
  duplicate, with lower fidelity, the real drawing the student will do with the real tool in
  the field — better to keep the simulation's job to teaching the *concept* and let the real
  app teach the *tool*.
- Field task: the lighter option (a text field + manual status on the existing SCR-14) was
  chosen over adding a dedicated new screen/Gate between SCR-13 and SCR-14. The heavier
  option remains available later if the methodist wants the field component to have its own
  ceremony/Gate — it was not implemented here because it would renumber the course's screen
  count (15→16) and Gate count (10→11), which is a canon change, not a demo tweak.
- `fieldEvidence` is a genuine, intentional extension of the 12-artifact list in
  `ARTIFACT_MODEL.md` (now 13) — flagged there for methodist confirmation, the same way KZ
  translations are flagged DRAFT rather than silently treated as final.
- No screen was renumbered, no Gate↔screen mapping changed, no existing artifact ID/label
  changed, and no backend/export/upload feature was added — "submitting" the field task only
  records a reference string in the browser, same as every other text field in this demo.

## What changed (v1.7 dedicated field-practice screen + Gate)

Promotes the v1.6 field-task block into its own screen and Gate — the heavier option
explicitly scoped out of v1.6 ("Scope decisions"), now implemented on explicit request
after discussing both the lighter (3A, shipped as v1.6) and heavier (3B/"Option B") designs.

**Renumbering**

- `SCREENS` gained a new entry: `{ scr: "SCR-14", moduleId: 8, gate: "G9" }`, inserted right
  after SCR-13 (module 8 continues; this field-practice screen does not belong to a new
  module, it is still the course's own field/object contour inside module 8).
- The old SCR-14 (итоговый пакет) and SCR-15 (защита) both shift up one slot, to SCR-15 and
  SCR-16 respectively.
- `GATES_DEF` gained `G10`. **G9 changed meaning**: it was "итоговая защита confirmed"
  before v1.7, it is now "field practice confirmed". The old G9 meaning moved to the new
  `G10`. This is the one genuinely irreversible-looking part of the change — any external
  reference to "Gate 9" from before v1.7 meant defense-readiness; from v1.7 on it means
  field-practice confirmation.
- `confirm-defense`'s handler was updated from `state.gates.G9 = "PASS"` to
  `state.gates.G10 = "PASS"`.
- The teacher panel's "Gate 0–9" heading was updated to "Gate 0–10".
- `SCREEN_RENDERERS` was re-sliced accordingly: index 13 is now the new field-practice
  screen, index 14 is итоговый пакет (unchanged content, just shifted), index 15 is защита
  (unchanged content, just shifted, now reads `gateBlock("G10")` instead of `gateBlock("G9")`).

**New SCR-14 "Полевая практика"**

- Carries forward, almost verbatim, the explanation/task text that lived inline on the old
  SCR-14 in v1.6 (the course's own "field/object contour: 7ч" from `COURSE_CANON.md`, done
  for real in `insp.housemasters.kz` on a real МКД assigned by the teacher/master) — same
  `fieldTaskRefInput` input and `submit-field-task` action, same `fieldEvidence` artifact
  (no new artifact added; it already existed from v1.6), same `.app-bridge-hint` naming the
  real app's export buttons as the evidence mechanism.
- New: its own `gateBlock("G9")`, so a teacher/master can explicitly PASS/REDO/BLOCK/STOP the
  field-practice step on its own, independent of the итоговый пакет screen — this is the
  concrete capability the lighter v1.6 design didn't have (there, the field task had no gate
  of its own; a teacher could only react to the artifact's DRAFT/SUBMITTED status, with no
  dedicated PASS/REDO/BLOCKED/STOP control tied specifically to field practice).
- `nextHint` now points to "сборка итогового пакета (SCR-15)" (was "SCR-14" before the shift).

**Shifted SCR-15 (итоговый пакет) and SCR-16 (защита)**

- SCR-15: identical content to the old SCR-14, minus the field-task UI (which moved out to
  the new SCR-14) — still shows the artifact package table (now including the `fieldEvidence`
  row, same as before) and the gate-log table, and the self-report block.
- SCR-16: identical content to the old SCR-15, just reading Gate G10 instead of G9 for its
  readiness check and its own `gateBlock`.

**Verification**

- `smoke_check.js`'s full screen-walk was updated from 15 to 16 iterations (RU+KZ).
- `smoke_check_v16.js`'s field-task checks were rewritten to target the new three-screen
  split: SCR-14 (field-task input/button + its own Gate G9 controls present; submitting sets
  `fieldEvidence` to SUBMITTED, visible on that same screen), SCR-15 (no duplicate field-task
  input; the `fieldEvidence` row appears correctly in the package table), SCR-16 (Gate G10
  present, not G9; confirming defense shows a PASS tag). All 12 checks in `smoke_check.js` and
  all 20 checks in `smoke_check_v16.js` pass against the v1.7 build.

**What was not changed**

- No new artifact was added — `fieldEvidence` already existed from v1.6; v1.7 only gave it a
  dedicated screen and gate instead of an inline block.
- No backend/export/upload feature was added — "submitting" the field task still only
  records a reference string in the browser.
- The real-app terminology bridge (v1.5) and the zone-map SVG diagram (v1.6, on SCR-03/
  SCR-04) are untouched by this change.

## What changed (v2.0 gate-advance consistency + teacher field-practice review)

An independent QA pass — `student_simulator.js` (Playwright) playing the full course exactly
as a real student would, every finding cross-checked directly against `app.js` source before
being acted on — found three gate/artifact design gaps, all fixed here. (Two separate,
simpler bugs the same pass found — the SCR-09 stop_now inconsistency and the 390px phone
overflow — were already fixed in v1.9.)

**1. Gates G1–G5 now auto-advance**

- Before: G1 (object), G2 (zone), G3 (system+component), G4 (visible sign), and G5 (Photo-ID)
  had no auto-PASS logic at all — unlike G0/G6/G7/G8/G10, which all auto-advance from a real
  student action. A student doing everything correctly would see roughly half the gate row
  stuck at "Не начато" with nothing indicating that was expected; the only path was the
  generic manual 6-button row every gate already exposes.
- Now: `select-object` sets G1, `toggle-zone` sets G2 (once ≥1 zone is chosen), `select-
  component` sets G3, `select-symptom` sets G4, and `select-photo` **and** the local-file
  upload path both set G5 — each to PASS, matching the one-directional "moves forward, never
  auto-reverts" pattern G0 already used (a teacher's manual REDO/BLOCKED is not auto-
  overwritten except by the same re-fire behavior every other auto-gate already has, e.g.
  saving another defect card re-asserts G6 = PASS the same way).
- The manual PASS/REDO/BLOCKED/STOP buttons stay on every gate, unchanged — this is purely
  additive auto-suggestion logic, not a removal of manual control.

**2. "Дефектная ведомость" (SCR-12) now tracks real content**

- Before: the `defectStatement` artifact's only status source was a manual `<select>`
  dropdown on SCR-12, with zero connection to the actual defect-cards table shown on that
  same screen. Since Gate G8 (SCR-13) auto-passes once `defectStatement !== NOT_STARTED`, a
  student could flip that one dropdown and pass G8 regardless of whether the ведомость
  reflected any real cards — the only artifact in the whole model not wired to the work it
  summarizes.
- Now: `submitDefectCard()` also sets `defectStatement` to SUBMITTED once ≥3 cards exist
  (DRAFT below that), the same real-content threshold already used for the `defectCards`
  artifact itself. The dropdown on SCR-12 remains, as a manual override only.

**3. New teacher-panel "Полевая практика — проверка задания" card**

- SCR-14 (field practice, v1.7) already lets a teacher PASS/REDO/BLOCK/STOP Gate G9 from the
  student's own screen; the gap was that reviewing it meant navigating there and reading the
  reference inline with the student's view.
- Added a third, full-width card to the teacher panel (`renderTeacherPanel()`) showing the
  submitted reference (`state.fieldTaskRef`, or an explicit "— ещё не отправлено —" when
  empty), the `fieldEvidence` artifact status, and Gate G9's own six-button control row
  (reusing the existing `gateBlock("G9")` — no new gate logic, no new state).
- **Deliberately not addressed here:** a student can still self-click any gate's PASS button,
  including G9, with no role-based restriction anywhere in the app — the QA pass flagged this
  as a broader "should the UI distinguish teacher-only gates from self-advancing ones"
  question, discussed and explicitly deferred as a separate, heavier decision (role-based
  rendering, not just an additive card) rather than folded into this round.

**Verification**

- `smoke_check.js` (12/12) and `smoke_check_v16.js` (20/20) both pass unchanged.
- `student_simulator.js` was updated alongside the fixes (its G1–G5 probes, the
  defectStatement-dropdown probe, and the artifact-card CSS selector — which needed updating
  once a 3rd `.teacher-card` existed — all now assert the corrected behavior) and runs
  106/106 clean, 0 console/page errors.

## What changed (v2.1 anglicism removal)

By explicit user request, swept `app.js` for the English/anglicism terms that had been a
deliberate, documented "fixed-term list" since v1.1/v1.2 ("confirmed with the requester" at the
time) — that earlier decision is now explicitly reversed for three named categories.

**1. Status words PASS / REDO / BLOCKED / STOP**

- `STR.ru.status` / `STR.kz.status` (and `STR.ru.risk` / `STR.kz.risk`'s BLOCKED entry) now
  hold fully localized words instead of the bare English enum name: RU Принято / На доработку /
  Заблокировано / Стоп; KZ Қабылданды / Түзету / Бұғатталды / Тоқтатылды. Since every gate/
  artifact tag renders through `tag(status)` → `t("status."+status)`, this alone relocalized
  every status badge across the whole app with one change per language.
- Every place that previously spelled the English word directly in prose or a label was
  reworded: the header's gate-progress chip (previously a hardcoded `'Gate PASS ' + count`
  string, now `t("ui.gatePassChipLabel")` + count — RU "Принято точек", KZ "Қабылданған
  нүктелер"), the teacher-panel warning banner, the SCR-16 readiness/not-ready text, the
  STOP-related button labels/captions/explanations (`stopTitle`, `stopCheckLabel`,
  `stopCheckText`, `stopBtn`, `safeActions.stop_now`, the risk-explanation and
  warnHighBlocked strings, the footer safety-rules list item), and the `statusLegend` string.
- Internal code — `GATE_STATUSES`/`ARTIFACT_STATUSES` arrays, `state.gates.G1 = "PASS"` etc.,
  the `tag-PASS`/`tag-REDO`/… CSS classes, and `data-status="PASS"` button attributes — is
  unchanged; these are never shown to the user and the display text was always a separate
  `t("status."+key)` lookup layered on top.

**2. The word "Gate"**

- `ui.gateTerm` (used by `gateBlock()` for every per-gate heading, and by the teacher panel's
  gates-card heading) changed from "Gate (контрольная точка)" / "Gate (бақылау нүктесі)" to
  plain "Контрольная точка" / "Бақылау нүктесі" — dropping the English word entirely rather
  than keeping it as a parenthetical gloss.
- `gateLog` (artifact display name) and `gateLogTableLabel` (SCR-15's gate-log table heading)
  both dropped "Gate-log" for "Журнал контрольных точек" / "Бақылау нүктелерінің журналы"
  (RU/KZ, worded slightly differently between the two to read naturally in each language).
  `selfReport`/"Self-report" was **not** in scope for this round and is unchanged.

**3. "Photo-ID", "Reset demo", "HTML demo"**

- "Photo-ID" → "Фотоидентификатор" (RU and KZ, identical spelling in both since it's a compound
  technical term) at every occurrence: the SCR-08 screen title, Gate G5's description, the
  `photoId` artifact name, SCR-07's next-hint, SCR-08's explanation text, and SCR-09's
  `fields.photo`/`tableHeaders.photo` strings.
- `resetBtnLabel`/`resetBtnTitle` ("Reset demo") → "Сбросить демо" / "Сброс демонстрации"
  (RU, already localized) and "Демоны қалпына келтіру" / "Демонстрацияны бастапқы күйге
  қайтару" (KZ, newly localized — previously left as the literal English phrase).
- "HTML demo (интерактивная демонстрация)" → "веб-демонстрация"/"веб-көрсетілім" on SCR-01's
  intro text and in the footer responsibility paragraph, both languages. "LMS", "бэкенд", and
  "production" in the same sentences were **not** touched — not part of the requested scope.

**Verification**

- `node -c app.js` passes (syntax-only check, no app.js logic changed in this round — purely
  string-literal and one `t()`-wiring edit for the header chip).
- Visual check at desktop width (RU and KZ) and at 390px phone width confirms no layout
  overflow from the longer Russian/Kazakh replacement words (e.g. "Принято точек" / "Қабылданған
  нүктелер" chips, "Контрольная точка" vs the old "Gate N").
- All three QA scripts were updated for the new display text and re-run clean:
  `smoke_check.js` (12/12 — one check's RU/KZ-comparison logic was also fixed, since it was
  comparing the *same* literal English word across both languages before, which no longer
  holds now that the word itself is localized per-language), `smoke_check_v16.js` (20/20,
  needed no changes — its one SUBMITTED-text check already had a CSS-class fallback),
  `student_simulator.js` (106/106, 0 console/page errors — updated every `.trim() === 'PASS'`-
  style assertion, the KZ-context self-pass check, the HIGH-risk STOP-text check, and the
  teacher-panel pass-count regex to match the new localized words).

## Scope confirmation

No backend, LMS, login, database, deployment step, or package installation was introduced.
The course logic (72h / 8 modules / core chain / student role) was implemented as specified,
not redesigned. No unrelated project files were altered.
