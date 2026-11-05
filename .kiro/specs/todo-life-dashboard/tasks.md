# Implementation Plan: To-Do Life Dashboard

## Overview

Build the To-Do Life Dashboard as three static files (`index.html`, `css/style.css`, `js/app.js`) using plain HTML, CSS, and Vanilla JavaScript organized with the Revealing Module Pattern. Each widget is a self-contained IIFE module; a thin `App` bootstrap wires them together on `DOMContentLoaded`. All user data is persisted via `localStorage`.

## Tasks

- [ ] 1. Scaffold project structure and HTML shell
  - [ ] 1.1 Create the root `index.html` with semantic markup shells for all four widget sections
    - Add `<head>` with `<meta charset>`, `<meta name="viewport">`, `<title>`, and `<link>` to `css/style.css`
    - Add `<div class="dashboard-grid">` containing four widget cards: `#greeting-widget`, `#focus-timer-widget`, `#task-manager-widget`, `#quick-links-widget`
    - Include placeholder headings, form elements, and list containers for each widget as defined in the design
    - Add `<script src="js/app.js" defer></script>` before `</body>`
    - Create empty `css/style.css` and `js/app.js` files so the page loads without missing-resource errors
    - _Requirements: 11.1, 11.2, 11.3, 11.4_

- [ ] 2. Implement CSS base styles and layout
  - [ ] 2.1 Write CSS custom properties, reset, and base typography
    - Define all color tokens (`--bg-gradient`, `--card-bg`, `--card-shadow`, `--text-primary`, `--text-secondary`, `--accent`, `--accent-hover`, `--danger`, `--success`) as CSS variables on `:root`
    - Apply box-sizing reset and font-stack (`'Segoe UI', system-ui, -apple-system, sans-serif`) to all elements
    - Set page background to `var(--bg-gradient)` on `body`
    - _Requirements: 12.1, 12.2_

  - [ ] 2.2 Implement the 2×2 CSS Grid dashboard layout and responsive breakpoints
    - Style `.dashboard-grid` as a 2-column grid for desktop (≥1280px), with `#greeting-widget` and `#task-manager-widget` spanning both columns
    - Add `@media` breakpoints: single-column flow for 768–1279px; single-column with reduced padding for <768px
    - Ensure no horizontal overflow at any breakpoint
    - _Requirements: 12.3, 12.5, 12.6_

  - [ ] 2.3 Style widget cards, typography, button states, and interactive feedback
    - Apply `var(--card-bg)`, `border-radius`, and `var(--card-shadow)` to each widget card
    - Style clock display with `clamp(2.5rem, 5vw, 4rem)` and `font-weight: 700`
    - Add `:hover`, `:focus`, and `:active` states for all buttons and inputs with transition ≤100ms
    - Style completed tasks with strikethrough and `var(--success)` color
    - Style validation error messages using `var(--danger)`
    - _Requirements: 12.1, 12.2, 12.4, 7.1_

- [ ] 3. Implement StorageService module
  - [ ] 3.1 Write the `StorageService` IIFE module in `js/app.js`
    - Implement `init()`: probe `localStorage` availability via a test write; set `available = false` and show a persistent banner if a `SecurityError` or `DOMException` is thrown
    - Implement `get(key)`: wrap `JSON.parse(localStorage.getItem(key))` in try/catch; return `null` on missing key or parse error
    - Implement `set(key, value)`: wrap `localStorage.setItem` in try/catch; catch `QuotaExceededError`, show widget-level inline error, return `false`
    - Implement `isAvailable()`: return `available` flag
    - _Requirements: 11.5, 11.6, 8.4_

  - [ ]* 3.2 Write property test for StorageService corruption recovery
    - **Property 10: Storage corruption recovery**
    - **Validates: Requirements 8.5, 10.5**

- [ ] 4. Implement GreetingWidget module
  - [ ] 4.1 Write the `GreetingWidget` IIFE module
    - Implement `_formatTime(date)` returning `"HH:MM:SS"` using `padStart(2, '0')`
    - Implement `_formatDate(date)` returning full weekday, month, day, and year string using `toLocaleDateString` options
    - Implement `_getGreeting(hour)` returning the correct string for the four time ranges (05–11 → Morning, 12–17 → Afternoon, 18–20 → Evening, 21–04 → Night)
    - Implement `_render(time, date, greeting)` updating the three DOM text nodes
    - Implement `_tick()` calling `new Date()`, checking `isNaN`, rendering placeholder if invalid, otherwise calling `_render`
    - Implement `init()` calling `_tick()` immediately then starting a `setInterval` at 1000ms
    - _Requirements: 1.1, 1.2, 1.3, 1.4, 2.1, 2.2, 2.3, 2.4, 2.5, 2.6_

  - [ ]* 4.2 Write property test for greeting boundary coverage
    - **Property 1: Greeting boundary coverage**
    - **Validates: Requirements 2.1, 2.2, 2.3, 2.4**

