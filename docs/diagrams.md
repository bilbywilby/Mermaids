Here are Mermaid diagrams for each of the four areas we covered — audit, features, file split, and tests.1. Audit: Bug Findings & Fix Flowflowchart TD
    subgraph AUDIT["🔍 Code Audit Findings"]
        direction TB
        
        B1["Math.min(...[]) → Infinity<br/>in minProjectStart"]
        B2["No debounce on JSON textarea<br/>Parse-per-keystroke lag"]
        B3["No SSR guard on<br/>window.localStorage access"]
        B4["Export Blob URL revoke<br/>lacks retry guard"]
        
        B1 -->|Severity: Low| F1["Already guarded ✅<br/>Empty check exists"]
        B2 -->|Severity: Medium| F2["Added useDebouncedCallback<br/>400ms delay before JSON.parse"]
        B3 -->|Severity: Medium| F3["Added typeof window !== undefined<br/>guards in usePersistentState"]
        B4 -->|Severity: Low| F4["URL.revokeObjectURL after click<br/>Acceptable — no fix needed"]
    end
    
    subgraph GANTT_EDGE["GanttChart yAxisWidth Edge Case"]
        E1["data.map(d => d.name.length)<br/>on empty array → []"]
        E1 --> E2["Math.max(100, ...[]) = 100<br/>Safe — fallback covers it"]
        E2 --> E3["Changed to Math.max(0, ...)<br/>for explicit safety"]
    end
    
    F2 --> RESOLUTION
    F3 --> RESOLUTION
    RESOLUTION["✅ Two fixes applied<br/>in PR #1 branch"]
    
    style B2 fill:#f59e0b,color:#000
    style B3 fill:#f59e0b,color:#000
    style B1 fill:#22c55e,color:#000
    style B4 fill:#22c55e,color:#000
    style RESOLUTION fill:#3b82f6,color:#fff
2. Feature Extensions: Component Interaction Diagrams
2a. Drag-and-Drop Task Reordering Flowflowchart LR
    subgraph DND["Drag-and-Drop Task Controller"]
        direction TB
        
        USER["👤 User drags task card"]
        USER --> DDC["DragDropContext<br/>onDragEnd handler"]
        
        DDC -->|DropResult| VALIDATE{"destination<br/>exists?"}
        
        VALIDATE -->|No| NOOP["No-op<br/>Return early"]
        VALIDATE -->|Yes| COPY["Copy tasks array<br/>[...projectData.tasks]"]
        
        COPY --> SPLICE["splice(source.index, 1)<br/>→ moved task"]
        SPLICE --> INSERT["splice(dest.index, 0, moved)<br/>→ reordered array"]
        
        INSERT --> SETSTATE["setProjectData<br/>{...prev, tasks: reordered}"]
        SETSTATE --> RERENDER["React re-renders<br/>filteredTasks recomputes"]
        RERENDER --> VISUAL["Task list updates<br/>with new order"]
    end
    
    subgraph COMPONENTS["Components Involved"]
        TCA["TaskController.tsx"]
        DCC["@hello-pangea/dnd"]
        APP["App.tsx state"]
        
        TCC --> DCC
        DCC --> APP
    end
    
    style USER fill:#3b82f6,color:#fff
    style SETSTATE fill:#22c55e,color:#000
    style NOOP fill:#64748b,color:#fff2b. Theme Toggle State Machinestate diagram-v2
    [*] --> dark: Default
    
    dark --> light: toggle()
    light --> dark: toggle()
    
    dark: On enter →\nclassList.remove('light')\nbg-slate-900 text-slate-100
    light: On enter →\nclassList.add('light')\nbg-gray-50 text-gray-800
    
    state "Persist to localStorage\nkey: ez-dashboard-theme" as PERSIST
    dark --> PERSIST
    light --> PERSIST
    PERSIST --> dark
    PERSIST --> light

note right of dark
  usePersistentState hook
  reads theme on mount
  applies document class via
  useEffect dependency
