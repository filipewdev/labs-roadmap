# Open Source Track — Overview

> Three labs that teach different aspects of the open source ecosystem.
> These sit alongside the main roadmap and can be done in parallel with
> other phases — they don't have infrastructure dependencies.

---

## The Two Distinct Skills

**Creating a library** and **contributing to open source** are often
conflated but are genuinely different:

| | Creating a Library | Contributing to OSS |
|---|---|---|
| **Codebase ownership** | Yours | Someone else's |
| **Primary skill** | API design, packaging, versioning | Reading unfamiliar code, giving & receiving code review, communication |
| **Audience** | Developers who will depend on your code | Maintainers who gatekeep a project |
| **Success metric** | Downloads, happy users, low issue count | Merged PR, maintainer relationship |
| **What it signals to employers** | You can design APIs others will use | You can navigate large codebases, work asynchronously, communicate in writing |

Both are valuable. Together they cover the full spectrum of skills that
international companies expect from senior engineers who claim "open source experience."

---

## The Three Labs

```
OS-A  Publish an npm library (br-utils from Lab 00-A)
      ↳ TypeScript, dual ESM/CJS, TypeDoc, semver, automated publishing
      ↳ ~8 hours

OS-B  Make your first real open source contribution + give a code review
      ↳ Any language, reading unfamiliar code, PR workflow, receiving AND giving code review
      ↳ 6–10 hours (spread over 2 weekends)

OS-C  Publish a pub.dev package (br_validators for Flutter)
      ↳ Dart, pub.dev scoring, cross-ecosystem portfolio breadth
      ↳ ~6 hours
```

---

## Recommended Order

**Week 1**: Do OS-A immediately after Lab 00-A. You already have the code;
the lab teaches you how to package and publish it properly.

**Week 2–3**: Start looking for OSS projects for OS-B *while doing other labs*.
Finding the right issue is the bottleneck — it can take a week of browsing.
Once you've claimed an issue, do the work in one focused session.

**Month 3+**: Do OS-C after Lab 21 (Flutter biometric auth), when you're
working heavily in Dart anyway. Revisiting the same problem (Brazilian validation)
in a second language and ecosystem deepens both skills.

---

## What Goes Into Your Portfolio

After completing all three labs, here is the exact language to use in your
GitHub profile, CV, and LinkedIn:

**GitHub profile README**:
```markdown
## Published Packages

| Package | Downloads | Description |
|---------|-----------|-------------|
| [@yourname/br-utils](link) | ![npm](badge) | CPF, CNPJ, CEP utilities for TypeScript |
| [br_validators](link) | ![pub](badge) | Same utilities for Dart/Flutter |

## Open Source Contributions

- [Project Name](link to merged PR) — [one sentence description]
```

**CV / LinkedIn**:
> Published and maintain two open source packages across npm and pub.dev ecosystems:
> `@yourname/br-utils` (TypeScript) and `br_validators` (Dart/Flutter).
> Contributor to [Project] — [one sentence].

---

## A Note on Library Maintenance

Publishing a library creates an ongoing responsibility. Users will file issues.
Some will be bugs in your code. Some will be feature requests. Some will be
"why doesn't this work" (user error).

You don't have to respond to everything immediately. You don't have to
implement every feature request. But you should:
- Fix genuine bugs within a few weeks
- Respond to issues even if just to say "confirmed, will fix" or "by design"
- Tag releases when you make changes

An abandoned library (no response to issues, no commits in 2+ years) reflects
poorly even if the code is fine. If you genuinely can't maintain it anymore,
archive the repo — that's more honest than silence.

This is a real ongoing commitment. Think about whether you want it before
publishing something widely used.

---

## AWS Parallel

**npm / pub.dev** → **AWS CodeArtifact**: private package registry for npm,
Maven, NuGet, and PyPI packages inside your AWS VPC. Large organizations
use CodeArtifact to host internal packages (with IAM access control) and to
proxy and cache public packages (for compliance and availability).

**Library versioning + automated publishing** → **AWS CodePipeline + CodeBuild**:
the same pattern — a git tag triggers a build pipeline that publishes an artifact.
In AWS the artifact might go to CodeArtifact or ECR instead of npm, but the
concepts are identical.

**Open source contribution workflow** → **internal GitHub Enterprise / GitLab**:
the fork → branch → PR → review → merge pattern is standard at every company
above early-stage startup. Contributing to open source is the best way to learn
this workflow when you don't have teammates yet.
