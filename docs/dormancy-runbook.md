# Dormancy Runbook

Daily and monthly jobs for wallet dormancy. Replace every period with your own policy values.

## Daily job

1. Select wallets where `last_customer_activity_at` is older than the inactivity threshold and state is ACTIVE. Move to INACTIVE and queue a notice.
2. Select INACTIVE wallets past the dormancy threshold. Move to DORMANT, snapshot `balance_at_dormancy`.
3. Skip any wallet with an open dispute, pending settlement, or legal hold. Log the skip reason.
4. Write every state change to the audit table with the job run id.

## Reactivation checks

- [ ] OTP to the registered MSISDN
- [ ] SIM-swap age check (block if swapped recently)
- [ ] Number-recycle signal from the operator for long-dormant wallets
- [ ] KYC refresh where policy requires it
- [ ] Record `reactivation_method` and actor

## Monthly

- [ ] Report of dormant count and total dormant balance for finance
- [ ] List of wallets reaching the closure threshold, reviewed by compliance before closure
- [ ] Confirm no fees were charged against dormant balances if your market forbids it
