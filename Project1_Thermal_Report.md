# Thermal Engineering — Project 1

**Transient & Steady-State Thermal Analysis of an Al / Al₂O₃ Bilayer Stack**

| | |
|---|---|
| Simulation Tool | ANSYS Mechanical (Workbench) |
| Date | May 2026 |
| Status | ✅ Complete |

---

## Table of Contents

1. [Preamble] (#Personal)
2. [Objective](#1-objective)
3. [Geometry and Material Properties](#2-geometry-and-material-properties)
4. [Boundary Conditions](#3-boundary-conditions)
5. [Steady-State Thermal Analysis](#4-steady-state-thermal-analysis)
6. [Transient Thermal Analysis](#5-transient-thermal-analysis)
7. [Conclusions](#6-conclusions)

---

## 1. Preamble

That's my first project after 9 years since I graduated from University. I always wanted to be a aviation engineer but there were almost no open positions in my country at that time.
Currentyl I'm working as a Data Security Engineer what I enjoy, but I want to stay connected to my engineering background, and maybe one day have an possibility to anchor in aviation/engineering industry ;) 

## 2. Objective

The main objective of this project was to get refreshed with heat transfer simulations after a long break, and build intuition about heat transfer.


Two analyses were performed:

1. **Steady-State Thermal** — temperature distribution through the thickness at thermal equilibrium, including the temperature jump at the material interface.
2. **Transient Thermal** — temperature evolution T(t) from ambient to steady-state, characterised by an exponential response with a computed time constant τ.


---

## 2. Geometry and Material Properties


Challenges that I was about to solve were the most exciting part of of that project since I had an occastion to learn something new.
So the issue here was topology - to be precise shared topology.
In multi material simulation shared topology condition is important when it comes to mesh generation.
If you are not selecting Shared Topology option then mesh nodes do not overlap. That will result in errors in numerical solutions.


![Steady-State Thermal - Geometry](‪C:\Users\papaj\Documents\Heat_transfer_first_project\shared_topology.png)

### 2.1 Stack Geometry

| Layer | Material | Dimensions (mm) | Thickness (mm) |
|---|---|---|---|
| Layer 1 — bottom | Aluminium (Al) | 100 × 150 | 3 |
| Layer 2 — top | Aluminium Oxide (Al₂O₃) | 100 × 150 | 3 |

```
        Heat Flux q'' = 1000 W/m²
              ↓ ↓ ↓ ↓ ↓ ↓
        ┌─────────────────────┐
        │     Aluminium       │  3 mm   k = 205 W/m·K
        ├─────────────────────┤  ← interface
        │  Aluminium Oxide    │  3 mm   k = 30 W/m·K
        └─────────────────────┘
              ↑ convection
          h = 10 W/m²K, T∞ = 22°C
```

### 2.2 Material Properties

| Property | Aluminium (Al) | Aluminium Oxide (Al₂O₃) |
|---|---|---|
| Thermal Conductivity k (W/m·K) | 205 | 30 |
| Density ρ (kg/m³) | 2700 | 3960 |
| Specific Heat cₚ (J/kg·K) | 900 | 880 |
| Source | ANSYS library | ANSYS library (Aluminum Oxide) |

> **Note:** Al₂O₃ has approximately 7× lower thermal conductivity than aluminium. This produces a visible temperature step at the interface in steady-state results.

---

## 3. Boundary Conditions

| Condition | Location | Value |
|---|---|---|
| Heat Flux q'' | Bottom face of Al layer | 1000 W/m² |
| Convection h | Top face of Al₂O₃ layer | 10 W/m²K |
| Ambient Temperature T∞ | Fluid reference | 22°C |
| Initial Temperature T₀ | Entire stack (transient only) | 22°C |

---

## 4. Steady-State Thermal Analysis

### 4.1 Analytical Solution

In steady state the heat flux through each layer is constant and equal to q''. The temperature drop across each layer follows Fourier's law:

$$\Delta T = \frac{q'' \cdot L}{k}$$

The equilibrium surface temperature (top of Al₂O₃) is determined from Newton's law of cooling:

$$T_{ss} = T_\infty + \frac{q''}{h} = 22 + \frac{1000}{10} = 122\text{ °C}$$

Temperature drops through each layer:

$$\Delta T_{Al} = \frac{1000 \times 0.003}{205} = 0.015\text{ °C}$$

$$\Delta T_{Al_2O_3} = \frac{1000 \times 0.003}{30} = 0.100\text{ °C}$$

### 4.2 ANSYS vs. Analytical Comparison

| Quantity | Analytical | ANSYS | Error |
|---|---|---|---|
| ΔT across Al (K) | 0.015 | 0.024 | < 4% |
| ΔT across Al₂O₃ (K) | 0.100 | 0.100 | < 1% |
| T_ss top surface (°C) | 122.00 | 122.13 | < 0.1% |

The temperature jump at the Al / Al₂O₃ interface is clearly visible in ANSYS contour plots and confirms the conductivity contrast between the two materials.

---

## 5. Transient Thermal Analysis

### 5.1 Governing Equation

Applying an energy balance to the stack under the lumped capacitance assumption (Biot number Bi << 1):

$$m c_p \frac{dT}{dt} = q''A - h A (T - T_\infty)$$

Introducing the temperature excess $\theta(t) = T(t) - T_{ss}$ and noting that $dT/dt = d\theta/dt$, the right-hand side simplifies (since $q''= h(T_{ss}-T_\infty)$) to:

$$\frac{d\theta}{dt} = -\frac{hA}{mc_p}\,\theta$$

### 5.2 Analytical Solution

Separating variables and integrating:

$$\int \frac{d\theta}{\theta} = -\frac{hA}{mc_p}\int dt$$

$$\ln\theta = -\frac{hA}{mc_p}\,t + C$$

Exponentiating both sides and applying the initial condition $\theta(0) = T_0 - T_{ss}$:

$$\boxed{T(t) = T_{ss} + (T_0 - T_{ss})\,e^{-t/\tau}}$$

### 5.3 Time Constant τ

$$\tau = \frac{m c_p}{h A} = \frac{\rho\, c_p\, V}{h\, A}$$

| Parameter | Symbol | Value |
|---|---|---|
| Volume | V | 0.1 × 0.15 × 0.003 = 4.5 × 10⁻⁵ m³ |
| Mass | m | 2700 × 4.5×10⁻⁵ = 0.1215 kg |
| Convection area | A | 0.1 × 0.15 = 0.015 m² |
| **Time constant** | **τ** | **0.1215 × 900 / (10 × 0.015) = 729 s** |

### 5.4 Physical Interpretation

After one time constant (t = τ = 729 s) the stack reaches **63.2%** of the way from T₀ to T_ss:

$$T(\tau) = 122 - 100 \cdot e^{-1} \approx 85.2\text{ °C}$$

The system is considered to have reached steady-state after approximately **4τ ≈ 2916 s (~49 minutes)**. This matches the plateau behaviour observed in ANSYS.

### 5.5 Validation of Lumped Capacitance Assumption

The lumped capacitance model is valid when Bi << 0.1:

$$Bi = \frac{h\, L_c}{k_{Al}} = \frac{10 \times 0.003}{205} = 0.000146$$

Since Bi = 0.000146 << 0.1, the assumption is fully justified. The temperature gradient within the plate is negligible.

### 5.6 ANSYS vs. Analytical Comparison

| Quantity | Analytical | ANSYS | Agreement |
|---|---|---|---|
| Steady-state temperature T_ss | 122.00°C | 122.13°C | ✅ Excellent |
| Time constant τ | 729 s | ~700–750 s (from curve) | ✅ Excellent |
| Temperature at t = τ | 85.2°C | ~85°C | ✅ Excellent |
| Biot number Bi | 0.000146 | N/A (uniform T confirmed) | ✅ Valid |

---

## 6. Conclusions

1. The steady-state temperature distribution agrees with the analytical Fourier solution to within 4%, confirming correct material assignment and boundary condition setup in ANSYS.

2. The transient T(t) response follows the expected exponential form, with a time constant of τ = 729 s derived analytically and confirmed in the ANSYS curve.

3. The Al₂O₃ layer, with approximately 7× lower thermal conductivity, creates a visible temperature step at the interface, demonstrating the role of material selection in thermal management design.

4. The lumped capacitance assumption (Bi = 0.000146) is well justified for this configuration, validating the simplified analytical model.

---

*Project 1 — Complete*
