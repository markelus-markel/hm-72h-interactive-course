# HM 72H Interactive Course Demo

This folder contains source materials, task instructions, and the resulting static HTML
interactive demo prototype for the 72-hour HouseMaster MCD course
«Хаузмастер МКД: технический осмотр, фиксация дефектов и сопровождение решений ОСИ / ПТ».

This is **not** LMS, **not** backend, **not** a production PWA, **not** an authentication system.
It is a self-contained, static, browser-openable interactive prototype for demonstration purposes.

## Source / spec files (read in this order)

1. `README.md` — this file
2. `COURSE_CANON.md` — course structure, modules, hours, core chain
3. `SCREEN_MAP.md` — the 15 required screens
4. `SAFETY_AND_RESPONSIBILITY.md` — safety rules and responsibility boundary
5. `ARTIFACT_MODEL.md` — student artifacts and their statuses
6. `DEMO_SCRIPT.md` — the presentation flow for a director/methodist demo
7. `TASK_FOR_CLAUDE.md` — the detailed implementation task
8. `HANDOFF_TO_CLAUDE.md` — implementation handoff checklist

## Demo prototype files

- `index.html` — the demo application (open this in a browser)
- `styles.css` — styles for the demo
- `app.js` — all interactive logic (vanilla JS, no dependencies)
- `HM_72H_INTERACTIVE_DEMO_REPORT.md` — implementation report

## How to run

No build step, no server, no installation required.

1. Open `index.html` directly in any modern desktop browser (Chrome, Edge, Firefox).
   Double-click the file, or drag it into a browser window.
2. The demo works entirely client-side. No network access, no login, no backend is used.

Optional: a "Сохранять прогресс в браузере" (save progress in this browser) checkbox in the
header turns on `localStorage` persistence for the demo state, so a page reload keeps your
place. It is **off by default** — with it off, reloading the page or opening `index.html`
again always starts a fresh demo.

## Bilingual mode (RU / KZ) — v1.3

The header includes a **Рус / Қаз** language switcher. It changes all visible interface text
(screen titles, module names, explanations, tasks, artifact names, gate names, footer, teacher
panel, STOP overlay) between Russian and Kazakh.

- **Russian is the canonical, reviewed baseline** (same content as v1.2, unchanged).
- **Kazakh is a draft translation** and is clearly marked as such in the UI itself: a small
  notice reading *"Қазақша мәтін — бастапқы нұсқа. Тілдік және әдістемелік тексеруді қажет
  етеді."* is shown in the header whenever Kazakh mode is active (a Russian equivalent notice
  is shown in Russian mode). See `HM_72H_INTERACTIVE_BILINGUAL_QA_REPORT.md` for full scope
  and what still needs a native-speaker/methodologist review.
- Switching language **never resets progress**: gate statuses, artifact statuses, saved defect
  cards, the current screen, and all other state are preserved across a switch. Saved defect
  cards even re-localize their zone/system/component/symptom/photo/risk/action labels to the
  newly selected language.
- The following stay identical in both languages by design (they are fixed technical anchors,
  not translated content): `PASS`, `REDO`, `BLOCKED`, `STOP`, `Gate`, `Photo-ID`, `HTML demo`,
  `Reset demo`, and every internal state/enum key used by the JavaScript.
- Free-text fields the student types (fact notes, hypothesis, self-report, message/act drafts)
  are **not** auto-translated when switching language — only the surrounding interface labels
  change. This is expected behavior, not a bug.

## How to demonstrate (7–10 minutes)

Follow `DEMO_SCRIPT.md`. In short:

1. Show course start and the 8 modules (SCR-01).
2. Show student role and safety rules (SCR-02) — optionally trigger the "Симулировать STOP"
   button here to show the safety boundary, then "Сбросить демо" to continue.
3. Show building zones (SCR-04).
4. Select a system and component (SCR-05 / SCR-06).
5. Record a visible sign (SCR-07) and link a Photo-ID (SCR-08).
6. Fill a defect card (SCR-09) — repeat to create 2–3 cards.
7. Choose risk and a safe action (SCR-10 / SCR-11).
8. Show REDO / BLOCKED / STOP behavior via the Gate status buttons on any screen, and the
   global "Симулировать STOP" button.
9. Show the defect statement and the educational act draft (SCR-12 / SCR-13).
10. Show the final artifact package and self-report (SCR-14).
11. Show the defense screen (SCR-15) and toggle "Режим преподавателя" (teacher view) to show
    the progress summary dashboard.

## Scope confirmation

No backend, no LMS, no database, no authentication, no deployment step, and no package
installation are part of this prototype. All 15 screens, Gate 0–9, and the artifact tracker
are simulated locally with buttons/selects and in-memory (optionally `localStorage`) state,
as specified in `TASK_FOR_CLAUDE.md`.
