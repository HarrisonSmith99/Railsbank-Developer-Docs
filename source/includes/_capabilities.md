# Capabilities

**Issue Ledgers**
-
- Digital bank accounts held by our partners.
- Currently we provide GBP and EUR ledgers, with USD coming later this year.
- One ledger can hold a single asset only. For example, if an enduser wants to hold GBP and EUR, then the enduser needs at least two ledgers: one for GBP, and one for EUR
- Ledgers can also be 'virtual', with 'virtual' funds that can be manually credited and debited to them that are attached to a real world asset. For instance, a gold ledger that reflects a stock of gold in a vault.

**Issue IBANs**
-
- A **UK Account Number** and **Sort Code** - the 'address' of the ledger - are automatically assigned to each GBP ledger.
- An **IBAN** - the 'address' of the ledger - can be assigned to any EUR ledger.
- You may have noticed that GBP ledgers automatically generate GB IBANs. As of now they are not swift enabled, but will be coming soon.

**Receive money**
-
- GBP ledgers are connected to **UK Faster Payments**, meaning funds can be accessed in them within a maximum of 3 hours after they are sent.
- EUR ledgers - with IBAN assigned - are connected to **SEPA Step 2**, meaning if they are send before 10 AM CET they will clear within the day, and if they are sent after they will clear overnight.
- Both GBP and EUR ledgers can be credited via a **Direct Debit** payment.
- Railsbank ledgers can be enabled to receive payments via **SWIFT**.

**Send Money**
-
- From Railsbank, ledgers money can be sent to beneficiaries across the globe.
- Money can be sent:
  - via **standard** transaction - to a beneficiary account outside of the Railsbank API.
  - via **inter-ledger** transaction - to another ledger within the API.
- Money can be sent from Railsbank ledgers via **SWIFT**.

**Collect Money**
-
- Through Railsbank you can create **mandates** against GBP and EUR bank accounts that are enabled for either **Bacs** or **Sepa Core** direct debiting.
- You can then make **payments** and **subscriptions** against these mandates, debiting the bank accounts.

**Convert Money**
-
- The **Railsbank Foreign Exchange** capability allows you to convert money between any of the **G10** currencies.
- You can get a quote before converting in order to check either the buying or selling rate at any point in time.

**Issue Cards**
-
- You can issue **Mastercard Debit Cards** attached to a Railsbank GBP or EUR ledger.
- This can be done entirely from the API with just a few calls, an industry-leading innovation.

**Spend Money**
-
- Using our **Mastercard Debit Cards**, your customers can spend the funds in their ledgers.

**Manage Beneficiaries**
-
- Beneficiaries of Railsbank **endusers** or **customers** can have multiple accounts in any of the **G10** currencies, to which money can be sent to from a Railsbank ledger.
