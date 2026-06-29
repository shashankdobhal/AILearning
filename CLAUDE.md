# AI Schools — Project Context

## What This Is
An educational website styled like W3Schools, built as a single `index.html` file. It teaches AI, ML, Deep Learning, and Git through interactive lessons, quizzes, and code examples. Live at: https://shashankdobhal.github.io/AILearning

## Tech Stack
- **Frontend:** Vanilla HTML/CSS/JS — single file (`index.html`), no build tools, no frameworks
- **Database:** Firebase Firestore — stores user signups
- **Auth:** Firebase Authentication (Email/Password) — protects the admin dashboard
- **Hosting:** GitHub Pages (auto-deploys from `main` branch)
- **Local dev:** `python3 -m http.server 8080` → http://localhost:8080

## Firebase Project
- **Project ID:** `ai-schools-e3a0c`
- **Auth domain:** `ai-schools-e3a0c.firebaseapp.com`
- **Firestore collection:** `signups`
- **Admin login:** configured in Firebase Console under Authentication > Users
- **Firestore rules:** public `create`, authenticated-only `read`/`delete`

## GitHub
- **Repo:** https://github.com/shashankdobhal/AILearning.git
- **Branch:** `main`
- **Push:** `git push origin main` (SSH key linked to `shashank821` GitHub account — use HTTPS with PAT or push directly as it has worked)

## Project Structure
```
AI Learning/
├── index.html          # Entire app — all HTML, CSS, JS in one file
├── CLAUDE.md           # This file
└── .claude/
    └── launch.json     # Preview server config (port 8080)
```

## Courses (all in index.html as JS arrays)
| Variable | Course | Lessons |
|---|---|---|
| `lessons` | AI Basics | 10 |
| `mlLessons` | Machine Learning | 10 |
| `dlLessons` | Deep Learning | 10 |
| `gitLessons` | Git Basics | 10 |

Course IDs for `showCourse()`: `'ai'`, `'ml'`, `'dl'`, `'git'`

## Key JS Architecture
- All lesson content is stored as HTML strings in backtick template literals inside JS arrays
- **CRITICAL:** Never use backtick template literals inside lesson content strings — it breaks the entire JS. Use string concatenation instead (e.g. `"text" + variable + "text"`)
- `courseConfig` object maps course IDs to lesson arrays and titles
- `showCourse(id)` — switches to a course and rebuilds the sidebar
- `showLesson(idx)` — renders a lesson from the active course
- `showHome()` — returns to homepage
- `scrollToCourses()` — goes home and scrolls to course cards

## Firebase Module (top of `<script type="module">`)
Exports these to `window.*` for use in regular JS:
- `window.saveSignupToFirebase(data)` — saves a signup doc
- `window.loadSignupsFromFirebase()` — returns all signup docs
- `window.deleteSignupFromFirebase(id)` — deletes one doc
- `window.clearAllSignupsFromFirebase()` — deletes all docs
- `window.firebaseLogin(email, password)` — Firebase Auth sign in
- `window.firebaseLogout()` — signs out
- `window.getAdminUser()` — returns current auth user or null

## Features Built
- 4 courses with 10 lessons each (sidebar nav, progress bar, prev/next buttons)
- Interactive "Try It" code editors with simulated output (`tryitOutputs` map)
- Multiple-choice quizzes with instant feedback
- SVG diagrams in lessons (neural networks, RNN unrolled, SVM margin, etc.)
- Sticky topbar + sticky progress bar
- **Sign Up modal** — collects: first name, last name, email, phone, role, course interest
- **Admin dashboard** — accessed via `Shift+A`, requires Firebase Auth login
  - Shows all signups from Firestore (newest first)
  - Export CSV button
  - Clear All button
  - Sign Out button
  - Shows logged-in admin email
- Admin indicator badge in topbar (visible when logged in)

## Signup Form Fields Collected
- `fname`, `lname`, `email`, `phone`, `role`, `course`, `createdAt` (server timestamp)

## Known Issues / Watch Out For
- JS syntax error risk: backtick template literals inside lesson content strings will silently break ALL JavaScript on the page. Always verify with: `awk '/<script>/{found=1;next} /<\/script>/{found=0} found{print}' index.html > /tmp/test.js && node --check /tmp/test.js`
- Firebase SDK version in use: `11.9.0`
- The `<script type="module">` block at the top handles Firebase; the main `<script>` block at the bottom handles everything else
- `tryitOutputs` is a flat map with keys like `0`, `3`, `5`, `ml0`, `ml2`, `ml3`, `ml4`, `ml7`, `dl1`, `dl6`, `git4` — add new entries here when adding Try It editors

## How to Add a New Course
1. Add a new JS array (e.g. `const pythonLessons = [...]`) before `let currentLesson = 0;`
2. Add entry to `courseConfig`: `python: { lessons: pythonLessons, title: 'Python Basics' }`
3. Add a course card in the HTML `.course-cards` section: `<a class="course-card" onclick="showCourse('python')">`
4. Add any tryit outputs to the `tryitOutputs` map

## Style Guide
- Primary green: `#04AA6D`
- Dark background: `#282A35`
- Info boxes: green left border (`#04AA6D`)
- Warning boxes: yellow left border (`#f39c12`)
- Code boxes: dark `#282A35` background, monospace
- No external CSS frameworks — everything is inline `<style>`

## Git Workflow
```bash
# Check status
git status

# Commit
git add index.html
git commit -m "Description of change"

# Push to GitHub (also deploys to GitHub Pages)
git push origin main

# Verify JS before committing
awk '/<script>/{found=1;next} /<\/script>/{found=0} found{print}' index.html > /tmp/test.js && node --check /tmp/test.js
```
