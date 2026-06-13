# CLAUDE.md — Flow App Context

## What This Is
Flow is a single-file React vertical scrolling timeline calendar. One HTML file (~650KB), pre-compiled from JSX to plain JavaScript. No build tools, no dependencies beyond React 18 CDN. Deployed at flowtimeline.netlify.app via GitHub (ricardol63/flow-app) → Netlify auto-deploy.

## Architecture Rules
- **Single file only.** Everything lives in index.html. Never split into multiple files.
- **Pre-compiled.** All code uses `React.createElement`, not JSX. If you edit the code, keep it in `React.createElement` format.
- **No Babel.** Babel standalone was removed for performance. Do not re-add it.
- **Firebase is optional.** Wrapped in try/catch with `_fbReady` guard. The app works entirely on localStorage if Firebase CDN fails. Never make Firebase required.
- **Mobile detection:** Global `_isMob = window.innerWidth < 600`. Used throughout for conditional behavior.
- **Drag-and-drop disabled on mobile.** All `draggable` attributes are `!_isMob`. Desktop mouse drag works; mobile uses long-press context menu instead.

## File Structure on GitHub
- `index.html` — the app (this file)
- `manifest.json` — PWA manifest
- `sw.js` — service worker
- `icon-192.png`, `icon-512.png` — placeholder icons (to be replaced)

## Core Concept
A vertical scrolling timeline where every task, appointment, and deadline lives at a point in time. Five zoom levels: Day, Week, Month, Quarter, Year. Tasks not done get pushed forward — no backlog, no overdue, no guilt.

Intro text (centered, bold, one line per sentence):
- "Put everything on your timeline."
- "If it's not done, push it forward."
- "That's it."

