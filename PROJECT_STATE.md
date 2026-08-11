# PROJECT_STATE

Updated: 2026-08-11
Milestone: M0 - Preflight and guardrails

## Done
- Existing AWS account; root user has no access keys
- IAM Identity Center: organization instance, primary region us-east-1,
  Single-Region configuration (verified: 0 customer managed KMS keys)
- Federated user with AdministratorAccess permission set, 8h session
- CLI profile "lab" via SSO. No static credentials on disk.
- [default] profile removed from ~/.aws/config, so any command without
  --profile fails loudly instead of silently using root (validated)
- Monthly cost budget configured in AWS Budgets with alerts at
  50% actual, 80% actual, 100% forecasted
- ADR-0001 records the us-east-1 decision; docs/cost-controls.md records
  the tagging convention and destroy policy

## Accepted risk, with deadline
- No MFA on the root user or the admin user. Deadline 2026-08-24.
- Verify at the start of every session:
  aws iam get-account-summary --profile lab --query 'SummaryMap.AccountMFAEnabled'
  Expected: 1

## Next actions
- Reusable evidence capture script under scripts/
- M0 acceptance review
- Phase 1: network foundation

## Cost
No billable resources exist.