end note2c. Milestone Dependency Line Rendering Pipelineflowchart TD
    subgraph MILESTONE["Milestone Overlay on Gantt Chart"]
        direction TB
        
        MS["milestones array<br/>[{name, date}]"]
        MS --> PARSE["parseDateUTC(ms.date)"]
        PARSE --> CALC["Compute day offset<br/>from minProjectStart"]
        CALC --> SCALE["xAxis.scale(dayOffset)<br/>→ pixel x-coordinate"]
        
        SCALE --> DRAW_LINE["Draw SVG line<br/>vertical dashed purple"]
        SCALE --> DRAW_LABEL["Draw SVG text<br/>milestone name at top"]
        
        DRAW_LINE --> OVERLAY["Customized component<br/>renders inside BarChart"]
        DRAW_LABEL --> OVERLAY
        
        OVERLAY --> RESULT["Gantt chart with<br/>vertical milestone markers"]
    end
    
    subgraph DATA_FLOW["Data Required"]
        MS_DATA["projectData.milestones"]
        MIN_START["minProjectStart<br/>(from tasks)"]
        AXIS_MAP["xAxisMap from Recharts<br/>internal scale"]
        
        MS_DATA --> PARSE
        MIN_START --> CALC
        AXIS_MAP --> SCALE
    end
    
    style OVERLAY fill:#a855f7,color:#fff
    style RESULT fill:#22c55e,color:#000
3. File Split: Module Dependency Graphflowchart TD
    subgraph ENTRY["Entry Points"]
        INDEX["index.html"]
        MAIN["main.tsx"]
        APP["App.tsx"]
        
        INDEX --> MAIN
        MAIN --> APP
    end
    
    subgraph CORE["Core Modules"]
        TYPES["types.ts<br/>Interfaces & Types"]
        CONST["constants.ts<br/>initialProjectData<br/>STATUS_COLORS"]
        DATES["utils/dates.ts<br/>parseDateUTC<br/>getMsPerDay"]
    end
    
    subgraph HOOKS["Hooks"]
        UPS["usePersistentState.ts<br/>localStorage + SSR guard"]
        UDC["useDebouncedCallback.ts<br/>400ms debounce"]
        UTH["useTheme.ts<br/>dark/light toggle"]
        
        UPS --> UTH
    end
    
    subgraph CHARTS["Chart Components"]
        GANTT["charts/GanttChart.tsx<br/>Stacked horizontal bars"]
        WORK["charts/WorkloadChart.tsx<br/>Stacked vertical bars"]
        PIE["charts/StatusPieChart.tsx<br/>Donut with labels"]
    end
    
    subgraph COMPONENTS["UI Components"]
        HEADER["Header.tsx<br/>Nav, Import/Export/Print/Theme"]
        JSON["JsonEditor.tsx<br/>Textarea + validation"]
        TASK["TaskController.tsx<br/>Drag-drop task list"]
    end
    
    subgraph STYLES["Styles"]
        GLOBAL["styles/globals.css<br/>Tailwind directives<br/>Print + theme overrides"]
    end
    
    subgraph TESTS["Test Suite"]
        SETUP["__tests__/setup.ts<br/>jest-dom import"]
        CALC_T["__tests__/calculations.test.ts<br/>13 test cases"]
        HOOK_T["__tests__/hooks.test.ts<br/>4 test cases"]
    end
    
    %% App imports
    APP --> TYPES
    APP --> CONST
    APP --> DATES
    APP --> UPS
    APP --> UDC
    APP --> UTH
    APP --> HEADER
    APP --> JSON
    APP --> TASK
    APP --> GANTT
    APP --> WORK
    APP --> PIE
    
    %% Component imports
    HEADER --> TYPES
    JSON --> TYPES
    TASK --> TYPES
    GANTT --> TYPES
    WORK --> TYPES
    PIE --> CONST
    
    %% Hook imports
    UTH --> UPS
    UTH --> TYPES
    
    %% Test imports
    CALC_T --> DATES
    HOOK_T --> UPS
    
    %% Styles
    MAIN --> GLOBAL
    
    style APP fill:#3b82f6,color:#fff
    style TYPES fill:#a855f7,color:#fff
    style CONST fill:#a855f7,color:#fff
    style UPS fill:#22c55e,color:#000
    style UDC fill:#22c55e,color:#000
    style TASK fill:#f59e0b,color:#0003b. Directory Tree Visualizationflowchart LR
    ROOT["ez_dashboard/"] --> SRC["src/"]
    ROOT --> CONFIG["Config Files"]
    ROOT --> GH[".github/"]
    
    CONFIG --> PKG["package.json"]
    CONFIG --> TSC["tsconfig.json"]
    CONFIG --> TSCN["tsconfig.node.json"]
    CONFIG --> VITE["vite.config.ts"]
    CONFIG --> VTEST["vitest.config.ts"]
    CONFIG --> TW["tailwind.config.js"]
    CONFIG --> PCSS["postcss.config.js"]
    CONFIG --> GITIGNOR[".gitignore"]
    CONFIG --> LIC["LICENSE"]
    CONFIG --> RDME["README.md"]
    
    GH --> WF["workflows/"]
    WF --> CI["ci.yml"]
    
    SRC --> INDEX["index.html"]
    SRC --> MAINTSX["main.tsx"]
    SRC --> APPTSX["App.tsx"]
    SRC --> TYPESTS["types.ts"]
    SRC --> CONSTTS["constants.ts"]
    SRC --> STYLEDIR["styles/"]
    SRC --> UTILSDIR["utils/"]
    SRC --> HOOKSDIR["hooks/"]
    SRC --> COMPDIR["components/"]
    SRC --> TESTSDIR["__tests__/"]
    
    STYLEDIR --> GCSS["globals.css"]
    
    UTILSDIR --> DT["dates.ts"]
    
    HOOKSDIR --> UPS2["usePersistentState.ts"]
    HOOKSDIR --> UDC2["useDebouncedCallback.ts"]
    HOOKSDIR --> UTH2["useTheme.ts"]
    
    COMPDIR --> HDR["Header.tsx"]
    COMPDIR --> JE["JsonEditor.tsx"]
    COMPDIR --> TC["TaskController.tsx"]
    COMPDIR --> CHARTS["charts/"]
    
    CHARTS --> GT["GanttChart.tsx"]
    CHARTS --> WT["WorkloadChart.tsx"]
    CHARTS --> PT["StatusPieChart.tsx"]
    
    TESTSDIR --> ST["setup.ts"]
    TESTSDIR --> CT["calculations.test.ts"]
    TESTSDIR --> HT["hooks.test.ts"]
    
    style ROOT fill:#1e293b,color:#fff
    style SRC fill:#3b82f6,color:#fff
    style COMPDIR fill:#a855f7,color:#fff
    style HOOKSDIR fill:#22c55e,color:#000
    style TESTSDIR fill:#f59e0b,color:#000
