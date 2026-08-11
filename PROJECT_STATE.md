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

## Accepted risk, with deadline
- No MFA on the root user or the admin user. Deadline ~2026-08-24
  (phone hardware failure, replacement pending).
- Verify at the start of every session:
  aws iam get-account-summary --profile lab --query 'SummaryMap.AccountMFAEnabled'

## Next actions
- Monthly cost budget with alerts at 50/80/100
- Tagging convention and docs/cost-controls.md
- M0 acceptance review before starting Phase 1 (network)

## Cost
USD 0.00 - no billable resources exist
