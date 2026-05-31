---
name: land-and-deploy
description: Merges PR+waits CI+deploys+canary checks
icon: 🚀
---

# land-and-deploy

Land and deploy workflow. Merges the PR, waits for CI and deploy, verifies production health via canary checks.

## When to Use

Use when: "merge", "land", "deploy", "merge and verify", "land it", "ship it to production".

Takes over after [[gstack /ship]] creates the PR.

## Triggers

- merge and deploy
- land the pr
- ship to production

## See Also

- [[gstack /ship]] — Creates the PR that land-and-deploy lands
- [[gstack /review]] — Quality assurance before deployment
- [[gstack /qa]] — Testing and verification

## Category

[[entities/concepts-frameworks/gstack]]
