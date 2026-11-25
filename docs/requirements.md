# PayLink Requirements

## 1. Problem Summary
PayLink is a company in Egypt that lets people pay bills from a digital wallet. Customers pay utility companies (billers), such as electricity, water, mobile and internet providers, through the PayLink mobile app or through agents. PayLink earns money by charging a small service fee on each paymentand and pays commissions to agents for payments made through their terminals.

PayLink holds customers' money, so mistakes are costly. A customer must never be charged twice, and must never be charged for a payment that the biller did not receive.

The system must return money correctly when a payment is confirmed as failed, while keeping the money held when the payment status is unknown until the system confirms whether the biller received the payment. It must also support reconciliation, which means checking PayLink's records against the billers' records. Finally, it must prevent the same payment from being processed twice, even if a request is repeated or a biller replies late.

This is difficult because billers behave differently: some answer fast, some are slow, some reply later, and some work only with daily files. The goal is a correct and trustworthy payment backend that handles these differences, makes it easy to add new billers, and stays reliable on the busiest day of the month.
## Stakeholders

| Stakeholder | Role | What they care about |
|---|---|---|
| Customers | People who use PayLink to pay utility bills through the mobile app or through agents, and manage their wallet balances | Paying bills easily, avoiding double charges, ensuring that money is not charged when the biller has not received the payment, receiving clear payment status updates, and viewing wallet balances and transaction history. |
| Agents | Small shops use PayLink terminals to pay bills on behalf of customers and receive commissions for completed payments. | Processing customer payments correctly, receiving their commissions, and ensuring that commissions are credited only when payments are completed. |
| Admins | PayLink staff who manage billers, services, fees and limits and administrative actions within PayLink. | Adding, editing, and disabling billers and services, updating fees and limits without redeployment, and keeping administrative actions recorded in the audit log. |
| Support employees | Employees who handle payment-related issues and review customer payment records. | Finding a payment and its full history, blocking or unblocking accounts, approving refunds, and reviewing reconciliation differences. |
| Billers (ElectroCo, AquaCo, TopUpNet, NetCo) | Utility and service providers that receive payments through PayLink using different integration methods. | Receiving accurate payment requests, being paid quickly and correctly, and getting each payment confirmed or refused clearly. |
| SMS and notification provider | External service that delivers SMS notifications to customers. | Receiving notification requests and delivering messages reliably. Failed messages should be retried, and eventually marked as undelivered if delivery continues to fail. |
| PayLink management | PayLink management responsible for the company's business direction and expectations for the new backend system. | A backend that is correct with money, easy to extend with new billers, and ready for the busiest day of the month. |
| Regulators | External authorities that may examine PayLink's handling of customers' money. | Ensuring that PayLink handles customers' money correctly and avoids financial errors that could damage trust or expose the company to regulatory consequences. |
| Development team | The engineering team that builds and maintains the system. | A design where adding a biller changes only one module, and where logic is well tested. |

## 3. User Stories

### US-01: Register a customer account (FR-1, Must)
**As a** new customer, **I want to** register an account, **so that** I can start using PayLink to pay bills.

- **Given** a person who has no PayLink account, **When** they complete registration, **Then** a customer account is created and its verification level is "basic".
- **Given** a registered customer with a basic level,**When** their daily payments are checked, **Then** the daily payment limit applied is 5,000 EGP.

### US-02: Upgrade a customer to verified (FR-2, Should)
**As an** admin, **I want to** upgrade a customer to verified level, **so that** the customer can pay more per day.

- **Given** a customer with a basic level, **When** an admin upgrades the account to verified, **Then** the verification level is "verified", the daily payment limit becomes 30,000 EGP, and an audit record is written.
- **Given** a customer who has not been upgraded, **When** they try to pay more than 5,000 EGP in one day in total, **Then** the payment that would pass the limit is rejected.
- **Given** a verified customer who has paid 5,000.00 EGP today, **When** they make another valid payment of 100.00 EGP, **Then** the payment is accepted, because the limit is 30,000 EGP.

### US-03: View wallet balance and history (FR-3, Must)
**As a** customer, **I want to** see my available balance, my held balance and my transaction history, **so that** I know how much I can spend and where my money went.

- **Given** a wallet with an available balance of 1,000.00 EGP, **When** the customer opens the wallet, **Then** both the available balance and the held balance are shown.
- **Given**  a wallet with 1,000.00 EGP available and 0.00 EGP held, **When** 352.50 EGP is held for a payment, **Then** the wallet shows 647.50 EGP available and 352.50 EGP held.
- **Given** a customer who has made a top-up and a payment, **When** they open the transaction history, **Then** both movements appear with their amounts.

### US-04: Top up the wallet (FR-4, Must)
**As a** customer, **I want to** top up my wallet by simulated card or by cash at an agent, **so that** I have money to pay bills.

- **Given** a wallet with 100.00 EGP available, **When** a top-up of 200.00 EGP by simulated card succeeds, **Then** the available balance is 300.00 EGP.
- **Given** a wallet with 100.00 EGP available, **When** a top-up of 200.00 EGP is made as agent cash, **Then** the available balance is 300.00 EGP.
- **Given** a successful top-up, **When** the customer views their history, **Then** the top-up appears as a movement with its amount.

### US-05: Record every balance change in the ledger (FR-5, Must)
**As the** PayLink System, **I want** every balance change to be recorded as a ledger entry and every balance to be calculated from the ledger, **so that** no money movement is lost or hidden.

