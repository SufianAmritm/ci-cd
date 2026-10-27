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