# Ensemble Workshop Presentation — Project Context

## What this is

An interactive workshop presentation for Chamber Singers (Vox Camerata) on ensemble artistic leadership and decision-making. Built with Reveal.js, hosted on GitHub Pages, with live Firebase voting for two interactive activities.

**Live URL**: `https://thevoxcamerata-sg.github.io/presentations/`

---

## Files

| File | Purpose |
|------|---------|
| `ensemble_workshop_presentation.html` | Main Reveal.js presentation (facilitator screen) |
| `ensemble_vote.html` | Participant voting page for slide 11 (Sort the Decisions) |
| `ensemble_icebreaker.html` | Participant page for the Two Truths & a Lie icebreaker |
| `ensemble_values.html` | Participant page for Activity 1 (Values Mapping) |
| `ensemble_participant_worksheet.html` | Printable/digital worksheet |

---

## Firebase

**Project**: chamber-workshop-1
**Region**: Singapore (asia-southeast1)
**Database URL**: `https://chamber-workshop-1-default-rtdb.asia-southeast1.firebasedatabase.app`
**Rules**: test mode (open read/write)

```javascript
const firebaseConfig = {
    apiKey:            "AIzaSyDDln74E24sGerHQCgpL7kSsthpF-H1u8U",
    authDomain:        "chamber-workshop-1.firebaseapp.com",
    databaseURL:       "https://chamber-workshop-1-default-rtdb.asia-southeast1.firebasedatabase.app",
    projectId:         "chamber-workshop-1",
    storageBucket:     "chamber-workshop-1.firebasestorage.app",
    messagingSenderId: "423187424700",
    appId:             "1:423187424700:web:842d4922ce6c4fd71777b9"
};
```

---

## Firebase data structure

### Values Mapping (Activity 1)
```
values/{session}/{deviceId}/
  v1: string    — first value (required)
  v2: string    — second value (optional)
  v3: string    — third value (optional)
```

### Sort the Decisions (slide 11)
```
votes/{session}/{deviceId}/{decisionKey} = "collective"|"consultative"|"delegated"|"director_led"
```

### Two Truths & a Lie (icebreaker slide)
```
icebreaker/{session}/
  entries/{deviceId}/
    name: string
    a: string          — Statement A
    b: string          — Statement B
    c: string          — Statement C
    lie: "a"|"b"|"c"   — which is the lie (hidden until reveal)
    photo: string      — base64 JPEG thumbnail, 120x120px (optional)
  state/
    active: deviceId | null
    revealed: boolean
  votes/{activeDeviceId}/{voterDeviceId}: "a"|"b"|"c"
```

---

## Session IDs

Both the presentation and participant pages use `?session=` URL param to namespace Firebase data. Defaults to today's date (`YYYY-MM-DD`) if omitted. Use matching session values on both URLs.

- Testing: `?session=test1`, `?session=test2`, etc.
- Live workshop: omit (uses date) or `?session=workshop`

---

## Presentation slide order

1. Title
2. Icebreaker Intro
3. Icebreaker Think *(prepare statements)*
4. **Icebreaker Share** ← interactive Two Truths & a Lie slide
5. Icebreaker Transition
6. Opening Script
7. Objectives
8. Timeline
9. Phase 1 Introduction
10. Activity 1: Values Mapping *(instructions)*
11. **Activity 1: Live Values** ← interactive values collection
12. Activity 2: Decision Types
13. **Examples to Sort** ← static examples
14. **Live Results** ← interactive vote results (Sort the Decisions)
15. Activity 3: Scenario Testing
16. Activity 4: Consensus Check
17. Closing
18. Closing Script
19. Participant Worksheet

---

## Interactive slides — how they work

### Icebreaker Share (slide 4)
- Participants go to `ensemble_icebreaker.html`
- They enter name, 3 statements, mark which is the lie, optionally take a selfie
- Their name appears as a pill in the names bar on the presentation slide as they submit
- Facilitator clicks a name → activates that person's question
- All participants' phones auto-switch to voting mode (A/B/C buttons)
- Live vote bars update on presentation
- Facilitator clicks **Reveal** → lie is highlighted, others fade
- Click next name to continue
- **Reset session** button: bottom-left corner of the slide, two-click safety (first click arms it red, second click within 4 seconds wipes all Firebase icebreaker data)

### Activity 1: Live Values (slide 11)

- Participants go to `ensemble_values.html` and submit up to 3 values
- Values appear live as chips on the presentation slide
- Repeated values grow larger and show a count badge — instant consensus signal
- Participants can edit their submission via "Edit my values" link
- **Reset session** button: bottom-left corner, two-click safety, wipes all submitted values

### Live Results (slide 14)
- Participants go to `ensemble_vote.html` during the "Examples to Sort" activity
- They assign each of 10 decisions to one of 4 categories (Collective, Consultative, Delegated, Director-led)
- Results aggregate in real time as stacked bar charts on the Live Results slide

---

## Device identity

Each browser generates a persistent device ID stored in `localStorage` key `vc_pid`. Format: `p` + timestamp base36 + 4 random chars. This persists across page reloads on the same browser.

---

## Reveal.js config

```javascript
Reveal.initialize({
    hash: true,
    controls: true,
    progress: true,
    center: true,
    transition: 'fade',
    width: 1000,
    height: 630,
    margin: 0.04
});
```

Base font size: 44px. All slide content uses `em` units relative to that.

---

## GitHub

**Repo**: `thevoxcamerata-sg/presentations`
**Branch**: master
**Deploy**: GitHub Pages (auto-deploys on push, ~2 min delay)

```
git add <files>
git commit -m "message"
git push origin master
```

---

## Known issues / gotchas

- **Curly quotes in JS**: Claude's text generation can substitute straight quotes `'` with curly quotes `'` `'` (U+2018/U+2019). These are not valid JS string delimiters and cause a SyntaxError that crashes the entire script. Always run `node -e "..."` to scan for non-ASCII in the `<script>` section after edits. Safe inside template literals (backtick strings) — only dangerous as string delimiters.
- **Firebase region**: databaseURL must use `asia-southeast1.firebasedatabase.app` format, not the default US `firebaseio.com` format.
- **GitHub Pages cache**: after pushing, browsers need `Ctrl+Shift+R` to force-reload the new version.
