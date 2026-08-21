# Findings

## 1. Data

Outcome: monthly state sports-betting handle for Arizona, Arkansas, Colorado,
Connecticut, and Delaware, 2019 to 2024. Each state's series starts the month it
legalized sports betting, so the panel is unbalanced. Source: `legalsportsreport.com`'s
revenue tracker, which aggregates official state gaming-commission monthly reports.

**Table 1. Summary statistics, monthly state handle**

| State | N | Mean ($M) | Median ($M) | SD ($M) | Min ($M) | Max ($M) |
|---|---|---|---|---|---|---|
| Arizona | 40 | 557.8 | 566.1 | 151.5 | 290.5 | 897.6 |
| Arkansas | 66 | 19.1 | 9.4 | 19.1 | 0.0 | 66.1 |
| Colorado | 56 | 392.2 | 400.3 | 163.7 | 25.6 | 716.4 |
| Connecticut | 39 | 148.9 | 150.1 | 46.1 | 54.6 | 243.7 |
| Delaware | 72 | 9.8 | 8.3 | 6.7 | 0.0 | 28.4 |

Arizona and Colorado are large multi-operator markets. Connecticut is capped by law at
three online licenses. Arkansas and Delaware never licensed any of the six endorsed
platforms at any point in 2019-2024, and serve as never-treated markets.

Endorsements: six major athlete-sportsbook endorsement or campaign-launch events, each
verified against a dated primary source (PR Newswire, BusinessWire, a corporate
newsroom, or dated trade press; see `data/endorsements.csv`).

| Athlete | Platform | Date | Timed to a major event? | Eligible states |
|---|---|---|---|---|
| Charles Barkley | FanDuel | 2020-12-21 | No | CO |
| Shaquille O'Neal | WynnBET | 2021-08-09 | No | CO |
| Peyton & Eli Manning | Caesars Sportsbook | 2021-11-09 | No | AZ, CO |
| Rob Gronkowski | FanDuel ("Kick of Destiny") | 2023-01-10 | Yes, Super Bowl LVII | AZ, CO, CT |
| Kevin Hart | DraftKings | 2023-08-14 | Yes, NFL season opener | AZ, CO, CT |
| Jamie Foxx | BetMGM | 2023-09-08 | Yes, NFL season start | AZ, CO |

A state counts as eligible only if the endorsed platform was licensed and operating
there at the announcement date; see `data/platform_state_operations.csv` for each
check and its source. Connecticut's license cap rules out Caesars, BetMGM, and
WynnBET. Arkansas and Delaware rule out every event.

## 2. Method

**Stacked event study.** Each event is estimated in its own sub-panel of eligible
states, observed from six months before to twelve months after the announcement,
rather than pooled into a single calendar-time regression. This keeps one
endorsement's post-period from contaminating another event's pre-period for the same
state. Colorado is eligible for all six events, and several of its 19-month windows
overlap.

```
log(handle)_{s,m,e} = alpha_{s,e} + sum_k beta_k * 1[K=k] + eps_{s,m,e}      k in {-6..12}\{-1}
```

`alpha_{s,e}` is a fixed effect for each state-event pair. `k=-1` is the omitted
reference month. Standard errors are clustered by state.

**Static pre/post specification**, the single-number companion to the same panel:

```
log(handle)_{s,m,e} = alpha_{s,e} + beta * Post_{s,m,e} + eps_{s,m,e}
```

The specification compares betting handle before and after endorsement announcements
within eligible state-event windows, using the same state-event fixed effects as the
event study. It is not a difference-in-differences design: the regression includes
only the three treated states (AZ, CO, CT), so there is no untreated group whose own
change over the same period is netted out. Arkansas and Delaware never license any of
the six endorsed platforms, but Delaware operates a single-operator lottery-style
market and Arkansas had no major national platform licensed during the sample period;
neither is comparable enough to serve as a control. Both are reported only as a
descriptive series in Table 1.

## 3. Results

**Event study (N=224, 6 events, 3 states, 12 state-event units), clustered SE by state:**

| k | β_k | SE | p |
|---|---|---|---|
| -6 | -0.178 | 0.139 | .201 |
| -5 | -0.100 | 0.117 | .392 |
| -4 | -0.026 | 0.099 | .793 |
| -3 | 0.013 | 0.054 | .813 |
| -2 | -0.023 | 0.035 | .502 |
| 0 | 0.138 | 0.032 | <.001 |
| 1 | 0.328 | 0.020 | <.001 |
| 2 | 0.416 | 0.017 | <.001 |
| 3 | 0.375 | 0.038 | <.001 |
| 4 | 0.362 | 0.036 | <.001 |
| 5 | 0.256 | 0.042 | <.001 |
| 6 | 0.174 | 0.045 | <.001 |
| 7 | 0.149 | 0.024 | <.001 |
| 8 | 0.197 | 0.034 | <.001 |
| 9 | 0.231 | 0.045 | <.001 |
| 10 | 0.264 | 0.037 | <.001 |
| 11 | 0.280 | 0.019 | <.001 |
| 12 | 0.373 | 0.022 | <.001 |

R² = .757. Pre-period coefficients (k=-6 to -2) are statistically indistinguishable
from zero. From the announcement month onward, log handle is significantly higher,
peaking around k=2 (+41.6 log points) and remaining elevated through k=12 (+37.3 log
points).

A pure attention effect should decay back toward baseline as media coverage fades;
these coefficients do not decay. The pattern is consistent with a persistent
endorsement effect, and equally consistent with these three states' betting handle
trending upward for reasons unrelated to any specific athlete: more operators
entering, more marketing spending industry-wide, and a national market that more than
tripled between 2020 and 2024. The event-study specification alone cannot distinguish
between the two.

