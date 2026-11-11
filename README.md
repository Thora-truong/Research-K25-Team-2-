# Research-K25-Team-2
This study examines how many distribution centers (DCs) an Australian retailer should open, and where, to balance transport cost, delivery distance, and inventory. Opening more DCs brings products closer to branches, but it also increases total inventory, so the goal is a reasonable balance rather than the minimum of any single measure. We use and compare two methods, a fast heuristic and an exact mixed-integer linear programming (MILP) model, to see how close the heuristic gets to the optimal solution. We also test whether the recommended network stays stable when key assumptions change.

Here is the flowchart of report: 
```mermaid
flowchart TD
    A["Raw data<br/>Research_Project_Data.xlsx<br/>(read-only, 4 sheets)"]

    subgraph P1["1. Data preparation (Notebook 1)"]
        B["Audit and aggregate by DestinationRef<br/>635 branches, total CBM and kg<br/>flag missing SA4 and duplicate rows"]
        C["Processed_Branch_Master.xlsx"]
        D["Candidate set<br/>largest-CBM branch per SA4 + current DC<br/>= 88 candidates"]
        E["Distance matrix (haversine)<br/>branch x candidate"]
    end

    subgraph P2["2. Model inputs (Notebook 2)"]
        F["Cost matrix c_ij<br/>intercept + slope x distance<br/>curve by DC state, FTL/LTL band"]
        G["Storage = s x I x sqrt(K)<br/>Handling = h x total CBM"]
        H["Baseline check: current 1-DC network<br/>total = 2,141,669 AUD"]
    end

    subgraph P3["3. Solve for each K = 1..6"]
        I["Exact MILP<br/>choose K DCs + assign branches"]
        J["Heuristic<br/>Greedy-Add then One-Swap"]
        K["Shared evaluator<br/>same cost and service metrics for both"]
    end

    subgraph P4["4. Experiments: 54 runs"]
        L["S0 base: MILP + Heuristic<br/>12 runs, answers RQ1 and RQ2"]
        M["S1-S7 robustness: MILP only<br/>42 runs, answers RQ3"]
    end

    N["Results files<br/>runs_all.csv, assignments.csv,<br/>facilities.csv, kstar_summary.csv"]
    O["Report and slides<br/>tables and figures generated from result files<br/>conditional recommendation for K and DC locations"]

    A --> B --> C
    C --> D --> E
    C --> F
    E --> F
    F --> G
    F --> H
    F --> I
    F --> J
    G --> K
    I --> K
    J --> K
    K --> L
    K --> M
    L --> N
    M --> N
    N --> O
```

```mermaid
flowchart LR
    S0["S0 Base"] --> Q1["RQ1: effect of K<br/>RQ2: heuristic vs MILP gap"]
    S1["S1 Transport cost +20%"] --> Q3["RQ3: does best K<br/>or DC location change?"]
    S2["S2 Transport cost -20%"] --> Q3
    S3["S3 CBM-weighted cost"] --> Q3
    S4["S4 kg instead of CBM"] --> Q3
    S5["S5 QLD and WA demand +20%"] --> Q3
    S6["S6 Different state mapping<br/>for NT and SA"] --> Q3
    S7["S7 Distance x 1.20"] --> Q3
```