- **Given** any action that changes a wallet (top-up, hold, charge, release or refund), **When** the action finishes, **Then** a ledger entry exists for that change.
- **Given** a wallet with ledger entries of +500.00 EGP and -150.00 EGP, **When** the balance is calculated, **Then** it is 350.00 EGP.
- **Given** a balance change, **When** the ledger is checked, **Then** no change was made to the balance without a matching entry.
- **Given** entries of 0.10 EGP and 0.20 EGP, **When** they are added, **Then** the result is exactly 0.30 EGP.

### US-06: Manage billers and services (FR-6, Must)
**As an** admin, **I want to** add, edit and disable billers and services, **so that** the catalog stays correct without changing the rest of the system.

- **Given** an admin, **When** they add a new biller with a service, **Then** the biller and service appear in the catalog as active.
- **Given** an existing biller, **When** the admin edits its details, **Then** the catalog shows the new details.
- **Given** an active biller, **When** the admin disables it, **Then** customers can no longer start a new payment to it.

### US-07: See billers and required input fields (FR-7, Must)
**As a** customer, **I want to** see the list of billers and the details each one needs, **so that** I enter the right information before paying.

- **Given** an active electricity biller (ElectroCo), **When** the customer opens its service, **Then** the form asks for a meter number.
- **Given** an active mobile top-up biller (TopUpNet), **When** the customer opens its service, **Then** the form asks for a phone number.
- **Given** a customer who leaves a required field empty, **When** they ask for the bill, **Then** the request is refused and the missing field is named.

### US-08: Run a bill inquiry and see the total (FR-8, Must)
**As a** customer, **I want to** see the bill amount, the fee and the total before I pay, **so that** I know exactly what I will be charged.

- **Given** a valid ElectroCo meter number with a bill of 350.00 EGP, **When** the customer runs an inquiry, **Then** the screen shows amount 350.00 EGP, fee 2.50 EGP and total 352.50 EGP.
- **Given** a mobile top-up of 50.00 EGP, **When** the customer asks for the total, **Then** the fee is 1.00 EGP, because 1.5% is 0.75 EGP and the minimum fee is 1 EGP.
- **Given** a customer who has run an inquiry, **When** they have not confirmed payment, **Then** their available and held balances are unchanged.

### US-09: Pay a bill from the wallet (FR-9, Must)
**As a** customer, **I want to** pay a bill from my wallet, **so that** I do not have to queue at the biller's office.

- **Given** a wallet with 1,000.00 EGP available and a total of 352.50 EGP, **When** the customer confirms payment, **Then** 352.50 EGP is moved to the held balance before the biller is contacted.
- **Given** a held payment, **When** the biller confirms it, **Then** the payment is Completed and the held money becomes a final charge.
- **Given** a wallet with 300.00 EGP available, **When** the customer confirms a payment with a total of 352.50 EGP, **Then** the payment is Rejected and the balance is unchanged.
- **Given** a held payment, **When** a live biller does not answer within 10 seconds, **Then** the payment is Unknown, the money stays held, and it is not refunded automatically.

### US-10: Repeat a request safely with an idempotency key (FR-10, Must)

**As a** customer, **I want to** send the same payment request twice without being charged twice, **so that** a slow network does not cost me money.

- **Given** a payment request has already been sent using a given idempotency key, **When** the same request with the same key is sent again, **Then** the current state of the original payment is returned without creating a second payment.
- **Given** the repeated request in the previous case, **When** the wallet is checked, **Then** only one payment exists for that key, with no second hold and no second charge.
- **Given** an idempotency key that was already used with one set of payment details, **When** the same key is sent with different payment details, **Then** the new request is refused.
- **Given** an idempotency key, **When** the system stores it, **Then** it is retained for the period specified in the system configuration.


### US-11: Follow the payment life cycle (FR-11, Must)
**As the** PayLink System, **I want** each payment to move only through the allowed states, **so that** the payment status remains correct and money is handled safely.

- **Given** a payment in Initiated, **When** the fraud checks pass and the wallet has enough funds, **Then** the payment becomes FundsHeld.
- **Given** a payment in Initiated, **When** a fraud check fails or the funds are insufficient, **Then** the payment becomes Rejected.
- **Given** a payment in FundsHeld, **When** the request is sent to the biller, **Then** the payment becomes SentToBiller.
- **Given** a payment in SentToBiller, **When** the biller confirms, refuses, or does not answer within the timeout, **Then** the payment becomes Completed, Failed or Unknown respectively.
- **Given** a payment in Unknown, **When** the biller later confirms, **Then** the payment becomes Completed. **When** the biller later refuses, or reconciliation finds no payment, **Then** it becomes Failed.
- **Given** a payment in Failed, **When** the hold is released to the wallet, **Then** the payment becomes Refunded.
- **Given** a payment in Initiated, **When** a change directly to Completed is attempted, **Then** the change is refused and the payment stays in Initiated.
- **Given** a payment in Completed, Rejected or Refunded, **When** any change of state is attempted, **Then** the change is refused and the state is unchanged.
- **Given** a payment in Unknown, **When** a direct change to Refunded is attempted, **Then** the change is refused and the payment stays Unknown, because Unknown is never refunded automatically.

### US-12: Hold funds before calling the biller (FR-12, Must)
**As a** customer, **I want** my money to be held before the biller is called and released if the payment fails, **so that** I am never charged for a payment that did not succeed.

- **Given** a wallet that starts with 1,000.00 EGP available and 0.00 EGP held, and a payment with a total of 352.50 EGP, **When** the payment reaches FundsHeld, **Then** the wallet shows 647.50 EGP available and 352.50 EGP held.
- **Given** a wallet with 300.00 EGP available and a payment with a total of 352.50 EGP, **When** the customer confirms the payment, **Then** no hold is created and no request is sent to the biller.
- **Given** a payment of 352.50 EGP in SentToBiller, **When** the biller refuses it, **Then** the payment becomes Failed, the hold is released, and the wallet shows 1,000.00 EGP available and 0.00 EGP held.
- **Given** a released hold after a failed payment, **When** the release is completed, **Then** the payment is Refunded.
- **Given** a payment in Unknown, **When** time passes without the biller's answer, **Then** the 352.50 EGP stays held and is not released automatically.

