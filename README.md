# CI - CD Workflow Example

## Basic Concepts:
        
| Term | Meaning |
| --- | --- |
| Pipeline | The whole automated process, from push to deploy |
| Workflow | GitHub Actions’ name for a pipeline, defined in a YAML file |
| Trigger / event | What starts it: push, pull_request, schedule, manual |
| Job | A group of steps that runs on one machine. Jobs can run in parallel or in sequence |
| Step | One command or one reusable action inside a job |
| Runner | The machine that executes the job (GitHub-hosted Ubuntu VM, or your own) |
| Action | A reusable step someone else wrote (actions/checkout, actions/setup-node) | 
| Artifact | A file produced by a job (build output, test report) passed on or downloaded |
| Cache | Saved dependencies (like node_modules) so later runs are faster |
| Secret | An encrypted value (API key, AWS credential) injected at runtime, never committed |
| Environment | A named target (staging, production) with its own secrets and optional approval gate | 
| Matrix | Run the same job across several versions (Node 20 and 22) |
| Rollback | Redeploying the previous good version when a release breaks |

## Pipeline Order:
1. Checkout code
2. Install dependencies (cached)
3. Lint and type-check
4. Run tests
5. Build (or build a Docker image)
6. Deploy to staging
7. Approval gate, then deploy to production

## Best Practices:
### Pipeline design

1. Fail fast. Run the cheapest checks first (lint, type-check, unit tests) and the slow ones (integration, e2e) after.
2. Keep the pipeline fast. Aim for under 10 minutes. Slow pipelines get bypassed. Use caching and run independent jobs in parallel.
3. Build once, deploy the same artifact everywhere. Never rebuild for production. Staging and production must run the identical image or bundle.
4. Keep the main branch always deployable. Use short-lived branches and small PRs.
5. Use branch protection. Require passing checks and at least one review before merging to main.

### Security

1. Never hardcode secrets. Use the platform’s secret store, and prefer short-lived credentials (OIDC to AWS) over long-lived access keys.
2. Never print secrets. Masking in logs is a safety net, not permission. The echo with DEPLOY_KEY in my example workflow is for demonstration only. Don’t copy that pattern into real pipelines.
3. Least privilege. Set permissions: contents: read at the top of workflows and grant more only to jobs that need it.
4. Pin third-party actions to a version tag, or a commit SHA for sensitive pipelines, since a compromised action runs with your secrets.
5. Scan dependencies and images (npm audit, Dependabot, Trivy) as a pipeline step.

### Reliability

1. Make deployments repeatable and reversible. Know your rollback path before you need it, such as redeploying the previous image tag.
2. Use approval gates for production and automatic deploys for staging.
3. Run smoke tests after deploy, for example hitting /health.
4. Use npm ci, not npm install, in pipelines for reproducible installs from the lockfile.
5. Keep pipeline config in the repo (pipeline as code), reviewed like any other code.
6. Set job timeouts (timeout-minutes) so a hung job doesn’t burn runner minutes.