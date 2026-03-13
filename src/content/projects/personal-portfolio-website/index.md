---
title: "Personal Portfolio Website"
description: "Personal portfolio site built with Astro, deployed to AWS S3 + CloudFront via GitHub Actions."
date: "Mar 8 2026"
demoURL: "oliverbirchill.co.uk"
repoURL: "https://github.com/oliverbirchill/portfolio"
---

**Stack:** Astro · AWS S3 · CloudFront · GitHub Actions  
**Type:** Personal project  

---

## Overview

This is the site you're on right now. Building it was deliberate, hands-on practice in using the theoretical knowledge I'd learnt in studying for my AWS Certified Cloud Practitioner exam, as well as a place to showcase my work. 

---

## Tech Stack & Why

**Astro** was the my first choice for a content-focused static site. It ships zero JavaScript by default, which means excellent Lighthouse scores and no unnecessary complexity. As content is just markdown, it's really easy to update pages with new projects and blog posts as needed.

I wanted to use **S3 + CloudFront** over something like Vercel or Netlify so I got actual practice deploying website infrastructure. Managed platforms like these are convenient, but they abstract away the infrastructure; manually deploying the infrastructure meant I got practice using AWS. 

Going with S3 and CloudFront meant:

- Full control over caching behaviour and cache invalidation
- Cheap hosting costs (S3 storage + CloudFront data transfer for a low-traffic static site is basically free)
- Developing familiarity with AWS

The big tradeoff here is considerably more setup as I had to configure the S3 bucket policy, wire up the CloudFront distribution with the correct origin endpoint, and setting viewer protocol policies manually. 

---

## Continuous Deployment

Deployments are fully automated via a GitHub Actions workflow that triggers on every push to `main`. The pipeline:

1. Checks out the repository
2. Configures AWS credentials from repository secrets
3. Installs dependencies and runs `astro build`
4. Syncs the `dist/` output to S3 with `aws s3 sync --delete` (the `--delete` flag ensures removed pages don't linger)
5. Creates a CloudFront invalidation on `/*` so the CDN immediately serves the new build

AWS credentials are scoped to a least-privilege IAM policy — the GitHub Actions user can only do `s3:PutObject`, `s3:ListBucket`, `s3:DeleteObject`, and `cloudfront:CreateInvalidation` and absolutely nothing else. This was a deliberate security decision: if the credentials were ever exposed, the threat actor is limited to only deploying objects within this bucket. 

---

## What I'd Do Differently / Things to implement in the future

 - Deploy the website using infrastructure as code
 - OIDC instead of long-lived credentials: The GitHub Actions workflow currently authenticates to AWS using a static access key stored as a repository secret. A better approach is to configure OpenID Connect (OIDC) between GitHub and AWS, allowing the workflow to assume an IAM role directly without any stored credentials. This eliminates the risk of a secret being leaked entirely.
 - Multiple environments: Right now there's only production. A proper dev/staging/prod setup — with separate S3 buckets and CloudFront distributions per environment — would allow changes to be previewed and tested before going live. Branch-based deployments in GitHub Actions (e.g. dev → staging, main → production) would make this straightforward to automate.
 - Smarter CloudFront invalidations: The current workflow invalidates /* on every deploy, which is simple but blunt — it purges the entire CDN cache even for unchanged assets. A better approach would use Astro's content-hashed filenames for static assets (which can be cached indefinitely) and only invalidate the small set of un-hashed entry points like index.html. Obviously this is absolutely fine for a small personal website, but I've noted it for consideration!