### US-13: Handle a payment that times out (FR-13, Must)

**As a** customer, **I want to** have a payment that gets no answer from the biller checked again later, **so that** I am neither charged wrongly nor refunded wrongly.

- **Given** a held payment to ElectroCo, **When** ElectroCo takes 12 seconds to answer, **Then** the payment becomes Unknown, the customer is told it is being processed, and the money stays held.
- **Given** a payment in Unknown, **When** the scheduled re-queries are made, **Then** the biller is asked again after 1 minute, 5 minutes and 15 minutes, all measured from the moment the payment enters Unknown, with no more than 3 re-queries.
- **Given** a payment in Unknown, **When** the biller confirms it on a re-query, **Then** the payment becomes Completed.
- **Given** a payment in Unknown, **When** the biller refuses it on a re-query, **Then** the payment becomes Failed.
- **Given** a payment in Unknown, **When** all 3 re-queries have been made without a clear answer, **Then** the payment stays Unknown, the money stays held, and nothing is refunded until reconciliation.

### US-14: Accept TopUpNet callbacks once (FR-14, Must)

**As a** customer, **I want to** the result that TopUpNet sends later to be applied exactly once, **so that** my mobile top-up is not charged twice.

- **Given** a mobile top-up payment that TopUpNet has accepted, **When** TopUpNet calls back with "success", **Then** the payment becomes Completed.

- **Given** a mobile top-up payment that TopUpNet has accepted, **When** TopUpNet calls back with "failed", **Then** the payment becomes Failed, and when the hold is released to the wallet the payment becomes Refunded.

- **Given** a mobile top-up payment in Unknown, **When** TopUpNet calls back with "success", **Then** the payment becomes Completed.

- **Given** a payment already Completed by a "success" callback, **When** TopUpNet sends the same "success" callback again, **Then** the payment stays Completed, the customer is charged only once, and the second callback is ignored and logged.

- **Given** a TopUpNet payment that is in SentToBiller after "accepted", **When** no callback arrives within 5 minutes (300 seconds) from the acceptance, **Then** the payment becomes Unknown and the money stays held.

### US-15: Support billers with different formats (FR-15, Must)
**As a** customer, **I want to** pay ElectroCo, AquaCo and TopUpNet bills in the same way, **so that** I do not need to know how each biller works.

- **Given** an ElectroCo bill, **When** the customer pays it, **Then** the payment is confirmed in the same request, without a later message from the biller.
- **Given** an AquaCo bill, **When** AquaCo takes 3 seconds to answer, **Then** the payment is still processed normally and does not become Unknown.
- **Given** an AquaCo payment, **When** AquaCo refuses it with an error code, **Then** the payment becomes Failed.
- **Given** a TopUpNet payment, **When** TopUpNet replies "accepted", **Then** the payment is not Completed until the later callback says "success".
- **Given** payments to ElectroCo, AquaCo and TopUpNet, **When** each one is processed, **Then** each follows the same payment life cycle.

### US-16: Calculate fees correctly (FR-16, Must)

**As a** customer, **I want to** be charged the correct service fee, **so that** the total I see is the total I pay.

- **Given** an electricity bill of 350.00 EGP, **When** the fee is calculated, **Then** the fee is 2.50 EGP.

- **Given** a water bill of 100.00 EGP, **When** the fee is calculated, **Then** the fee is 2.50 EGP.

- **Given** a mobile top-up of 200.00 EGP, **When** the fee is calculated, **Then** the fee is 3.00 EGP (1.5%).

- **Given** a mobile top-up of 20.00 EGP, **When** the fee is calculated, **Then** the fee is 1.00 EGP, because 1.5% is 0.30 EGP and the minimum is 1 EGP.

- **Given** a mobile top-up whose 1.5% fee results in more than two decimal places, **When** the fee is calculated, **Then** the fee is always rounded up to the next piaster.

- **Given** an internet bill of 200.00 EGP, **When** the fee is calculated, **Then** the fee is 2.00 EGP.

- **Given** an internet bill of 200.01 EGP, **When** the fee is calculated, **Then** the fee is 4.00 EGP.

- **Given** a promotion for one biller with a start and end date, **When** a payment to that biller is made on either the start date or the end date, **Then** the fee is 0.00 EGP.

- **Given** the same promotion, **When** a payment to that biller is made outside the start and end dates, **Then** the normal fee applies.

- **Given** the same promotion, **When** a payment to a different biller is made inside the period, **Then** the normal fee applies.

- **Given** an internet bill, **When** the fee tier is selected, **Then** the tier is based on the bill amount before adding the service fee.

### US-17: Pay for a customer as an agent and earn commission (FR-17, Should)

**As an** agent, **I want to** pay a bill on behalf of a customer and earn a commission, **so that** I am paid for serving customers at my terminal.

- **Given** an agent who pays an electricity bill of 350.00 EGP with a fee of 2.50 EGP, **When** the payment completes, **Then** 1.00 EGP (40% of the fee) is credited to the agent's wallet.

- **Given** an agent who pays a mobile top-up of 200.00 EGP with a fee of 3.00 EGP, **When** the payment completes, **Then** 1.20 EGP is credited to the agent's wallet.

