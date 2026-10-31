# Package inference and Go build constraints

## Pre-registered plan — 2026-10-31

Baseline: `034a8f6d8e64a14dd31a91becbf1f57ae71691a7`.

Hypothesis: `hyb generate` treats a sibling excluded by Go build constraints
as a package authority. A `//go:build ignore` generator in `package main`
beside a production `package demo` should not prevent generation. An excluded
file alone should not choose a production package either.

Before changing production code, run real CLI regressions against that baseline.
Use `go list -e -find -json .` as the independent selection oracle. Check a
never-selected generator, a legacy build constraint, an opposite-platform file,
and excluded-only siblings. Controls: an enabled custom tag must still cause a
genuine package conflict; ordinary conflicts and stale generated outputs must
retain their existing behavior. Successful generation and freshness checks must
be followed by an ordinary Go test of the generated function.

Candidate design: remove only the basenames reported in Go's `IgnoredGoFiles`
from the existing sibling list, preserving the existing test/generated-file
filters and conflict checks. Use `-find` to avoid dependency resolution; do not
run generators, fetch project dependencies, or modify module files. Report a
selection-command failure rather than guessing. Test the final design against
module and module-free fixtures and Go build flags before adopting it.

Run the focused suite before and after the fix, then the compiler workspace,
checker corpus and differential loop with Rust 1.99.0 and Go 1.26.9. Review all
changed code and meaningful excluded files with Open Code Review delegation.
This is tooling correctness, not a new compiler soundness or security claim.
PR #1900 separately covers dot/underscore filename prefixes; do not duplicate
or take over that change. Hosted CI and any bounty decision remain maintainer
gates. No award or receipt is assumed.

## Results

Not run at registration.
