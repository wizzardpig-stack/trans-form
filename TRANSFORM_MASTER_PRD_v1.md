# trans.form
## Master Product Requirements Document
**Version:** 1.0  
**Status:** Source of truth for first serious build  
**Product type:** Windows-first local media transformer with a web-native interface  
**Primary wedge:** A beautiful, trustworthy YouTube-to-audio experience without ads, fake download buttons, redirects, or sketchy UX.

---

## 1. Product thesis

Most YouTube-to-MP3 tools feel disposable, deceptive, or ugly. trans.form should feel like a premium media instrument.

The product should let a user paste a supported media URL or import a local media file, immediately understand what was detected, preview it, select the exact section they want, transform it locally, and export a clean audio file.

The experience must feel unusually polished for a converter while remaining fast, simple, private, and inexpensive to operate.

**Core flow:**

`IMPORT → PREVIEW → SELECT → TRANSFORM → EXPORT`

---

## 2. Non-negotiable product principles

1. **No ad-tech UX.** No popups, redirects, fake buttons, countdowns, deceptive download controls, or clutter.
2. **Local-first processing.** Media transformation should happen on the user's device whenever technically possible.
3. **No account required.**
4. **No cloud media storage by default.**
5. **Near-zero ongoing infrastructure cost.**
6. **Fast path first.** A user who only wants an MP3 should be able to paste/import, confirm, and export with minimal friction.
7. **Power is optional.** Trimming, splitting, waveform selection, metadata, and analysis appear without making the basic flow feel complicated.
8. **Music intelligence only when relevant.** KEY / SCALE / BPM must appear only when the source is confidently detected as music.
9. **No generic AI/SaaS visual language.** The interface should feel like a purpose-built media instrument, not a dashboard template.
10. **Production evidence is part of the build.** QA, tests, known issues, supported environments, and release-readiness evidence must live in the repo.

---

## 3. Important architecture decision

A pure browser-only PWA cannot reliably implement pasted YouTube URL extraction because direct media retrieval is constrained by platform delivery mechanisms, browser CORS, signature handling, and changing upstream behavior.

Therefore V1 should be a **Windows-first desktop application with a web-native UI**, using local native tooling for retrieval and transcoding.

Recommended architecture:

- **Shell:** Tauri
- **Frontend:** React + TypeScript + Vite
- **Media retrieval:** local `yt-dlp`
- **Media transform / extraction:** local FFmpeg
- **Waveform / playback:** Web Audio API plus generated waveform data
- **Persistent local settings/history:** local app storage / SQLite only if justified
- **No remote backend required for core V1**

The app should only process media the user is authorized to download or transform, and should not attempt to bypass DRM or protected paid content.

Architecture is allowed to change only if the replacement preserves:
- real pasted-URL support,
- local processing,
- near-zero operating cost,
- comparable reliability,
- and a simpler maintenance story.

---

## 4. V1 user journeys

### 4.1 Paste a media URL
1. Launch trans.form.
2. Paste a supported URL into the primary import field.
3. App validates the URL.
4. App retrieves lightweight metadata first:
   - title
   - thumbnail
   - duration
   - source
   - creator/channel when available
5. UI transitions into Preview.
6. User can immediately choose an output format or optionally refine the selection.
7. Media is retrieved and transformed locally.
8. Export is written to the user's chosen location.

### 4.2 Import a local file
Support drag/drop and file picker for common audio/video formats.

The imported source enters the same Preview → Select → Transform → Export flow.

### 4.3 Quick MP3 path
A user who does not want editing should be able to:
- paste/import,
- select MP3,
- press Transform,
- export.

Do not force waveform editing, metadata editing, or analysis.

### 4.4 Trim / select
The Preview screen exposes a real waveform with:
- draggable start handle
- draggable end handle
- click/scrub playback
- current time
- selection duration
- reset selection
- precise timestamp entry as secondary control

### 4.5 Split
Allow multiple selected ranges to be exported as separate files without making the default interface feel like an audio workstation.

### 4.6 History
Local-only History remembers:
- title
- thumbnail when available
- source
- output format
- selected range(s)
- export filename
- KEY / SCALE / BPM when applicable
- date transformed

History must not retain full source media unless the user explicitly chooses to.

---

## 5. Output formats

V1 target formats:

- MP3
- WAV
- M4A
- FLAC
- OGG

The UI should expose only format-relevant quality settings.

Examples:
- MP3 bitrate
- lossless indication for WAV / FLAC
- codec / quality only when it meaningfully changes the result

Do not overwhelm the user with FFmpeg terminology.

---

## 6. Preview and waveform

Preview is a major product surface, not a tiny utility panel.

Requirements:
- prominent source artwork / thumbnail
- source title
- playback
- duration
- waveform
- active selection visually embedded in waveform
- start/end times
- output format
- transformed filename preview

The waveform should feel physical and responsive, not like a generic `<input type="range">`.

Selection interactions must work with mouse and touch.

---

## 7. Music intelligence

Run music analysis only when the app confidently identifies the source as a song/music track.

