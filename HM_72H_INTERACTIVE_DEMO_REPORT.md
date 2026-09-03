# HM_72H Interactive Course Demo — Implementation Report v1.3

## Changelog

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

## Screens implemented (15/15)

SCR-01 Старт курса · SCR-02 Роль студента и safety · SCR-03 Карта МКД как объекта ·
SCR-04 Зоны дома · SCR-05 Инженерные системы · SCR-06 Система → компонент ·
SCR-07 Видимый признак / симптом · SCR-08 Фотофиксация / Photo-ID · SCR-09 Карточка дефекта ·
SCR-10 Риск · SCR-11 Безопасное действие · SCR-12 Дефектная ведомость ·
SCR-13 Учебный проект технического акта · SCR-14 Итоговый пакет · SCR-15 Защита / результат.

Each screen shows: screen ID + title, current module (with hours), a short explanation, the
student task, the artifact(s) produced, PASS/REDO/BLOCKED/STOP gate controls (where a gate
applies to that screen), and a "Далее: …" next-action hint plus Prev/Next navigation. The
left sidebar lists all 15 screens grouped by module, highlights the current screen, and shows
a small color-coded dot per screen reflecting its linked gate's status. The header shows a
persistent course status panel: screens hours/modules, screen counter, gate PASS count,
overall progress bar, and a BLOCKED/STOP warning chip when relevant.

## Gates implemented (10/10)

G0–G9 are all present as visible control points, each tied to the screen named in
`TASK_FOR_CLAUDE.md`:

- G0 (role/safety) — SCR-02, auto-set to PASS when the safety checkbox is confirmed (also
  settable manually)
- G1 (object/simulation selected) — SCR-03
- G2 (zone selected) — SCR-04
- G3 (system + component identified) — SCR-06
- G4 (visible sign recorded) — SCR-07
- G5 (Photo-ID linked) — SCR-08
- G6 (defect card completed) — SCR-09, auto-set to PASS on first saved card
- G7 (risk + safe action defined) — SCR-11, auto-set to PASS when a neutral message is saved
  with both a risk and an action chosen
- G8 (defect statement assembled) — SCR-13, auto-set to PASS when the act draft is saved
- G9 (final package ready for defense) — SCR-15, set to PASS when "защита пройдена" is
  confirmed

Every gate also exposes all six statuses (NOT STARTED / IN PROGRESS / PASS / REDO / BLOCKED /
STOP) as explicit buttons, so a teacher can demonstrate any gate behavior manually at any time,
independent of the auto-suggestions above.

## Artifact tracker behavior

All 12 required artifacts (карта зон, маршрутный чек-лист, таблица «система → компонент»,
карточки дефекта (мин. 3), Photo-ID, дефектная ведомость, учебный проект технического акта,
нейтральное сообщение ответственному лицу, safety-log, gate-log, self-report, итоговая защита)
are listed in the always-visible right-hand panel, each with a status dropdown
(NOT STARTED / DRAFT / SUBMITTED / PASS / REDO / BLOCKED). Several artifacts auto-update their
status as the student progresses (e.g. selecting a zone moves the route checklist to DRAFT/
SUBMITTED, saving 3+ defect cards moves the defect-cards artifact to SUBMITTED); every status
can also be changed manually at any time.

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
  and choosing "Остановиться и не продолжать (STOP)" as a safe action on SCR-11 also triggers
  it. Triggering STOP shows a full-screen red overlay explaining the safety boundary and
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
count, risk-level distribution, a BLOCKED/STOP alert when relevant, and full tables of all 10
gate statuses and all 12 artifact statuses.

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

## Scope confirmation

No backend, LMS, login, database, deployment step, or package installation was introduced.
The course logic (72h / 8 modules / core chain / student role) was implemented as specified,
not redesigned. No unrelated project files were altered.
