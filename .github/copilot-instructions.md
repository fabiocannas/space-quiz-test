# Orbit quiz

## Running and checking

- Open `index.html` directly with a `file://` URL, preferably in the integrated browser. The app must remain usable offline without a server, dependencies, or external assets.
- There are no configured build, lint, or automated test commands. Validate behavior in the browser.
- To check a single correct-answer case, reload, choose **Mars**, and confirm green feedback, a factual explanation, **1 point**, progress **1/10**, and an enabled **Next question** button.
- To check a single incorrect-answer case, reload, choose **Venus**, and confirm red shake feedback, Mars identified as correct, **0 points**, and progress **1/10**. Answers must lock after selection.
- To check answer shortcuts, reload and press **A–D** before focusing an answer: nothing must happen. Tab to any answer, then press **B** with real keyboard input: Mars must be chosen exactly as if clicked, with focus moving to **Next question**. Check all four letters, including uppercase, from within the answers. Further A–D presses, A–D on the results screen or focused question heading after advancing/restarting, and Ctrl/Alt/Meta combinations must do nothing. Shortcuts become available again only after focus returns to an enabled answer.
- For a full smoke test, complete all ten questions, verify the final score and accuracy, then use **Explore again** and confirm score, progress, and question reset. Test Tab/Shift+Tab, Enter/Space, and the A–D shortcuts with real browser keyboard input; programmatic `.focus()`, `.click()`, and dispatched `KeyboardEvent`s do not verify keyboard activation. The integrated browser's typing action only fires `input` events, so use trusted key presses (for example Playwright's `keyboard.press`) for shortcut checks.
- Check narrow viewports, light/dark color schemes, and reduced-motion mode when changing presentation.

## Architecture and state

All markup, CSS, inline SVG artwork, question data, and application logic live in `index.html`. There is no framework, routing, backend, or persistent storage.

- `questions` contains objects with `category`, `question`, `answers`, a zero-based `correct` answer index, and an explanatory `fact`.
- `current`, `score`, and `answered` are the in-memory quiz state. `$()` looks up DOM elements by ID; IDs connect the static markup to rendering and event handlers.
- `renderQuestion()` rebuilds answer buttons and resets feedback, response-console status, and the disabled next action. `selectAnswer()` guards against repeat scoring, locks answers, reveals feedback, updates score/progress, and focuses the next action.
- The answers group's `keydown` handler maps unmodified letter keys to the matching rendered answer button (A–D) only while an enabled answer button has focus, satisfying the focus-only option of WCAG 2.1.4. It calls the matching button's `click()`, so shortcuts share `selectAnswer()` scoring, locking, feedback, and focus. It ignores auto-repeat, IME composition, Ctrl/Alt/Meta combinations, answered questions, and the hidden quiz on the results screen, and calls `preventDefault()` only for keys it handles.
- The next-button handler advances to another question or `showResults()`. Results replace the quiz via `hidden`, with reaction tiers at scores of 5 and 8. Restart resets state and removes remaining confetti.
- Progress counts completed answers, not the currently displayed question. Each correct answer earns one point.
- The experience is designed for ten questions: some markup and result calculations hard-code `10`, including `score * 10` for accuracy. If the question count changes, update these alongside the data.

## Presentation and accessibility conventions

- Preserve the opening HTML design contract: a narrow, centered 540px column, sans-serif 15px body, teal/mint palette, and mission-control response console. Mobile adjustments use the 440px breakpoint.
- Use the existing CSS custom properties for colors. Light values are defined in `:root`; dark overrides live in `prefers-color-scheme: dark`. Keep correct and incorrect states on their dedicated semantic tokens.
- The intro's star field uses pseudo-elements; shooting stars and the orbiting satellite are decorative. The satellite rotates around SVG coordinates `117px 71px`, the Earth's center.
- Enable star twinkling and shooting stars only under `prefers-reduced-motion: no-preference`. The reduced-motion rule disables animations and transitions globally, and JavaScript separately gates results confetti with `matchMedia`. Keep shooting stars invisible when animation is disabled.
- Use native buttons, `disabled` for locked actions, and the existing `[hidden]` rule for inactive screens. Keep visible focus styles, live score/feedback regions, and textual answer-state labels rather than relying only on green/red color.
- Preserve focus transitions: question heading after advancing/restarting, next action after answering, and results heading on completion. Decorative SVGs, stars, answer letters, and state marks are hidden from assistive technology.
- Answer buttons expose their letter through `aria-keyshortcuts` only while focus is within the answers group and answers are unlocked; leaving the group or answering removes it. The instruction's A–D hint explains that an answer must have focus, uses `<kbd>` keycaps styled like the answer letters, is hidden on touch-only devices (`hover: none` and `pointer: coarse`), and hard-codes four answers per question.
- Dynamic question and answer copy is assigned with `textContent`; answer elements are constructed with DOM APIs and `replaceChildren()`. Keep user-facing content out of interpolated HTML.
