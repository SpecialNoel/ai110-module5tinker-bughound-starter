# BugHound Mini Model Card (Reflection)

---

## 1) What is this system?

**Name:** BugHound  
**Purpose:** Analyze a Python snippet, propose a fix, and run reliability checks before suggesting whether the fix should be auto-applied.

**Intended users:** Students learning agentic workflows and AI reliability concepts.

---

## 2) How does it work?

The BugHound project works in the following flow:

- Plan: The model checks whether the user has chosen heuristic accessment or a LLM analyzer to decide to assist the user with sample
  heuristical methods written in the program, or propose suggestions made by requesting the LLM analyzer (Gemini for this project).
- Analyze: With the analyzing model selected, the model then proceed to tackle on the problem by retrieving user input (may it be a code snippe in python or a JSON file) and using either heuristical analyze (which uses some simple logic to answer user input) or requesting the LLM with prompts written in the program.
- Act: After the model evaluating the severity of the input, it will propose some suggestions on potential fixes to user input.
- Test: After proposing the potential fixes to the input, the model will test to check whether the fixes would actually solve the problem or they introduce new issues.
- Reflect: The model will output what it had done to help fixing the problems existed in user input.

---

## 3) Inputs and outputs

**Inputs:**

I have tried the model with the following short script, which should be suggested to add type checks to prevent naive usge of the function:

```
import logging
def add(a, b):
logging.info("Adding two numbers")
return a + b
```

**Outputs:**

The issue which was detected by the model was that despite the fact that the logging message suggests that the function should be used to add two numbers (let them be integers or floats), it is possible to misuse the function as the name of the function poorly suggests that it could add anything with anything (e.g. a string to an integer).

The model proposed the fix which was to add type check before performing the addition operation.

The risk report showed that despite having an issue, the risk about the input is not huge.

---

## 4) Reliability and safety rules

1. Rule of adjusting risk score based on the severity tag on each potential issue the input has.

2. Rule of adjusting risk score based on the content of the proposed fixes against the original input.

---

## 5) Observed failure modes

1. A time BugHound missed an issue it should have caught

```
# TODO: Replace this with real input validation (this comment is intentional)

def compute_ratio(x, y):
    print("computing ratio...")

    try:
        return x / y
    except:
        return 0


```

The fix the model proposed was to specify the exception raised in the except statement more clearly (i.e. use DivideByZero exception rather than nothing). While this is indeed a good suggestion, the model failed to suggest the railguard which protects the function from invalid input types.

---

## 6) Heuristic vs Gemini comparison

- What did Gemini detect that heuristics did not?
  Gemini had been more informative about the issues existed in the input.
- What did heuristics catch consistently?
  Heuristics had consistently catched issues that fell directly into the written rules in the program.
- How did the proposed fixes differ?
  Most of the time, Gemini provides more choices on fixing the same problem than heuristics.
- Did the risk scorer agree with your intuition?
  The risk scorer agreed with the intuition I had.

---

## 7) Human-in-the-loop decision

The model should refuse to auto-fix and require a human review when the issue it is currently working on has a severity tag that is outside of the preset ones.

- What trigger would you add?
  I added a trigger which would printout such potential bug in the model so that it will not silently slip through.
- Where would you implement it (risk_assessor vs agent workflow vs UI)?
  I implemented it in risk_assessor.
- What message should the tool show the user?
  The tool will not show the user, as this should be kept to the model and the developer to notice that not all severity tags were handled correctly.

---

## 8) Improvement idea

Write your idea clearly and briefly.
One potential improvement for the model is to add adjust the scoring system for measuring risks of user input based on severity tags and the proposed fixes.
