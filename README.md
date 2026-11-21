# Composite Wing: Unidirectional (UD) vs. 2x2 Twill Woven Laminate Comparison

ANSYS ACP (Pre) + ANSYS Mechanical study of a NACA 2412 carbon-fibre wing section (`100 mm` chord, `500 mm` span) under a distributed `250 N` load, comparing an 8-ply unidirectional (`UD`) layup against an 8-ply `2x2 Twill` woven layup. Includes Classical Laminate Theory (CLT) hand checks (`hand_calc/clt_check.py`), kinematic tip-twist calculations, and ply failure assessments using **Maximum Stress**, **Tsai-Wu**, **Hashin**, and **Puck** criteria.

> **Note on Project Images:** All ANSYS ACP thickness/layup plots, mesh views, boundary conditions, deformation contours, and Composite Failure Tool screenshots for both runs are uploaded in the repository above.

---

## 1. Models Compared

| Parameter | Run A: Unidirectional (`UD`) | Run B: `2x2 Twill` Woven |
| :--- | :--- | :--- |
| **Material** | `Epoxy Carbon UD (230 GPa) Prepreg` | `Epoxy Carbon Woven (230 GPa) Prepreg` (`2x2 Twill`) |
| **Layup (bottom to top)** | `[0 / 25 / 0 / 25]s` (8 plies) | `[0 / 45 / 0 / 45]s` (8 plies) |
| **Ply Thickness** | `0.15 mm` | `0.15 mm` |
| **Total Laminate Thickness** | `1.20 mm` | `1.20 mm` |
| **Layup Symmetry & Balance** | Symmetric, unbalanced (`B = 0`; non-zero `A16`, `D16`) | Symmetric, balanced (`B = 0`, `A16 = 0`, `D16 = 0`) |
| **Geometry** | Open single aerofoil skin (NACA 2412 upper skin, `100 mm` chord, `500 mm` span; area `51,058 mm²`) | Closed NACA 2412 aerofoil shell (`100 mm` chord, `500 mm` span) |
| **Mesh** | `11,488` shell elements (`3 mm` sizing) | `11,488` shell elements |
| **Boundary Condition** | Fixed support on root edges (`redge(ACP (Pre))`) | Fixed support on root edges (`redge(ACP (Pre))`) |
| **Load** | `250 N` in `-Y` on wing skin (`skin(ACP (Pre))`), ramped over `1 s` | `250 N` in `-Y` on wing skin (`skin(ACP (Pre))`), ramped over `1 s` |
| **Run Date & Software** | 16 Nov 2026 — ANSYS 2026 R1 (Student Licence) | 20 Nov 2026 — ANSYS 2026 R1 (Student Licence) |

---

## 2. Structural & Failure Results

| Result | Run A: `UD` (Open Skin, `1.20 mm`) | Run B: `2x2 Twill` Woven (Closed Shell, `1.20 mm`) |
| :--- | :---: | :---: |
| **Maximum total deformation** | `70.3 mm` | `15.515 mm` (`15.5 mm`) |
| **Mean total deformation** | `28.4 mm` | `6.78 mm` |
| **Tip deflection (`Y`), leading-edge / trailing-edge** | `-69.694 mm` / `-70.317 mm` | `[x]` mm / `[x]` mm (`Directional Deformation` & `2`) |
| **Tip twist angle ($\Delta\theta$)** | `0.36°` (`0.357°` active bend-twist coupling) | `~0.00°` (uniform spanwise bending; $D_{16} = 0$) |
| **Maximum equivalent stress (ply reference)** | `~215 MPa` (localized at root corner) | `55.157 MPa` (distributed across root leading edge) |
| **Baseline Max IRF (Tsai-Wu / Max Stress)** | `3.13` (Mean `0.40`) | `0.24062` (`0.24`, Mean `0.052`) |
| **Reserve Factor (`1 / Max IRF`)** | `0.32` (**Fails** at root in `25°` plies) | `4.16` (**Passes** with high margin) |
| **Active Failure Criteria (Multi-Criteria Run)** | Tsai-Wu (`tw`), Hashin (`hf, hm`), Puck (`pf, pmA–C`) | Max Stress, Tsai-Wu (`tw`), Hashin (`hf, hm`), Puck (`pf, pmA–C`) |

*An `IRF > 1.0` indicates predicted ply failure; `IRF < 1.0` indicates the laminate survives, with `1 / IRF` representing the safety factor to failure.*

