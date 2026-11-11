# Research-K25-Team-2
This study examines how many distribution centers (DCs) an Australian retailer should open, and where, to balance transport cost, delivery distance, and inventory. Opening more DCs brings products closer to branches, but it also increases total inventory, so the goal is a reasonable balance rather than the minimum of any single measure. We use and compare two methods, a fast heuristic and an exact mixed-integer linear programming (MILP) model, to see how close the heuristic gets to the optimal solution. We also test whether the recommended network stays stable when key assumptions change.

# Data Model
```mermaid
%%{init: {"theme": "base", "themeVariables": {
  "fontFamily": "Inter, Segoe UI, Arial, sans-serif",
  "fontSize": "15px",
  "primaryColor": "#eef2ff",
  "primaryBorderColor": "#6366f1",
  "primaryTextColor": "#1e1b4b",
  "lineColor": "#64748b"
}}}%%
erDiagram
    dim_scenario {
        string scenario_id PK
        string description
    }
    dim_branch {
        string branch_id PK
        string state
        string sa4
        float  total_cbm
        float  total_kg
    }
    dim_candidate {
        string dc_id PK
        string state
        float  lat
        float  lon
    }
    dim_cost_curve {
        string state
        string mode
        float  intercept
        float  slope
        float  min_km
        float  max_km
    }
    fact_runs {
        string run_id PK
        string scenario_id FK
        string method
        int    k
        float  delivery_cost
        float  storage_cost
        float  handling_cost
        float  total_cost
        float  mean_km
        float  p95_km
        float  coverage_250_500
        float  runtime_sec
        float  gap_pct
    }
    fact_assignments {
        string run_id FK
        string branch_id FK
        string dc_id FK
    }
    fact_lane_cost {
        string branch_id FK
        string dc_id FK
        float  distance_km
        float  lane_cost
    }

    dim_scenario  ||--o{ fact_runs        : "1 scenario, many runs"
    fact_runs     ||--o{ fact_assignments : "1 run, 635 branches"
    dim_branch    ||--o{ fact_assignments : ""
    dim_candidate ||--o{ fact_assignments : ""
    dim_branch    ||--o{ fact_lane_cost   : ""
    dim_candidate ||--o{ fact_lane_cost   : ""
    dim_cost_curve }o..o{ dim_candidate   : "curve by DC state"
```

# Flowchart
```mermaid
%%{init: {
  "theme": "base",
  "themeVariables": {
    "fontFamily": "Inter, Segoe UI, Helvetica, Arial, sans-serif",
    "fontSize": "16px",
    "lineColor": "#64748b"
  },
  "flowchart": { "wrappingWidth": 520, "nodeSpacing": 40, "rankSpacing": 45, "padding": 18 }
}}%%
flowchart TD
    A["<b>1. Raw data</b><br/>635 branches, demand, cost curves"]
    B["<b>2. Clean data and build inputs</b><br/>Demand per branch, 88 candidate DCs, distances, delivery costs"]
    C["<b>3. Choose K DCs (K = 1 to 6)</b><br/>MILP (exact) vs Heuristic (fast)"]
    D["<b>4. Evaluate both with the same formulas</b><br/>Cost, distance, coverage, runtime"]
    E["<b>5. Test 7 changed assumptions</b><br/>Cost, demand, distance, state mapping"]
    F["<b>6. Conclusion</b><br/>How many DCs, where, and how stable"]

    A --> B --> C --> D --> E --> F

    classDef s1 fill:#e0f2fe,stroke:#0284c7,stroke-width:2px,color:#0c4a6e;
    classDef s2 fill:#dcfce7,stroke:#16a34a,stroke-width:2px,color:#14532d;
    classDef s3 fill:#fef9c3,stroke:#ca8a04,stroke-width:2px,color:#713f12;
    classDef s4 fill:#ffedd5,stroke:#ea580c,stroke-width:2px,color:#7c2d12;
    classDef s5 fill:#fae8ff,stroke:#c026d3,stroke-width:2px,color:#701a75;
    classDef s6 fill:#fee2e2,stroke:#dc2626,stroke-width:2px,color:#7f1d1d;

    class A s1
    class B s2
    class C s3
    class D s4
    class E s5
    class F s6
```

