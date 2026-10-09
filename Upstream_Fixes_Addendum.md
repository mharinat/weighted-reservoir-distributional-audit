# Post-submission verification of upstream fixes

Tests: 7 October 2026. Documentation updated: 9 October 2026. Supplement to ReScience C submission #139; the originally submitted manuscript and its pinned experimental source remain unchanged.

A response from the original corresponding author has been received. Statements in the initial submission saying that no response had been reported are superseded. Private correspondence is not reproduced or quoted here. The tests below concern publicly accessible source.

## Author confirmation and upstream integration

Adriano Meligrana confirmed the existence of the two reported implementation issues and, in correspondence dated 9 October 2026, permitted us to state that confirmation. This permission does not imply endorsement of the manuscript as a whole. The private correspondence is neither reproduced nor quoted. The public description of [upstream PR #154](https://github.com/JuliaDynamics/StreamSampling.jl/pull/154) independently records acknowledgment of @mharinat for reporting the WRAExp-J retrieval bias and the binomial-CDF off-by-one error. No credit is claimed for the other fixes.

PR #154 was merged on 9 October 2026 at 01:02:32 UTC, as commit `69cc82a4a979853c6e54249dc02e75d8e701a8b8`. The merged `comparison/samplers.jl` is byte-identical to the file at the tested commit `f5eef18df8c59792e4fd5dda7aebfa1dd6780c87`; both have SHA-256 `fde8a4ef460c07035e81d1b316b4506e68193f6a7b1af07e8c0898d836d8f458`. This identity check involved no new sampling or timing experiment and does not validate other modified files or establish a new package release. The verification archive retains the original 7 October report; this living addendum records subsequent documentation updates.

## Pinned source and scope

Source: https://github.com/JuliaDynamics/StreamSampling.jl/blob/f5eef18df8c59792e4fd5dda7aebfa1dd6780c87/comparison/samplers.jl . The branch name `fixes` is mutable; this report refers exclusively to that commit.

Inspection identifies three relevant changes: category activation now follows the distinct-rank counter; the binomial recurrence uses n-(i-1); and the short-stream SKIP retrieval uses differences of cumulative weights. The branch also modifies package code, merging and interfaces. Those additional changes were not validated by this study. Author-supplied benchmark CSVs were inspected as source material, not treated as independently replicated measurements. No timings were rerun.

## Executed checks

All checks are targeted post-submission diagnostics selected after inspecting the patch. Julia 1.12.1 and the previously saved Project/Manifest were used on the same host. The supplied check.jl records seeds and parameter choices.

1. Coupled-output comparison against our existing conversion repair: N in {4,8,40}, m in {2,4}, and uniform/increasing/decreasing weights; 10,000 queries per configuration. Across 18 configurations and 180,000 paired queries, no output mismatch occurred. Each configuration reused one fixed reservoir and cloned the RNG state. This is empirical agreement in those cases, not a proof of equivalence.
2. Native corrected conversion: one million queries from a fixed four-item, four-slot unit-weight reservoir, assessed using the complete occupancy partition distribution. The two-pair frequency was 0.140782, compared with 9/64 = 0.140625; the Wilson 95% interval was [0.140102, 0.141465], and the event-indicator sample SD was 0.347797. The Pearson statistic was 1.171874 on four degrees of freedom, p = 0.882707.
3. The positive-binomial count helper was sampled one million times for each of (n,p) = (4,0.25), (8,0.4), (20,0.1), against the positive-conditioned binomial law.
4. Short-stream SKIP retrieval: weights (1,2), reservoir size four, 200,000 retrievals from one fixed reservoir. Every result had valid length and labels; counts of label two were compared with Binomial(4,2/3).

| Check | Repetitions | Pearson statistic | df | p | Holm-adjusted p |
|---|---:|---:|---:|---:|---:|
| conversion | 1000000 | 1.171874 | 4 | 0.882707 | 1.000000 |
| count_4_0.25 | 1000000 | 1.255970 | 3 | 0.739614 | 1.000000 |
| count_8_0.4 | 1000000 | 6.143674 | 7 | 0.523077 | 1.000000 |
| count_20_0.1 | 1000000 | 2.336704 | 9 | 0.984957 | 1.000000 |
| short_stream | 200000 | 8.535711 | 4 | 0.073812 | 0.369061 |

Count-test tail categories were pooled sequentially to expected frequency at least five, merging any last remainder into the previous bin. Holm correction covers these five tests. None rejects at family alpha 0.05; non-rejection does not establish equivalence or universal correctness. Repeated queries of a fixed reservoir must not be counted as fresh independently constructed reservoirs.

## Reproduction

Extract Upstream_Fixes_Verification.zip. With Julia 1.12.1 and dependencies instantiated from the included Project.toml and Manifest.toml, run `julia --startup-file=no --project=. check.jl`, then `python analyze.py` using the research artifact's pinned Python requirements. Run on a copy: these scripts write output CSVs and results.json. comparison.json and fixes.json preserve retrieved public GitHub metadata.

## Interpretation and limits

These checks support the proposed changes on the tested cases and preserve the original counterexamples as evidence about the earlier pinned source. They do not certify extreme floating-point cases, every reservoir size, the released package, all branch changes, historical-environment identity, or unchanged performance. No inference about editorial acceptance follows from these results.