### How to Read This Comparison
1. **Section Geometry Drives the Stiffness Jump (`70.3 mm` vs. `15.5 mm`):** On an identical cross-section, the `[0 / 45 / 0 / 45]s` woven laminate has a *lower* spanwise in-plane modulus ($E_x = 43.3\text{ GPa}$) than the `[0 / 25 / 0 / 25]s` UD laminate ($E_x = 77.6\text{ GPa}$). Run B deflects `4.5×` less because it is modelled as a **closed aerofoil shell** ($I_{\text{closed}} \approx 7.9\times$ larger than the open upper skin $I_{\text{open}}$).
2. **Weave Architecture Eliminates Root Matrix Failure (`IRF 3.13` vs. `0.24`):** Bidirectional `0°/90°` fibres in the `2x2 Twill` weave raise transverse tensile strength from `29 MPa` to `805 MPa` and shear strength from `60 MPa` to `125 MPa`, eliminating the resin cracking seen in Run A's `25°` UD plies.
3. **Bend-Twist Coupling Trade-Off:** Run A's unbalanced `+25°` plies generate non-zero $D_{16}/D_{11} = 0.098$, producing `0.36°` of elastic tip twist (which can be flipped to `-25°` for passive nose-down aerodynamic washout). Run B's balanced weave sets $D_{16} = 0$, giving zero laminate-induced twist.

---

## 3. Hand Calculations (`hand_calc/clt_check.py`)

Material constants verified against ANSYS Engineering Data defaults:
* **`Epoxy Carbon UD (230 GPa) Prepreg`:** $E_1 = 121\text{ GPa}$, $E_2 = 8.6\text{ GPa}$, $G_{12} = 4.7\text{ GPa}$, $\nu_{12} = 0.27$; $X_t = 2231\text{ MPa}$, $X_c = 1082\text{ MPa}$, $Y_t = 29\text{ MPa}$, $Y_c = 100\text{ MPa}$, $S_{12} = 60\text{ MPa}$, $S_{23} = 32\text{ MPa}$.
* **`Epoxy Carbon Woven (230 GPa) Prepreg`:** $E_1 = E_2 = 61.34\text{ GPa}$, $G_{12} = 3.30\text{ GPa}$, $\nu_{12} = 0.04$; $X_t = Y_t = 805\text{ MPa}$, $X_c = Y_c = 509\text{ MPa}$, $S_{12} = 125\text{ MPa}$, $S_{23} = 65\text{ MPa}$.

### 3.1 Classical Laminate Theory (CLT) Stiffness (`h = 1.20 mm`)

| Property | Run A: `UD [0/25/0/25]s` | Run B: `Woven [0/45/0/45]s` |
| :--- | :---: | :---: |
| **Laminate Thickness ($h$)** | `1.20 mm` | `1.20 mm` |
| **In-Plane Modulus ($E_x$)** | `77.6 GPa` | `43.3 GPa` |
| **Flexural Modulus ($E_{x,\text{flex}}$)** | `96.2 GPa` | `51.1 GPa` |
| **In-Plane Shear Modulus ($G_{xy}$)** | `9.0 GPa` | `16.3 GPa` |
| **Bend-Twist Coupling ($D_{16} / D_{11}$)** | `0.098` | `0.000` |
| **Shear-Extension Coupling ($A_{16} / A_{11}$)** | `0.168` | `0.000` |
| **Coupling Matrix ($[B]$)** | `0` (symmetric) | `0` (symmetric) |

### 3.2 Cantilever Tip Deflection Check ($\delta = \frac{w L^4}{8 E_x I}$)

| Cross-Section (`NACA 2412`, `c = 100 mm`, `L = 500 mm`) | `UD 1.20 mm` | `Woven 1.20 mm` |
| :--- | :---: | :---: |
| **Open single skin ($I_{\text{open}} \approx 640\text{ mm}^4$)** | `78.6 mm` | `140.9 mm` |
| **Closed aerofoil shell ($I_{\text{closed}} \approx 5060\text{ mm}^4$)** | `9.9 mm` | `18.5 mm` |
| **ANSYS FEA Result** | **`70.3 mm`** *(open skin)* | **`15.515 mm`** *(closed shell)* |

### 3.3 Kinematic Tip Twist Angle
$$\Delta\theta = \arctan\left(\frac{|\delta_{\text{TE}}| - |\delta_{\text{LE}}|}{c}\right)$$
* **Run A (`UD`):** $\Delta\theta = \arctan\left(\frac{70.317 - 69.694}{100}\right) = \mathbf{0.357^\circ \approx 0.36^\circ}$.
* **Run B (`Woven`):** $D_{16} = 0$ eliminates laminate-induced twist (`~0.00°`; verify exact `Y` tip-edge probes from `Directional Deformation` & `Directional Deformation 2`).

