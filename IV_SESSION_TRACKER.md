# IV Session Tracker

**Read this file first at the start of every session.**

---

## Current Step

**Phase 1 — Step 2: Wu-Hausman endogeneity test**

(Step 1 = orientation/codebase reading — completed in earlier sessions)

---

## Phase 1 Checklist

- [x] Step 1: Orientation — read `Feiv`, understand 2SLS flow, first_stage(), IV_weakness_test()
- [ ] Step 2: Wu-Hausman endogeneity test
- [ ] Step 3: Sargan overidentification test
- [ ] Step 4: Anderson-Rubin test (weak-instrument-robust inference)
- [ ] Step 5: Summary diagnostics display (combined output table)
- [ ] Step 6: Cinelli-Hazlett sensitivity analysis

---

## What's Already Implemented (as of session recovery 2026-03-11)

In `pyfixest/estimation/models/feiv_.py`:

| Method | Description |
|---|---|
| `get_fit()` | 2SLS estimation |
| `first_stage()` | First-stage OLS via recursive `feols()` call |
| `IV_weakness_test()` | F-stat + effective F (Olea-Pflueger 2013) |
| `IV_Diag()` | Dispatcher that calls weakness test |
| `eff_F()` | Effective F-stat computation |

---

## Progress Log

| Date | What happened |
|---|---|
| 2026-03-11 | Session tracker recovered after files were lost (never committed to git). Reconstructed from memory + codebase read. Starting fresh from Step 2. |

---

## Notes for Next Session

- Validate Wu-Hausman against R's `ivDiag` package
- Wu-Hausman idea: regress 2SLS residuals on all regressors + first-stage residuals; test if first-stage residual coefficient = 0
- Formula syntax: `Y ~ exog | FE | endog ~ instruments`
