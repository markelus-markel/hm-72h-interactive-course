# HM_72H Interactive Course Demo — Bilingual (RU/KZ) QA Report (v1.3)

Scope: full QA pass of the v1.3 RU/KZ bilingual implementation, covering all 20 acceptance
criteria from the bilingual-mode task, plus a byte-for-byte check that the original spec files
were not touched.

## Method

- No live link to the Windows machine exists in this session; testing was performed against
  this session's copy of the same `index.html` / `styles.css` / `app.js` files being delivered.
- `node -c app.js` syntax check.
- Automated headless-Chromium (Playwright) walkthrough: language switcher, RU↔KZ content
  checks, state-preservation checks across a language switch, all 15 screens in both
  languages, Prev/Next, gate status buttons, artifact dropdowns, a full defect-card creation
  flow (3 cards) followed by a language switch to confirm saved cards re-localize, the
  STOP/Reset cycle, teacher mode, and layout at 1440px/820px — 30 automated checks total.
- Captured every `console.error` / uncaught page error and every network request during the
  entire run.
- Byte-for-byte diff of the 7 original spec files (`ARTIFACT_MODEL.md`, `COURSE_CANON.md`,
  `DEMO_SCRIPT.md`, `HANDOFF_TO_CLAUDE.md`, `SAFETY_AND_RESPONSIBILITY.md`, `SCREEN_MAP.md`,
  `TASK_FOR_CLAUDE.md`) against the original project archive, and a check that `docs/`,
  `output/`, `src/` remain empty.

## FILES CHANGED

- `index.html` — updated (header brand/actions, footer, and STOP overlay now fully rendered
  by `app.js`, consistent with how sidebar/artifacts/teacher panel already worked)
- `app.js` — rewritten (RU/KZ `STR` dictionary + `t()` lookup, language switcher, ID-based
  reference data and defect-card storage with re-localizing resolver functions, `state.lang`)
- `styles.css` — updated (added `.lang-switch` / `.lang-btn` / `.kz-draft-note` styles only)
- `README.md` — updated (new "Bilingual mode (RU / KZ)" section)
- `HM_72H_INTERACTIVE_DEMO_REPORT.md` — updated (new v1.3 changelog + "What changed" +
  "KZ text status" sections; title bumped to v1.3)
- `HM_72H_INTERACTIVE_BILINGUAL_QA_REPORT.md` — created (this report)

No other files were modified. The 7 original spec `.md` files are unchanged (verified below).

## ACCEPTANCE CRITERIA — RESULTS (20/20)

1. **Opens without a build step** — PASS. Direct `file://` load of `index.html`.
2. **Language switcher visible** — PASS. "Рус / Қаз" buttons in the header; active language
   visually highlighted.
3. **RU mode matches v1.2 content** — PASS. All v1.2 strings (screen text, gate/status
   legends, Photo-ID/HTML demo/Reset demo parentheticals, footer, teacher panel) reproduced
   verbatim as the RU baseline; spot-checked "Gate (контрольная точка)" and "HTML demo
   (интерактивная демонстрация)".
4. **KZ mode changes visible labels** — PASS. Switching to KZ changes brand text, sidebar,
   screen content, gate names, artifact names, footer, and teacher panel; spot-checked "Gate
   (бақылау нүктесі)".
5. **Switching language does not reset state** — PASS. Verified with a concrete scenario: G0
   set to PASS and the safety checkbox checked in RU, then switched to KZ — both were still
   PASS/checked; switched back to RU — still PASS.
6. **All 15 screens work in both languages** — PASS. Walked SCR-01…SCR-15 in RU and then again
   in KZ with zero console/page errors.
7. **Prev/Next works** — PASS. 14×Next reaches SCR-15, 14×Prev returns to SCR-01.
8. **Gate 0–9 visible and functional in both languages** — PASS. Gate status buttons cycle
   correctly (tested REDO on G0); gate names and the "Gate (контрольная точка)"/"Gate
   (бақылау нүктесі)" anchors render per language; PASS/REDO/BLOCKED/STOP always render as the
   same English words in both languages.
9. **Gate PASS counter works in both languages** — PASS. Counter is language-independent
   (numeric), confirmed unaffected by language switches.
