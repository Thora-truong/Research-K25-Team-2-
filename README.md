# Research-K25-Team-2
This study examines how many distribution centers (DCs) an Australian retailer should open, and where, to balance transport cost, delivery distance, and inventory. Opening more DCs brings products closer to branches, but it also increases total inventory, so the goal is a reasonable balance rather than the minimum of any single measure. We use and compare two methods, a fast heuristic and an exact mixed-integer linear programming (MILP) model, to see how close the heuristic gets to the optimal solution. We also test whether the recommended network stays stable when key assumptions change.

Here is the flowchart of report: 
```mermaid
flowchart TD
    A["1. Raw data<br/>635 branches, demand, cost curves"]
    B["2. Clean data and build inputs<br/>demand per branch, 88 candidate DCs,<br/>distances, delivery costs"]
    C["3. Choose K DCs (K = 1 to 6)<br/>MILP (exact) vs Heuristic (fast)"]
    D["4. Evaluate both with the same formulas<br/>cost, distance, coverage, runtime"]
    E["5. Test 7 changed assumptions<br/>cost, demand, distance, state mapping"]
    F["6. Conclusion<br/>how many DCs, where, and how stable"]

    A --> B --> C --> D --> E --> F
```
