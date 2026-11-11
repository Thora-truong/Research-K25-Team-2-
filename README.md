# Research-K25-Team-2
This study examines how many distribution centers (DCs) an Australian retailer should open, and where, to balance transport cost, delivery distance, and inventory. Opening more DCs brings products closer to branches, but it also increases total inventory, so the goal is a reasonable balance rather than the minimum of any single measure. We use and compare two methods, a fast heuristic and an exact mixed-integer linear programming (MILP) model, to see how close the heuristic gets to the optimal solution. We also test whether the recommended network stays stable when key assumptions change.

# Data Model
```mermaid
%%{init: {"theme": "base", "themeVariables": {"fontFamily": "Inter, Segoe UI, Arial, sans-serif", "fontSize": "14px"}}}%%
erDiagram
    OUTBOUND_RAW {
        string DestinationRef FK
        string GroupID
        string Section
        float  KG
        float  CBM
    }
    BRANCH_MASTER_RAW {
        string DestinationRef PK
        string ShipToID
        string SA4
        string State
        float  Latitude
        float  Longitude
    }
    COST_CURVE_RAW {
        string State
        string Mode
        float  Intercept
        float  Slope
        float  MinKM
        float  MaxKM
    }
    CURRENT_SCENARIO_RAW {
        string Facility PK
        float  Latitude
        float  Longitude
        float  AvgInventoryCBM
        float  StorageRate
        float  HandlingRate
    }

    BRANCH_DEMAND {
        string DestinationRef PK
        float  total_cbm
        float  total_kg
        string State
        bool   missing_sa4_flag
        bool   candidate_base_flag
    }
    CANDIDATE {
        string dc_id PK
        string State
        float  Latitude
        float  Longitude
    }

    DISTANCE {
        string branch_id PK, FK
        string dc_id PK, FK
        float  distance_km
    }
    COST {
        string branch_id PK, FK
        string dc_id PK, FK
        float  lane_cost
    }
    SCENARIO {
        string scenario_id PK
        string description
    }

    RUN {
        string run_id PK
        string scenario_id FK
        string method
        int    k
        float  delivery_cost
        float  storage_cost
        float  handling_cost
        float  total_modeled_cost
        float  weighted_mean_km
        float  weighted_p95_km
        float  coverage_250
        float  coverage_500
        float  runtime_seconds
        string solver_status
        float  heuristic_gap_pct
    }
    FACILITY_OPEN {
        string run_id PK, FK
        string dc_id PK, FK
    }
    ASSIGNMENT {
        string run_id PK, FK
        string branch_id PK, FK
        string dc_id FK
    }

    OUTBOUND_RAW }o--|| BRANCH_MASTER_RAW : "aggregate by DestinationRef"
    BRANCH_MASTER_RAW ||--|| BRANCH_DEMAND : "join + sum"
    BRANCH_DEMAND ||--o| CANDIDATE : "largest CBM per SA4"
    CURRENT_SCENARIO_RAW ||--o| CANDIDATE : "add NSW-DC01"
    BRANCH_DEMAND ||--o{ DISTANCE : "from"
    CANDIDATE ||--o{ DISTANCE : "to"
    DISTANCE ||--|| COST : "apply cost curve"
    COST_CURVE_RAW ||--o{ COST : "by DC state"
    SCENARIO ||--o{ RUN : "has"
    RUN ||--o{ FACILITY_OPEN : "opens"
    RUN ||--o{ ASSIGNMENT : "assigns"
    CANDIDATE ||--o{ FACILITY_OPEN : "is"
    BRANCH_DEMAND ||--o{ ASSIGNMENT : "served"
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