The model is re-estimated with a linear trend specific to each state-event unit. Under
this specification the coefficient path inverts: pre-period coefficients (k=-6 to -3)
become large and significantly positive, and post-period coefficients turn negative
and grow more negative through k=12. The 19-month window is too short to separately
identify an endorsement's effect from a state-event unit's own linear trend when both
are estimated from the same data. The dynamic, month-by-month result is unstable under
this control. Full output: `output/event_study_coefficients.csv` (main spec) and
`output/event_study_model_summary.txt` (both specs).

**Static pre/post (N=224), the single-number version:**

| Spec | β (post) | SE, cluster/state | SE, HC1 | Implied Δhandle | R² |
|---|---|---|---|---|---|
| (1) Basic | +0.323 | 0.073 (p<.001) | 0.050 (p<.001) | +38.2% | .737 |
| (2) + state-event linear trend | +0.310 | 0.068 (p<.001) | 0.071 (p<.001) | +36.3% | .778 |

The single average post-vs-pre gap is stable across both specifications, about 31 to
32 log points, roughly a third higher handle in the twelve months after an endorsement
announcement than in the six months before, whether or not a state-specific trend is
included. Full output: `output/did_model_summary.txt`.

**Robustness: leave-Colorado-out (`04_robustness_leave_co_out.py`).** Colorado is
eligible for all six events and accounts for 114 of the panel's 224 rows, 51 percent.
Dropping it removes Barkley and Shaq, which were Colorado-only events, leaving four
events and 110 observations across Arizona and Connecticut:

| Spec | β (post) | SE, cluster (2 clusters) | SE, HC1 | Implied Δhandle |
|---|---|---|---|---|
| (1) Basic, no CO | +0.226 | 0.010 | 0.058 | +25.4% |
| (2) + trend, no CO | +0.228 | 0.086 | 0.094 | +25.6% |

The sign holds; the estimate falls by about a third, from 38 percent to 25 percent.
Two pre-period event-study coefficients (k=-4, k=-3) turn significantly positive once
Colorado is removed; the absence of a pre-trend in the full panel does not hold in
this subsample. Spec (1)'s cluster SE (0.010) is implausibly small next to its HC1
counterpart (0.058), consistent with the small-cluster problem, worse here with only 2
clusters than in the main specification's 3; HC1 is the more credible number in this
table. Full output: `output/leave_co_out_summary.txt`.

**Robustness: timed-to-major-event vs. untimed (`05_timed_vs_untimed.py`).** Three of
the six events were timed to a major sporting event (Gronkowski/Super Bowl LVII,
Kevin Hart/NFL opener, Jamie Foxx/NFL season start) and have the widest state coverage
of the six:

| Group | N | States | β (post) | SE, cluster | SE, HC1 | Implied Δhandle | R² |
|---|---|---|---|---|---|---|---|
| Timed to major event | 152 | 3 | +0.223 | 0.006 | 0.047 | +25.0% | .825 |
| Not timed | 72 | 2 | +0.557 | 0.080 | 0.118 | +74.6% | .512 |

The pattern is inconsistent with a seasonality explanation: if the football calendar
were driving the result, the timed events would show the larger jump; instead they
show the smaller one. The untimed estimate rests on only 2 clusters and an R² of .512.

## 4. What this does and doesn't support

Supported: states see higher betting handle in the year following a major
athlete-sportsbook endorsement announcement than in the months before it, and that
average gap is not explained away by an ordinary linear growth trend specific to each
state-event window. The direction holds in every specification and subsample tested:
the main spec, the trend control, leave-Colorado-out, and the timed/untimed split. The
size does not; it ranges from about 25 percent to about 75 percent depending on which
states and events are included, and Colorado's weight in the panel is the largest
single source of that range.

Not supported: a causal account of why. The dynamic month-by-month path is not robust
to the same trend control that leaves the average gap intact, so this design cannot
rule out that these are fast-growing young markets that would have grown this much
regardless of any specific athlete deal. A cleaner test would need either a
never-treated comparison state matched on market age and size, which this five-state,
two-multi-operator-state sample does not have, or platform-specific handle showing
whether the endorsed platform's own share moves rather than only the state's total,
which is not publicly available at the state level.

## 5. Limitations

- Colorado accounts for 114 of the panel's 224 rows, just over half, and is eligible
  for all six events. The leave-Colorado-out check above shows the headline estimate
  meaningfully lower, and a pre-trend reappearing, without it.
- Cluster-robust inference with three clusters (AZ, CO, CT) is not well-behaved
  asymptotically; `statsmodels` flags a rank-deficient covariance matrix on the
  clustered event-study specification. HC1 (non-clustered) SEs are reported alongside
  both regressions as a cross-check; they tell a similar story for the static result
  and a less precise one for the dynamic path. The leave-Colorado-out and
  timed/untimed splits run on only 2-3 clusters.
- The six events are the major, dated, independently verifiable endorsements
  identified within this project's scope, not a comprehensive census. A small number
  of other athletes with reported sportsbook ties (Wayne Gretzky, Emmitt Smith, Baker
  Mayfield) could not be independently confirmed against a dated primary source and
  are absent from this dataset.
- Every outcome measure is a state's total betting handle across all licensed
  operators; platform-specific handle is not publicly available. An endorsement that
  mainly shifts bettors between existing platforms rather than growing the total
  market would show up as a null or attenuated effect on state totals even if the
  endorsed platform gained real share.
- The data-collection approach used for this project returned monthly handle history
  for five states. A larger panel, closer to the roughly three dozen states with legal
  sports betting nationally, would require either a paid data feed or manually
  compiled state gaming-commission reports.
