# Git-to-Cloud Deployment Playbook

**Learn Git, Infrastructure as Code, CI/CD, cloud identity, approvals and application deployment by changing a small set of values — without pretending Azure, AWS and GCP are identical.**

This is an interview lab and reusable template library. It uses `tripathies/rahulsite` as a case study and separates observed implementation from recommended patterns.

## The pipeline pattern

**Trigger → Permissions → Runner → Checkout → Toolchain → Cloud authentication → Validate / Build → Artifact / Plan → Approval → Deploy → Verify**

The syntax repeats across providers. Authentication, Terraform provider/backend, infrastructure modules and deployment targets are provider-specific. Switching cloud is not just changing a subscription ID.

## Two delivery streams

1. **Infrastructure stream:** Terraform init → validate → plan → approval → apply → outputs.
2. **Application stream:** checkout → test → build/package/image → deploy to staging → smoke test → release approval → production → monitor/rollback.

Infrastructure provisions the runtime. Application delivery releases code to it. Keep the pipelines independently controlled.

## Repository layout

```text
app/                 # Web, function and container samples
infra/               # Terraform bootstrap, modules and environments
templates/
  github-actions/
    azure/           # Azure IaC and app deployment workflows
    aws/             # AWS IaC, Lambda and ECS workflows
    gcp/             # GCP IaC, Cloud Run and Functions workflows
  azure-devops/      # Dedicated approval example
docs/                # Walkthroughs and interview notes
diagrams/            # Editable architecture diagram
```

## Start learning

1. `docs/01-git-vscode-workflow.md`
2. `docs/02-infrastructure-as-code.md`
3. `docs/03-application-delivery.md`
4. `docs/04-cloud-auth-and-state.md`
5. `docs/05-approvals-and-release.md`
6. `docs/06-rahulsite-current-state.md`
7. `docs/07-interview-cheat-sheet.md`
8. `docs/08-change-the-values.md`
9. `docs/09-provider-switching.md`

## Production safety

- Never commit credentials, Terraform state, secret tfvars, publish profiles or private keys.
- Prefer OIDC/workload identity federation and short-lived credentials.
- Scope identities to least privilege.
- Separate state and identities per environment/root stack.
- Review Terraform plans before apply.
- Configure actual production approvers in GitHub Environment protection or Azure DevOps checks; YAML alone does not create an approval group.
- Pin third-party actions to reviewed full commit SHAs for production.
- Test in a sandbox: templates may create billable resources.

## Important distinction

This repository contains **learning templates**, not pre-authorized live deployments. Configure cloud identity, backend, environment protection, variables, permissions, target resources and networking before running them. The RahulSite audit must be read as a point-in-time observation, not a claim that every recommended workflow already exists.

## Official references

- GitHub Actions OIDC: https://docs.github.com/en/actions/security-for-github-actions/security-hardening-your-deployments/about-security-hardening-with-openid-connect
- Azure OIDC: https://learn.microsoft.com/en-us/azure/developer/github/connect-from-azure-openid-connect
- Azure App Service: https://learn.microsoft.com/en-us/azure/app-service/deploy-github-actions
- Azure Functions: https://learn.microsoft.com/en-us/azure/azure-functions/functions-how-to-github-actions
- Terraform AzureRM backend: https://developer.hashicorp.com/terraform/language/backend/azurerm
- AWS credentials/OIDC: https://github.com/aws-actions/configure-aws-credentials
- GCP OIDC: https://docs.github.com/en/actions/how-tos/secure-your-work/security-harden-deployments/oidc-in-google-cloud-platform

**Learning motto:** One workflow shape. Separate cloud identities. Separate state. Separate deployment targets. Approval before production.