- [ ] 5. Implement FocusTimerWidget module
  - [ ] 5.1 Write the `FocusTimerWidget` IIFE module with state machine and countdown logic
    - Declare closure state: `state` (STOPPED/RUNNING/PAUSED), `remainingSeconds` (1500), `intervalHandle` (null)
    - Implement `_renderDisplay(totalSeconds)` formatting as `MM:SS` and updating the DOM display element
    - Implement `_updateControlState(state)` enabling/disabling Start, Stop, Reset buttons per the state machine rules
    - Implement `_tick()`: decrement `remainingSeconds`; call `_renderDisplay`; if reaches 0, clear interval, call `_playAudioCue`, set state to STOPPED
    - Implement `_start()`: if state is STOPPED or PAUSED, set state to RUNNING, create interval, call `_updateControlState`; no-op if already RUNNING
    - Implement `_stop()`: if state is RUNNING, clear interval, set state to PAUSED, call `_updateControlState`
    - Implement `_reset()`: clear interval, set `remainingSeconds = 1500`, set state to STOPPED, call `_renderDisplay`, call `_updateControlState`
    - Implement `_playAudioCue()`: create `AudioContext`, schedule a short beep (oscillator, 1–2 seconds), close context when done
    - Implement `init()`: call `_renderDisplay(1500)`, set state to STOPPED, bind click handlers, call `_updateControlState`
    - _Requirements: 3.1, 3.2, 3.3, 3.4, 3.5, 3.6, 4.1, 4.2, 4.3, 4.4, 4.5, 4.6_

  - [ ]* 5.2 Write property test for timer countdown monotonicity
    - **Property 9: Timer countdown monotonicity**
    - **Validates: Requirements 3.2, 3.3, 3.4**

- [ ] 6. Implement TaskManagerWidget module
  - [ ] 6.1 Write `generateId()` utility and `TaskManagerWidget` IIFE module skeleton with CRUD operations
    - Implement `generateId()` using `crypto.randomUUID()` with `Math.random()` fallback as specified in the design
    - Declare closure state: `tasks` array (in-memory source of truth)
    - Implement `_addTask(description)`: trim and validate (non-empty, ≤200 chars); create Task object with `generateId()`, `createdAt: Date.now()`, `completed: false`; push to `tasks`; call `StorageService.set('tld_tasks', tasks)`; call `_renderList()`; show validation error if invalid
    - Implement `_editTask(id, newDescription)`: trim and validate; if whitespace-only revert silently; mutate in-memory array; call `StorageService.set`; call `_renderList()`
    - Implement `_deleteTask(id)`: splice from array; call `StorageService.set`; if returns `false`, re-insert and show inline error; call `_renderList()`
    - Implement `_toggleComplete(id)`: flip `completed`; call `StorageService.set`; call `_renderList()`
    - _Requirements: 5.1, 5.2, 5.3, 5.5, 6.1, 6.2, 6.3, 6.4, 6.5, 7.1, 7.2, 7.3_

  - [ ] 6.2 Implement task list DOM rendering and edit mode
    - Implement `_renderTask(task)` creating a `<li>` with checkbox, description `<span>`, Edit button, and Delete button; apply strikethrough style when `task.completed` is true
    - Implement `_renderList()` clearing the `<ul>` and rebuilding from the in-memory `tasks` array
    - Implement `_enterEditMode(id)` replacing the description `<span>` with a pre-filled `<input>` and Save/Cancel controls
    - Implement `_exitEditMode(id, save)` confirming or discarding the edit and restoring read-only display
    - Implement `_showValidationMessage(msg)` and `_clearValidationMessage()` for the add-task input
    - Wire the add-task form to call `_addTask` on button click and Enter key
    - _Requirements: 5.1, 5.2, 5.3, 5.4, 6.1, 6.2, 6.3, 6.4_

  - [ ] 6.3 Implement task list persistence: load from storage on init
    - Implement `init()`: call `StorageService.get('tld_tasks')`; if result is null or not an array, initialize `tasks = []`; if result is a non-array (non-null), show corrupted-data notification; call `_renderList()`
    - _Requirements: 8.1, 8.2, 8.3, 8.5_

  - [ ]* 6.4 Write property test for task addition growing the list
    - **Property 2: Task addition grows the list**
    - **Validates: Requirements 5.2**

  - [ ]* 6.5 Write property test for whitespace-only task rejection
    - **Property 3: Whitespace-only tasks are rejected**
    - **Validates: Requirements 5.3**

  - [ ]* 6.6 Write property test for task addition round-trip persistence
    - **Property 4: Task addition round-trip persistence**
    - **Validates: Requirements 8.1, 8.2**

  - [ ]* 6.7 Write property test for task completion toggle being self-inverse
    - **Property 5: Task completion toggle is self-inverse**
    - **Validates: Requirements 7.1**

