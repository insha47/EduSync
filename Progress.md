# Progress.md — EduSync

> Update this file at the END of every working session. Paste it back into the Claude Project knowledge so the next chat knows where you are.

**Last updated:** 2026-10-28
**Current phase:** Phases 1–7 and 9 (partly) built; awaiting your testing in a real browser.
**Target:** Tier A demo as a published Claude artifact
**Live demo:** https://claude.ai/artifact/HkXnQ51EHwLsVJnLYuv73Z
**Repo:** https://github.com/insha47/EduSync

---

## Legend
`[ ]` not started · `[~]` in progress / partly simulated · `[x]` done · `[!]` blocked

## Phase 0 — Setup
- [x] Create Claude Project "EduSync"
- [x] Upload ARCHITECTURE.md and Progress.md to Project knowledge
- [x] Paste PROJECT_INSTRUCTIONS.md into Project instructions
- [x] Decide final app name (EduSync)
- [ ] Get Groq API key (for Tier B only; never paste keys into chat or code)
- [x] GitHub repo created and files pushed (extra step)

## Phase 1 — Foundation
- [x] Mock data file (users, classes, timetable, subjects, syllabus)
- [x] Login page + role selector
- [x] Role router + navigation shell (Student / Teacher / Admin)
- [x] Theme, layout, mobile-first styles

## Phase 2 — Student core
- [x] Attendance dashboard (monthly %, per subject)
- [x] "Lectures needed to reach 75%" + "bunk budget" (`calcAttendance`)
- [~] QR scan screen — simulated: student types the code shown under the QR (no camera / jsQR yet)

## Phase 3 — Teacher core
- [x] "Who teaches what" dashboard (teacher Home)
- [x] Start attendance → rotating QR (new code every 20 s)
- [x] Live attendance count (+ "Simulate a student scan" demo button)
- [x] 10-min pre-lecture notification (timer toast + preview button)

## Phase 4 — Submission portal
- [x] Teacher: create assignment, upload note/PYQ (text only, no file upload)
- [x] Student: view + submit (text or link, late flag)
- [x] Teacher: submitted vs pending list + remind (simulated toast)

## Phase 5 — AI
- [x] Syllabus structure + tracker UI
- [x] AI tutor chat (topic-grounded; uses the page's Ask Claude permission)
- [x] Quick quiz per topic

## Phase 6 — Communication & safety
- [x] Class group + common room chat UI
- [x] Teacher community channels
- [x] Regex moderation + AI moderation (AI only for ambiguous words)
- [x] Red/green flag system + admin reversal
- [~] WebCrypto encryption demo (AES-GCM demo card only; not real E2E chat)

## Phase 7 — Admin
- [~] Approval queue (accounts only; uploads/posts not wired)
- [x] Broadcast messages
- [x] Fees register + tracker
- [x] Moderation dashboard (Reports tab)

## Phase 8 — Extras
- [x] Campus feed (events + achievements, likes, filter) — simulated word filter only
- [x] Clubs with vacancies + volunteering (added by request, not in original architecture)
- [ ] Video lecture launcher (Jitsi)

## Phase 9 — Ship
- [~] Bug pass on all 3 roles (syntax check + stub-DOM smoke test passed; not a real browser)
- [~] Publish artifact (published; not yet tested on a phone)
- [x] Demo script (in README.md)
- [ ] Demo video
- [x] README (honest limitations)
- [ ] Submission

---

## Decisions Log
| Date | Decision | Why |
|---|---|---|
| 2026-10-28 | Two tiers: artifact demo first, backend later | Published artifacts can't call Groq or run servers |
| 2026-10-28 | Vanilla JS (no React) for the single HTML file | Smaller file, no CDN dependency |
| 2026-10-28 | Built Campus feed + Clubs early, in one "Campus" tab | User request; keeps bottom nav at 6 tabs |
| 2026-10-28 | QR attendance uses typed code instead of camera | Camera scanning needs a real phone; simulated and labelled in the UI |
| 2026-10-28 | Code rotates every 20 s with simulated signing | Real signing needs the Tier B server |

## Proposed architecture additions (need approval before editing ARCHITECTURE.md)
- `clubs(id, name, about, lead_id)`
- `club_vacancies(id, club_id, title, detail)`
- `vacancy_volunteers(vacancy_id, user_id)`

## Known Issues
- Phases 1–4 and Campus not yet tested in a browser by the builder.
- QR image uses the cdnjs `qrcode-generator` 1.4.4 script; URL unverified. If no QR image shows, the 6-character code still works.
- Data lives in memory: a page refresh resets everything and signs you out (expected for Tier A).
- Admin approval of uploads and posts is not wired up yet.
- Tutor and AI moderation need the viewer's permission for the page to ask Claude; without it the tutor shows an unavailable message and moderation uses the word filter only.
- Video lecture launcher (Jitsi) not built.

## Test checklist (Phases 1–4 + Campus)
1. Log in as each role: `TY01`, `sunita@edusync.demo`, `admin@edusync.demo` (password `demo123`). `TY04` should be blocked as pending.
2. Student `TY01`: DBMS shows 70% and "attend the next 4 lectures in a row".
3. Teacher: start attendance, note the code. Student: enter the code, then try again ("already marked").
4. Teacher: close and save, then check the student's percentage changed.
5. Teacher posts an assignment; student submits; teacher sees Submitted vs Pending.
6. Campus: post an Achievement, like it, post a vacancy as the club lead, volunteer as another student.
7. Check 360 px width and dark mode.

## Next Session — Start Here
1. Test everything on a real phone and laptop; list every error.
2. Run `git pull origin main`, replace `edusync.html`, add `README.md`, then `git add .`, `git commit`, `git push`.
3. Ask Claude to fix the bugs, then build the Jitsi video launcher and upload/post approval.
4. Record the demo video and submit.
