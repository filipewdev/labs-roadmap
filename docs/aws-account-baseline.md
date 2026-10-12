# AWS Account Baseline

> A one-time setup for any AWS account used in these labs: lock down the
> root user, give yourself a safe daily identity, and make surprise charges
> impossible to miss. Do it before the first lab that touches AWS (PF-1 is
> usually the first). About an hour, and everything here is free.

---

## Why This Comes First

An AWS account with a root password, no MFA and no budget is the most common
way a learning project turns into a large bill or a compromised account. None
of the steps below costs money, and most are one-time.

---

## 1. Clean Up (as root, or your current access)

- [ ] **Billing → Bills:** review the last few months by service and region; delete anything unused that still charges
- [ ] **Resource Explorer or Tag Editor, all regions:** look for forgotten EC2 instances, EBS volumes and snapshots, Elastic IPs, NAT gateways, load balancers, RDS databases and S3 buckets
- [ ] **IAM → Users and the credential report:** delete unused users and access keys

## 2. Lock Down the Root User

- [ ] MFA on, ideally two devices (an authenticator app and a security key)
- [ ] No root access keys, ever
- [ ] Root password in a password manager; two-factor authentication on the root email account
- [ ] **Account page → "IAM user and role access to Billing information" → Activate**, so your daily identity can see costs
- [ ] **Account page → Alternate contacts:** billing, operations and security point to an email you read

After this section, the root user is used only for the few tasks that need it
(changing the root email or password, closing the account, the support plan,
some tax and payment settings).

## 3. A Daily Identity: IAM Identity Center

Prefer IAM Identity Center over an IAM user: you sign in through a portal and
get temporary credentials, so there are no long-lived keys to leak.

- [ ] Enable IAM Identity Center (it enables AWS Organizations, which is free; the account becomes the management account)
- [ ] Customise the **access portal URL** (`https://<name>.awsapps.com/start`). It can be set **only once**, so choose a neutral, lasting name
- [ ] Create your user, with MFA on first sign-in
- [ ] Create groups by role, not by person: `Administrators`, `ReadOnly`
- [ ] Create permission sets from AWS managed policies: `AdministratorAccess` (8-hour session) and `ReadOnlyAccess` (12-hour session)
- [ ] Assign the groups (not the user) to the account with their permission sets
- [ ] Sign in through the portal, then stop using root
- [ ] Command line: `aws configure sso`, with one profile per permission set (for example `main-readonly`, `main-admin`). No access keys

Use the read-only permission set for browsing and the administrator one only
when changing something. Automation (CI/CD) never uses Identity Center: it gets
its own IAM role through OIDC, as in PF-1.

## 4. Cost Guardrails

- [ ] **AWS Budgets:** a monthly cost budget with an email alert at a small amount (US$5 fits the labs)
- [ ] **Cost Anomaly Detection:** a monitor for AWS services and an email subscription with a low threshold
- [ ] **Billing preferences → Alert preferences:** Free Tier alerts on (off by default for an organization's management account); CloudWatch billing alerts on (it only publishes the `EstimatedCharges` metric; it can't be turned off, and it costs nothing)
- [ ] **Cost allocation tags:** a day after your first tagged resources exist, activate your tag keys (`Project`, `Environment`, `Owner`). Costs are grouped by tag only from activation onward

## 5. Security Guardrails

- [ ] **S3 Block Public Access at account level:** all four settings on. Buckets can be stricter, never looser; CloudFront with Origin Access Control still works
- [ ] **IAM Access Analyzer, external access:** zone of trust = the organization, in your main region (analyzers are per region)
- [ ] **Look once:** Trusted Advisor's free checks and the 90-day CloudTrail event history

---

## Naming and Tagging

**Names:** lowercase kebab-case, `scope-what-qualifier`. For example
`account-monthly-cost-usd5`, `org-external-access`, and later
`project-site-bucket`.

**Tags:** a small required set, applied everywhere:

| Key | Example | Notes |
|---|---|---|
| `Project` | `portfolio` | Only on project resources, not account-wide items |
| `Environment` | `prod`, `dev`, `shared` | |
| `Owner` | your handle | Never a personal email |
| `ManagedBy` | `terraform` or `manual` | Warns against hand-editing managed resources |
| `Purpose` | `cost-monitoring`, `security` | Useful on account-wide items |

Tags are case-sensitive and must never hold secrets or personal data. In
Terraform, set them once with the AWS provider's `default_tags` block.

---

## Free, but Optional for Study

- **AWS CloudShell:** a signed-in terminal in the browser
- **Resource Explorer** with an aggregator index: search every region at once
- **IAM policy simulator** and **Access Analyzer policy validation:** test policies before using them
- **Well-Architected Tool:** review a lab's design against the six pillars
- **Compute Optimizer** (opt in for the organization) and **Cost Optimization Hub:** free recommendations, mostly for compute; little to show on a static site

## Leave Off (They Cost Money or Act on Their Own)

- Compute Optimizer **enhanced infrastructure metrics** (paid) and **Automation** (changes resources automatically)
- Access Analyzer's **unused access** analyzer (paid)
- GuardDuty, Security Hub, Inspector, Macie and AWS Config: several start as **free trials that turn into charges**. Turn them on only for a study session, then off
- Control Tower: it enables paid services and is built for real multi-account setups
- A paid CloudTrail trail or CloudTrail Lake, while the free 90-day history is enough

---

## Later: More Than One Account

AWS recommends running no workloads in the management account: it holds
billing, identity and organisation policies only. Workloads go in member
accounts grouped in OUs (for example `Security`, `Workloads/Prod`,
`Workloads/NonProd`, `Sandbox`), protected by service control policies (SCPs),
which don't apply to the management account. Whether a lab's resources live in
the management account or a member account is a real trade-off; record it as an
ADR when you decide.
