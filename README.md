## 📋 About

This repository hosts Mermaid-based diagrams documenting system architecture, data flows, CI pipelines, module dependencies, and more. All diagrams use [Mermaid](https://mermaid.js.org/) syntax and render automatically on GitHub — no external tools or image hosting required.

## 📁 Contents

| File | Description |
|------|-------------|
| [`docs/diagrams.md`](docs/diagrams.md) | Full diagram collection with 10 diagrams + explanatory tables |

## 📊 Diagrams Included

| # | Diagram | Type | Purpose |
|---|---------|------|---------|
| 1 | Bug Audit Findings | Flowchart | Issue severity, fix status, resolution path |
| 2 | Drag-and-Drop Flow | Flowchart | Drag → splice → state update → re-render |
| 3 | Theme Toggle State Machine | State Diagram | Dark ↔ light transitions + persistence |
| 4 | Milestone Overlay Pipeline | Flowchart | Date parse → scale → SVG line/text render |
| 5 | Module Dependency Graph | Flowchart | Import relationships across all files |
| 6 | Directory Tree | Flowchart | Filesystem layout visualization |
| 7 | Test Suite Organization | Flowchart | Test cases grouped by suite |
| 8 | CI Pipeline Flow | Flowchart | Trigger → checkout → install → test → build |
| 9 | Test Coverage Gap Analysis | Flowchart | What's tested vs. what needs tests |
| 10 | Full System Data Flow | Flowchart | End-to-end input → state → compute → render |

## 🔧 Tech Stack

- **Diagram Language:** [Mermaid v2](https://mermaid.js.org/)
- **Hosting:** GitHub (native Mermaid rendering)
- **Related Projects:** [bilbywilby/ez_dashboard](https://github.com/bilbywilby/ez_dashboard)

## 🚀 Usage

### View Diagrams

Diagrams render automatically on GitHub. Simply browse to [`docs/diagrams.md`](docs/diagrams.md).

### Edit Diagrams

1. Clone the repository:
   ```bash
   git clone https://github.com/bilbywilby/Mermaids.git
   cd Mermaids
   ```

2. Edit `docs/diagrams.md` — all diagrams are inline Mermaid code blocks.

3. Preview locally with the [Mermaid Live Editor](https://mermaid.live):
   - Copy any ```` ```mermaid ```` block
   - Paste into the editor
   - Adjust and copy back

4. Commit and push:
   ```bash
   git add docs/diagrams.md
   git commit -m "docs: update diagram descriptions"
   git push origin main
   ```

### Embed in Other Repos

Reference diagrams from other repositories using GitHub's relative links or raw URLs:markdown
<!-- Link to the full doc -->
Architecture Diagrams
<!-- Raw link for embedding -->
Module Dependency Graph
📐 Mermaid Syntax Cheat Sheetflowchart TD
    A[Start] --> B{Decision}
    B -->|Yes| C[Action]
    B -->|No| D[End]ElementSyntaxFlowchartflowchart TD / flowchart LRState Diagramstate diagram-v2Subgraphsubgraph NAME["Label"]Decision node{"question?"}Styled nodestyle ID fill:#color,color:#colorLink label`A -->Comment%% comment
Full syntax reference: mermaid.js.org/syntax
🤝 Contributing

Fork the repository
Create a branch: git checkout -b diagram/new-diagram-name
Add or update diagrams in docs/diagrams.md
Verify rendering on GitHub
Open a pull request

Guidelines

Use descriptive node IDs (e.g., SETSTATE not A1)
Add style declarations for color-coded severity
Include a plain-text summary table below each diagram
Keep subgraph labels short but meaningful
Test on mermaid.live before committing

📄 License
MIT © 2026 bilbywilby
🔗 Links

Mermaid Documentation
Mermaid Live Editor
GitHub Mermaid Support
Related: ez_dashboard


---


