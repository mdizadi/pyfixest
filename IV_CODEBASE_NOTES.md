# IV Codebase Notes

Notes accumulated while learning and implementing IV extensions in pyfixest.

---

## Key Files

| File | Role |
|---|---|
| `pyfixest/estimation/models/feiv_.py` | `Feiv` class — main IV implementation |
| `pyfixest/estimation/models/feols_.py` | `Feols` base class — `Feiv` inherits from this |
| `pyfixest/estimation/formula/parse.py` | Formula parsing (`FixestFormula`) |
| `pyfixest/estimation/internals/demean_.py` | Demeaning logic |
| `pyfixest/estimation/internals/solvers.py` | `solve_ols()` solver |

---

## Step 1: Codebase Orientation

### 2SLS Flow

1. `Feiv.__init__()` calls `Feols.__init__()` (super)
2. Estimation pipeline (called externally in the right order):
   - `demean()` — demeans Y, X, Z, endogvar using fixed effects
   - `to_array()` — converts DataFrames to numpy
   - `drop_multicol_vars()` — drops collinear columns from Z
   - `wls_transform()` — applies weights
   - `get_fit()` — runs 2SLS
3. `first_stage()` — separate method, called by user or `IV_Diag()`

### 2SLS Math (in `get_fit`)

```
tZX = Z'X,  tXZ = X'Z,  tZy = Z'Y,  tZZinv = (Z'Z)^{-1}
H = X'Z (Z'Z)^{-1}
A = H Z'X = X'Z (Z'Z)^{-1} Z'X
B = H Z'Y = X'Z (Z'Z)^{-1} Z'Y
beta_hat = A^{-1} B        <- 2SLS estimator
u_hat = Y - X beta_hat     <- 2SLS residuals

Scores  = Z * u_hat
Hessian = Z'Z
Bread   = H' (H Z'X H')^{-1} H   <- for sandwich vcov
```

### First Stage (`first_stage()`)

- Calls `feols()` recursively with the first-stage formula
- Stores: `_pi_hat`, `_X_hat` (fitted values), `_v_hat` (residuals), `_model_1st_stage`
- Then calls `IV_weakness_test(["f_stat"])`

### IV Weakness Test (`IV_weakness_test`)

- `f_stat`: Wald test on the instrument coefficients in the first stage
  - Null: all instrument coefficients = 0
  - Uses `model_1st_stage.wald_test(R=R)` with a restriction matrix R
  - Result stored in `_f_stat_1st_stage`, `_p_value_1st_stage`
- `effective_f`: Olea-Pflueger (2013) effective F-stat
  - `F_eff = π' Q_zz π / trace(Σ Q_zz)`
  - where π = first-stage instrument coefficients, Q_zz = Z'Z, Σ = robust vcov

### Formula Syntax

```
Y ~ exog_controls | fixed_effects | endog ~ instruments
```

---

## Step 2: Wu-Hausman Test (TODO)

*To be filled in when implemented.*

---

## Step 3: Sargan Test (TODO)

*To be filled in when implemented.*

---

## Step 4: Anderson-Rubin Test (TODO)

*To be filled in when implemented.*