10. **Artifact tracker works in both languages** — PASS. Artifact names translate; status
    dropdown values and their effect are unaffected by language (tested "Карта зон"/"Аймақтар
    картасы" → DRAFT).
11. **SCR-09 defect card works in both languages** — PASS. Form fields, labels, and the
    Fact/Hypothesis distinction render correctly in both languages; created 3 cards.
12. **STOP overlay works in both languages** — PASS. Triggered STOP; overlay blocks
    navigation underneath (a click elsewhere is a no-op); overlay text is language-appropriate
    ("STOP (остановка по безопасности)" in RU / "STOP (қауіпсіздік бойынша тоқтату)" in KZ).
13. **Reset demo works in both languages** — PASS. Reset clears STOP, returns to SCR-01, and
    resets gates/artifacts/cards; language preference itself is intentionally kept across a
    Reset (language is a display setting, not "progress" — see "What to review").
14. **Teacher mode works in both languages** — PASS. Teacher panel opens/closes, and its
    heading/summary/legend/Gate-Artifact tables render in the active language.
15. **Saved defect cards remain visible across a language switch** — PASS, and slightly
    exceeded: cards are not just visible but correctly **re-localize** their
    zone/system/component/symptom/Photo-ID/risk/action text when the language changes,
    because cards store stable IDs rather than pre-resolved display strings. Verified: a card
    created in RU showing "Водоснабжение" displays "Сумен жабдықтау" after switching to KZ,
    with no data loss.
16. **No console errors** — PASS. Zero `console.error`/page errors across the entire scripted
    run (both languages, all screens, all interactions, STOP/Reset, teacher mode).
17. **No external network/API calls** — PASS. Zero non-`file://` requests observed.
18. **No overflow at 1440px/820px** — PASS. `scrollWidth` did not exceed `clientWidth` at
    either width, in both languages.
19. **No backend/database/login/deployment/package install added** — PASS. Confirmed by
    inspection — no such code was added; the app remains a static, dependency-free
    `index.html` + `styles.css` + `app.js` bundle.
20. **Original spec markdown files unchanged** — PASS. Byte-for-byte diff confirms
    `ARTIFACT_MODEL.md`, `COURSE_CANON.md`, `DEMO_SCRIPT.md`, `HANDOFF_TO_CLAUDE.md`,
    `SAFETY_AND_RESPONSIBILITY.md`, `SCREEN_MAP.md`, `TASK_FOR_CLAUDE.md` are identical to the
    original project archive; `docs/`, `output/`, `src/` remain empty. `README.md` and
    `HM_72H_INTERACTIVE_DEMO_REPORT.md` were updated as explicitly permitted by the task (to
    document the bilingual change), and this QA report is the newly created deliverable.

## KZ TEXT STATUS

**DRAFT / NEEDS HUMAN LANGUAGE REVIEW.** Every Kazakh string in the interface is a first-draft
translation supplied for this task. None of it has been reviewed by a native Kazakh-speaking
methodologist or checked for course-specific terminology consistency. This status is not just
a note in this report — it is shown directly inside the running demo:

- In **Kazakh mode**, the header always shows: *"Қазақша мәтін — бастапқы нұсқа. Тілдік және
  әдістемелік тексеруді қажет етеді."*
- In **Russian mode**, the header always shows the Russian equivalent: *"Казахская версия —
  draft. Требуется языковая и методическая проверка."*

Neither notice can be dismissed, hidden, or turned off from the UI.

## SMOKE TEST

30/30 automated checks passed (see full list in the method section above); zero console
errors; zero non-`file://` network requests; no layout overflow at 1440px or 820px in either
language.

One bug was found and fixed during this pass: the SCR-06 ("Система → компонент") screen's RU
and KZ prose blocks were missing an `explanation` string, which caused a runtime error the
first time SCR-06 was rendered with a system already selected — the effect would have been a
broken/incomplete SCR-06 screen (component chips not rendering) the first time a demonstrator
reached that screen after selecting a system. Root cause: an incomplete data entry left over
from drafting, compounded by a fragile regex-based text transform in the renderer that assumed
a differently-shaped string. Fix: added a proper `explanation` string for SCR-06 in both
languages, and replaced the regex transform with a direct string interpolation. Re-ran the full
smoke suite after the fix — all 30 checks now pass, including a full walk of all 15 screens in
both languages with a system/component actually selected.

## WHAT TO REVIEW

- **KZ translation quality:** all Kazakh strings need a native-speaker and methodologist pass
  — terminology consistency (e.g. technical system/component names), register, and any
  domain-specific phrasing a professional Kazakh-language methodologist would adjust. This is
  expected and flagged in the UI itself; see "KZ text status" above.
- **Reset + language interaction:** by design, clicking "Reset demo" clears all course progress
  (gates, artifacts, defect cards, screen position) but keeps the currently selected UI
  language, since language is a display preference rather than course progress. If a reviewer
  would prefer Reset to also return the language to Russian, that is a one-line change; flagging
  it here as a decision rather than making it silently.
  A quick manual click-through in Chrome on the target machine is still worth doing once, to
  catch anything environment-specific that headless testing can't surface (font rendering of
  Kazakh-specific characters, printing, etc.) — this remains true from every prior QA round and
  is not new to bilingual mode.
- As with prior rounds, this session has no access to the actual Windows machine/file path, so
  the exact local file identity (matching earlier QA rounds' "item 2") could not be
  independently re-verified here; it is not expected to differ, since only file *contents* were
  changed, not filenames or locations.

## FINAL RECOMMENDATION

READY_FOR_KZ_LANGUAGE_REVIEW
