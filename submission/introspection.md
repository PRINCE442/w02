# Design Introspection

Your own reflection on the design decisions you made this week. Written in your own words — this is
distinct from the LLM's assessment of your code, and distinct from your code-walk video.

Aim for a page. Cite your actual code: name the class and method you are talking about.

## 1. What you built

In two or three sentences: which types you wrote this week, and what each one is responsible for.

Ans=> AgeMonths: This type makes sure that no animal's age can ever be negative or more than 480months.
      Animal: This describes the intake records of an animal in the animal shelter.

## 2. Design decisions

Pick the two or three decisions you actually had to think about, and for each one:

- **What was the choice?** What were the alternatives you considered?
- **What did you pick, and why?** What would have gone wrong with the other option?
- **What did it cost?** Every real design decision costs something.

Good candidates: where you put a piece of behaviour and why it belongs there rather than somewhere
else; how you represented something so that an invalid version could not be built; where you chose to
delegate to existing code rather than re-deriving an answer.

Ans=> 1. AgeMonths.toString() would print "1y 11m", but Animal.toString() would still print "1 year, 11 months". The system would end up showing two different string formats for the exact same age and also Failed tests: Any unit tests checking if Animal's age representation matches AgeMonths would fail because Animal’s hardcoded math and string formatting were never updated. It costs that Animal depends on AgeMonths getting its format right.

    2. Putting the null check before name.strip(). If you swap them then java would crash with a NullPointerException, Making sure the null check is before name.strip() ensures that the crash is prevented and throws a clear IntakeException instead. So:

strip first → NullPointerException (ugly crash, unhelpful message)
null check first → IntakeException("name must not be null") (clear and intended)

Without the null check, it would cost us an extra if statement.


## 3. Invariants

What does your code guarantee about itself, and where is each guarantee enforced?

For each type that validates its input: what must always be true of an instance once it exists, and
which line makes that true? If a guarantee is enforced in more than one place, say why — and whether
that is deliberate or duplication.

Ans => AgeMonths: 0 to 480, enforced in of(). Animal: a non-blank trimmed name and nothing null, enforced in the constructor. The Animal does not re-check the age range.
The animal constructor does not check that age is between 0 and 480 because it has already been checked in the AgeMonths object, so checking it again in Animal class would lead to pointless duplication or repetition of code.

     Species is an enum instead of three Strings like "Dog", "Cat", "Bird" because "Dog" is the species' display label, the readable version of DOG. DOG("Dog") calls the enum's constructor and stores "Dog" in its private final label field, which label() returns.
     An enum beats plain string because it Restricts Allowed Values: With a String, someone could accidentally pass in "dog", "DOG", "puppy", "banana", or even an empty string "", and the compiler wouldn't stop them. An enum forces the program to only accept valid, predefined choices (Species.DOG, Species.CAT, Species.BIRD). 

## 4. Testing

- Which cases did you add beyond the provided tests, and what made you think of them?
- Which test was hardest to write, and what did writing it teach you about your own design?
- What is still untested, and how would you test it if you had another hour?

Ans=> 1 - @Test
  void oneBelowTheMaximumIsAllowed() {
    AgeMonths age = AgeMonths.of(AgeMonths.MAX_MONTHS - 1);
    assertEquals(479, age.months());
    assertEquals("39 years, 11 months", age.toString());
  }

  2 @Test
  void theMaximumDescribesItselfAsWholeYears() {
    assertEquals("40 years", AgeMonths.of(AgeMonths.MAX_MONTHS).toString());
  }

  3 @Test
  void theNegativeRefusalNamesTheOffendingValue() {
    IntakeException tooYoung = assertThrows(IntakeException.class, () -> AgeMonths.of(-1));
    assertTrue(tooYoung.getMessage().contains("-1"),
        "the message should name the value that was rejected");
  }

  4 @Test
  void pluralsWorkBeyondTwoYears() {
    assertEquals("3 years, 1 month", AgeMonths.of(37).toString());
    assertEquals("3 years, 2 months", AgeMonths.of(38).toString());
  }

 5 @Test
  void aNullSpeciesMessageNamesSpecies() {
    IntakeException e = assertThrows(IntakeException.class,
        () -> new Animal("Rex", null, AgeMonths.of(5), LocalDate.of(2026, 9, 21)));
    assertTrue(e.getMessage().contains("species"));
  }

  6 @Test
  void tabsAndNewlinesAroundTheNameAreTrimmed() {
    Animal a = new Animal("\tRex\n", Species.DOG, AgeMonths.of(5), LocalDate.of(2026, 9, 21));
    assertEquals("Rex", a.name());
  }

  7 @Test
  void toStringWithWholeYearsHasNoMonthsPart() {
    Animal a = new Animal("Rex", Species.DOG, AgeMonths.of(24), LocalDate.of(2026, 1, 5));
    assertEquals("Rex (Dog, 2 years, intake 2026-01-05)", a.toString());
  }

- For me, the hardest test was: I'd say the test of plural works beyond two years. It's idea is just a bit too complex to grasp. 

-  A test for an untested null-name message would instantiate Animal with a null name (or call a method with null) and verify using assertThrows(IntakeException.class, ...) that an exception is thrown containing the expected error message, and it checks that the message contains "name".


## 5. What you would change

Given another day, what would you do differently — and what stopped you this week? Be specific;
"write more tests" is not an answer.


Ans=> I would add the missing message tests, and run Checkstyle simultaneously instead of waiting till the end.

## 6. What you found hard

The honest one. What took the longest, what did you get wrong first, and what finally made it click?
This is not marked on whether you struggled — everyone does — but on whether you can say clearly
where and why.

Ans=> I thought the 20 failing tests meant something was broken, the test count didn't add up, and the git commit failed. Also, I leaned on an LLM to write toString() and also the constructor.