- [ ] 7. Checkpoint — Core widgets working
  - Ensure StorageService, GreetingWidget, FocusTimerWidget, and TaskManagerWidget are wired into the `App` bootstrap and functional end-to-end. Ensure all tests pass, ask the user if questions arise.

- [ ] 8. Implement QuickLinksWidget module
  - [ ] 8.1 Write the `QuickLinksWidget` IIFE module with add and remove operations
    - Declare closure state: `links` array (in-memory source of truth)
    - Implement `_validateUrl(url)`: return `true` if and only if url starts with `"http://"` or `"https://"`
    - Implement `_addLink(name, url)`: validate name (non-empty, ≤50 chars) and URL; enforce 20-link cap; create Link object with `generateId()`, `createdAt: Date.now()`; push to `links`; call `StorageService.set('tld_links', links)`; call `_renderList()`
    - Implement `_removeLink(id)`: splice from `links`; call `StorageService.set`; call `_renderList()`
    - Implement `_showValidationMessage(field, msg)` and `_clearValidationMessages()`
    - _Requirements: 9.1, 9.2, 9.3, 9.4, 9.6, 10.1, 10.2, 10.3_

  - [ ] 8.2 Implement quick-links DOM rendering, link buttons, and persistence init
    - Implement `_renderLink(link)` creating a `<button>` that calls `window.open(link.url, '_blank')` on activation, plus a `×` remove control with `aria-label`; both must be keyboard-activatable
    - Implement `_renderList()` clearing the links container and rebuilding from in-memory `links`; render empty state without orphaned elements when list is empty
    - Wire the add-link form to call `_addLink` on button click
    - Implement `init()`: call `StorageService.get('tld_links')`; if result is null or not an array initialize `links = []`; if non-array non-null show notification; call `_renderList()`
    - _Requirements: 9.2, 9.5, 10.1, 10.2, 10.4, 10.5, 10.6_

  - [ ]* 8.3 Write property test for URL validation boundary
    - **Property 6: URL validation boundary**
    - **Validates: Requirements 9.4**

  - [ ]* 8.4 Write property test for link addition round-trip persistence
    - **Property 7: Link addition round-trip persistence**
    - **Validates: Requirements 10.3, 10.4**

  - [ ]* 8.5 Write property test for links list capacity enforcement
    - **Property 8: Links list capacity enforcement**
    - **Validates: Requirements 9.6**

- [ ] 9. Implement App bootstrap and wire all modules
  - [ ] 9.1 Write the `App` IIFE module and `DOMContentLoaded` initialization sequence
    - Implement `App.init()` calling `StorageService.init()`, then `GreetingWidget.init()`, `FocusTimerWidget.init()`, `TaskManagerWidget.init()`, `QuickLinksWidget.init()` in order
    - Attach `App.init` to the `DOMContentLoaded` event
    - Verify all modules are declared in `js/app.js` before the `App` module so the initialization order is safe
    - _Requirements: 11.3, 11.4_

- [ ] 10. Final checkpoint — Full integration
  - Load `index.html` in a browser (Chrome, Firefox, Edge, or Safari latest). Confirm all four widgets render, timer counts down, tasks persist across page reload, links persist across page reload, and no console errors appear. Ensure all tests pass, ask the user if questions arise.

## Notes

- Tasks marked with `*` are optional and can be skipped for faster MVP delivery
- Each task references specific requirements for traceability
- Checkpoints (tasks 7 and 10) are integration validation points
- Property tests validate universal correctness properties using `fast-check` (dev dependency only — the production files remain dependency-free)
- Unit tests validate specific examples and edge cases alongside property tests
- The production bundle (`index.html`, `css/style.css`, `js/app.js`) has zero runtime dependencies per Requirements 11.1

## Task Dependency Graph

```json
{
  "waves": [
    { "id": 0, "tasks": ["1.1"] },
    { "id": 1, "tasks": ["2.1", "2.2", "3.1"] },
    { "id": 2, "tasks": ["2.3", "3.2", "4.1"] },
    { "id": 3, "tasks": ["4.2", "5.1", "6.1"] },
    { "id": 4, "tasks": ["5.2", "6.2", "8.1"] },
    { "id": 5, "tasks": ["6.3", "6.4", "6.5", "8.2"] },
    { "id": 6, "tasks": ["6.6", "6.7", "8.3", "8.4", "8.5"] },
    { "id": 7, "tasks": ["9.1"] }
  ]
}
```
