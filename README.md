# Research-K25-Team-2
This study examines how many distribution centers (DCs) an Australian retailer should open, and where, to balance transport cost, delivery distance, and inventory. Opening more DCs brings products closer to branches, but it also increases total inventory, so the goal is a reasonable balance rather than the minimum of any single measure. We use and compare two methods, a fast heuristic and an exact mixed-integer linear programming (MILP) model, to see how close the heuristic gets to the optimal solution. We also test whether the recommended network stays stable when key assumptions change.

## Flowchart
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

