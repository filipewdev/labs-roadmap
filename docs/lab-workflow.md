# Lab Workflow Guide

> How to approach each lab from start to finish. This guide applies to every
> lab in the roadmap regardless of language or topic. The core principle:
> **you think first, then use tools (including AI) to sharpen your thinking.**
> Never the reverse.

---

## Weekly Schedule (Weekend Labs)

Labs are designed for weekend work. The weekday prep is optional but makes
the weekend significantly more productive.

```
Friday evening    (15-20 min)  Read spec + docs + set up repo
Saturday morning  (10-15 min)  Write your plan, then code
Saturday/Sunday   (bulk)       Implement test-first
Sunday            (30 min)     AI review + lab journal + partner discussion
```

---

## Friday Evening — The Prep (15-20 min)

### Step 1: Read the Lab Spec Fully

Not skimming. Read:
- The **Done When** checklist — this is your definition of done
- The **Key Concepts** — these are what you need to understand, not just implement
- The **Testing Requirement** — know what tests you're writing before you write code
- The **AI Usage Note** — each lab has specific guidance on when AI helps and when it hurts

### Step 2: Read the Official Docs

Read the official documentation for the main tool in the lab. Not a tutorial,
not a Medium article, not a YouTube video — the actual docs written by the
people who built the tool.