### 3.4 Root Ply Failure Check (Linear `IRF = 1 / RF`)
Using root bending moment $M = F L / 2 = 62.5\text{ N}\cdot\text{m}$ and linear `IRF` ($\sqrt{\text{FI}}$ for quadratic criteria):

| Case & Ply | $\sigma_1$ (MPa) | $\sigma_2$ (MPa) | $\tau_{12}$ (MPa) | Max Stress IRF | Hashin Fibre IRF | Hashin Matrix IRF | Tsai-Wu IRF |
| :--- | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| **Run A (`UD` Open) — Tension `0°` ply** | `816.0` | `-10.7` | `-39.3` | `0.65` | `0.75` | `0.00` | `0.45` |
| **Run A (`UD` Open) — Tension `25°` ply** | `225.0` | `20.6` | `-60.6` | **`1.01`** | `1.01` | **`1.24`** | **`1.24`** |
| **Run A (`UD` Open) — Comp. `0°` ply** | `-385.0` | `5.0` | `18.5` | `0.36` | `0.36` | `0.36` | `0.70` |
| **Run A (`UD` Open) — Comp. `25°` ply** | `-106.0` | `-9.7` | `28.6` | `0.48` | `0.10` | `0.00` | `0.24` |
| **Run B (`Woven` Closed) — Root `0°` ply** | `75.1` | `-1.5` | `0.0` | `0.09` | `0.09` | `0.00` | `0.09` |
| **Run B (`Woven` Closed) — Root `45°` ply** | `36.8` | `36.8` | `-19.8` | `0.16` | `0.16` | `0.16` | `0.16` |

*(Beam theory predicts `IRF = 1.24` in the `25°` UD plies vs. `3.13` in FEA because 1D beam equations neglect restrained Poisson contraction and corner stress concentrations at the clamped root.)*

---

## 4. Hashin & Puck Failure Theories (2D Plane-Stress)

* **Hashin (`hf`, `hm`):** Evaluates four separate failure modes—fibre tension, fibre compression, matrix tension ($\left(\frac{\sigma_2}{Y_t}\right)^2 + \left(\frac{\tau_{12}}{S_{12}}\right)^2$), and matrix compression—pinpointing whether carbon fibres or epoxy resin fail first.
* **Puck (`pf`, `pmA`, `pmB`, `pmC`):** Evaluates inter-fibre failure on the physical fracture plane through the ply thickness using ANSYS inclination constants (`p21(+) = 0.325`, `p21(-) = 0.275`, `p22(+) = 0.225`, `p22(-) = 0.225`):
  * **Mode A (`pmA`):** Transverse tension + shear ($0^\circ$ fracture plane) — governs Run A's `25°` UD plies.
  * **Mode B (`pmB`):** Moderate transverse compression + shear (internal friction increases shear strength).
  * **Mode C (`pmC`):** High transverse compression causing an inclined $\approx 53^\circ$ wedge fracture (delamination trigger).

### Multi-Criteria FEA Envelope Comparison

| Criterion | Run A (`UD` Open Skin) Max IRF | Run B (`Woven` Closed Shell) Max IRF | Critical Ply & Governing Mode |
| :--- | :---: | :---: | :--- |
| **Tsai-Wu (`tw`)** | `3.13` | `0.24062` | Run A: `25°` plies at root / Run B: root leading edge |
| **Hashin (`hf`, `hm`)** | `[x]` | `[x]` | Run A: Matrix tension (`hm`) in `25°` plies / Run B: Safe (`< 0.25`) |
| **Puck (`pf`, `pmA`, `pmB`, `pmC`)** | `[x]` | `[x]` | Run A: Inter-fibre Mode A (`pmA`) at root / Run B: Safe (`< 0.25`) |

---

## 5. Limitations and Next Steps

* **Like-for-Like Section Comparison:** Re-run Run A (`UD [0 / 25 / 0 / 25]s` at `1.20 mm`) on the **closed NACA 2412 shell** so both laminates share identical cross-sectional geometry.
* **Hybrid Laminate Optimization:** Test a hybrid stackup (`[45° Woven / -25° UD / 0° UD / -25° UD]s`) to combine root matrix/shear protection with controlled nose-down aerodynamic washout.
* **Aerodynamic Loading (1-Way FSI):** Map real 3D surface pressure from **ANSYS Fluent** onto the closed wing shell to replace the uniform `250 N` structural load.
* **Scope Note:** No mesh convergence study or physical coupon test data is included yet. Built under the ANSYS Student Licence for engineering learning and portfolio demonstration.

---

## 6. Repository Files

* `README.md` — Comparative technical documentation and failure analysis.
* `hand_calc/clt_check.py` — Python script (`numpy`) computing CLT stiffness matrices, open/closed beam deflections, linear root ply IRFs, and tip twist angles.
