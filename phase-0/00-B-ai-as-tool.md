# Lab 00-B — AI as a Coding Tool

> **Time box**: 4 hours (one session)  
> **Language**: Any — this lab is about workflow, not syntax  
> **Standalone**: Yes — do this after 00-A

---

## Goal

Build a set of personal rules for using AI in your coding workflow — rules that accelerate your learning rather than replacing it.

---

## Done When

- [ ] You've completed all 5 exercises below and written your findings in a personal `AI_WORKFLOW.md` file
- [ ] You have a short mental checklist you'll apply to every subsequent lab
- [ ] You can articulate — in one sentence each — when you *should* and *should not* use AI assistance

---

## Why This Lab Exists

AI is the best coding assistant in history. It is also the easiest way to stop learning how to code.

The specific trap for someone in your position — a tech lead getting back to coding — is that AI makes you *feel* productive while atrophying the exact skills you're trying to rebuild. You can generate 300 lines of code in 10 minutes without understanding any of it. The code works. Nothing gets learned. You've just made yourself dependent on a tool that, when it's wrong (and it will be), you can't debug.

The goal of this lab is to build **deliberate habits** around AI use before they get formed by default.

---

## Exercise 1: The Specification Trap (45 min)

**Step 1**: Ask an AI to build a function that calculates the Luhn algorithm (used for credit card number validation). Ask for TypeScript. Accept the output without reading it carefully.

**Step 2**: Now write tests for that function using what you learned in Lab 00-A. Run the tests.

**Step 3**: Examine the function carefully. Answer these questions in writing:
- Could you explain how it works to someone else?
- Could you debug it if a test failed without AI help?
- Could you modify it if the requirement changed (e.g., "now also support length-13 numbers")?
- Could you write it again from scratch, without looking?

**Step 4**: Delete the AI-generated function. Write it yourself, from scratch. Use the tests as your spec (they should already be passing — now make them pass again with *your* code).

**What to note**: How long did it take to write it yourself vs reading and understanding the AI version? What did you actually learn in each approach?

---

## Exercise 2: The Review Mode (30 min)

Take the utility library from Lab 00-A. Ask an AI:

> *"Review this implementation of `isValidCPF`. What are potential issues, edge cases it misses, or ways it could be more readable?"*

Then evaluate each suggestion:
- Is it correct?
- Does it improve readability or robustness?
- Is it a style preference that doesn't matter?
- Is it actually wrong?

**What to note**: How many suggestions were genuinely useful? How many were noise? Can you tell the difference?

This is the correct use of AI for code review: it finds things you missed, but *you* decide what to accept. You need enough understanding to evaluate the suggestions.

---

## Exercise 3: The Rubber Duck Upgrade (30 min)

Traditional rubber duck debugging: explain your code to a rubber duck until you find the bug.

AI rubber duck: explain your code to an AI and ask it *questions*, not for solutions.

Try this prompt structure on any function from Lab 00-A:

> *"I'm implementing [function]. Here's what I have: [code]. I'm not asking you to fix it. I want you to ask me 3 clarifying questions that would help me find the issue myself."*

Then answer the questions. Usually, answering question 2 reveals the problem.

**What to note**: When you ask AI to ask *you* questions instead of giving you answers, how does the interaction feel different?

---

## Exercise 4: Calibrating Trust (45 min)

AI makes confident mistakes. Calibrate your trust level.

Ask an AI the following questions and then verify each answer independently:

1. *"What is the maximum size of a UDP datagram?"*
2. *"In PostgreSQL, what's the difference between `CHAR(10)` and `VARCHAR(10)` in terms of storage?"*
3. *"In Go, can a nil pointer dereference happen inside a method called on a nil receiver?"*
4. *"What's the CPF validation algorithm?"*

For each answer: look it up independently. Was the AI right? Partially right? Confidently wrong?

**What to note**: What pattern do you see in where AI is reliable vs where it's not? (Hint: well-documented facts vs subtle behaviour vs implementation details of specific versions.)

---

## Exercise 5: The "Just Enough" Prompt (30 min)

Most AI prompts either:
- Ask too little and get generic output
- Ask too much and get a solution that teaches nothing

Write three versions of a prompt asking AI to help you implement the Fibonacci sequence with memoization:

**Version A** (too vague): *"How do I implement Fibonacci?"*

**Version B** (too much): *"Write a TypeScript function for Fibonacci with memoization using a Map. Include TypeScript types, JSDoc comments, and error handling for negative numbers."*

**Version C** (calibrated): You write this. The goal: get help that unblocks you without doing the work for you.

Try all three. Look at the outputs. Which output required the most thinking from you?

**What to note**: Draft your own rule for writing "Version C" prompts. What makes a prompt produce output you have to engage with rather than just copy?

---

## Your Personal AI Workflow Rules

After the five exercises, write `AI_WORKFLOW.md` in your utils-lab repo. It should have:

**When I will use AI:**
- (Your list, from the exercises — likely: review mode, rubber duck questions, calibrating understanding, documentation)

**When I will NOT use AI:**
- (Your list — likely: first attempt at any new concept, writing tests, debugging)

**My "just enough" prompt template:**
- (The pattern you found in Exercise 5)

**Red flags I'll watch for:**
- (Signs I'm using AI as a crutch — e.g., pasting output without reading it, not being able to explain what the code does)

---

## AI Usage Note for This Lab

There's a deliberate irony here: this is the one lab where you should use AI *a lot* — but as the subject of study, not as a tool for output. You're not building something. You're building a relationship with a tool.

---

## What to Put in the README

Nothing public — this is a personal workflow document. `AI_WORKFLOW.md` lives in your repo as a reference you'll update over time.

The only portfolio signal here is indirect: engineers who use AI thoughtfully write better code with fewer AI-shaped patterns. Hiring managers notice this in code reviews even when they can't articulate why.

---

## After This Lab: The Rule for Every Subsequent Lab

Every lab card has an **AI usage note** that specifies a permitted use. The default rule, when the note doesn't say otherwise:

> **First attempt always on your own. AI enters as a reviewer or a rubber duck, not a generator.**
