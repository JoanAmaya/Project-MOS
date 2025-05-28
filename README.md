##  Authors

| Code      | Username                   | Full Name                      |
|-----------|----------------------------|--------------------------------|
| 202110756 | d.barbosam@uniandes.edu.co | David Mateo Barbosa Monsalve   |
| 202011318 | j.amayab@uniandes.edu.co   | Joan Sebastian Amaya Bohórquez |
| 202012455 | n.lara@uniandes.edu.co     | Nicolas Lara Gómez             |

---

## 📊 Analysis of Results and Visualization

The following figures illustrate the routing solution for the three problem scenarios defined in the project. Each graph visually represents how cargo is delivered across the network, showing the movement of vehicles between depots, clients, and stations (when applicable). Each vehicle is assigned a distinct color, and the nodes are shaped according to their role in the model.

---

###  Case I – Basic Routing with Capacity Constraints

- This scenario contains only depots and clients.
- Vehicles are constrained by their maximum cargo capacity.
- Each client is visited exactly once.
- The objective function minimizes transport and maintenance costs.

 **CSV Output**: [`model/I/solution.csv`](/models/I/solution.csv)

---

###  Case II – Routing with Fuel Autonomy and Refueling

- This scenario includes fuel stations and autonomy constraints.
- Vehicles may refuel at designated stations when needed.
- The model ensures fuel sufficiency across the route through explicit fuel flow constraints.
- Capacity and routing rules from Case I still apply.

 **CSV Output**: [`model/II/solution.csv`](/models/II/solution.csv)

---

###  Case III – Routing with Refueling and Tolls

- Toll costs are added to the previous case, including both fixed per-client fees and variable surcharges based on delivered cargo.
- The solver must balance tolls, refueling, cargo, and distance.
- Despite the increased complexity, the solver finds a feasible configuration that respects delivery, capacity, and autonomy requirements.

 **CSV Output**: [`model/III/solution.csv`](/models/III/solution.csv)

---

>  **Note:** Given the computational weight of the three scenarios—particularly Case III—the solver may terminate before reaching global optimality. Nonetheless, all key requirements are respected in the output: cargo limits, autonomy, refueling rules, and toll structures. Routes may not form perfectly optimal cycles but are valid, coherent, and interpretable under real constraints.

---

##  Implementation Rationale and Structure

### Unified Model Design

The original model implemented in Part 1 was redesigned to support a unified and extendable architecture capable of handling all three problem variations. The updated formulation allows activation of features like fuel stations and tolls on demand, avoiding the need to rewrite constraints for each scenario.

This restructuring was necessary to:

- Model cargo delivery, refueling behavior, and toll-related costs within one logical structure.
- Ensure scalable testing without manually changing between versions of the model.
- Support conditional constraint inclusion for cases II and III.

---

### Testing Methodology and Departure Enforcement

All vehicles are enforced to **depart from a depot**, even if they are unused. This simplifies test control and avoids the need to define scenario-specific rules or to reconstruct the model per case. It also enables a consistent solution-building pipeline within a single execution loop.

This choice favors reproducibility and clarity during verification while maintaining solver flexibility.

---

### Robust and Modular Implementation

- Constraints, variables, and objectives are declared through cleanly separated functions.
- In-line comments explain the purpose of each constraint block (e.g., flow conservation, MTZ for subtour elimination, fuel propagation).
- The route reconstruction avoids runtime errors caused by incomplete cycles through robust `dict.pop(..., 0)` logic.
- Fuel and cargo flow are enforced using Big-M constraints when nodes are not visited, maintaining feasibility without over-constraining the model.

---

### Centralized Execution in `main.ipynb`

A single Jupyter Notebook—[`main.ipynb`](/main.ipynb)—manages the full execution flow across all three cases. It loads data, builds the solver, adjusts flags dynamically, and visualizes results without code duplication.

This structure supports clarity, modularity, and reuse, which are crucial for maintainability and evaluation.

---
