# Certified separatrix transport in a Kerr oscillator

Reproduction package for A. M. Tishin, *Certified inverse control of separatrix transport in a parametrically driven Kerr oscillator*, version 8, Online Resource 2. Journal: Nonlinear Dynamics. Author affiliations: Lomonosov Moscow State University and Moscow Institute of Physics and Technology. Correspondence: tishin@amtc.org.

## Interval proof in Supplement E and Fig. 1

Theorem 1 classifies the tangencies and counts of the selected primary branch pair in a specified control rectangle at pump depth 0.20 and zero static detuning. The count convention is one fundamental segment of the exact branch-preserving stroboscopic map T = −P.

From the root of this package, verify the proof with the stored interval panels:

    cd certified_contour
    sh full_contour/reproduce.sh

The script writes `full_contour/data/closed-contour-certificate.json` and verifies the input panel hashes.

To rebuild all interval data directly from the differential equations, run from the package root:

    FULL=1 WORKERS=8 sh certified_contour/full_contour/reproduce.sh

The command compiles the integrators, validates the local chart samples, regenerates the wide, axial and narrow flow panels, and verifies the complete contour. The stored nonvalidated diagonal trajectory only proposes wide-panel launch centers. For a clean rebuild, extract a fresh copy, delete the JSON files in `certified_contour/full_contour/data/` and `certified_contour/contour/data/`, and then run the command. Keep `certified_contour/boundary/data/` for the launch-center input.

The clean source run reproduced all 54 panel and certificate JSON files byte for byte. See `certified_contour/SOURCE_REBUILD.json` for the environment, per-file SHA-256 hashes and final result, and `certified_contour/SOURCE_REBUILD.log` for the calculation log. The build requires GNU C++, binary128, libquadmath, hardware FMA, enabled assertions and NumPy long double with at least a 64-bit significand.

From the package root, regenerate Fig. 1 using the certified box data:

    python figures/plot_certified_contour.py certified_contour/full_contour/data figures

Both panels of the figure use the interval data directly. Panel (b) shows the certified crossing intervals at the two anchor settings. The numerical area calculations and two-harmonic comparison are reproduced separately.

## Phase control of the incoming lobe areas

At depth 0.20 and zero detuning, the phase sweep uses the original interval panels and the already certified two-lobe rectangle. Within the sweep, the amplitude ratio differs from 0.85542915029090 by at most 5 × 10⁻⁸. The phase ranges from −0.05 × 10⁻⁶ to 2.65 × 10⁻⁶ rad. Run from the package root:

```sh
PYTHONPATH="$PWD/certified_contour/full_contour/code" python phase_partition/check_flux.py certified_contour > phase-replay.log
cmp phase-replay.log phase_partition/replay.log
```

The checker brackets the four crossings in the fundamental segment, validates the image of the first crossing under the exact branch-preserving map, and integrates the original-flow graph gap and its phase derivative. It verifies opposite strict signs for the two incoming area derivatives on all 27 adjacent phase intervals. Near zero phase each incoming lobe carries 47.8–52.2% of the total. On the phase interval 2.60 × 10⁻⁶ to 2.65 × 10⁻⁶ rad the second lobe carries 77.9–82.1% of the total. The exterior comparison is evaluated separately at the central amplitude ratio. The recorded replay is in `phase_partition/replay.log`.

Fig. 2 plots the outward-rounded interval data in `phase_partition/phase_scan_intervals.csv`. Regenerate the data and figure in a working copy from the package root:

```sh
python phase_partition/extract_phase_scan.py . phase-scan-replay.csv
cmp phase-scan-replay.csv phase_partition/phase_scan_intervals.csv
python phase_partition/plot_phase_scan.py phase-scan-replay.csv Fig2_Phase_Partition
```

The extraction replays the interval checks before exporting the intervals. Each plotted band encloses the area for the entire phase cell and the full specified amplitude-ratio strip.

## Explicit simultaneous depth and detuning neighborhood

For the full contour result, both the depth displacement from 0.20 and the static detuning may vary independently up to 10⁻¹⁶ in absolute value. The original amplitude and phase rectangle remains in force. The contour remains a single simple closed curve with four cusps, and the fixed two-lobe tolerance rectangle remains interior. The interval proof applies to the specified primary arcs. The neighborhood is a sufficient certificate in the normalized model.

Run the final checks on the stored uniformly enclosed arcs from the package root:

```sh
python uniform_contour/uniform/uniform_verify.py certified_contour uniform_contour/arcs uniform-checked
cmp uniform-checked/result.json uniform_contour/certificate/result.json
```

To regenerate the joint depth and detuning enclosures from the equations and then check the result:

```sh
python uniform_contour/uniform/uniform_launch.py certified_contour regenerated-launch
python uniform_contour/uniform/uniform_transport.py certified_contour regenerated-launch regenerated-arcs --scale 1e-16
python uniform_contour/uniform/uniform_verify.py certified_contour regenerated-arcs regenerated-check
cmp regenerated-check/result.json uniform_contour/certificate/result.json
```

The output directories must be fresh. The joint launch and flow drivers use the C++ and arithmetic requirements given above. `uniform_contour/` contains the code, launch samples, arc panels, certificate and logs. All checks require enabled Python and C++ assertions. Byte-for-byte comparisons describe the reference arithmetic platform. On a compatible platform, strict interval checks and the stated topological conclusions remain the criteria for reproduction.

## Contents

| Directory | Contents |
|---|---|
| `phase_partition` | Phase-control verifier, interval data, replay and Fig. 2 generator |
| `uniform_contour` | Joint depth and detuning enclosures, source rebuild and final contour verification |
| `nd_compensation` | Fixed predictions, full-flow lobe counts, area tests at two depths, static-detuning control, and a historical graphical abstract generator |
| `predicted_transport` | Direct geometry and convergence data for the predicted setting at depth 0.20 |
| `sectorial_matching` | Sectorial Stokes calculations, detuning identities and predicted pump settings |
| `refined_matching` | Symbolic checks for the second- and third-order hierarchy |
| `matching_research` | Earlier exact identities and finite-depth compensation tests |
| `closure_research` | Phase and amplitude sensitivity checks and earlier geometry controls |
| `compensation_research` | Baseline simulations, root histories and geometric checks |

## Setup

Use Python 3.12 and install the scientific packages in `requirements.txt`. Each stage keeps its own scripts and data. Run stage commands from that stage's directory and retain the sibling directories for cross-stage scripts. Some calculations overwrite their archived output files. Use a working copy to preserve the released data.

## Reproduce the Nonlinear Dynamics checks

Run from `nd_compensation/`. The stored inputs in `data/frozen_protocol.json` and `data/frozen_followup.json` were set before their respective integrations. To regenerate the full-flow results and summary, run:

```bash
python code/protocol.py --workers 8 amplitude_07 amplitude_13 phase_07 phase_13 diagonal_07 diagonal_097 diagonal_103 diagonal_13 depth016_original depth016_leading depth016_predicted matched_leading matched_predicted
python code/protocol.py --refined --workers 5 diagonal_097 diagonal_103 depth016_leading depth016_predicted matched_predicted
python code/protocol.py --refined --workers 2 diagonal_0998 diagonal_1002
python code/summarize.py > data/summary.json
python code/graphical_abstract.py
```

The summary and the historical graphical abstract can also be regenerated directly from the archived full-flow data with the last two commands. Set the worker counts to suit the available CPU resources.

For the depth-0.20 convergence audit, run `python code/summarize_geometry.py` from `predicted_transport/`. Its inputs are the archived geometric data and the original fixed prediction. In `sectorial_matching/`, the refined sectorial run and the detuning checks can be reproduced with:

```bash
python code/sectorial_jump.py --rho 6/7 --radius 80 --offset 4 --spacing .25 --order 44 --depth 80 --digits 90 --targets 10 14 18 22 26 --output data/refined.json
python code/verify_detuning.py
python code/predicted_tuning.py .2 --output data/predicted_020.json
python code/summarize.py
```

The two-harmonic boundary formula and the full-flow values in these earlier checks concern the reduced comparison and finite samples. The interval proof for the original-flow contour is reproduced separately above.

`MANIFEST_SHA256.json` lists the SHA-256 of every delivered file other than the manifest itself.


## Finite-depth transport range

The frozen settings are in nd_transport_range/data/frozen_grid.json.
The sweep uses the earlier invariant-manifold integrator. From the repository root:

    export PYTHONPATH="$PWD/nd_compensation/code"
    python nd_transport_range/code/sweep.py
    python nd_transport_range/code/sweep.py --narrow-chart zero_024_lead zero_024_pred
    python nd_transport_range/code/sweep.py --refined
    python nd_transport_range/code/summarize_grid.py

The two nominal runs at 0.24 are expected to fail to invert on span 2.
Use the second command to rerun the same parameters on span 1.5. The
higher-degree comparison uses span 2. To recompute, clear the copied result
JSON files in this sweep directory while keeping the frozen input. All
comparisons are at discrete points.

## Joint depth and detuning phase derivatives

See joint_phase/README.md for the Appendix E.10 certificate and replay commands.

## Inverse transport and the convex floor

Theorem 3, Theorem 4 and Appendix E.11 are replayed via inverse_control/README.md. Figure 3 sources are in figures/.

The current graphical abstract is figures/Kerr_Graphical_Abstract.png. Run python figures/plot_inverse_targets.py to regenerate it and Figure 3. The earlier contour graphic and its generator are marked historical.
