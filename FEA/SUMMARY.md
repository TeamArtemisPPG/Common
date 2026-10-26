# Rough FEA summary – Quadruped & Hexapod (Ansys MAPDL 2026 R1 Student)

Solve date: 26-10-2026 (actual run time, see each `mapdl_solve_log.out`). Material: Al 6061-T6 (E = 68.9 GPa, ν = 0.33, Sy = 276 MPa).

Elements: SOLID187 (10-node tet). Loads include dynamic factor 2.0; quadruped trot = 2 feet in stance, hexapod tripod = 3.

## Mass budget / load case
| Robot | Mass est. (kg) | Weight (N) | Load per stance foot (N) |
|---|---|---|---|
| quad | 3.02 | 29.6 | 29.6 |
| hex | 2.77 | 27.2 | 18.1 |

## Ansys FEA results (real solves, 4-level mesh convergence)

| Part | Load (N) | Nodes / Elems (finest) | Peak σvm (MPa) | p99 σvm (MPa) | Max defl (mm) | FoS (yield) | p99 change last refinement | Reaction check (N) |
|---|---|---|---|---|---|---|---|---|
| quad_shank_plate | 29.6 | 71559 / 37520 | 25.78 | 13.24 | 0.2641 | 10.7 | -0.9% | 29.63 |
| quad_thigh | 29.6 | 54254 / 27064 | 12.21 | 5.33 | 0.0270 | 22.6 | +2.0% | 29.63 |
| quad_foot_base | 29.6 | 38083 / 25213 | 0.11 | 0.03 | 0.0000 | 2499.9 | -1.1% | 29.63 |
| hex_base_plate | 54.4 | 99831 / 49440 | 5.85 | 2.76 | 0.0470 | 47.2 | -3.1% | -54.40 |
| hex_thigh | 18.1 | 32680 / 16282 | 10.04 | 3.62 | 0.0152 | 27.5 | +3.4% | 18.13 |
| hex_tibia | 9.1 | 29624 / 14523 | 24.28 | 9.87 | 0.7096 | 11.4 | +0.7% | 9.07 |
| hex_c1_bracket | 18.1 | 29033 / 15158 | 165.73 | 126.65 | 1.1421 | 1.7 | +0.7% | 18.13 |

### Critical stress nodes (finest mesh, top 5 hot spots, global coords mm)

**quad_shank_plate**: (-173.2, 62.1, -81.0) → 25.78 MPa; (-171.5, 45.6, -66.2) → 20.53 MPa; (-173.5, 49.0, -100.2) → 19.47 MPa; (-172.8, 66.4, -89.3) → 18.01 MPa; (-172.5, 37.9, -61.5) → 17.91 MPa
**quad_thigh**: (-135.6, 46.5, -32.9) → 12.21 MPa; (-135.6, 97.7, -3.3) → 11.49 MPa; (-134.8, 49.5, -50.8) → 11.11 MPa; (-135.5, 74.8, -23.5) → 10.56 MPa; (-136.3, 69.4, -12.6) → 10.46 MPa
**quad_foot_base**: (-133.5, 142.6, -208.5) → 0.04 MPa; (-169.7, 148.8, -212.5) → 0.04 MPa; (-171.5, 140.7, -194.7) → 0.04 MPa; (-133.5, 136.8, -194.7) → 0.04 MPa; (-171.5, 143.5, -209.5) → 0.04 MPa
**hex_base_plate**: (46.2, 59.4, -1.0) → 4.67 MPa; (-18.6, -67.0, -1.0) → 4.62 MPa; (-48.6, -47.7, -1.0) → 4.60 MPa; (74.3, 21.1, 1.0) → 4.57 MPa; (28.4, -69.7, -1.0) → 4.52 MPa
**hex_thigh**: (82.2, -173.4, 10.5) → 10.04 MPa; (79.7, -169.1, 24.5) → 9.92 MPa; (81.3, -173.9, 23.5) → 9.21 MPa; (78.8, -169.6, 11.4) → 9.12 MPa; (104.5, -184.0, 32.0) → 6.06 MPa
**hex_tibia**: (100.8, -196.0, -115.9) → 24.28 MPa; (107.1, -208.6, -113.7) → 23.37 MPa; (97.7, -195.5, -109.3) → 21.01 MPa; (106.0, -210.3, -108.8) → 16.44 MPa; (101.1, -191.2, -117.4) → 12.91 MPa
**hex_c1_bracket**: (61.3, -115.7, 20.0) → 138.38 MPa; (66.6, -110.3, 17.5) → 138.08 MPa; (65.4, -113.3, 22.3) → 129.54 MPa; (65.0, -113.5, 13.0) → 129.00 MPa; (60.3, -113.9, 14.7) → 127.92 MPa