- **Given** an agent payment that is in SentToBiller or Unknown, **When** the payment has not completed, **Then** no commission is credited.

- **Given** an agent payment, **When** it becomes Failed, **Then** no commission is credited.

- **Given** an agent payment that becomes Completed through a callback, retry or reconciliation, **When** the Completed state is recorded, **Then** the agent's commission is credited.

- **Given** an agent payment where a promotion makes the service fee 0.00 EGP, **When** the payment completes, **Then** the agent's commission is 0.00 EGP.

- **Given** a customer who pays directly in the mobile app, **When** the payment completes, **Then** no agent commission is recorded.

### US-18: Reject payments above the limits (FR-18, Must)

**As a** customer, **I want to** a payment that is above the limits to be rejected with a clear reason, **so that** I understand why it did not go through.

- **Given** a payment whose total amount including the service fee is above 10,000 EGP in a single payment (for example 10,000.01 EGP), **When** the customer confirms it, **Then** the payment is rejected and the reason names the single-payment limit.

- **Given** a payment whose total amount including the service fee is below 5 EGP (for example 4.99 EGP), **When** the customer confirms it, **Then** the payment is rejected and the reason names the single-payment limit.

- **Given** a basic account that has already paid 4,000.00 EGP today, **When** it confirms a payment whose total amount including the service fee is 1,500.00 EGP, **Then** the payment is Rejected and the reason names the daily limit of 5,000 EGP.

- **Given** a verified account that has already paid 29,000.00 EGP today, **When** it confirms a payment whose total amount including the service fee is 1,500.00 EGP, **Then** the payment is Rejected and the reason names the daily limit of 30,000 EGP.

- **Given** a verified account with enough funds and no payments today, **When** it confirms a payment with a total amount of 10,000.00 EGP, **Then** the payment is not rejected for its size.

- **Given** a verified account with enough funds and no payments today, **When** it confirms a payment with a total amount of exactly 5.00 EGP, **Then** the payment is not rejected for being below the minimum.

- **Given** a payment rejected for a limit, **When** the wallet is checked, **Then** the available and held balances are unchanged.

- **Given** a customer's daily payments are checked, **When** the daily limit is calculated, **Then** the total amounts including service fees of Completed and held payments are counted, while Rejected and Failed payments are not counted.

- **Given** a customer's daily payments are checked, **When** the system determines which payments belong to the day, **Then** it uses the calendar day in Cairo time.

### US-19: Block an account after too many fast payments (FR-19, Should)

**As a** customer, **I want** my account to be protected from rapid repeated payments, **so that** my money is safe from misuse.

- **Given** an account that has made 5 accepted payments within a sliding 60-second window, **When** it makes a sixth payment within that window, **Then** the sixth payment is Rejected and the account is blocked for 10 minutes.

- **Given** an account that has made exactly 5 accepted payments within 60 seconds, **When** the fifth payment is made, **Then** the account is not blocked.

- **Given** an account blocked for 10 minutes, **When** the customer makes a bill payment 5 minutes later, **Then** the payment is Rejected.

- **Given** an account blocked for 10 minutes, **When** 10 minutes have passed, **Then** the customer can make a valid bill payment again.

- **Given** an account with Rejected or Failed payments, **When** the velocity rule counts payments within the 60-second window, **Then** those Rejected or Failed payments are not counted.

### US-20: Find a payment and see its history (FR-20, Must)

**As a** support employee, **I want to** search for a payment by its reference and see its full history, **so that** I can answer customer questions and investigate problems.

- **Given** a payment with a known reference, **When** a support employee searches by that reference, **Then** that payment is found and shown.

- **Given** a payment that was Initiated, FundsHeld, SentToBiller and then Completed, **When** support opens its history, **Then** every one of these states is shown in the order it happened, with the time of each state change.

- **Given** a payment that went through Unknown before it became Completed, **When** support opens its history, **Then** the Unknown state and the later Completed state are both shown with their times.

- **Given** a payment's history, **When** support opens it, **Then** the history shows the payment amounts, messages sent, biller replies and callbacks, and support actions.

- **Given** a reference that does not belong to any payment, **When** support searches for it, **Then** no payment is shown and support is told that none was found.

### US-21: Block and unblock an account (FR-21, Should)

**As a** support employee, **I want** to block and unblock an account, **so that** I can stop misuse and restore access when it is safe.

- **Given** an active account, **When** support blocks it, **Then** a new bill payment from that account is Rejected.

- **Given** a blocked account, **When** support unblocks it, **Then** the customer can make a valid bill payment again.

- **Given** an account blocked by support, **When** the customer tries to pay a bill, **Then** the payment is Rejected with a reason that the account is blocked.

- **Given** an account blocked by the velocity rule, **When** the customer views the wallet or makes a top-up, **Then** those actions remain available while bill payments are rejected.

- **Given** an account blocked by the velocity rule or by support, **When** support unblocks it, **Then** the block ends and the customer can make a valid bill payment again.


### US-22: Get a message when a payment completes or fails (FR-22, Must)
**As a** customer, **I want to** receive an SMS and a push message when my payment completes or fails, **so that** I know the result without checking the app.

- **Given** a payment that becomes Completed, **When** the completion is recorded, **Then** the customer receives one SMS and one push message.
- **Given** a payment that becomes Failed, **When** the failure is recorded, **Then** the customer receives one SMS and one push message.
- **Given** a payment that becomes Failed because the biller refused it, **When** the SMS is sent, **Then** it states a clear reason for the failure.
- **Given** a payment that becomes Completed, **When** the messages are sent, **Then** the payment stays Completed whether or not the messages are delivered.

