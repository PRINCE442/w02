# Assignment 01 — TA feedback: Prince (Prince Ani)

- **Evaluated by:** Yilin Pan (TA)
- **Date:** 2026-10-08

## Scores

| Category | Pts | Student's LLM eval | TA verified | Notes |
|---|---|---|---|---|
| Immutability & encapsulation | 25 | 25 | 25 | |
| Constructor validation | 25 | 25 | 25 | |
| Correctness | 15 | 15 | 15 | |
| Testing | 15 | 12 | 9 | Graded on the code at the deadline |
| Code quality & style | 10 | 7 | 7 | |
| Introspection, LLM eval & code walk | 10 | 8 | 10 | Code walk covered `Species` |
| **Total** | **100** | **92** | **91** | |

## Checks

- [x] All tests pass (32/32)
- [x] No `UnsupportedOperationException` stubs left
- [x] Checkstyle warnings: 0
- [x] Coverage (lines / branches): 100% / 100%
- [x] Nothing out of scope (inheritance, collections, `equals`/`hashCode`)
- [x] `submission/LLM-Evaluation.md` present (added Oct 6)
- [x] `submission/introspection.md` present (added Oct 6)

## Where the TA disagrees with the student's LLM evaluation

- Testing −3 (12 → 9): the tests your LLM gave you credit for (`oneBelowTheMaximumIsAllowed`,
  `theMaximumDescribesItselfAsWholeYears`, `theNegativeRefusalNamesTheOffendingValue`,
  `pluralsWorkBeyondTwoYears`) were added on Oct 6, after the Oct 4 deadline, so they aren't graded.
  As submitted, there was no `MAX_MONTHS - 1` test, only the null-species message was checked, and
  one test repeated the provided ones.
- Code quality: I agree with 7. The misplaced `toString()` Javadoc and the indentation were both in
  the code at the deadline.
- Cat 6 +2: your introspection does explain `Species`, and your code walk covered it, so full credit.

## Feedback

The `Animal` constructor is well done: every check runs before any assignment, and each one has a
clear message. Tests are the main gap. When the provided suite already reaches 100% coverage, focus
on what it doesn't check, like `MAX_MONTHS - 1` and the content of each exception message. You left
the "Your response" part of the LLM evaluation empty. That's the part where you say which criticisms
you accept or reject and why. Also, "What you changed: Nothing" isn't accurate, since the same commit
changed your code and tests. Next time, finish the code before the deadline, and keep the
self-evaluation about what you actually submitted.

## Grade

**Final grade:** 91 / 100