When music is detected, show:
- BPM
- KEY
- SCALE / mode when confidence allows
- analysis confidence
- `÷2` and `×2` BPM correction controls

The presentation can take inspiration from professional music-analysis software, but it must not mimic Antares or another product's trade dress.

### Non-music behavior

For podcasts, lectures, interviews, spoken videos, and other non-musical sources:
- do **not** show KEY or BPM placeholders,
- do **not** show meaningless `—` music cards.

If a transcript is already available from the source or generated by an explicitly supported local capability, it may be shown.

Otherwise show nothing in that region.

---

## 8. Metadata and filenames

Before export, allow lightweight metadata editing:

- filename
- title
- artist
- album
- track number where appropriate
- artwork where supported by output format

Provide sensible filename defaults based on retrieved metadata.

Filename sanitation must be cross-platform safe.

---

## 9. Visual direction

trans.form should look like a **premium transformation instrument**.

### Core visual metaphor

Media enters as one form, becomes a living signal, and resolves into another form.

The interface should visually communicate transformation rather than "download website."

### Processing moment

The processing state is a signature scene:
- 3D particles
- sound-wave structures
- particles reorganizing / resolving as transformation progresses
- restrained premium-tech accents
- meaningful progress, not fake theatrical delay

The animation must degrade gracefully on weak hardware and must never delay the actual export merely for presentation.

### Composition

Prefer:
- one continuous working surface
- integrated waveform and source media
- deliberate asymmetry when useful
- tight hierarchy
- dark neutral base with luminous signal behavior
- high legibility

Avoid:
- generic dashboard cards
- excessive pills
- huge rounded containers
- neon cyberpunk overload
- glassmorphism everywhere
- decorative status badges
- fake console text
- gratuitous tracking / wide letter spacing
- thick glowing accent stripes on cards
- visual effects that make the converter slower or harder to read

### Logo direction

Prior visual preference:
- explore the previously favored **B direction first**
- use **D direction** as the alternate
- logo should work as app icon and compact header mark

---

## 10. Interaction rules

- Paste should work immediately when focus is in the import field.
- Drag/drop must provide unmistakable feedback.
- Keyboard users must be able to complete the primary workflow.
- Escape cancels dismissible transient states where safe.
- Never hide destructive or cancel behavior.
- Transformation progress must reflect real work.
- Cancelling a transformation must stop the underlying process cleanly.
- App must recover gracefully from invalid URLs, removed videos, network failure, unsupported formats, FFmpeg failure, and interrupted downloads.
- Errors should tell the user what failed and what they can do next.

---

## 11. Performance requirements

The app should feel instant before media processing begins.

Targets for V1:
- launch to interactive UI: fast on ordinary Windows hardware
- no long main-thread freezes
- waveform generation performed without visibly locking the UI
- visual processing animation adapts to device capability
- long media must not require loading the entire decoded source into frontend memory at once
- subprocess output must be streamed rather than buffered indefinitely
- temporary files cleaned safely
- cancellation must release subprocesses and file handles
- history should remain responsive with hundreds of entries

---

## 12. Privacy and security

- No account required.
- No analytics that contain media URLs or filenames by default.
- Do not upload source media for core functionality.
- Never execute arbitrary user-provided command arguments.
- Strictly construct `yt-dlp` / FFmpeg arguments.
- Sanitize filenames and output paths.
- Protect against command injection.
- Validate allowed protocols and URL input.
- Clean temporary files after success, cancellation, or recoverable failure.
- Do not expose internal shell output as raw UX.
- Document all bundled/downloaded binaries and licenses.

---

## 13. URL support

V1 should architect URL retrieval behind an adapter/interface rather than hard-wiring the UI to one provider.

Initial wedge:
- YouTube URLs where retrieval is technically available and permitted

The product architecture should allow later adapters without rewriting the UI.

Do not market V1 as "download anything from anywhere."

---

## 14. Offline behavior

Local file transformation should work offline after the required local binaries/resources are installed.

Pasted network URLs naturally require connectivity.

History, preview of retained metadata, settings, and local-file transforms should not require an account or backend.

---

## 15. Empty, loading, success, and failure states

Every major state must be intentionally designed:

- fresh launch
- valid URL detected
- metadata retrieval
- local file imported
- waveform generation
- ready to transform
- active transform
- transform complete
- cancelled
- invalid URL
- unsupported media
- upstream retrieval failure
- disk permission failure
- insufficient disk space
- FFmpeg failure
- app restart with previous history

No dead screens.

---

## 16. Accessibility

V1 release gate:
- keyboard-completable core flow
- visible focus states
- semantic controls
- adequate text contrast
- reduced-motion mode
- processing animation does not communicate progress through motion alone
- screen-readable names for icon controls
- large enough touch targets for later mobile-compatible surfaces

---

## 17. Scope exclusions for V1

Do not add:
- accounts
- subscriptions
- cloud libraries
- social features
- AI chat
- collaborative editing
- full DAW features
- stem separation
- noise removal
- vocal isolation
- video editing
- playlist management
- batch crawling
- browser extension
- arbitrary website scraping
- server-side transcoding
- monetization UI