### US-23: Retry failed notifications (FR-23, Should)
**As a** customer, **I want** a failed message to be sent again before PayLink gives up, **so that** I am more likely to receive my payment result.

- **Given** an SMS that fails to send, **When** the failure is detected, **Then** the system retries it up to 3 times.
- **Given** an SMS that failed once, **When** a retry succeeds, **Then** the message is marked delivered and no more retries are made.
- **Given** an SMS or email that fails on all 3 retries, **When** the last retry fails, **Then** the message is marked undelivered.
- **Given** the SMS provider is down when a payment completes, **When** the messages cannot be sent, **Then** the payment is still Completed.

### US-24: Change fee and limit values without redeploying (FR-24, Should)
**As an** admin, **I want to** edit fee and limit values without redeploying the system, **so that** business changes can be applied quickly.

- **Given** the fixed electricity fee of 2.50 EGP, **When** an admin changes it to a different value, **Then** the next bill inquiry for electricity shows the new fee, and the system was not redeployed.
- **Given** the daily limit of 5,000 EGP for basic accounts, **When** an admin changes it to a different value, **Then** the next payment from a basic account is checked against the new limit.
- **Given** an admin who has edited a fee or limit value, **When** the admin views the value again, **Then** the new value is shown.

### US-25: Keep an audit log of money movements and admin actions (FR-25, Must)
**As an** admin, **I want** every money movement and admin action to be written to an audit log, **so that** I can show who did what, when and why.

- **Given** any money movement (top-up, hold, charge, release, refund, fee credit or commission credit), **When** it happens, **Then** an audit record exists with who, when and why.
- **Given** an admin who adds, edits or disables a biller or service, **When** the action is done, **Then** an audit record exists with who, when and why.
- **Given** an admin who edits a fee or limit value, **When** the change is saved, **Then** an audit record exists with who, when and why.
- **Given** all money movements of one day, **When** they are compared with the audit log, **Then** 100% of them have an audit record.

### US-26: Produce a daily reconciliation report (FR-26, Should)

**As a** support employee, **I want** a daily report that compares PayLink's payments with a biller's settlement file, **so that** I can find differences and fix them.

- **Given** a settlement file and PayLink records that agree on every payment, **When** reconciliation runs, **Then** the report is produced and lists no differences.

- **Given** a settlement file and PayLink records that disagree on a payment, **When** reconciliation runs, **Then** the report lists that payment as a difference.

- **Given** a payment in Unknown that the settlement file lists as received, **When** reconciliation runs, **Then** the payment becomes Completed.

- **Given** a payment in Unknown that the settlement file does not list, **When** reconciliation finds no payment, **Then** the payment becomes Failed.

- **Given** a settlement file from a mock biller, **When** reconciliation reads the file, **Then** it uses the common file layout containing payment reference, amount and status, and matches each line by payment reference.

- **Given** a payment that is already Refunded but the settlement file lists it as received, **When** reconciliation runs, **Then** the report flags the payment as a difference, the payment remains Refunded, and no automatic change is made to the payment state or wallet balance. Support handles the difference outside the payment lifecycle.

### US-27: Request a refund of a completed payment (FR-27, Could)
**As a** customer, **I want to** request a refund of a completed payment, **so that** I can get my money back when there was a problem.

- **Given** a completed payment, **When** the customer requests a refund, **Then** the request waits for support approval and the wallet balance is unchanged.
- **Given** a refund request waiting for approval, **When** support approves it, **Then** the refunded amount is returned to the customer's wallet and a ledger entry records it.
- **Given** a refund request waiting for approval, **When** support does not approve it, **Then** the wallet balance is unchanged.
- **Given** a payment that is not Completed, **When** the customer asks for a refund of it, **Then** the request is refused.
- **Given** a completed payment of 350.00 EGP with a fee of 2.50 EGP, **When** support approves the refund, **Then** 352.50 EGP (amount plus fee) is returned to the customer's wallet, the payment stays Completed, and the refund is recorded as a separate refund record.
- **Given** a completed agent payment with a commission of 1.00 EGP, **When** support approves the refund, **Then** 1.00 EGP is taken back from the agent's wallet with a ledger entry.
- **Given** a completed payment, **When** a customer asks for a partial refund, **Then** the request is refused, because only full refunds are allowed.
### US-28: View daily totals (FR-28, Could)

**As an** admin, **I want to** view daily totals, **so that** I can follow the business and spot problems with billers.

- **Given** a day with two Completed payments, each with a fee of 2.50 EGP, **When** the admin views the daily totals, **Then** fees earned show 5.00 EGP.

- **Given** a day with one agent commission of 1.00 EGP, **When** the admin views the daily totals, **Then** commissions paid show 1.00 EGP.

- **Given** a day with one Failed AquaCo payment, **When** the admin views the daily totals, **Then** failures for AquaCo show 1.

- **Given** a day with no payments, **When** the admin views the daily totals, **Then** payments, fees earned, commissions paid and failures all show zero.

- **Given** daily totals are displayed for a specific day, **When** the payment count and amount are calculated, **Then** only Completed payments are included in the payment count and amount.

- **Given** daily totals are calculated, **When** the system determines which day a payment belongs to, **Then** it uses the Cairo calendar day.
### US-29: Two simultaneous payments, balance covers only one (Hard case 4, Must)
**As a** customer, **I want** my balance to stay correct when two payments arrive at the same moment, **so that** I am never charged more than I have.

- **Given** a wallet with 400.00 EGP available and two payments of 352.50 EGP each that arrive at the same moment, **When** both are processed, **Then** exactly one payment reaches FundsHeld and the other is Rejected.
- **Given** the same two payments, **When** both are processed, **Then** the wallet shows 47.50 EGP available and 352.50 EGP held.
- **Given** the same two payments, **When** the balance is checked at any moment during processing, **Then** the available balance is never below 0.00 EGP.
- **Given** the Rejected payment, **When** it is processed, **Then** no request is sent to the biller for it.

