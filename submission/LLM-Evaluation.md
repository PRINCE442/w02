# LLM Evaluation

Produced with an LLM using the prompt in `LLM-Evaluation-prompt.md`, then reviewed and answered by
you. **Both halves are required** — an unread LLM assessment pasted in whole is worth nothing.

---

## Assessment

> Note from the evaluator: this assessment was produced in the same Claude conversation that helped
> the student write parts of `AgeMonths.toString()`, the `Animal` constructor and several tests. It
> was asked to score the submitted code strictly regardless, but the reader should weigh that.

Material assessed: `AgeMonths.java`, `Animal.java`, `Species.java`, `AgeMonthsTest.java`,
`AnimalTest.java`, `submission/introspection.md`, JaCoCo coverage summary. Checkstyle output was
**not** provided, so style findings below are from reading the code, not from the tool.

### 1. Immutability and encapsulation — 25 / 25

- Every field is `private final`: `Animal` declares `private final String name;`,
  `private final Species species;`, `private final AgeMonths age;`,
  `private final LocalDate intakeDate;`, and `AgeMonths` declares `private final int months;`.
- There are no setters and no method assigns to a field after construction.
- No accessor leaks a mutable reference. `name()` returns a `String`, `species()` an enum constant,
  `age()` an `AgeMonths` (immutable, `final` class), `intakeDate()` a `LocalDate` (immutable). All
  are safe to return directly.
- Nothing is unnecessarily public: the helper is `private static String plural(...)`, and the
  `AgeMonths` constructor stays private behind `of()`.
- Observation, no deduction: `AgeMonths` is `final` but `Animal` is declared `public class Animal`,
  so a subclass could override `toString()` or the accessors. This is how the starter shipped it and
  inheritance is the subject of Lab 2, so it is reasonable to leave as is.

### 2. Constructor validation — 25 / 25

Each required rule, checked individually in `Animal(String, Species, AgeMonths, LocalDate)`:

| Rule | Enforced by | Present |
|---|---|---|
| Null name refused | `if (name == null)` | Yes |
| Empty or whitespace-only name refused | `String trimmed = name.strip();` then `if (trimmed.isEmpty())` | Yes |
| Null species refused | `if (species == null)` | Yes |
| Null age refused | `if (age == null)` | Yes |
| Null intake date refused | `if (intakeDate == null)` | Yes |
| Name stored stripped | `this.name = trimmed;` | Yes |

- Validate-then-assign is respected: all five checks precede the first `this.` assignment.
- The null check correctly precedes `name.strip()`, avoiding a `NullPointerException`.
- `strip()` rather than `trim()` is a good choice: it also removes Unicode whitespace.
- Every refusal throws `IntakeException` with a message naming the argument
  (`"species must not be null"`, `"intakeDate must not be null"`), and the blank-name message also
  reports the rejected value (`"name must not be blank, was \"" + name + "\""`).
- The constructor correctly does **not** re-check the age range; `AgeMonths.of()` already guarantees
  it.

### 3. Correctness — 15 / 15

Tracing `AgeMonths.toString()`:

| Months | Path taken | Output |
|---|---|---|
| 0 | `isUnderOneYear()` | `"0 months"` |
| 1 | `isUnderOneYear()` | `"1 month"` |
| 11 | `isUnderOneYear()` | `"11 months"` |
| 12 | `remainderMonths() == 0` | `"1 year"` |
| 23 | combined | `"1 year, 11 months"` |
| 24 | `remainderMonths() == 0` | `"2 years"` |
| 25 | combined | `"2 years, 1 month"` |

All match the specification, including `"0 months"` (zero is plural) and the omission of the months
part for whole years. `Animal.toString()` produces
`"Luna (Cat, 1 year, 11 months, intake 2026-09-21)"` for the reference animal, relying on
`species.label()`, `AgeMonths.toString()` and `LocalDate.toString()` (ISO by default).

### 4. Testing and coverage — 12 / 15

Coverage: 100% of instructions (207/207) and branches (22/22). As the assignment notes, the provided
suite already reached this, so the number says nothing about the added tests.

Strengths of the added tests:
- `oneBelowTheMaximumIsAllowed` covers the `MAX_MONTHS - 1` boundary the assignment asks for, and
  also checks its description (`"39 years, 11 months"`).
- `theNegativeRefusalNamesTheOffendingValue` fills a real gap: the provided suite only checked the
  message for the too-old case.
- `pluralsWorkBeyondTwoYears` checks the singular/plural rule outside the small values the provided
  tests use.
- `tabsAndNewlinesAroundTheNameAreTrimmed` checks non-space whitespace, which the provided
  `theNameIsTrimmed` does not.
- `toStringWithWholeYearsHasNoMonthsPart` exercises the whole-year path through `Animal`, which the
  provided `Animal` tests (23 and 1 months only) never reach.
- All added exception tests assert `IntakeException` specifically, not a generic exception.

