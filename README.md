[Uploading README.md…]()
# EduSync

One platform for Students, Teachers and Admins: QR attendance, assignments, an AI tutor, safe chat, fees and a campus feed.

**Live demo:** https://claude.ai/artifact/HkXnQ51EHwLsVJnLYuv73Z
**Design:** see `ARCHITECTURE.md` · **Status:** see `Progress.md`

## Run it
Open the live demo link, or double-click `edusync.html` (needs internet for fonts and the QR library).
Demo logins (password `demo123`): student `TY01`, teacher `sunita@edusync.demo`, admin `admin@edusync.demo`. `TY04` is pending until an admin approves it.

## What is in it
- **Attendance:** rotating QR code (changes every 20 s), classroom check, one mark per student, live count, and "lectures needed to reach 75%" plus a bunk budget.
- **Tasks:** teachers post assignments, notes and previous year papers; students submit; teachers see submitted vs pending and send reminders.
- **AI tutor:** syllabus-grounded explanations, quick quizzes and a progress tracker per subject.
- **Chat and safety:** class group, common room, teacher community. Messages are checked on the sender's device (word filter first, AI only for unclear wording). Red and green behaviour flags show a reason and can be reversed by an admin.
- **Admin:** approvals, fees register, broadcast announcements, flag review.
- **Campus:** feed for events and achievements, clubs with vacancies and volunteers.

## Honest limitations (Tier A demo vs Tier B production)
| Feature | In this demo | Needs Tier B |
|---|---|---|
| Data | In memory. A refresh resets everything. Not shared between people | Database (Supabase) |
| QR scan | Student types the 6-character code. Classroom check is a tick box | Camera scan plus real geolocation, server-signed codes |
| AI tutor and moderation | Claude via the page (needs the viewer's permission) | Backend that calls Groq with the key hidden |
| Encryption | Browser AES-GCM demo only | Real end-to-end keys per user |
| Video lectures | Not built | Jitsi room per lecture |
| Notifications | In-page toast timer | Scheduled jobs and web push |
| Uploads | Text or link only | Object storage |
| Admin approval of uploads and posts | Not wired up | Approval queue |

Nothing here has been tested on a real phone yet. Please report bugs.

## Demo script (about 3 minutes)
1. **Student (`TY01`):** Attendance shows DBMS at 70% and "attend the next 4 lectures in a row". Open Tutor, study "Normalization", tap Quick quiz.
2. **Teacher (`sunita@edusync.demo`):** Attendance, Start attendance, show the rotating QR and code.
3. **Student:** enter the code to mark present, then try again to show "already marked".
4. **Teacher:** Close and save. Then post an assignment and show Submitted vs Pending.
5. **Chat:** send a normal message, then a rude one and show it blocked with a flag.
6. **Admin:** approve `TY04`, record a fee payment, send a broadcast, and reverse the flag in Reports.
7. **Close:** show the Tier A vs Tier B table above and the architecture diagram.

## Innovation points
Rotating QR plus classroom check, a syllabus-grounded tutor, moderation on the sender's device (compatible with end-to-end encryption), and a transparent, reversible behaviour score.
