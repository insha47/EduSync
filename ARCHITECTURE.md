# EduSync — Student Education System: Architecture

> Working name: **EduSync**. Rename freely.
> Purpose: One platform for Students, Teachers and Admins covering attendance, communication, assignments, online lectures and an AI tutor.

---

## 0. READ THIS FIRST — Platform reality check

The project will be **built** in a Claude Project and **demoed/deployed** as a Claude published artifact (free). That environment has hard limits:

| Limit | Consequence |
|---|---|
| Published pages can only make network calls to approved hosts; **api.groq.com is blocked** | Groq cannot be called directly from a published artifact |
| No server runtime in an artifact | No real backend, no real E2E crypto key server, no WebRTC signalling server |
| Browser storage is per-viewer | Data is not shared between users unless a shared-state capability is available |

**Therefore the architecture has two tiers:**

- **Tier A – Competition Demo (runs inside Claude artifact):** All 3 roles, all screens, mock data held in app state. AI tutor uses `window.claude.complete` / Claude API *inside the artifact preview* (check what is available at publish time with the Artifact tool's `capabilities` action).
- **Tier B – Production Architecture (documented + partially built):** Real backend on free tiers, Groq called **server-side** with the key hidden. Show this in your submission as the "how it scales" design, and deploy the backend if time allows.

Judges reward a working demo first, a credible architecture second. Do Tier A completely before touching Tier B.

---

## 1. Users & Roles

| Role | Core goals |
|---|---|
| **Student** | Mark attendance fast, see attendance % and lectures needed, submit work, chat, attend online lectures, learn with AI tutor |
| **Teacher** | Know own timetable, get pre-lecture alerts, run attendance, post assignments/notes/PYQs, see who hasn't submitted, talk to students & teachers |
| **Admin** | Approve all data (users, uploads, timetable), broadcast messages, register & track fees, view moderation reports |

Auth flow: Login page → pick role → credentials → role-based redirect → role dashboard.

---

## 2. Feature Modules

### 2.1 Auth & Roles
- Login (email/roll no + password), role selector.
- Admin approves new Teacher/Student accounts (status: `pending → approved`).
- Route guard per role.

### 2.2 Attendance (QR, time + place)
- Teacher starts a session → system generates a **signed, short-lived QR token** (rotates every 15–30 s) containing `sessionId, classId, issuedAt, signature`.
- Student scans → server/app validates: token not expired, student enrolled in class, **geofence** (browser Geolocation within radius of classroom) and **time window**.
- Result: `present` recorded once per student per session (idempotent).
- Anti-proxy: rotating QR, geofence, one device per student per session.
- **Monthly report:** `attended / held` per subject → percentage; compute *"lectures you must attend to reach 75%"*:
  If `held` lectures happened and the student attended `a`, and they attend the next `n` in a row:
  ```
  (a + n) / (held + n) >= 0.75   =>   n >= 3*held - 4*a      (take max(0, ceil(...)))
  ```
  Example: held 20, attended 14 (70%) → n >= 60 - 56 = 4 more lectures in a row.
  Also show "bunk budget" (lectures you can still miss and stay ≥75% if you attend all others): `floor((a - 0.75*held) / 0.75)` when a/held ≥ 75%.

### 2.3 Communication Platform
- **Class group** (one per class/subject) – students + teacher.
- **Campus common room** – every student, online-presence list.
- **Teacher community** (WhatsApp-community style) – teachers + students channels.
- Messages: text, file, reply. Presence ("online now").
- **Safety layer** (see 2.9): moderation + behaviour flags.
- **Encryption:** Tier A – simulate with WebCrypto (AES-GCM, per-chat key) to demonstrate; Tier B – real E2E using per-user key pairs (ECDH) and server storing only ciphertext. Be honest in the submission: "E2E and server-side moderation conflict" → solve with **client-side moderation before encryption** (message is checked on sender's device, flagged events report only metadata).

### 2.4 Online Lectures (video)
- Tier A: embedded meeting-link launcher + lecture schedule + "join" button (opens Jitsi Meet room `https://meet.jit.si/<roomId>`; free, no backend).
- Tier B: Jitsi/LiveKit embed with room per lecture; join logged as optional attendance signal.

### 2.5 Submission Portal
- Teacher: create assignment (title, subject, due date, attachment), upload notes, upload PYQs.
- Student: view, download, submit file/text before deadline; late flag.
- Teacher: **submission tracker** – submitted / pending list, one-click reminder notification.
- Admin approval queue for uploaded content (per requirement "admin approves entire data").

### 2.6 AI Tutor (syllabus-based)
- Admin/Teacher uploads **syllabus** → structured as `Subject → Units → Topics`.
- Student picks a topic → tutor explains at chosen level, gives examples, short quiz.
- **Syllabus tracker:** per student, topic status `not started / learning / understood`; progress bar per subject.
- Grounding: system prompt includes the topic list so the tutor stays in-syllabus and refuses off-syllabus chatter politely.
- Model: Groq (Llama-class) via backend proxy (Tier B) / in-artifact model (Tier A).

### 2.7 Teacher Dashboard & Notifications
- "Who teaches what": table `Teacher × Subject × Class × Department × Day/Time` (solves the NEP confusion).
- 10-minute pre-lecture notification: *"Class TY-CS-A, Dept CS, 42 students, Room 204 at 10:30"*. Tier A: in-app toast driven by a timer against the timetable; Tier B: scheduled job + web push.

### 2.8 Admin Panel
- Approval queue (users, uploads, timetable changes).
- Broadcast announcements to all / department / class.
- **Fees:** fee structure, per-student paid/due, mark payment, overdue list, receipts (PDF optional).
- Moderation dashboard: flagged messages, flag scores.

### 2.9 Safety, Moderation & Behaviour Score
- **Moderation agent:** classifies each message → `ok | profanity | off_topic | harassment`. Pipeline: fast keyword/regex filter first (free, instant) → LLM check only when ambiguous (saves Groq quota).
- **Behaviour score:** red flag = −1, green flag = +1. Events: moderation hit (−1), teacher manual flag, on-time submissions streak (+1), helpful-answer in chat (+1).
  - Keep it **transparent and appealable**: every flag shows reason + who/what issued it; Admin can reverse. Don't auto-punish on AI alone.

### 2.10 Campus Feed (LinkedIn-style)
- Posts (text + image), like, comment, filter by department. Posts go through the same moderation. Optional: Admin approval for official posts.

---

## 3. System Architecture

### 3.1 Tier A — Artifact Demo (build this first)

```
┌──────────────────────── Claude Published Artifact (single HTML) ───────────────────────┐
│  UI Layer (React via UMD / vanilla)                                                     │
│   Login → RoleRouter → [StudentApp | TeacherApp | AdminApp]                             │
│                                                                                         │
│  State Layer: one store (in-memory) seeded with MOCK_DATA                               │
│   users, classes, timetable, sessions, attendance, assignments, submissions,            │
│   messages, posts, fees, syllabus, flags                                                │
│                                                                                         │
│  Services (pure JS modules in the same file)                                            │
│   attendanceService  (QR token gen/verify, % math)                                      │
│   moderationService  (regex + AI check)                                                 │
│   aiTutorService     (prompt builder + model call)                                      │
│   notificationService(timetable timer)                                                  │
│   cryptoService      (WebCrypto AES-GCM demo)                                           │
└─────────────────────────────────────────────────────────────────────────────────────────┘
        │ optional: published-page runtime capabilities (shared db, ask-Claude) if enabled
```

### 3.2 Tier B — Production

```
 Browser (React SPA)  ──HTTPS──▶  API (Node/Express or FastAPI)  ──▶  PostgreSQL (Supabase free)
        │                               │                    └────▶  Object storage (Supabase Storage)
        │ WebSocket (Supabase Realtime) │
        │                               └──▶ Groq API (key stored ONLY server-side)
        └──▶ Jitsi Meet (video)
```

Free-tier suggestions: **Supabase** (DB + auth + realtime + storage), **Render/Vercel** (API/static hosting), **Groq** (LLM).

---

## 4. Data Model (core tables)

```
users(id, name, email, role[student|teacher|admin], dept, status[pending|approved], roll_no, behaviour_score)
classes(id, name, dept, year, division)
enrollments(student_id, class_id)
subjects(id, name, dept, syllabus_id)
timetable(id, class_id, subject_id, teacher_id, day, start, end, room, lat, lng)
sessions(id, timetable_id, date, qr_secret, status[open|closed])
attendance(id, session_id, student_id, marked_at, lat, lng, method)
assignments(id, class_id, subject_id, teacher_id, title, desc, due_at, file_url, approved)
submissions(id, assignment_id, student_id, file_url, submitted_at, late)
resources(id, subject_id, type[note|pyq], file_url, approved)
channels(id, type[class|common|teacher_community], class_id)
messages(id, channel_id, sender_id, body_enc, created_at, mod_status)
posts(id, author_id, body, image_url, created_at, approved)
comments(id, post_id, author_id, body)
fees(id, student_id, total, paid, due_date, status)
fee_payments(id, fee_id, amount, paid_on, mode)
syllabus(id, subject_id)  units(id, syllabus_id, name)  topics(id, unit_id, name, order)
topic_progress(student_id, topic_id, status)
flags(id, user_id, delta[-1|+1], reason, source[ai|teacher|system], created_at, reversed)
notifications(id, user_id, type, body, read, created_at)
```

---

## 5. Key Flows

**QR Attendance**
1. Teacher taps *Start Attendance* → session opens, QR shown, rotates every 20 s.
2. Student taps *Scan* → camera reads QR (BarcodeDetector API / jsQR library) → app checks token + geofence + enrolment.
3. Success animation (< 3 s total). Duplicate scan = "already marked".

**Assignment loop**
Teacher posts → Admin approves (if required) → Student notified → Student submits → Teacher sees pending/submitted → Teacher reminds pending.

**AI Tutor**
Select subject → unit → topic → Tutor explains → "Quick quiz" → mark topic understood → progress updates.

**Moderation**
Message typed → regex filter → (ambiguous) AI classify → ok: send / flagged: block + show reason + log flag −1 (reviewable).

---

## 6. Non-Functional Requirements
- Mobile-first (students use phones); works at 360 px width.
- Attendance action ≤ 3 taps.
- Accessibility: contrast AA, labels on inputs.
- Privacy: minimum data; no message plaintext on server in Tier B.
- Rate limiting on AI calls; cache tutor answers per topic.

---

## 7. Build Order (MVP-first)

1. Mock data + login + role routing
2. Student: attendance % + "lectures needed" calculator
3. Teacher: QR session + student scan simulation
4. Submission portal
5. AI tutor + syllabus tracker
6. Chat + moderation + flags
7. Admin panel (approvals, fees)
8. Feed, video launcher, notifications
9. Polish, demo script, README, architecture diagram
10. (Stretch) Tier B backend with Groq

---

## 8. Competition Deliverables Checklist
- [ ] Live published demo link
- [ ] 2–3 min demo video (login as each role)
- [ ] This architecture doc + diagram
- [ ] README with setup + limitations (be honest about Tier A vs B)
- [ ] Slide on innovation: rotating QR + geofence, syllabus-grounded tutor, client-side moderation compatible with E2E, transparent behaviour score
