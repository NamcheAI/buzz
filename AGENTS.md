# Repository contract

- This is a deployment wrapper for `block/buzz`, never a source fork.
- Do not vendor, patch, or copy upstream application source into this repo.
- Select upstream only with the full commit SHA in `upstream.lock`.
- Build the selected commit with upstream's own `Dockerfile`.
- Merge to `main` releases staging; a `v*` tag promotes the already-staged
  digest to production. Never rebuild for production.
- Keep host, tailnet, DNS, database, and secret management in `NamcheAI/infra`.
- Use Conventional Commit messages and PR titles. Squash merge only.
