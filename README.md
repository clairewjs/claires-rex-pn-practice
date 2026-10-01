# Claire’s REx-PN Practice: full GitHub version

This package contains the complete website, student accounts, independent instructor login, server scoring, and central class logs. GitHub stores the code. Cloudflare Workers serves the website and D1 stores records. GitHub Pages alone cannot run this backend.

## Publish

1. Create a **private** GitHub repository named `claires-rex-pn-practice`. Upload the extracted package contents into the repository root. Never upload passwords, credentials CSVs, or student database exports.
2. Create a Cloudflare account. On your computer install Node.js 24 or newer and Python 3, download your repository, and open a terminal in its folder.
3. Run `npm ci`, then `npm test`. Run `npx wrangler login` to authenticate directly with Cloudflare.
4. Run `npx wrangler d1 create claires-rex-pn`. Copy the returned database ID into `wrangler.jsonc`, replacing `REPLACE_WITH_YOUR_DATABASE_ID`. Commit that configuration change to your repository.
5. Run `npx wrangler d1 migrations apply DB --remote` to initialize the new database.
6. Run `npx wrangler deploy` to deploy the website. Instructor login stays disabled until the following secrets are configured.
7. Run `python3 scripts/instructor-credentials.py`. It asks for your new password without displaying it, then prints a salt and password hash. Keep both private. Run `npx wrangler secret put INSTRUCTOR_PASSWORD_SALT` and paste the salt at its prompt. Run `npx wrangler secret put INSTRUCTOR_PASSWORD_HASH` and paste the hash. Do not send your password to ChatGPT or commit these values. Your instructor username is `claire`.
8. Open the URL Wrangler provides, followed by `/instructor`. Sign in with `claire` and the password you chose. Create student accounts and privately give students their assigned credentials and the main website URL.

The generated `workers.dev` URL has no ChatGPT in it. A paid domain is optional. Cloudflare plans have usage limits; test a class-sized sign-in and account creation workload before relying on a free plan.

## Updates from GitHub

In Cloudflare Workers, connect the GitHub repository under Builds. Use build command `npm test` and deploy command `npx wrangler deploy`. The D1 binding and instructor secrets must remain configured. Apply new SQL migrations before deploying changes that require them. Alternatively, pull GitHub changes locally and repeat test and deploy commands.

## What Claire can track

Dashboard: assigned accounts, last sign-in, completed practice sets, attempts, in-progress status, scores and detailed completed reviews. Full CSV exports: student summaries, attempts and activity. Claire can reset passwords or disable access without deleting records. Students must change their temporary password at first login. A sign-in alone never counts as completion.

## Existing website and records

This is a separate deployment. It does not automatically copy existing student accounts or class records from the original site. Export your class logs there before changing student links. If existing accounts must be preserved, arrange a private database migration before switching students; do not place database exports in GitHub. The original site is not changed by this package.

## Instructor recovery and backups

To reset your instructor password, rerun the credential generator and update both Cloudflare secrets. Replacing the hash invalidates earlier instructor sessions. Student passwords are reset in the dashboard. Export course logs regularly. Cloudflare D1 backup/recovery is managed separately in your Cloudflare account.

## Validation

`npm test` builds the Worker and verifies authentication, rejection of forged platform headers, roles, student isolation, password changes, server scoring, locked answers, resume, timeout, logs, CSV safety, password reset, disabled access and request origin checks. It also checks page rendering using a simulated DOM. Live Cloudflare deployment and real browser testing are still required after setup.

Original educational content: ten lessons, ten six-question sets and a 60-question mock using the same question bank. The 70% practice benchmark does not predict REx-PN passing or readiness. Not affiliated with NCSBN or BCCNM.