**Servo torque (quad)**: worst horizontal joint-to-foot lever ≈ 112 mm → 3.33 N·m vs ST3215 stall 2.94 N·m (margin 0.9×).

**Servo torque (hex)**: worst horizontal joint-to-foot lever ≈ 145 mm → 2.64 N·m vs ST3215 stall 2.94 N·m (margin 1.1×).

## Hand calculations – both robots (classical beam theory, NOT Ansys)

Method: σ = M·c/I (+ F/A for links); δ from cantilever / simply-supported formulas. Section = solid rectangle with the same volume per length (A = V/L), depth = 2nd-largest bounding-box dimension. Deliberately crude; FEA shown alongside.

**Read with care:** for the links the hand values are 4-36x LOWER than FEA because a smooth equivalent beam ignores holes, cut-outs, fillets and thin-wall brackets (stress concentrations). Treat it as an order-of-magnitude sanity check; the FEA governs, and the C1 bracket (FEA FoS 1.7) is the critical part.

| Part | Load (N) | Hand σ (MPa) | Hand δ (mm) | Hand FoS | FEA peak σ (MPa) | FEA FoS | Model |
|---|---|---|---|---|---|---|---|
| quad_chassis | 59.3 | 0.2 | 0.000 | 1512.2 | not solved | – | box beam, span 337 mm (diagonal hips), central load |
| quad_thigh | 29.6 | 2.5 | 0.002 | 112.3 | 12.2 | 22.6 | cantilever L=116 mm, lever 128 mm, eq. section 0.9x108.7 mm |
| quad_shank_plate | 29.6 | 4.2 | 0.007 | 65.3 | 25.8 | 10.7 | cantilever L=163 mm, lever 138 mm, eq. section 0.3x145.6 mm |
| quad_foot_base | 29.6 | 0.3 | 0.000 | 1090.4 | 0.1 | 2499.9 | cantilever L=44 mm, lever 27 mm, eq. section 22.3x32.0 mm |
| hex_base_plate | 54.4 | 7.1 | 0.212 | 38.7 | 5.9 | 47.2 | plate strip, span 140 mm (tripod hips), UDL |
| hex_thigh | 18.1 | 2.3 | 0.001 | 122.1 | 10.0 | 27.5 | cantilever L=62 mm, lever 78 mm, eq. section 2.0x45.1 mm |
| hex_tibia | 9.1 | 1.8 | 0.131 | 156.1 | 24.3 | 11.4 | cantilever L=170 mm, lever 19 mm, eq. section 1.0x27.1 mm |
| hex_c1_bracket | 18.1 | 4.6 | 0.001 | 59.8 | 165.7 | 1.7 | cantilever L=50 mm, lever 123 mm, eq. section 1.4x47.0 mm |

## Not simulated / modelling notes

- **Quadruped chassis**: CAD body has a self-intersecting surface; gmsh/tetgen PLC error, so not solved. Repair geometry in Fusion (or Ansys SpaceClaim) and rerun.
- hex_base_plate: 2 mm plate hits the 128k student node cap; deflection still changing ~6% at the last level (not fully converged).
- hex_tibia: twin parallel rods share the foot load, so each rod is solved at F/2 on its own axis. quad shank plates (mirrored pair) loaded on their mid-plane.

## Caveats
- Peak stresses sit at sharp internal corners (geometric singularities) and keep rising slowly with refinement; the p99 stress and deflection are the converged quantities.
- Bolted joints, servo housings, contact and fatigue are not modelled. Single static worst-case load per part.