### US-30: Ignore a duplicate callback and log it (Hard case 6, Must)
**As a** support employee, **I want** a repeated "success" callback to be ignored and logged, **so that** a payment is never completed twice and I can see that it happened.

- **Given** a payment already Completed by a "success" callback from TopUpNet, **When** the same "success" callback arrives again, **Then** the payment stays Completed and its state history shows only one completion.
- **Given** the same duplicate callback, **When** it is processed, **Then** no second charge, no second fee credit and no second agent commission is recorded.
- **Given** the same duplicate callback, **When** support checks the log, **Then** a log entry shows that the second callback was received and ignored.
- **Given** a payment already Completed by a "success" callback, **When** TopUpNet sends a "failed" callback for the same payment, **Then** the payment stays Completed, the conflict is logged, and it is flagged for support review.

### US-31: Flag a settlement difference for a Failed payment (Hard case 7, Must)
**As a** support employee, **I want** a payment that the settlement file lists but PayLink has as Failed to be flagged, **so that** I can review it.

- **Given** a payment that PayLink has as Failed and a settlement file that lists it as received, **When** reconciliation runs, **Then** the reconciliation report lists this payment as a difference.
- **Given** the same difference, **When** the report is read, **Then** it shows the payment reference, that PayLink has it as Failed, and that the biller listed it as received.
- **Given** the same difference, **When** reconciliation finishes, **Then** the payment's state and the wallet balance are not changed automatically.

## 4. Non-Functional Requirements

| ID | Category | Requirement | Target number | How it is measured | Priority |
|---|---|---|---|---|---|
| NFR-1 | Correctness | The system shall remain correct under repeated and simultaneous requests, with no double charges and no negative balances. | 0 double charges; 0 negative balances | Pass/fail: run the duplicate-request and simultaneous-payment tests . Count payments charged more than once and wallets below 0.00 EGP. Both counts must be 0. | Must |
| NFR-2 | Performance | The system shall sustain a high payment throughput against the mock billers. | 100 payments per second, sustained for 1 minute | Pass/fail: run a 1-minute load test against the mock billers (ElectroCo, AquaCo, TopUpNet) and record payments per second. The rate must hold for the whole minute. | Must |
| NFR-3 | Resilience | The system shall remain available for payments to the other billers while one biller is down. | 0 payments to other billers stopped | Pass/fail: set one mock biller to fail on purpose  and send payments to the other billers. None of them may be stopped. | Must |
| NFR-4 | Modifiability | The system shall be easy to extend, so that adding a new biller needs changes only in the Biller Integration module and in configuration. | 0 files changed outside the Biller Integration module and configuration | Pass/fail: check the list of changed files for a test biller . Any file outside that module and configuration fails the test. | Must |
| NFR-5 | Auditability | The system shall be fully auditable, so that every money movement can be traced to who, when and why. | 100% of money movements have an audit record | Pass/fail: compare all money movements in a test run with the audit records. Every movement must have a record with all three details. | Must |
| NFR-6 | Testability | The system shall be testable, with its domain logic covered by automated tests. | At least 70% line coverage on domain logic | Pass/fail: the coverage report produced by the pipeline on every push must show 70% or more. | Must |