4. Tests: Coverage Map & CI Pipeline
4a. Test Suite Organizationflowchart TD
    subgraph VITEST["Vitest Test Runner"]
        direction TB
        CONFIG_FILE["vitest.config.ts<br/>environment: jsdom<br/>globals: true"]
        SETUP_FILE["setup.ts<br/>@testing-library/jest-dom"]
        
        CONFIG_FILE --> SETUP_FILE
    end
    
    subgraph CALC_SUITE["calculations.test.ts — 13 cases"]
        direction TB
        
        S1["computeOverallProgress<br/>4 tests"]
        S1 --> T1["returns 0 for empty tasks"]
        S1 --> T2["averages progress values"]
        S1 --> T3["handles undefined progress"]
        S1 --> T4["rounds correctly (38)"]
        
        S2["computeGanttOffsets<br/>3 tests"]
        S2 --> T5["zero offset for earliest task"]
        S2 --> T6["positive offset for later tasks"]
        S2 --> T7["minimum duration of 1 day"]
        
        S3["computeWorkload<br/>3 tests"]
        S3 --> T8["zeroed entries for no-task members"]
        S3 --> T9["counts statuses correctly"]
        S3 --> T10["handles unknown assignees"]
        
        S4["parseDateUTC<br/>2 tests"]
        S4 --> T11["parses ISO date strings"]
        S4 --> T12["returns current date for falsy input"]
    end
    
    subgraph HOOK_SUITE["hooks.test.ts — 4 cases"]
        direction TB
        
        S5["usePersistentState<br/>4 tests"]
        S5 --> T13["returns initial when storage empty"]
        S5 --> T14["persists value to localStorage"]
        S5 --> T15["reads from localStorage on init"]
        S5 --> T16["falls back on corrupt data"]
    end
    
    VITEST --> CALC_SUITE
    VITEST --> HOOK_SUITE
    
    style S1 fill:#22c55e,color:#000
    style S2 fill:#3b82f6,color:#fff
    style S3 fill:#a855f7,color:#fff
    style S4 fill:#f59e0b,color:#000
    style S5 fill:#ec4899,color:#fff4b. CI Pipeline Flowflowchart LR
    subgraph TRIGGER["Trigger Events"]
        PUSH["push to<br/>main / orchestrator/**"]
        PR["pull_request<br/>to main"]
    end
    
    PUSH --> JOBS
    PR --> JOBS
    
    subgraph JOBS["GitHub Actions CI"]
        direction TB
        
        CHECKOUT["actions/checkout@v4<br/>Clone repository"]
        CHECKOUT --> NODE["actions/setup-node@v4<br/>Node 20 + npm cache"]
        NODE --> INSTALL["npm ci<br/>Clean install from lockfile"]
        INSTALL --> TEST["npm run test:run<br/>Vitest single-run mode"]
        TEST --> BUILD["npm run build<br/>tsc + vite build"]
    end
    
    TEST -->|Pass| PASS_CHECK["✅ All tests green"]
    TEST -->|Fail| FAIL_CHECK["❌ Tests failed<br/>Block merge"]
    BUILD -->|Pass| PASS_BUILD["✅ Build artifact produced"]
    BUILD -->|Fail| FAIL_BUILD["❌ TypeScript or<br/>Vite errors"]
    
    PASS_CHECK --> MERGE_READY["PR ready for review"]
    PASS_BUILD --> MERGE_READY
    FAIL_CHECK --> BLOCKED["PR blocked"]
    FAIL_BUILD --> BLOCKED
    
    style PASS_CHECK fill:#22c55e,color:#000
    style PASS_BUILD fill:#22c55e,color:#000
    style FAIL_CHECK fill:#dc2626,color:#fff
    style FAIL_BUILD fill:#dc2626,color:#fff
    style MERGE_READY fill:#3b82f6,color:#fff
    style BLOCKED fill:#7f1d1d,color:#fff4c. Test Coverage Gap Analysisflowchart TD
    subgraph COVERED["✅ Currently Tested"]
        C1["parseDateUTC<br/>date parsing"]
        C2["computeOverallProgress<br/>progress averaging"]
        C3["computeGanttOffsets<br/>day offset calculation"]
        C4["computeWorkload<br/>team workload aggregation"]
        C5["usePersistentState<br/>localStorage read/write/fallback"]
    end
    
    subgraph GAPS["⚠️ Not Yet Tested — Future Coverage"]
        G1["handleTaskStatusUpdate<br/>status → progress mapping"]
        G2["handleJsonImport<br/>FileReader + parse flow"]
        G3["useDebouncedCallback<br/>timer behavior"]
        G4["useTheme<br/>toggle + persistence"]
        G5["handleExport<br/>Blob + download trigger"]
        G6["handleDragEnd<br/>array reorder logic"]
        G7["filteredTasks<br/>multi-criteria filter chaining"]
        G8["statusDistribution<br/>count aggregation"]
    end
    
    subgraph RECOMMENDED["📋 Recommended Next Steps"]
        R1["Add component tests<br/>with React Testing Library<br/>render + interaction"]
        R2["Add integration test<br/>full App render + filter"]
        R3["Add E2E with Playwright<br/>drag-drop + theme toggle"]
    end
    
    COVERED --> RECOMMENDED
    GAPS --> RECOMMENDED
    
    style COVERED fill:#22c55e,color:#000
    style GAPS fill:#f59e0b,color:#000
    style RECOMMENDED fill:#3b82f6,color:#fff
