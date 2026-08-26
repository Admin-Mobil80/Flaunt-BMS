# Flaunt BMS

Internal admin/business-management static frontend for Flaunt. Plain HTML/CSS/JS in [`public/`](public/), no build step. Deploys to the shared S3 bucket `webapps.flaunt.network` (under the `/BMS` prefix) behind its own CloudFront distribution at `bms.flaunt.network`.

**This stack does not own the shared S3 bucket** — it imports the bucket's regional domain name from [Flaunt-PORTAL](https://github.com/Admin-Mobil80/Flaunt-PORTAL)'s CloudFormation exports via `Fn::ImportValue`. **Flaunt-PORTAL's stack must be deployed at least once before this stack's first deploy**, or the deploy will fail with a missing-export error.

## One-time account setup (do this before the first push to `main`)

1. Confirm [Flaunt-PORTAL](https://github.com/Admin-Mobil80/Flaunt-PORTAL) has already been deployed at least once (its exports must exist).
2. Confirm the GitHub OIDC bootstrap stack has been deployed (see [Flaunt-BACKEND/bootstrap/github-oidc.yaml](https://github.com/Admin-Mobil80/Flaunt-BACKEND/blob/main/bootstrap/github-oidc.yaml)) — this creates the `flaunt-bms-deploy-role` this workflow assumes.
3. Look up your Route 53 hosted zone ID for `flaunt.network`:
   ```bash
   aws route53 list-hosted-zones-by-name --dns-name flaunt.network
   ```
4. In [`.github/workflows/deploy.yml`](.github/workflows/deploy.yml), replace the two placeholders:
   - `DEPLOY_ROLE_ARN` — the `flaunt-bms-deploy-role` ARN from the bootstrap stack's output
   - `HOSTED_ZONE_ID` — the value from step 3
5. Push to `main`. CI will create the cert, distribution, and DNS record (no bucket — that already exists from Portal's stack).

## Local development

Just open `public/index.html` in a browser, or serve the folder with any static file server.
