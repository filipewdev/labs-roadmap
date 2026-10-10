# Lab PF-1 — Static Site Infrastructure on AWS

**Goal**: Serve a one-page placeholder over HTTPS from a private S3 bucket
behind CloudFront, with strict security headers, all defined in Terraform and
deployed by GitHub Actions without a single stored AWS key.

**Time box**: 8 hours

**Language**: Terraform (HCL) + YAML + Shell

**AWS parallel**: This lab *is* AWS. It covers S3, CloudFront, Route 53, ACM, IAM roles, OIDC federation, CloudFront Functions and Budgets: a large slice of the Solutions Architect Associate exam, done for real.

**Done when**:
- [ ] S3 bucket is private: Block Public Access on, no website endpoint, versioning on
- [ ] CloudFront serves the bucket through Origin Access Control (OAC); the bucket policy allows only that distribution
- [ ] ACM certificate (issued in `us-east-1`) covers the apex domain and `www`; `www` redirects to the apex
- [ ] Route 53 hosted zone with alias records for the apex and `www`
- [ ] Response headers policy sets: Content-Security-Policy (with `frame-ancestors 'none'`, `object-src 'none'`, `base-uri 'self'`), Strict-Transport-Security, X-Content-Type-Options, Referrer-Policy, Permissions-Policy
- [ ] A viewer-request CloudFront Function sends `/` to `/en/`, `/pt/` or `/fr/` from `Accept-Language` (English by default) and maps clean URLs to `index.html`
- [ ] Terraform state lives in a remote S3 backend with locking enabled
- [ ] GitHub Actions assumes an IAM role through OIDC; the trust policy is pinned to your repo and main branch; the role can only sync to that bucket and invalidate that distribution
- [ ] Pull requests run `terraform plan`; merging to main applies and deploys
- [ ] An AWS Budgets alert fires at US$5 per month
- [ ] MDN HTTP Observatory grades the placeholder page A+ (score 100 or more)
- [ ] No AWS access keys exist in the repo, in GitHub secrets, or anywhere except your own SSO session

**Key concepts**:
- Why OAC and a private bucket beat a public S3 website endpoint
- Why the CloudFront certificate must live in `us-east-1`
- CloudFront Functions vs Lambda@Edge: what each can do, what each costs
- OIDC federation: the token GitHub issues, the `aud` and `sub` claims, and how the trust policy checks them
- Response headers policies, and why a header CSP can do things a `<meta>` CSP can't (`frame-ancestors`)
- Terraform remote state and locking: what goes wrong without them
- Alias records vs CNAMEs at the zone apex

**Watch out for**:
- CloudFront's flat-rate free plan doesn't include custom response headers policies. Use pay-as-you-go; at personal-site traffic it stays inside the free tier.
- GitHub's OIDC `sub` claim format can differ for newer repositories. Check the claim your repo actually sends before pinning the trust policy to it.
- Skip AWS WAF for this site; its fixed monthly cost outweighs everything else here.

**Local environment**: Terraform CLI and AWS CLI with an IAM Identity Center (SSO) profile. No containers needed.

**Testing requirement**: CI runs `terraform fmt -check`, `terraform validate`, `tflint` and an IaC scan (`trivy config` or `checkov`) on every pull request. After apply, a `test.sh` asserts with `curl`: HTTPS returns 200; HTTP redirects to HTTPS; `www` redirects to the apex; every security header is present with the expected value; the bucket's direct URL returns 403; `/` redirects by `Accept-Language`. A CI step calls the Observatory API and fails below 100.

**AI usage note**: Good questions: *"Why does OAC need a bucket policy condition on `AWS:SourceArn`?"*, *"What does this trust policy condition actually match?"*. Write the Terraform yourself; read the provider docs for each resource first.

**What to put in README**: "Static hosting on AWS: private S3 behind CloudFront with OAC, security headers from a response headers policy, language routing in a CloudFront Function, everything in Terraform, deployed by GitHub Actions through OIDC with no stored keys. Observatory A+, about US$1 a month."