5. Bonus: Full System Data Flowflowchart TD
    subgraph INPUT["Input Layer"]
        INIT["initialProjectData<br/>(constants.ts)"]
        IMPORT["File Import<br/>(FileReader API)"]
        TEXTAREA["JSON Textarea<br/>(debounced 400ms)"]
        DRAG["Drag-Drop<br/>(@hello-pangea/dnd)"]
        SELECT["Status Select<br/>(per-task dropdown)"]
        FILTER["Filter Selects<br/>(4 dropdowns)"]
        THEME_BTN["Theme Button<br/>(toggle dark/light)"]
    end
    
    subgraph STATE["State Layer"]
        PROJ_STATE["projectData<br/>useState"]
        FILT_STATE["filter<br/>usePersistentState"]
        VIEW_STATE["viewMode<br/>usePersistentState"]
        JSON_STATE["jsonInput<br/>useState"]
        ERR_STATE["jsonError<br/>useState"]
        THEME_STATE["theme<br/>usePersistentState"]
        LOCAL["localStorage<br/>3 keys persisted"]
    end
    
    subgraph COMPUTE["Computation Layer (useMemo)"]
        PROGRESS["overallProgress"]
        FILTERED["filteredTasks"]
        MINSTART["minProjectStart"]
        GANTT["ganttData"]
        WORKLOAD["workloadData"]
        STATUS["statusDistribution"]
    end
    
    subgraph RENDER["Render Layer"]
        HEADER_C["Header"]
        JSON_C["JsonEditor"]
        GANTT_C["GanttChart"]
        WORK_C["WorkloadChart"]
        PIE_C["StatusPieChart"]
        TASK_C["TaskController"]
        METRICS["MetricCards"]
    end
    
    INIT --> PROJ_STATE
    IMPORT --> PROJ_STATE
    TEXTAREA --> JSON_STATE
    TEXTAREA -->|debounced| PROJ_STATE
    DRAG --> PROJ_STATE
    SELECT --> PROJ_STATE
    FILTER --> FILT_STATE
    THEME_BTN --> THEME_STATE
    
    FILT_STATE --> LOCAL
    VIEW_STATE --> LOCAL
    THEME_STATE --> LOCAL
    
    PROJ_STATE --> PROGRESS
    PROJ_STATE --> FILTERED
    PROJ_STATE --> MINSTART
    FILT_STATE --> FILTERED
    FILTERED --> GANTT
    MINSTART --> GANTT
    PROJ_STATE --> WORKLOAD
    PROJ_STATE --> STATUS
    
    PROGRESS --> METRICS
    FILTERED --> TASK_C
    GANTT --> GANTT_C
    WORKLOAD --> WORK_C
    STATUS --> PIE_C
    JSON_STATE --> JSON_C
    THEME_STATE --> HEADER_C
    VIEW_STATE --> HEADER_C
    
    style PROJ_STATE fill:#3b82f6,color:#fff
    style COMPUTE fill:#a855f7,color:#fff
    style LOCAL fill:#22c55e,color:#000
    style RENDER fill:#f59e0b,color:#000
Quick Reference: All Diagrams
#DiagramTypePurpose1Bug Audit FlowFlowchartSeverity, fix status, resolution path2aDnD FlowFlowchartDrag → splice → state update → re-render2bTheme State MachineState DiagramDark ↔ light transitions + persistence2cMilestone Overlay PipelineFlowchartDate parse → scale → SVG line/text render3Module Dependency GraphFlowchartImport relationships across all files3bDirectory TreeFlowchartFilesystem layout visualization4aTest Suite OrganizationFlowchart17 test cases grouped by suite4bCI PipelineFlowchartTrigger → checkout → install → test → build4cCoverage Gap AnalysisFlowchartWhat's tested vs what needs tests5Full System Data FlowFlowchartEnd-to-end input → state → compute → render
You can paste any of these into a Markdown file rendered by GitHub, or into mermaid.live for standalone preview. Want me to create a docs/diagrams.md file with all of these embedded for the repo?

git add docs/diagrams.md git commit -m 
"docs: add architecture diagrams"
git push origin docs/new
