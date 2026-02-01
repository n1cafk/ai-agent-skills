---
name: mermaid-diagrams
description: Create diagrams and visualizations using Mermaid syntax. Use when generating flowcharts, sequence diagrams, class diagrams, ER diagrams, Gantt charts, or any visual documentation.
license: MIT
source: hoodini/ai-agents-skills
---

# Mermaid Diagrams

## Flowchart

```mermaid
flowchart TD
    A[Start] --> B{Is it valid?}
    B -->|Yes| C[Process data]
    B -->|No| D[Show error]
    C --> E[End]
```

### Syntax
```
flowchart TD  %% TD = top-down, LR = left-right
    A[Rectangle]    %% Square brackets = rectangle
    B(Rounded)      %% Parentheses = rounded
    C{Diamond}      %% Curly braces = decision
    E[(Database)]   %% Cylinder shape
    
    A --> B         %% Arrow
    B -.-> C        %% Dotted arrow
    C ==> D         %% Thick arrow
```

## Sequence Diagram

```mermaid
sequenceDiagram
    participant U as User
    participant S as Server
    U->>S: POST /api/data
    activate S
    S-->>U: 200 OK
    deactivate S
```

## Class Diagram

```mermaid
classDiagram
    class User {
        +String id
        +String name
        +login()
    }
    User "1" --> "*" Order : places
```

## ER Diagram

```mermaid
erDiagram
    USER ||--o{ ORDER : places
    ORDER ||--|{ LINE_ITEM : contains
```

## Gantt Chart

```mermaid
gantt
    title Project Timeline
    dateFormat YYYY-MM-DD
    section Planning
    Requirements :a1, 2024-01-01, 7d
    Design       :a2, after a1, 14d
```

## State Diagram

```mermaid
stateDiagram-v2
    [*] --> Idle
    Idle --> Processing: Submit
    Processing --> Success: Valid
    Processing --> Error: Invalid
```

## Pie Chart

```mermaid
pie title Browser Market Share
    "Chrome" : 65
    "Safari" : 19
    "Firefox" : 10
```

## Resources

- **Mermaid Docs**: https://mermaid.js.org/
- **Live Editor**: https://mermaid.live