## 5.Question or ambiguity
| # | Question or ambiguity | Why it is unclear (page number) | Options | Decision | Reason |
|---|---|---|---|---|---|
| 1 | What counts toward the daily limit? | The limit is 5,000 EGP (basic) or 30,000 EGP (verified) per day. The PDF does not say whether the service fee, held money, or Rejected and Failed payments count (pp. 2, 5). | A) Bill amounts of Completed payments only; B) Amount plus fee of Completed and held payments; C) Total amount paid (bill amount,  and service fee) for Completed and held payments | C) The total amount paid (bill amount,  and service fee) for Completed and held payments. Rejected and Failed payments do not count. | The daily limit applies to the total amount the customer actually pays, including the bill amount and service fee. Held money is already committed to a payment, so counting it prevents two simultaneous payments from exceeding the daily limit. Rejected and Failed payments do not count because they are not completed payments. |
| 2 | Is the day a calendar day or a rolling 24 hours? | "Per day" is used without a definition, and no time zone is given (pp. 2, 5). | A) Calendar day in one fixed time zone; B) Rolling 24 hours | A) Calendar day in Cairo time. | PayLink works in Egypt. One fixed time zone is simple to explain and to test. |
| 3 | Do the 5 EGP minimum and 10,000 EGP maximum apply to the bill amount or the total with fee? | The PDF specifies a minimum of 5 EGP and a maximum of 10,000 EGP per payment, but does not clarify whether these limits apply to the bill amount alone or the total amount including the service fee. | A) Compare the bill amount only against the limits; B) Compare the total amount including the service fee against the limits. | B) The total amount including the service fee must be between 5 EGP and 10,000 EGP. | The limits apply to the total amount the customer actually pays. Therefore, if the bill amount plus the service fee exceeds 10,000 EGP, the payment is rejected. The validation happens before the money is held, and any payment outside the limits changes from Initiated to Rejected. |
| 4 | When an agent pays for a customer, whose wallet is charged, and how is the payment linked to the agent? | The PDF says an agent pays "on behalf of a customer" and that the commission goes to the agent's wallet. It never says which wallet pays the bill (pp. 2, 5, 9). | A) Customer's wallet is charged, the agent only earns commission; B) Agent's own wallet is charged | A) The customer's wallet is charged. The payment record stores the agent who made it. | The brief says "on behalf of a customer". One rule (bills are paid from the customer's wallet) keeps the ledger simple, and the agent's wallet only receives the commission. |
| 5 | What commission does an agent earn when a promotion makes the fee 0? | The commission is 40% of the service fee, and a promotion sets the fee to 0. It is not stated which fee the 40% is taken from (p. 5, FR-17 on p. 9). | A) 0.00 EGP, because 40% of 0 is 0; B) 40% of the fee that would apply without the promotion | A) 0.00 EGP. | The brief defines the commission as 40% of the service fee, and the fee charged is 0. |
| 6 | If a payment is Unknown and later confirmed, when is the agent commission paid? | Commission is credited "when the payment completes" (p. 5), and Unknown can become Completed through a callback, a retry or the settlement file (p. 4). The PDF does not say that a late confirmation also pays the commission (pp. 4, 5, 9). | A) Paid when the state becomes Completed, by any path; B) Paid only when the biller confirms directly; C) Paid only after reconciliation confirms | A) Paid when the payment becomes Completed, by any path. | The brief ties the commission to the payment completing, not to how it completed. |
| 7 | How does the velocity rule count payments? Is the sixth payment processed, do Rejected payments count, and is the 60 seconds a sliding window? | The rule says more than 5 payments in 60 seconds blocks the account for 10 minutes. It does not say what happens to the sixth payment or how the window is measured (pp. 5, 9). | A) The sixth payment is refused, the window slides, and only accepted payments count; B) The sixth payment is processed and the block starts after it; C) Fixed one-minute windows | A) The sixth payment is refused and the block starts. The window slides, and only accepted payments count. | "More than 5" means the sixth is the one that triggers the rule. A sliding window is the natural reading, and it matches US-19. |
| 8 | During a block, which actions are refused, and does support unblocking end the block early? | The PDF says the account is "blocked" and that support can block and unblock accounts. It does not say whether the two blocks are the same, or whether viewing the wallet and top-ups are still allowed (pp. 2, 5, 9). | A) Only payments are refused, and support can end any block; B) All actions are refused, and support can end only support blocks | A) Only bill payments are refused. Viewing the wallet and top-ups still work. Support can end any block. | The block comes from a payment check. This is the simplest rule and it matches US-21. |
| 9 | Are the re-query times (1, 5 and 15 minutes) counted from the moment the payment became Unknown, or from the previous re-query? | The rule lists "after 1, 5 and 15 minutes" without a starting point (pp. 5, 8). | A) All three counted from the moment of Unknown (at 1, 5 and 15 minutes); B) Each counted from the previous re-query (at 1, 6 and 21 minutes) | A) All three counted from the moment the payment became Unknown. | The brief gives a list of times after the event, which reads as a schedule. It is also the easiest to test (US-13). |
| 10 | Does a refund of a completed payment return the service fee, can it be partial, and is the agent's commission taken back? | FR-27 says support approves a refund. It says nothing about the fee, partial amounts or commission (pp. 3, 5, 9). | A) Full amount including fee, commission taken back; B) Bill amount only, fee and commission kept; C) Partial refunds allowed | A) Full amount including the fee. No partial refunds. The agent's commission is taken back with a ledger entry. | This matches a Failed payment, where the whole held total returns to the wallet. FR-27 is Could, so the simplest rule is used. |
| 11 | How is the 1.5% mobile top-up fee rounded when it has more than two decimals? | The PDF requires exact decimals and a 1.5% fee, but gives no rounding rule (pp. 4, 5, 6). | A) Round half up to 2 decimals; B) Always round up to the next piaster | B) Always round up to the next piaster. | Always rounding up ensures that any fraction of a piaster is rounded to the next full piaster. For example, a fee of 1.231 EGP becomes 1.24 EGP, while 1.236 EGP becomes 1.24 EGP. |
| 12 | Who upgrades a customer to verified? | FR-2 says a customer "can be upgraded" but names no actor or proof (p. 8). | A) Admin; B) Support employee; C) Automatic after a check the team defines | A) An admin upgrades the customer. The action is written to the audit log. | The brief gives no automatic check. Admins already manage system values, and support investigates payments. |
| 13 | Which notification channels are required? | The module list names SMS, email and push (p. 7). FR-22 names SMS and push (pp. 2, 9). Page 5 retries SMS or email only. | A) SMS and push required, email supported but not tied to payment results; B) All three channels for every message; C) SMS and push only | A) SMS and push are required for payment results. Email is supported as a channel with the same retry rule. | FR-22 is the requirement, and the retry rule on page 5 covers SMS and email. |
| 14 | Which state is a TopUpNet payment in between "accepted" and the callback, and how long does it wait before becoming Unknown? | TopUpNet calls back "minutes later" (p. 4). The life cycle has no dedicated waiting state. The 10-second timeout is stated for "live billers" only (pp. 4, 5, 8). | A) Stay in SentToBiller until the callback, then become Unknown after 5 minutes without a callback; B) Stay in SentToBiller until the callback or reconciliation, with no timeout. | A) The payment stays in SentToBiller after "accepted". If no callback arrives within 5 minutes (300 seconds) after receiving "accepted", it becomes Unknown. | The life cycle has no new waiting state, so no new state is invented. The 10-second timeout applies to Live Billers and is not applied to TopUpNet. The 5-minute callback timeout is adopted as a system design decision to allow time for TopUpNet's asynchronous response. |
| 15 | If NetCo is built, how does a customer see amount, fee and total, and what are its payment states while waiting for the result file? | NetCo has no live inquiry, uploads a daily file and gets results the next morning. FR-8 still asks for amount, fee and total before paying. NetCo is optional (pp. 4, 8). | A) Do not build NetCo; B) Build it with a customer-entered amount; C) Build it with a separate waiting step | A) NetCo is not built in this project. | FR-15 marks it optional, and it conflicts with FR-8 (no live inquiry). It stays listed as a biller in the stakeholders. |
| 16 | Is the 200 EGP internet fee tier based on the bill amount or the total? | The tier is "up to 200 EGP" and "above" and does not say what is measured (p. 5). | A) Bill amount; B) Total including fee | A) Bill amount. | The fee is added to the bill amount. Measuring the tier on the total would make the fee depend on itself. |
| 17 | Are the promotion start and end dates inclusive? | Dates are "configurable start and end dates", with no rule for the boundary days (p. 5). | A) Both days included; B) Start included, end excluded | A) Both days are included. | It is the natural reading of "start and end dates" and is easy to test at both boundaries. |
| 18 | What does "full history" contain in FR-20? | The PDF says "full history" without listing contents (p. 9). | A) State changes with times; B) State changes plus amounts, messages, callbacks and support actions; C) B plus ledger and audit records | B) State changes with times, amounts, messages sent, biller replies and callbacks, and support actions. | Support needs to see why a state changed, and US-30 requires duplicate callbacks to be visible. |
| 19 | What does "payments" mean in the daily totals of FR-28? | The totals could be a count, an amount, or both. The PDF does not say if only Completed payments count or what a "day" is (p. 9). | A) Count of Completed payments; B) Amount of Completed payments; C) Both count and amount | C) Both the count and the amount of Completed payments. A day is a calendar day in Cairo time, as in row 2. | It gives the admin the full picture and uses the same day rule as the limits. |
| 20 | How exactly does a repeated idempotency key behave? | FR-10 and hard case 1 say a repeat returns the first result. The PDF does not say what is returned while the first payment is still running, what happens if the same key comes with different details, or how long a key is remembered (pp. 6, 8). | A) Repeat returns the first payment's current state, and changed details are refused; B) Repeat always returns the first stored result; C) Keys are remembered for a period the team sets | A) A repeat returns the current state of the first payment, and the same key with different details is refused. Keys are remembered for a period kept in configuration. | It avoids a second payment in every case. The retention period is a business setting, so it belongs in configuration. |
| 21 | What happens if a later TopUpNet callback contradicts an earlier one (for example "failed" after "success")? | Hard case 6 covers only a repeated "success". The PDF does not cover a conflicting result or how a callback is trusted (pp. 4, 6, 8). | A) First final result stands, the later callback is ignored and logged; B) The conflict is logged and flagged for support, with the state unchanged | B) The payment state is unchanged, and the conflict is logged and flagged for support. | A final state never changes, and a conflict may mean real money is wrong, so a person should review it. |
| 22 | Which billers send settlement files, and what does a file line contain? | The settlement file is the biller's daily list (p. 3). Page 2 shows ElectroCo sending one, and page 4 says a file can confirm an Unknown payment. No format or matching rule is given (pp. 2, 3, 4, 9). | A) Every biller sends a file matched by PayLink's payment reference; B) Only some billers send files; C) The team defines one simple common file layout | C) The team defines one simple common layout (payment reference, amount, status). Each mock biller sends a daily file in it, and lines are matched by payment reference. | The brief gives no format. One layout keeps reconciliation simple, and the mock billers are written by the team. |
| 23 | Which differences does reconciliation flag, and what may support do afterwards? | Hard case 7 covers only a Failed payment that the file lists. The life cycle has no way out of Failed except Refunded, and Refunded is final. A Refunded payment listed as received is not covered (pp. 4, 5, 6, 9). | A) Flag all such differences, including Refunded, and let support resolve them outside the life cycle; B) Flag only Failed payments listed as received | A) All such differences are flagged, including Refunded. Reconciliation only resolves payments that are in Unknown (to Completed or Failed, which are allowed transitions). For any other difference (Failed or Refunded listed as received), it changes nothing and support resolves the case outside the life cycle.| The aim of reconciliation is to find money problems. Not changing states automatically follows the rule that Completed, Rejected and Refunded are final. |
| 24 | Which priority do the hard cases get, given that FR-26 (reconciliation) is Should but hard case 7 needs it? | The PDF gives hard cases no priority. It asks for a test for each of the 8 cases and says all 8 must pass, while FR-26 is Should (pp. 6, 9, 12, 15). | A) All hard cases are Must; B) Each takes the priority of its related FR; C) All Must, with FR-26 kept as Should except the part hard case 7 needs | A) All hard cases are Must. | Pages 12 and 15 require a passing test for all 8. The reconciliation needed by hard case 7 is built even though FR-26 is Should. |
| 25 | **Contradiction:** FR-27 asks for a refund of a completed payment, but the life cycle makes Completed final. | Page 5 says Completed is final and that Refunded follows only Failed (when the hold is released). FR-11 allows no other transitions (p. 8). FR-27 asks for a refund of a Completed payment (p. 9). The refund definition on page 3 mentions "failed or cancelled" payments, but there is no Cancelled state (pp. 3, 4, 5, 8, 9). | A) Add a Completed to Refunded transition (breaks "final" and FR-11); B) Keep Completed final and record an approved refund as a separate refund record and ledger entry; C) Leave FR-27 out for now, since it is Could | B) The payment stays Completed. An approved refund is recorded as a separate refund record with a ledger entry that returns the money to the wallet. The team will ask the TA to confirm. | The brief says Completed is final and FR-11 allows only the listed transitions. This satisfies FR-27 without breaking the life cycle, and it matches US-27. |