These can be reconsidered only after the core converter is demonstrably excellent.

---

## 18. Technical shape

Suggested repository:

```text
trans-form/
├─ src/
│  ├─ app/
│  ├─ components/
│  ├─ features/
│  │  ├─ import/
│  │  ├─ preview/
│  │  ├─ selection/
│  │  ├─ transform/
│  │  ├─ music-analysis/
│  │  └─ history/
│  ├─ lib/
│  └─ styles/
├─ src-tauri/
│  ├─ src/
│  └─ capabilities/
├─ tests/
│  ├─ unit/
│  ├─ integration/
│  └─ fixtures/
├─ docs/
│  ├─ qa/
│  ├─ architecture/
│  └─ evidence/
├─ openspec/
└─ README.md
```

Prefer the fewest dependencies that satisfy real requirements. Do not substitute a large framework or component library for deliberate product design.

---

## 19. Build sequence

### Milestone 0: repository + tooling
- create repo
- initialize project
- initialize OpenSpec
- confirm Superpowers availability
- establish formatting / linting / test commands
- add release evidence directories
- document architecture decision

### Milestone 1: vertical slice
Prove the entire core path with ugly/minimal styling first:
1. paste one supported URL
2. retrieve metadata
3. download locally
4. extract MP3 locally
5. save file
6. cancel cleanly
7. surface failures

Do not build the elaborate visual layer until this path is reliable.

### Milestone 2: beautiful import + preview
- source metadata
- artwork
- playback
- waveform
- selection
- output chooser
- filename

### Milestone 3: transformation instrument
- real progress
- 3D particle / sound-wave transformation scene
- reduced-motion fallback
- cancellation
- completion transition

### Milestone 4: formats + metadata
- MP3 / WAV / M4A / FLAC / OGG
- format-specific options
- metadata editor
- filename sanitation

### Milestone 5: music intelligence
- music-vs-non-music classification
- BPM
- KEY
- SCALE
- half/double tempo correction
- confidence handling

### Milestone 6: history + offline/local-file polish
- local history
- repeat export
- local file transforms
- offline behavior

### Milestone 7: release gate
- regression suite
- accessibility
- performance
- Windows packaging
- clean install test
- clean uninstall test
- error-path testing
- security review
- README
- licenses
- known issues
- evidence

---

## 20. Acceptance criteria

V1 is not complete until all of the following are evidenced:

1. User can paste a supported YouTube URL and retrieve its metadata.
2. User can transform authorized media into MP3 entirely on-device after retrieval.
3. User can import a local video/audio file and transform it.
4. User can preview and trim via waveform.
5. All five output formats work against fixtures.
6. Cancel works during retrieval and transformation.
7. Temporary files are cleaned.
8. Invalid / unavailable sources fail gracefully.
9. KEY / SCALE / BPM never appear for clearly non-musical content.
10. BPM correction controls work.
11. History persists locally across restart.
12. No account or backend is required for the core workflow.
13. The UI has no ads, popups, fake buttons, redirects, or countdowns.
14. The signature processing animation is smooth on supported hardware and respects reduced motion.
15. Primary flow is keyboard accessible.
16. Security review finds no shell-command injection path from user-controlled input.
17. App packages and launches successfully on a clean supported Windows environment.
18. Automated tests and manual QA evidence are checked into the repository.
19. No known P0/P1 defects remain.
20. A release candidate passes the complete pre-launch quality gate.

---

## 21. OpenSpec + Superpowers operating rules

This repository uses **OpenSpec as the canonical record of what is being built** and **Superpowers as the engineering process used to build and verify it**.

### OpenSpec owns
- requirements
- accepted product behavior
- architectural decisions
- scoped changes
- acceptance criteria
- change history

### Superpowers owns
- brainstorming before ambiguous design/feature work
- implementation planning
- test-driven development where appropriate
- systematic debugging
- isolated worktrees when useful
- verification before completion
- code review

### Conflict rule
If an implementation plan contradicts an accepted OpenSpec requirement, the OpenSpec requirement wins unless the spec is explicitly changed first.

### Anti-drift rule
Do not silently redesign the product during implementation. Any material deviation must be proposed as an OpenSpec change before coding it.

### Completion rule
Never claim a milestone is complete from code inspection alone. Run the relevant automated tests and perform real interaction/render verification.

---

## 22. First OpenCode task

After the repository is created and OpenSpec is initialized, give OpenCode this instruction:

> Read `TRANSFORM_MASTER_PRD_v1.md` completely. Treat it as the current product source of truth. Use the Superpowers `brainstorming` skill only to identify unresolved technical decisions or contradictions, not to broaden the product. Then create the initial OpenSpec proposal for Milestone 0 and Milestone 1 only. Do not implement yet. The proposal must preserve the local-first architecture, near-zero operating cost, Windows-first Tauri direction, actual pasted-URL vertical slice, anti-generic visual constraints, security requirements, and the acceptance criteria in this PRD. Break the work into a vertical slice that proves URL → metadata → local retrieval → MP3 extraction → save → cancellation → failure handling before visual polish. Show the resulting spec and plan for review before applying it.
