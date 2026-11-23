# AUV-HX1 hybrid drone feasibility - progress (2026-10-28)
Input STL: /mnt/user-data/attachments/6887e279-ad36-5519-9db1-a93b6b34fa00/c8acdfc6-hybrid_drone.stl (mm, hover pose, nose +Y)
Scratchpad: /tmp/claude-0/-home-claude/6887e279-ad36-5519-9db1-a93b6b34fa00/scratchpad  (scripts: build.py calc.py mkcase.py; OpenFOAM v2412 via micromamba in mm/, serial only; cases case_water, case_air(bad: slip walls), case_a4, case_a10)

## Done
- Geometry: 930x708x250 mm hover. Wing is SOLID 0.84 L block (536x176x20 mm, AR 3.05, S=0.0943 m2) -> cannot hold water as modelled. Props 330 mm dia, blade pitch ~6.6-7.4 in. Hull tubes OD80, wall ~2.5 mm.
- Displacement (my solid-cored union) 3.97 L vs author 4.09 L; mass 3.87 kg incl 0.39 kg unmodelled -> net buoyancy +0.10..+0.22 kgf, uncertain; must weigh real build.
- 10 m: 98 kPa gauge. Hull tube buckling SF 2.9 (pessimistic E=8 GPa, long) to 24; end caps SF>12; dome fine. Risks = motors/servos/penetrators not sealed, not structure.
- Flooded wing: partial fill free-surface GG' ~520 mm (unstable); needs >=8 baffles or full/empty only. Need ~0.32-0.5 kg water to dive passively.
- CFD water, swim pose, 1 m/s, kOmegaSST, 1.43M cells, bare (no props): drag 4.72 N, CdA=0.0094 m2; lift +2.9 N at alpha 0 (wing camber/asymmetry).
- Swim speed est (13x7ish prop, 4114 400KV): ~1.0 m/s at 20 A, 1.2 at 30 A, 1.4 at 40 A per motor.
- Hover: T/W 1.45 nominal, ~1.28 after ~13% wing download (est). Stall speed 22-26 m/s vs prop pitch speed ~18-24 m/s -> pure airplane mode not feasible; tilt-rotor/bicopter mode with partial wing lift is.
- First air run (alpha 8, 20 m/s) INVALID: slip top/bottom walls killed AoA. Lift only 1.9 N.
## Pending
- case_a4 / case_a10 (alpha 4,10 deg, 20 m/s air, freestream BCs, 320 iter) were running; read postProcessing/forces/0/force.dat (cols: t total xyz, pressure, viscous), lift = Fz cos a - Fx sin a, drag = Fx cos a + Fz sin a. If killed, rerun: cd case_aN; micromamba run -n cfd simpleFoam (MAMBA_ROOT_PREFIX=/tmp/claude-0/-home-claude/6887e279-ad36-5519-9db1-a93b6b34fa00/scratchpad/mm/root).
- Then write final answer: verdict + CFD software list (OpenFOAM, SU2, SimScale free community 2000 core-h/10 sims/public projects, Fusion simulation for FEA pressure, XFLR5/OpenVSP/AVL, QBlade) ; user laptop is weak (4GB) -> use cloud.
- Deliver: out/AUV-HX1_swim_pose_watertight_mm.stl (may need repair), case zip.

## Update 2026-11-20 (v2 review + results)
- Competition: AUV-IITB CAD Design Challenge 2026 (pool/shallow AUV, 1 m envelope, 10 m depth, Round 1 deadline 1 Nov 2026 passed).
- v1 CFD results: water 1 m/s drag 4.72 N, CdA 0.0094 m2, lift +2.9 N at 0 deg. Air 20 m/s: 4 deg L=5.0 N D=2.6 N; 10 deg L=12.2 N D=4.1 N (runs stopped at ~180/320 iters; trends settled; +-20%).
- v2 findings: STEP names are timestamps; wing (body 77) is open non-watertight, planform 0.194 m2, 26 mm thick; props ~218x248 mm (~10 in); tubes 3 mm wall, ring stiffeners, 25 mm end caps; envelope 990x708x503 mm (hover pose); stray 2-face fragments near vacuum ports; hull-only displaced volume 2.78 L, CB centroid (0,-3,9.6) mm; vertical keel to z=-257.
- OpenFOAM v2412 via micromamba from conda-forge works but serial only (no MPI). Use freestream/rotated-model for angle of attack, NOT slip top/bottom walls.