| Lab topic | What to read |
|---|---|
| Go (first time) | [A Tour of Go](https://go.dev/tour/) — do the exercises |
| Go (subsequent labs) | `go doc` for the specific packages you'll use |
| PostgreSQL | Official docs for the feature (e.g., [Window Functions](https://www.postgresql.org/docs/current/tutorial-window.html), [EXPLAIN](https://www.postgresql.org/docs/current/using-explain.html)) |
| Redis | [Redis data types](https://redis.io/docs/data-types/) and the command reference for each structure |
| MongoDB | [Aggregation Pipeline](https://www.mongodb.com/docs/manual/core/aggregation-pipeline/) |
| RabbitMQ | [RabbitMQ Tutorials](https://www.rabbitmq.com/tutorials) (official, language-specific) |
| Kafka | [Kafka Introduction](https://kafka.apache.org/intro) — genuinely well written |
| Docker | [Dockerfile reference](https://docs.docker.com/reference/dockerfile/) and [Compose specification](https://docs.docker.com/compose/compose-file/) |
| Nginx | [Nginx beginner's guide](https://nginx.org/en/docs/beginners_guide.html) then the directive reference |
| Prometheus | [Prometheus concepts](https://prometheus.io/docs/concepts/data_model/) |
| GraphQL | [GraphQL Learn](https://graphql.org/learn/) — the official tutorial |
| Kubernetes | [Kubernetes concepts](https://kubernetes.io/docs/concepts/) |
| Terraform | [Terraform intro](https://developer.hashicorp.com/terraform/intro) |
| Flutter | [Flutter docs](https://docs.flutter.dev/) for the specific feature |
| Python/ML | [NumPy quickstart](https://numpy.org/doc/stable/user/quickstart.html), [PyTorch tutorials](https://pytorch.org/tutorials/) |

**Why official docs over tutorials**: Tutorials show you *how*. Docs explain
*why* and *what else exists*. The labs teach concepts, not steps. If you follow
a tutorial first, you'll copy patterns without understanding them — and the
lab will feel like you're just transcribing someone else's work.

### Step 3: Set Up the Repo and Docker

Remove Saturday morning friction:

```bash
# Create the repo
mkdir lab-08-postgresql && cd lab-08-postgresql
git init

# Copy docker-compose.yml from the lab's local environment section
# and start services
docker compose up -d

# Verify services are running
docker compose ps

# Create the project structure (language-specific)
# TypeScript: pnpm init, install dependencies
# Go: go mod init
# Python: python -m venv .venv, pip install
```

When you sit down Saturday morning, everything is already running. You open
the editor and start coding immediately.

---

## Saturday Morning — The Plan (10-15 min)

Before writing any code, spend 10-15 minutes writing a plan. Five bullet
points. On paper, in a markdown file, or in your head — doesn't matter.
The act of planning is what matters.

### Example: Planning Lab 11 (RabbitMQ)

```markdown
## My plan for Lab 11

1. Verify RabbitMQ is running in Docker, explore the management UI
2. Build the producer: HTTP endpoint → topic exchange
3. Build one consumer (inventory) and verify messages flow
4. Add failure simulation (20% random failure) + retry logic
5. Configure DLQ, build DLQ consumer, write tests
```

This takes 5 minutes and saves hours of wandering.

### Using AI to Challenge Your Plan

After writing your plan, you can optionally ask AI to stress-test it.

#### The right way

```
"I'm starting Lab 11 (RabbitMQ order processing pipeline). My plan is:
1. Docker compose with RabbitMQ + management plugin
2. Build producer: HTTP POST /orders → topic exchange with routing key order.created
3. Build inventory consumer first, verify message flow
4. Add 20% random failure + manual ack with retry counter in headers
5. Configure DLQ exchange + binding, build DLQ logger consumer
6. Write tests: unit for routing logic, integration for the DLQ flow

What am I likely to get stuck on? What should I understand about
AMQP before writing the producer?"
```

**Why this works**: You've already thought through the approach. AI is
reviewing your plan, not creating it. This is the same skill as presenting
a technical plan to your team.

#### The wrong way

```
"Plan Lab 11 RabbitMQ for me. What should I build first?"
"Give me a step-by-step guide to implement Lab 11"
"What's the architecture for a RabbitMQ order processing system?"
```

**Why this fails**: You skip the thinking. The plan AI gives you will be
correct, but you won't understand why each step is in that order. When
something goes wrong (and it will), you won't know how to adapt because
the plan was never yours.

---

## Saturday/Sunday — The Implementation

### The Coding Loop

```
1. Write a failing test (or define what "done" looks like for this piece)
2. Implement until the test passes
3. Refactor if needed
4. Commit with a conventional commit message
5. Repeat for the next piece
```

This is test-driven development (TDD). It feels slow at first. By Lab 05,
it will feel natural. By Lab 15, you won't be able to code without it.

### When You Get Stuck

Getting stuck is not a failure — it's the learning happening. How you
respond to being stuck determines whether you actually learn.

#### Situation: Syntax error you don't understand

**Do this**:
```
"I'm getting this error in Go:

  cannot use msg (variable of type amqp.Delivery) as type []byte in argument

What does this error mean? What's the type mismatch?"
```

**Not this**:
```
"Fix this error: cannot use msg as type []byte"
"My Go code doesn't compile, here's the file, make it work"
```

**Why**: Understanding the error teaches you the type system. Having AI fix
it teaches you nothing — you'll hit the same class of error tomorrow and
still not understand it.

#### Situation: A concept you don't understand

**Do this**:
```
Step 1: Read the official docs section on the concept
Step 2: If still unclear, ask AI to explain:

"I'm reading about RabbitMQ exchanges. I understand that a direct exchange
routes by exact key match. But I don't understand when I'd use a topic
exchange vs a fanout exchange. Can you explain with a concrete example?"
```

**Not this**:
```
"Explain RabbitMQ to me"
"What's the difference between all the exchange types?"
```

**Why**: The first shows you've already read and partially understood.
AI fills the specific gap. The second asks AI to teach you from scratch —
which means you skip the struggle of reading docs, which is where real
understanding forms.

#### Situation: Your approach isn't working

**Do this**:
```
"I'm building a RabbitMQ consumer in Go. My approach: I read from the
channel in a for loop and call a handler function for each message.
But when the handler fails, I want to retry 3 times before sending
to the DLQ.

My current approach is re-publishing the message to the same queue with
a retry counter in the headers. But I'm seeing the message get redelivered
immediately without the updated header.

What's wrong with my mental model of how re-publishing works?"
```

**Not this**:
```
"How do I implement retry logic in RabbitMQ?"
"Give me a Go RabbitMQ consumer with retry and DLQ"
"My consumer isn't working, here's my code, fix it"
```

**Why**: The first describes what you tried, what you expected, and what
actually happened. This is the same format as a good bug report — and AI
(like a colleague) can pinpoint the misconception. The second skips your
own thinking entirely.

#### Situation: You have no idea where to start on a piece

This usually means you skipped understanding a concept. Go back to the
official docs. If the docs are unclear, ask AI to explain the concept —
not to write the code.

**Do this**:
```
"I need to implement a DataLoader for my GraphQL resolvers to solve the
N+1 problem. I've read the DataLoader docs but I don't understand how
batching works. Can you walk me through what happens when GraphQL resolves
a list of books, each with an author, and how DataLoader batches the
author queries?"
```

**Not this**:
```
"Implement DataLoader for my GraphQL book/author schema"
"Write a batching function for my author resolver"
```

### When AI IS the Right Tool During Implementation

Not all AI use during coding is bad. Here are legitimate uses:

**Debugging a configuration file** (Docker Compose, Nginx, Prometheus):
```
"My Prometheus scrape config isn't picking up the Go server's /metrics
endpoint. Here's my prometheus.yml. What's wrong with the target config?"
```
Configuration debugging is tedious and error-prone. AI is good at spotting
typos in YAML. This is fine.

**Understanding an error from a tool you don't control**:
```
"Kafka is giving me: 'LEADER_NOT_AVAILABLE for partition 0'. What does
this mean and what's the typical cause when running locally in Docker?"
```
Infrastructure errors are often cryptic. AI translating them into plain
language is a legitimate use.

**Comparing approaches you've already thought about**:
```
"I'm deciding between cursor-based and offset-based pagination for my
books API. I think cursor is better for infinite scroll but offset is
simpler for page numbers. Am I right? What am I missing?"
```
You've already formed an opinion. AI validates or corrects it.

---

## After the Lab — Review and Reflect

### AI Code Review (15-20 min)

After your implementation is complete and tests pass, ask AI to review
your code. This is the "AI as reviewer" pattern from the methodology.

**A good review request**:
```
"Here's my RabbitMQ consumer code (Go). Review it for:
- Error handling gaps
- Patterns that wouldn't work in production under load
- Anything I'm doing that's idiomatic in Node.js but wrong in Go
- Potential race conditions in the retry logic"
```

**What to do with the feedback**: Don't blindly apply every suggestion.
For each one, ask yourself: "Do I understand why this is better?" If not,
ask AI to explain. If you disagree, that's fine — form your own opinion
and document why in your README.

### Lab Journal (15 min)

Open the lab journal issue (use the template in `.github/ISSUE_TEMPLATE/`).
Fill in while the experience is fresh:

- **What clicked**: the moment something made sense
- **What confused me**: what took longer than expected
- **What I'd do differently**: the decision you'd change if starting over
- **Key concepts (3-5)**: in plain language, with "so what?" for each

The confusion you feel mid-lab is evidence of learning. Once it makes sense,
that confusion is hard to reconstruct. Write it down now.

### Partner Discussion (30-60 min, Sunday or Monday)

If you're working with a study partner:

```
1. Each person shows their implementation (5-10 min each)
2. Compare: what did you do differently? Why?
3. Discuss: what confused you? What clicked?
4. Challenge: ask each other "why did you choose X instead of Y?"
```

This is not pair programming. You worked independently. The value is in
the comparison — seeing a different solution to the same problem teaches
you that there are multiple valid approaches, and why each has tradeoffs.

---

## Common Mistakes to Avoid

### Mistake: Following a YouTube tutorial alongside the lab

**What happens**: You watch someone build the same thing and follow along.
It feels productive — the code works, the tests pass. But a week later
you can't rebuild it from memory because you never made the decisions
yourself.

**Instead**: The lab spec is your guide. The official docs are your
reference. If you're completely lost, ask AI to explain a concept —
not to walk you through the implementation step by step.

### Mistake: Using AI-generated boilerplate as your starting point

**What happens**: You ask AI to scaffold the project structure, generate
the docker-compose file, set up the test framework, and create stub files.
You then "fill in" the implementation. This feels efficient but you skip
understanding the project structure — which is part of the learning.

**Instead**: An empty directory is your starting point. Create each file
yourself. If you don't know what files you need, that's a signal to read
the framework's "getting started" guide in the official docs.

The one exception: `docker-compose.yml` for services you're not building
(PostgreSQL, Redis, RabbitMQ). Using AI or docs to get the compose file
right is fine — these are infrastructure, not your application.

### Mistake: Reading other people's solutions before attempting your own

**What happens**: You search GitHub for "rabbitmq go tutorial" and read
someone's implementation before starting yours. Your solution ends up
looking like theirs — but you can't explain why you made each decision
because you didn't make them.

**Instead**: Attempt the lab with only the spec and official docs. Get
stuck. Struggle. After you're done, then compare with others (or with
your study partner). The comparison is valuable — but only after you
have your own solution to compare against.

### Mistake: Spending too long before asking for help

**What happens**: You're stuck on the same problem for 2 hours. You refuse
to use AI or ask anyone because you want to "figure it out yourself."
Your Saturday is gone and you've written 20 lines of code.

**Instead**: The 30-minute rule. If you're stuck on the same thing for
30 minutes with no progress, it's time to get help — but get the right
kind of help. Describe what you've tried and where you're stuck. Ask for
an explanation of the concept, not a fix for the code. Then go back and
apply your new understanding yourself.

### Mistake: Polishing instead of finishing

**What happens**: You spend 3 hours on the first two checklist items,
making them perfect. The remaining items don't get done. You have a
beautiful producer and no consumers.

**Instead**: First pass: make everything work (ugly is fine). Second pass:
clean up. A complete, rough implementation teaches more than a half-finished
polished one. Hit every item in the "Done When" checklist before refactoring
anything.

---

## The One-Page Checklist

Print this or keep it open. Use it for every lab.

```
PREP (Friday, 15-20 min)
  □ Read the lab spec fully (Done When + Key Concepts + Testing)
  □ Read official docs for the main tool
  □ Set up repo + docker compose + verify services running

PLAN (Saturday, 10-15 min)
  □ Write your plan in 5 bullet points
  □ Optional: ask AI to challenge your plan

BUILD (Saturday/Sunday, bulk of the time)
  □ Code test-first: failing test → implement → pass → commit
  □ When stuck: docs first → AI for explanation → never AI for code
  □ Hit every item in Done When before polishing anything

REVIEW (Sunday, 30-45 min)
  □ Ask AI to review your completed code
  □ Fill in lab journal issue (what clicked, what confused, what you'd change)
  □ Discuss with study partner (compare implementations)
  □ Commit final changes, verify CI is green
```
