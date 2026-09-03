# TASK FOR CLAUDE

TASK: Build HM_72H Interactive Course HTML Demo v1.0

PROJECT:
HouseMaster / hm_72h_course

OBJECTIVE:
Собрать автономный интерактивный HTML demo для 72-часового курса
«Хаузмастер МКД: технический осмотр, фиксация дефектов и сопровождение решений ОСИ / ПТ».

IMPORTANT:
This is NOT a backend, NOT LMS, NOT production PWA, NOT authentication system.
Build a static/local interactive prototype that can be opened in browser and demonstrated to a college director, methodist, teacher, or working group.

Do not expand scope.
Do not redesign the course.
Do not create new methodology.
Implement the fixed interactive course logic only.

CANONICAL COURSE LOGIC:
The student learns to research an apartment building as an engineering functional system.

Core chain:
дом → зона → система → компонент → дефект → симптом → фото → карточка → риск → действие → акт → защита

Student role:
- student observes and records visible signs;
- student does not act as expert;
- student does not repair;
- student does not enter unsafe/closed zones;
- student does not make official conclusions;
- student produces educational artifacts only.

COURSE STRUCTURE:
Total: 72 academic hours.

Modules:
1. МКД как объект эксплуатации — 8 ч
2. Инженерные системы МКД — 12 ч
3. Типовые дефекты, симптомы и риски — 10 ч
4. Методика технического осмотра дома — 10 ч
5. Фотофиксация и цифровая карточка дефекта — 8 ч
6. Технический акт и дефектная ведомость — 10 ч
7. Коммуникация с ОСИ / ПТ / УК / жителями — 6 ч
8. Практический осмотр МКД и итоговая защита — 8 ч

Hour balance:
- theory: 25
- practice: 23
- digital work: 17
- field/object contour: 7

REQUIRED DEMO MODEL:
Implement 15 screens:

SCR-01 — Старт курса
SCR-02 — Роль студента и safety
SCR-03 — Карта МКД как объекта
SCR-04 — Зоны дома
SCR-05 — Инженерные системы
SCR-06 — Система → компонент
SCR-07 — Видимый признак / симптом
SCR-08 — Фотофиксация / Photo-ID
SCR-09 — Карточка дефекта
SCR-10 — Риск
SCR-11 — Безопасное действие
SCR-12 — Дефектная ведомость
SCR-13 — Учебный проект технического акта
SCR-14 — Итоговый пакет
SCR-15 — Защита / результат

REQUIRED UI STRUCTURE:
Each screen must show:
1. screen ID and title;
2. current module;
3. short explanation;
4. student task;
5. artifact produced;
6. PASS / REDO / BLOCKED / STOP logic;
7. next action.

REQUIRED NAVIGATION:
- left or top navigation with all 15 screens;
- current screen highlight;
- previous / next buttons;
- progress indicator;
- visible course status panel.

REQUIRED GATES:
Implement Gate 0–9 as visible control points:

Gate 0 — student understands role and safety
Gate 1 — object or simulation selected
Gate 2 — zone selected
Gate 3 — system and component identified
Gate 4 — visible sign recorded
Gate 5 — Photo-ID linked
Gate 6 — defect card completed
Gate 7 — risk and safe action defined
Gate 8 — defect statement assembled
Gate 9 — final package ready for defense

Gate statuses:
- NOT STARTED
- IN PROGRESS
- PASS
- REDO
- BLOCKED
- STOP

Gate behavior may be simulated locally with buttons/selects.
No backend required.

REQUIRED ARTIFACT TRACKER:
Show a checklist / tracker of final student artifacts:

- карта зон;
- маршрутный чек-лист;
- таблица «система → компонент»;
- минимум 3 карточки дефекта;
- Photo-ID;
- дефектная ведомость;
- учебный проект технического акта;
- нейтральное сообщение ответственному лицу;
- safety-log;
- gate-log;
- self-report;
- итоговая защита.

Each artifact should have status:
NOT STARTED / DRAFT / SUBMITTED / PASS / REDO / BLOCKED.

REQUIRED INTERACTIVE ELEMENTS:
At minimum implement:
1. module/screen navigation;
2. gate status change buttons;
3. artifact checklist status changes;
4. one sample defect-card form;
5. one sample Photo-ID selector;
6. risk selector: LOW / MODERATE / HIGH / BLOCKED;
7. STOP scenario button that visibly blocks continuation until reset;
8. demo reset button;
9. teacher view toggle or panel showing progress summary.

