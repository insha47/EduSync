# Progress.md — EduSync

> Update this file at the END of every working session. Paste it back into the Claude Project knowledge so the next chat knows where you are.

**Last updated:** 2026-10-28
**Current phase:** Phase 0 — Setup
**Target:** Tier A demo as a published Claude artifact

---

## Legend
`[ ]` not started · `[~]` in progress · `[x]` done · `[!]` blocked

## Phase 0 — Setup
- [ ] Create Claude Project "EduSync"
- [ ] Upload ARCHITECTURE.md and Progress.md to Project knowledge
- [ ] Paste PROJECT_INSTRUCTIONS.md into Project instructions
- [ ] Decide final app name
- [ ] Get Groq API key (for Tier B only; never paste keys into chat or code)

## Phase 1 — Foundation
- [ ] Mock data file (users, classes, timetable, subjects, syllabus)
- [ ] Login page + role selector
- [ ] Role router + navigation shell (Student / Teacher / Admin)
- [ ] Theme, layout, mobile-first styles

## Phase 2 — Student core
- [ ] Attendance dashboard (monthly %, per subject)
- [ ] "Lectures needed to reach 75%" + "bunk budget"
- [ ] QR scan screen (simulated camera + real jsQR if possible)

## Phase 3 — Teacher core
- [ ] "Who teaches what" dashboard
- [ ] Start attendance → rotating QR
- [ ] Live attendance count
- [ ] 10-min pre-lecture notification

## Phase 4 — Submission portal
- [ ] Teacher: create assignment, upload note/PYQ
- [ ] Student: view + submit
- [ ] Teacher: submitted vs pending list + remind

## Phase 5 — AI
- [ ] Syllabus structure + tracker UI
- [ ] AI tutor chat (topic-grounded)
- [ ] Quick quiz per topic

## Phase 6 — Communication & safety
- [ ] Class group + common room chat UI
- [ ] Teacher community channels
- [ ] Regex moderation + AI moderation
- [ ] Red/green flag system + admin reversal
- [ ] WebCrypto encryption demo

## Phase 7 — Admin
- [ ] Approval queue
- [ ] Broadcast messages
- [ ] Fees register + tracker
- [ ] Moderation dashboard

## Phase 8 — Extras
- [ ] Campus feed
- [ ] Video lecture launcher (Jitsi)

## Phase 9 — Ship
- [ ] Bug pass on all 3 roles
- [ ] Publish artifact + test link on phone
- [ ] Demo script + video
- [ ] README (honest limitations)
- [ ] Submission

---

## Decisions Log
| Date | Decision | Why |
|---|---|---|
| 2026-10-28 | Two tiers: artifact demo first, backend later | Published artifacts can't call Groq or run servers |

## Known Issues
_(none yet)_

## Next Session — Start Here
1. Complete Phase 0.
2. Ask Claude to build Phase 1 only (one phase per chat).
