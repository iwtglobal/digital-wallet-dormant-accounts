# Digital Wallet Dormant Accounts

A practical guide to handling dormant and inactive accounts on a **mobile wallet platform**: when an e-wallet becomes dormant, what happens to the balance, how reactivation works, and which records finance and compliance will ask for later. Written for operators and payment teams evaluating [EVD System](https://evdsystem.com/) by [MoboGage](https://evdsystem.com/about-mobogage/).

---

## What counts as dormant

A **dormant wallet** is a customer or merchant e-wallet that has had no customer-initiated activity for a defined period. The period is set by the scheme rules or the local regulator, so treat any number in this repo as a placeholder you replace with your own policy.

The tricky part is the word "customer-initiated". Interest credits, fee debits, system-generated cashback and bulk salary credits pushed by an employer usually do not reset the clock. A P2P send, a cash-out at an agent, a QR payment or a successful login with a balance check usually does. Write that list down per transaction type, because support agents will be asked "why is my account blocked, I received money last week?" and they need a clear answer.

## Why it matters on a wallet platform

Dormant accounts are where a lot of quiet risk sits:

- They are attractive to account takeover, because the real owner is not watching.
- The MSISDN behind them may have been recycled by the telco to a new subscriber.
- Their balances are a liability on the books that someone has to report.

That combination is why a wallet platform should treat dormancy as a lifecycle state with its own rules, not just a reporting filter.

## A state model that has worked

```
ACTIVE -> INACTIVE (soft warning) -> DORMANT (restricted) -> CLOSED / TRANSFERRED
                   ^                         |
                   |---- reactivation -------|
```

**INACTIVE** is a warning stage. The customer gets an SMS or push, and nothing is blocked yet.

**DORMANT** restricts outbound value. Inbound credits are a policy choice. Many teams allow credits but block debits until the customer re-verifies, so that salary or refunds are not bounced.

**Reactivation** should require more than a PIN. A fresh OTP to the registered MSISDN plus a check that the SIM has not been swapped recently is the common minimum, and some markets require a KYC refresh at an agent.

**CLOSED / TRANSFERRED** is where unclaimed balances move to a holding account under whatever unclaimed-funds rule applies in your market.

## Worked example (figures are illustrative)

A wallet holds 1,240.00 units. Last customer-initiated activity was a QR payment. After the policy's inactivity period it moves to INACTIVE and an SMS goes out. No response, so after the dormancy period it becomes DORMANT. A cashback credit of 15.00 lands during dormancy; it is accepted because credits are allowed, and it does not reset the clock. Balance is now 1,255.00.

The customer then visits an agent to cash out. The agent app shows "Account dormant, reactivation required". The customer passes OTP and a SIM-swap check, the state returns to ACTIVE, and the reactivation event is written with `actor=AGENT`, `agent_id`, and the verification method used.

## Records to keep

| Field | Why |
|-------|-----|
| `last_customer_activity_at` | Drives the clock, separate from `last_any_activity_at` |
| `dormancy_state` and `state_changed_at` | Shows exactly when restrictions began |
| `notice_sent_at`, `notice_channel` | Proves the customer was told |
| `reactivation_method` | OTP, agent KYC, branch, call centre |
| `balance_at_dormancy` | Needed for liability and unclaimed-funds reports |
| `msisdn_recycle_check_at` | Evidence the number still belongs to the same person |

## Things that go wrong

**Recycled numbers.** The telco reissues the MSISDN to a new person, who then "reactivates" a wallet that is not theirs. Ask the operator for a number-recycle or SIM-age signal before allowing reactivation on long-dormant accounts.

**Fees eating the balance.** If monthly fees keep running during dormancy, small balances drain to zero and the customer complains that the money vanished. Several regulators restrict this; check yours.

**Merchant wallets.** A dormant merchant wallet with pending settlements should not be closed until settlement is finished and chargebacks have aged out.

A step list for ops teams is in [docs/dormancy-runbook.md](./docs/dormancy-runbook.md).

---

## FAQ

**What is a dormant account on a digital wallet?**
It is an e-wallet with no customer-initiated activity for a period set by policy or regulation. Outbound transactions are usually restricted until the owner reactivates it.

**Does receiving money keep a wallet active?**
Usually not. Most policies count only actions the customer starts, such as sending, paying, or cashing out.

**How should a dormant wallet be reactivated?**
With a fresh OTP to the registered number and a SIM-swap or number-recycle check at minimum. Long-dormant accounts often need KYC refresh.

**What happens to the balance of a closed dormant wallet?**
It moves to a holding account and is handled under the market's unclaimed-funds rules. The customer's claim does not disappear.

**Does EVD System cover wallets as well as vouchers?**
MoboGage's broader stack includes digital wallet and mobile money alongside the EVD System voucher platform.

---

## Further reading

- [Wallet cash-in, cash-out, transfers and reversals](https://evdsystem.com/evd-software/wallet-cash-in-out-transfers-and-reversals/) on evdsystem.com
- [Mobile money platform with merchant payment](https://evdsystem.com/mobile-money-platform-with-merchant-payment/) on evdsystem.com
- Related repo: [digital-wallet-account-closure](https://github.com/iwtglobal/digital-wallet-account-closure)

## License

MIT. See [LICENSE](./LICENSE). Educational material only, not legal or regulatory advice.
