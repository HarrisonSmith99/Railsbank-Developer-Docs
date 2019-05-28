# FAQs

1. How long does it take to be onboarded to Railsbank and get **Live** API Keys?
- At the moment, about 2 weeks, so over 10 times faster than if you tried to open  accounts with a traditional bank.

2. What are the **PlayLive** limits?
- 3 endusers
- 3 ledgers
- Maximum value of a transaction: £5 or €5
- Maximum value of aggregated transactions: £20 or €20.  

3. I have hit one of the **PlayLive** limits, what should I do?
- Unfortunately, due to compliance reasons we cannot raise your limits in PlayLive, so please be sure that you have sent all the money out of your ledgers before hitting the limit
- If you're enjoying Railsbank and think it could be useful to your business, [email us](mailto:support@railsbank.com), ideally with your `customer_id` (see the #docTextSection:c89caSCSLsLftoCgb guide to learn how).
- Continue your testing on **Play** - it works in exactly the same way just with fake money.

4. I have tried to use one of the Railsbank **Capabilities** and received an error of `customer does not have product enabled`, what should I do?
- [Email us](mailto:support@railsbank.com), with your `customer_id` and the product you need enabling.
- Note however, that it may be the case that the product requires a contract.

5. I have sent money to a beneficiary and if I fetch the transaction it has `transaction-status-accepted`, but the money hasn't arrived in the beneficiary's account, why?
- The most likely reason for this is that the transaction has broken a **Partner Firewall Rule** and fallen into **Partner Quarantine**.
- Due to 'tipping-off' regulation our partners cannot inform you if a transaction falls into their quarantine queue. If their compliance officer **passes** the transaction, your beneficiary will receive the money, if not, you will be returned the money, along with an update to the transaction status to `declined`.

6. Are you 'crypto-friendly'?
- Yes. We built **The Compliance Firewall** with helping our **Partners** manage the crypto risk and capitalize upon it's potential in mind.

7. I've got an API-Key but when I try to use it to send a request to the API, the API tells me: `You are not authenticated`. Why might this have occurred?
- The first reason this occurs is if you try to use an API key for a different arena to the one you are sending the request in i.e. using a **Play** API-Key to try and do something in **PlayLive**.
- The second reason is that you have forgot to put `API-Key` followed by a space before the string of characters that you were given. For a key to work it should look something like this: `API-Key sfheuogh4th48thhrghwieurh`.