Deductions:
- **(−3) Message content is not tested for every refusal.** Messages are checked for a blank name
  (provided) and a null species (added), but not for a null name, null age, or null intake date. The
  rubric grades "a message that names the problem" for every argument, so each deserves a test.
- Minor, no further deduction: `oneBelowTheMaximumIsAllowed` builds the value from
  `AgeMonths.MAX_MONTHS - 1` but then asserts the literal `479` and `"39 years, 11 months"`. If
  `MAX_MONTHS` ever changed, this test would fail for the wrong reason.

Three specific untested cases:
1. The message for `new Animal(null, ...)` contains `"name"`.
2. The message for a null `age` contains `"age"`.
3. The message for a null `intakeDate` contains `"intake"` or `"date"`.

### 5. Code quality and style — 7 / 10

- **(−2) Misplaced Javadoc in `AgeMonths`.** The line
  `/** Formats a count with its unit, pluralised unless the count is one. */` sits directly above
  `@Override public String toString()`, so that one-liner is what documents `toString()`. The full
  `toString()` Javadoc (format examples, singular/plural note) is left as a detached comment with no
  member, and `plural(...)` has no comment at all. A reader of the generated Javadoc would see the
  wrong description for `toString()`.
- **(−1) Inconsistent indentation and stray whitespace.** The starter uses 2-space indentation, but
  the bodies of `AgeMonths.toString()` and the `Animal` constructor use 4 spaces, the closing brace of
  `toString()` and the `private static String plural` line start at column 0, and there is a
  whitespace-only line after `plural`. A Google-style Checkstyle configuration will likely flag
  these. (Checkstyle output was not provided, so this is unconfirmed.)
- Delegation is done well. `Animal.toString()` concatenates `age`, which calls
  `AgeMonths.toString()`, and the words "year" and "month" do not appear in `Animal`. If the age
  format changed, only `AgeMonths` would need editing. Within `AgeMonths`, `toString()` reuses
  `years()`, `remainderMonths()` and `isUnderOneYear()` instead of repeating `/ 12` and `% 12`.
- All public members written by the student keep the starter's Javadoc purpose statements.

### 6. Scope discipline and code walk — 8 / 10

- No inheritance, no collections, no `equals`/`hashCode`. Scope discipline is fully respected, and
  `twoIdenticalAnimalsAreDifferentObjects` still passes.
- Evidence of understanding `Species` (from `introspection.md`, section 3): the student correctly
  explains that `DOG("Dog")` calls the enum's constructor and stores `"Dog"` in a `private final`
  `label` field returned by `label()`, and that an enum prevents values such as `"puppy"` or `""` that
  a `String` would allow. This shows understanding of the field-and-method point.
- **(−1)** The opening sentence of that paragraph gives the display label as the *reason* `Species` is
  an enum, which is not a reason. The introspection also does not mention that `Species` overrides
  `toString()` to return the label, while `name()` still returns `"DOG"`, a distinction worth knowing
  for the code walk.
- **(−1) Other introspection weaknesses.**
  - Section 2, decision 1 describes the consequence of re-deriving the age but never states the
    decision or the alternative.
  - Section 2, decision 2's cost line is inverted ("Without the null check, it would cost us an extra
    if statement"). The null check *is* the extra `if`.
  - Section 4 pastes the test code but does not say what prompted each test, which is what the
    template asks.
  - The "still untested" answer covers only the null-name message, not null age or date.
- Section 6 honestly acknowledges relying on an LLM for `toString()` and the constructor. That candour
  is a strength, and the code walk is the place to show those lines are now understood.

### Total: 92 / 100

**Single most important thing to do differently next time:** treat every documented rule as needing
its own test of the *message*, not only the exception type. Three of the five refusals in `Animal` are
still untested at the level the rubric grades.

**One thing done genuinely well:** the `Animal` constructor. It checks every argument before assigning
anything, checks null before calling `strip()`, stores the stripped name, and throws an
`IntakeException` whose message names the offending argument each time.

**Coverage reported:** 100% line coverage (51/51 lines; 207/207 instructions, 22/22 branches) from
`build/reports/jacoco/test/html/index.html`



## Your response

The part that is actually marked. For each point below, a few sentences.

### Where it is right

Which criticisms do you accept? For each, say what you would change and why you agree.

### Where it is wrong

Which criticisms do you reject, and on what grounds? LLMs confidently misread code, invent
requirements that are not in the specification, and flag correct code as broken. Disagreeing with a
specific reason is worth more marks here than agreeing with everything.

### What it missed

What do you know is weak in your submission that the assessment did not mention? Volunteering this
costs you nothing and demonstrates you understand your own code.

### What you changed

If you changed anything as a result, say what and why. If you changed nothing, say that and defend
it.
Nothing

## Declaration

- Which LLM and version you used: Claude Opus 5.5 (Anthropic), via claude.ai
- Confirm you understand every line you submitted, regardless of who or what wrote it: [yes/no] Yes