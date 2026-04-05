# Hot/Cold V2 — Complete Rebuild Specification

## Product Vision
A social intelligence suite — 6 tools sharing one engine, accessed from a single app. Each tool is a "mode" with its own onboarding, UI, and output. All run in-browser, no backend.

---

## THE 6 MODES

### 1. 🔥❄️ Social Reader (existing Hot/Cold — upgraded)
**Tagline:** "Read the Room"
- Paste conversation OR live audio
- Directional relationship scoring (A→B ≠ B→A)
- Speaker profiles, relationship panels, network graph
- HUD mode for real-time alerts
- **NEW:** VADER sentiment engine, dialogue act classification, warmth×competence 2D scoring, emotional contagion tracking

### 2. 🎤 Pitch Coach
**Tagline:** "Sound Like a Leader"
- Record yourself speaking (30s-5min)
- Real-time feedback on: pitch variation, speech rate, filler words, energy dynamics, pause patterns
- Charisma score based on Rosenberg's acoustic correlates
- Visual pitch contour display (like a heart rate monitor)
- Before/after comparison: record, get feedback, practice, record again
- Tips panel: research-backed advice on what to improve

### 3. 💕 Relationship Pulse  
**Tagline:** "Check Your Temperature"
- Paste text conversations from a relationship (any messaging app)
- Track over multiple sessions (localStorage)
- Trend dashboard: warmth trajectory over days/weeks
- Detect: declining question frequency, response compression, asymmetry shifts, exit language creep
- Alerts: "Response times have doubled" / "Inclusive language dropped 40%"
- Two-person focused (not group)

### 4. 👔 Meeting Intelligence
**Tagline:** "Who Actually Has Power?"
- Paste Zoom/Teams transcript or meeting notes
- Map: speaking time distribution, interruption matrix, who yields to whom, agenda control
- Org chart reconstruction from communication patterns
- Detect: performative agreement, silent power plays, disengaged members
- Output: power map + engagement map + key moments

### 5. 🧠 Mood Mirror
**Tagline:** "How Do You Actually Sound?"
- Single-person voice analysis — no conversation partner needed
- Record 30 seconds of speaking
- Read: energy level, stress markers, emotional valence, vocal warmth, clarity
- Track over time (localStorage): daily mood chart
- Based on GeMAPS feature set: F0, jitter, shimmer, energy, spectral features
- Simple output: mood wheel + 3-5 key metrics + trend

### 6. 🎮 Social Sandbox
**Tagline:** "Train Your Social Radar"
- Pre-loaded famous conversations (movie scripts, debate excerpts, podcast moments)
- App analyzes the dynamics
- User guesses: "Who has power?", "Who's warming up?", "When does the dynamic shift?"
- Score against the algorithm's analysis
- Teaches the 20-30 fundamental interaction patterns
- Gamified: levels, accuracy scores, pattern badges

---

## SHARED ENGINE (upgraded)

### Sentiment Analysis
- **VADER** implementation (rule-based, handles social media text perfectly)
  - Handles: capitalization as intensifier ("GREAT" > "great")
  - Handles: punctuation ("good!!!" > "good")
  - Handles: degree modifiers ("very good" > "good")
  - Handles: negation ("not good" = negative)
  - Handles: emoji sentiment
  - Compound score: -1 to +1
- Keep AFINN as fallback/supplementary

### Dialogue Act Classification
Pattern-based classifier for each message:
- STATEMENT (default)
- QUESTION (contains ?)
- BACKCHANNEL ("mmhmm", "yeah", "right", "uh huh", "I see")
- AGREEMENT ("agree", "exactly", "totally", "yes", "for sure", "same")
- DISAGREEMENT ("disagree", "no way", "actually", "but", "however")
- APOLOGY ("sorry", "my bad", "apologize")
- COMPLIMENT (positive adjective + "you/your")
- EXIT_SIGNAL (existing detection)
- SELF_DISCLOSURE (first person + emotion/personal words)
- COMMAND/REQUEST (imperative mood patterns)

### Warmth × Competence 2D Scoring
Instead of single warmth score, each speaker gets:
- **Warmth axis** (0-1): friendliness, trustworthiness, connection-seeking
- **Competence axis** (0-1): assertiveness, clarity, topic control, solution-offering
- Displayed as 2D position: warm+competent, warm+submissive, cold+dominant, cold+passive

### Emotional Contagion Tracking
- Track how sentiment shifts propagate through the group
- After speaker A says something positive/negative, measure if subsequent speakers shift
- Visualize as ripple effect on the network graph
- Identify: "mood setters" (their emotions spread) vs "mood resistant" (unaffected by others)

### Audio Feature Extraction (GeMAPS-inspired)
For live audio modes (Pitch Coach, Mood Mirror, Social Reader live mode):
- F0 (fundamental frequency / pitch) via autocorrelation
- F0 statistics: mean, std, range, contour shape
- Jitter (pitch perturbation — stress indicator)
- Shimmer (amplitude perturbation — stress indicator)  
- RMS energy + dynamics
- Speech rate (syllable detection)
- Pause patterns (frequency, duration, placement)
- Spectral centroid + spectral flux
- Filler word detection ("um", "uh", "like", "you know")
- All at ~30Hz via Web Audio API AnalyserNode