## Theme System
Three UI modes: **Light**, **Dark**, **Bold**. Five accent colors: Blue, Amber, Sage, Rose, Electric.
- **Bold** uses clean near-white backgrounds (#fcfcfd) with deep purple accent (#4020e0). It should feel crisp and high-contrast, NOT lavender or hazy.
- **Color Theme was removed** (Pastel/Vivid/Earth/Mono selector). The app always uses the muted pastel palette for task dots. Do not re-add Color Theme.
- Task dot colors are deliberately muted/desaturated — dusty, elegant tones, not bright neon. Think watercolor paper, not highlighter pens.

## Menu Structure (exact order)
**Top (mobile only):** ☀ Plan and ☽ Review side by side (matching accent color tint — both use P.ac, NOT different colors). On desktop, Plan and Review are in the header only — not in the menu.
**Main section** (always visible, three items only):
- Inbox (with badge)
- Blueprint — "Design your week."
- Goals
**"More" toggle** (collapsed by default):
- Reset — "When you're stuck, start here."
- Milestones — "Mark a date that matters."
- How Flow Works — "The philosophy in 60 seconds."
- Shortcuts — desktop only, hidden on mobile
**"Rituals" toggle** (collapsed by default):
- Savor — "Train your mind to see the good."
- Highlights — "When you want a lift."
- Vision — "Reconnect with your goals."
**"Appearance" toggle** (collapsed by default): Mode + Accent only
**Sign in / account** — always at the very bottom, only shows when Firebase loads
**Desktop header:** ☀ and ☽ icons between hamburger and search for quick Plan/Review access
**First visit:** How Flow Works auto-opens after splash screen fades
**Contextual surfacing:** Review step 1 links to Highlights, Review step 4 links to Savor

## Module Names — FINAL (do not change)
These names were carefully chosen. Do not rename without explicit instruction:
- ☀ Plan (morning bookend) — "Set your day"
- ☽ Review (evening bookend) — "Close your day"
- Reset (was "Refocus" / "Clarity Reset") — ACT-based, 3 steps
- Savor (gratitude journaling)
- Highlights (was "Jar of Awesome" / "Cookie Jar" / "My Cookie Jar")
- Vision (was "Envision Goals" / "Envision") — the module name
- Revisit — the button action inside Goals to re-read your vision
- Begin — the button to start writing a vision (shows "Edit" after vision exists)
- Blueprint (was "Ideal Week")
- Milestones (was "Add Milestone")
- Shortcuts (was "Keyboard Shortcuts")

## Naming Principles
- One word per menu item wherever possible
- No grandiose language — keep everything down to earth
- "Grateful" is a cliché word to avoid. The gratitude prompt is "When did today feel good?" not "Today I'm grateful for..."

## ☀ Plan (Morning Bookend) — 4 Steps
1. Scan timeline + inbox items with tap-to-add-to-today for each inbox item
2. Brain dump textarea — add to Inbox or Today
3. Choose your ONE thing (Dr. Gail Matthews, Dominican University)
4. Set your intention (prefrontal cortex priming)

## ☽ Review (Evening Bookend) — 4 Steps
1. Celebrate progress — completed tasks + write wins (Teresa Amabile, Harvard)
2. Push forward — unfinished tasks, "long-press to move" on mobile (Zeigarnik Effect)
3. Glance at tomorrow (Cal Newport, Deep Work shutdown ritual)
4. Gratitude — prompt: "When did today feel good?" (Emmons & McCullough, 2003)

## Reset (ACT-Based) — 3 Steps
1. Name the resistance — "What are you avoiding?" (affect labeling)
2. Reconnect with what matters — "Why does this matter?" (ACT values clarification)
3. One small step — "What's the smallest next action?" (Gollwitzer implementation intentions, 2-3x follow-through)
Final step adds the action to the timeline as a task.

## Vision System (inside Goals)
Each goal has a 4-part vision built on research:
1. **The Outcome** — vivid end state (Locke & Latham Goal Setting Theory)
2. **The Why** — deep personal purpose (self-determination theory / Tony Robbins)
3. **The Obstacle** — inner pattern most likely to derail you (Oettingen's mental contrasting — pure positive visualization alone decreases performance)
4. **The Move** — if-then plan: "When [obstacle], I will [action]" (Gollwitzer implementation intentions)

Buttons in Goals workspace:
- **Begin** — opens 4-step guided writer (before vision exists)
- **Edit** — reopens writer with existing content (after vision exists)
- **Revisit** — inline preview of all 4 parts within the goal workspace
- **Revisit** — in More menu, opens full-screen immersive read-through of all goals

The Vision module tagline is "Reconnect with your goals." — NOT "Revisit all your goal visions" (too grandiose).

## Highlights
Tooltip: "Collect what's gone right — wins, breakthroughs, moments that moved you. Anytime you want a lift, they're your fuel."
Placeholder: "A win, breakthrough, or something that moved you..."
Empty state: "No highlights yet. Add wins, breakthroughs, or anything that moved you."
The "Review" button opens focused revisiting mode.

## How Flow Works — 7 Principles
First principle: "Put every task, appointment, and deadline on the timeline. Zoom out to Month view and your whole month is visible at a glance. What's coming up stays on your radar naturally — no effort, no surprises."

## Mobile-Specific Behavior
- All text is conditional: "long-press" instead of "right-click", "tap" instead of "hover/click"
- Drag-and-drop: HTML5 drag API disabled; Day view uses touch-based drag (300ms hold-to-drag with ghost element)
- "Drop to move to Today" banner hidden
- Shortcuts hidden from menu
- Header is two rows: Row 1 = ☰ + month + theme toggle. Row 2 = Today + D/W/M/Q/Y
- All modals are full screen (no rounded corners, no gaps)
- Goals panel stacks vertically (goal list on top, workspace below)
- Menu fills entire screen with × close button
- Search button hidden (keyboard-driven)
- Reduced day rendering: 60-180 days vs 730 on desktop (critical for performance)
- Inputs forced to 16px to prevent auto-zoom
- Buttons have 36px minimum height
- Task pills: 16px font, more padding, bigger checkboxes
- Day row heights increased (Month: 48px, Week: 88px, Day: 180px)
- Dots: 12px with 6px gap (desktop: 10px with 5px gap)

## Splash Screen
First visit only (localStorage flag "flow-visited"). FLOW text fades in/out over ~2.5 seconds. Tap/click anywhere to skip. Returning users skip straight to the app. Do not show splash on every visit.

## Onboarding
- No auto-opening modals on first visit. Users land directly on the timeline.
- 6 sample tasks across today + tomorrow teach core mechanics hands-on.
- Mobile starts in Day view (zoomIdx 4), desktop starts in Month view (zoomIdx 2).
- How Flow Works is in the menu (More section) for curious users, not forced.
- Blueprint intro banner shows on first open of Blueprint panel (dismissible, localStorage flag).

## Dot Hover (Desktop Only)
Hovering over a dot in Month view shows a styled tooltip with the task name, color, and time. This is separate from the day row HoverPreview which shows all tasks. The dot tooltip uses `onMouseEnter`/`onMouseLeave` with React state — NOT a native `title` attribute. Mobile dots have no hover behavior.

## Firebase
- Config: apiKey "AIzaSyCwS-HH8r2cdf2CZb7a6TXjlY43x9T8F9Q", projectId "flow-app-3ad9c"
- Google Sign-In, Firestore cloud sync, 1s debounce auto-save
- Firestore location: nam5 (United States)
- Authorized domain: flowtimeline.netlify.app
- Security rules: only authenticated users read/write their own data

## Design Preferences (Richard's)
- Concise responses, no grandiose language
- Muted, elegant color palette — not bright or loud
- Down-to-earth tone in all copy
- Research citations included but not in user-facing tooltips (keep tooltips simple)
- One word names for menu items
- Visual clarity over decoration
- Performance over features — don't add anything that slows the app

## Things That Were Tried and Removed
- **Cinema Theme** — white timeline on black, couldn't get the effect right
- **Color Theme selector** (Pastel/Vivid/Earth/Mono) — too subtle, cluttered the menu
- **Babel standalone** — removed for performance (was causing 2-4 second load delay)
- **Native title attributes on dots** — caused ugly browser tooltips, replaced with styled React tooltips
- **Tony Robbins-style Refocus** (brain dump → weigh → choose → commit) — replaced with ACT-based Reset
- **"Today I'm grateful for..."** — replaced with "When did today feel good?"
- **"Jar of Awesome"** — renamed to Highlights
- **Envision / Envision Goals** — renamed to Vision (module) + Revisit (action)
- **"Write Vision"** — renamed to "Begin"

## Known Issues / Pending Work
- Blueprint layout needs fixing on mobile (schedule view too thin on right)
- ~~Touch-based drag-and-drop for mobile Day view~~ — DONE: 300ms hold-to-drag with ghost, reorder all-day, move timed blocks, schedule/unschedule between sections
- Month view long-press preview on day rows (not yet built)
- ~~Up/down arrows for reordering tasks in Day view~~ — NOT NEEDED: touch drag-and-drop handles reordering
- Performance can still be improved on older phones
- Polished app icon needed (current icons are placeholders)
- Google Calendar integration would require server-side code
