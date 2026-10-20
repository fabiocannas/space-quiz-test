# Orbit

A pocket-sized space exploration quiz: answer ten multiple-choice questions,
discover the science behind each answer, and see your final score and accuracy.
Orbit includes immediate answer feedback, keyboard-accessible controls, light
and dark themes, and reduced-motion support.

## Run locally

Open `index.html` directly in your browser (double-click the file or use a
`file://` URL). No server, dependencies, installation, or build step is needed.
The quiz works offline; all styles, artwork, questions, and logic are contained
in that one file.

Choose an answer, read the explanation, then select **Next question**. After
ten questions, select **Explore again** to restart. Use Tab and Shift+Tab to
move between controls, and Enter or Space to activate them. When an answer
button has focus, A-D also selects the corresponding answer.

## Deployment

The [Pages workflow](.github/workflows/pages.yml) packages only `index.html`
and deploys it to GitHub Pages whenever changes reach `main`. Pull requests
run the packaging job without publishing. There is no compilation step.
The workflow can also be run manually from the Actions tab on `main`.

In repository **Settings > Pages**, the build source must be **GitHub Actions**.
The `github-pages` environment and the workflow run show the deployed URL.
Any environment approval rules must be satisfied before deployment proceeds.