---

## UI ARCHITECTURE

### Hub Screen (replaces old landing)
After the marketing landing page, user reaches the Hub:

```
┌─────────────────────────────────────┐
│  HOT/COLD                           │
│  Social Intelligence Suite           │
│                                     │
│  ┌──────┐  ┌──────┐  ┌──────┐     │
│  │🔥❄️  │  │ 🎤   │  │ 💕   │     │
│  │Social │  │Pitch │  │Pulse │     │
│  │Reader │  │Coach │  │      │     │
│  └──────┘  └──────┘  └──────┘     │
│                                     │
│  ┌──────┐  ┌──────┐  ┌──────┐     │
│  │ 👔   │  │ 🧠   │  │ 🎮   │     │
│  │Meeting│  │Mood  │  │Social│     │
│  │Intel  │  │Mirror│  │Sandbox│    │
│  └──────┘  └──────┘  └──────┘     │
│                                     │
└─────────────────────────────────────┘
```

Each mode card shows: icon, name, one-line description, and a subtle animation.

### Tour System
Each mode has its own tour (3-5 steps):
- Semi-transparent overlay with spotlight on the relevant UI element  
- Step indicator (dots)
- "Skip Tour" always visible
- Tour plays on first visit to each mode (tracked in localStorage)
- Concise: each step is one sentence + highlighted element

### Mode Navigation
- Hub is always accessible via home button (top-left)
- Each mode is a self-contained screen set
- Shared components: audio capture, text parser, sentiment engine
- localStorage: remembers last-used mode, tour completion, Relationship Pulse history, Mood Mirror history

---

## LANDING PAGE (full redesign)

### Structure:
1. **Hero** — "Social Intelligence. In Your Browser." + animated suite preview
2. **The Six Tools** — Grid of all 6 modes with descriptions and mini-demos
3. **How The Engine Works** — Pipeline visualization (text → analysis → visualization)
4. **"We Read Signals, Not Minds"** — Honesty section
5. **Privacy** — On-device, no cloud, open source
6. **Science** — Citations (expanded: Pentland, VADER, GeMAPS, Fiske warmth/competence, Gottman, Barsade contagion)
7. **CTA** — "Start Free →"

### Design:
- Keith Haring energy: bold outlines, vivid primaries, energetic figures
- Floating ambient orbs
- Scroll-reveal animations
- Phone mockup showing the Hub
- Each tool gets its own color accent within the Haring palette

---

## DESIGN SYSTEM

### Colors (per mode):
- Social Reader: Red/Blue gradient (hot/cold)
- Pitch Coach: Gold/Yellow (#FFD700)
- Relationship Pulse: Pink/Rose (#FF69B4)  
- Meeting Intelligence: Purple (#BF5FFF)
- Mood Mirror: Teal/Cyan (#00E5FF)
- Social Sandbox: Green (#00CC66)

### Shared:
- Background: #0a0a0a
- Bold 2-3px outlines on everything
- Inter font (400, 700, 800, 900)
- Glassmorphism panels
- Spring animations on interactions
- 12-16px rounded corners

---

## BUILD ORDER

### Phase 1: Core + Hub + Upgraded Social Reader
- VADER engine
- Dialogue act classifier
- Warmth × competence scoring
- Hub screen with all 6 mode cards
- Tour system framework
- Upgraded Social Reader (all existing features + new scoring)
- New landing page

### Phase 2: Pitch Coach + Mood Mirror (audio-focused)
- Shared audio capture + feature extraction module
- Pitch Coach UI + feedback + charisma scoring
- Mood Mirror UI + tracking + mood wheel

### Phase 3: Relationship Pulse + Meeting Intelligence (text-focused)
- Relationship Pulse UI + localStorage history + trend charts
- Meeting Intelligence UI + power mapping + interruption matrix

### Phase 4: Social Sandbox (gamified)
- Pre-loaded conversation library
- Quiz/scoring system
- Pattern badge system

### Phase 5: Polish + Test
- Tour content for all modes
- Cross-mode navigation
- Responsive testing (375px, 414px, 768px)
- Functional testing of every button/flow
- Performance testing (should be snappy)

---

## TECHNICAL CONSTRAINTS
- Single HTML file (or minimal file set)
- No external JS libraries (except Google Fonts)
- Mobile-first (iPhone portrait primary)
- All processing in-browser
- localStorage for persistence
- Total file size target: <200KB (excluding any CDN fonts)

---

## QUALITY BAR
This should feel like a $9.99/month app suite that people gladly pay for but get for free. Every micro-interaction matters. Every transition is smooth. Every piece of text is clear. The tour should make first-time users feel smart, not overwhelmed. Each mode should be useful in under 30 seconds from first tap.

When completely finished with each phase, run:
openclaw system event --text "Done: [phase description]" --mode now