SAMPLE DEFECT CARD FIELDS:
- Defect Card ID
- Zone
- System
- Component
- Visible sign
- Photo-ID
- Fact
- Hypothesis
- Risk level
- Safe next action
- Status

IMPORTANT DISTINCTIONS:
The UI must clearly show:
- fact ≠ hypothesis;
- photo confirms visible sign, not cause;
- educational act ≠ official inspection act;
- REDO ≠ failure;
- BLOCKED / STOP = safety or critical boundary issue.

REQUIRED SAFETY MESSAGES:
Include visible safety panel:
- do not open electrical panels;
- do not touch pipes, cables, valves, pumps, meters;
- do not enter closed zones;
- do not photograph people, children, apartment numbers, documents, personal data;
- do not argue with residents;
- do not promise repairs;
- do not make expert conclusions;
- stop and report unsafe situations to teacher/master.

RESPONSIBILITY BOUNDARY:
Include a short footer / info panel:
HouseMaster Academy is a conditional working name for the educational direction / educational contour of the HouseMaster platform and mobile application.
The rights holder of methodology, materials, and digital contour is ТОО «Абай-Гермес».
ТОО «Абай-Гермес» provides methodology, templates, HTML demo, workbook structure, and consultative support.
ТОО «Абай-Гермес» does not undertake direct organization, supervision, or execution of field works unless separately agreed in writing.
College is responsible for educational process, teachers, safety, access, accompaniment, and assessment.

VISUAL STYLE:
- clean educational dashboard;
- Russian language;
- readable on laptop;
- responsive enough for tablet;
- no excessive animation;
- professional college-demo look;
- no neon, no startup hype;
- clear cards, progress bars, tables, tags;
- use neutral palette with HouseMaster green/blue accents if needed.

TECHNICAL REQUIREMENTS:
- Create a self-contained static prototype.
- Prefer simple structure:
  /index.html
  /styles.css
  /app.js
  /README.md
  /HM_72H_INTERACTIVE_DEMO_REPORT.md

No external backend.
No API calls.
No login.
No data persistence required beyond local JS state.
If using localStorage, make it optional and documented.
The demo must work by opening index.html in browser.

ACCEPTANCE CRITERIA:
1. index.html opens without build step.
2. All 15 screens are reachable.
3. Gate 0–9 are visible.
4. Gate status changes work.
5. Artifact tracker works.
6. Defect card demo works.
7. STOP scenario visibly blocks or warns.
8. Teacher summary view/panel works.
9. Course structure 72h / 8 modules is visible.
10. Safety and responsibility boundaries are visible.
11. No backend/LMS/PWA scope is introduced.
12. No claim of expert qualification or official inspection.
13. Demo can be shown to director in 7–10 minutes.
14. README explains how to open and demonstrate.
15. Final report documents files changed, behavior implemented, limitations, and next suggested review.

DEMO SCRIPT TO SUPPORT:
The demo should support this presentation flow:
1. Show course start and 8 modules.
2. Show student role and safety.
3. Show building zones.
4. Select system and component.
5. Record visible sign.
6. Link Photo-ID.
7. Fill defect card.
8. Choose risk and safe action.
9. Show REDO / STOP behavior.
10. Show final artifact package.
11. Show teacher progress summary.

DELIVERABLES:
- index.html
- styles.css
- app.js
- README.md
- HM_72H_INTERACTIVE_DEMO_REPORT.md

REPORT MUST INCLUDE:
- implementation summary;
- file list;
- how to run;
- how to demo;
- which screens are implemented;
- which gates are implemented;
- artifact tracker behavior;
- safety/STOP behavior;
- known limitations;
- confirmation that no backend/LMS/PWA scope was added.

STOP CONDITIONS:
Stop and report before continuing if:
- there is an existing implementation that conflicts with this task;
- required files are missing but referenced by the project;
- the task would require backend, database, authentication, deployment, or package installation;
- the implementation would alter unrelated project files;
- you are unsure which folder is the correct project folder.

FINAL RESPONSE FORMAT:
After completion, report:

STATUS: COMPLETE / BLOCKED / NEEDS REVIEW

FILES CHANGED:
- ...

WHAT WORKS:
- ...

WHAT TO REVIEW:
- ...

LIMITATIONS:
- ...

DO NOT COMMIT unless explicitly instructed.
