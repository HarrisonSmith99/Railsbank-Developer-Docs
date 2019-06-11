# Sending Money

- It is incredibly simple to send money using the Railsbank API.
- There are two ways that money can be sent (without being converted):
  1. Between ledgers: an inter-ledger transaction.
  2. To a beneficiary: a standard transaction.
- To send money, you will require:
  - An enduser or a customer holding a ledger with funds on it.
  - Another ledger or a beneficiary to send the money to.

## Send to a Beneficiary
> **Example Request - EUR**

```shell
--request POST "https://playlive.railsbank.com/v1/customer/transactions"
--header "Content-Type: application/json"
--header "Accept: application/json"
--header "Authorization: API-Key <<yourAPI-Key>>"
{
    "payment_type": "payment-type-EU-SEPA-Step2",
    "reference": "this is a test payment",
    "amount": "3",
    "ledger_from_id": "6630b391-c5ce-46c1-9d23-a82a9e27f82d",
    "beneficiary_id": "8763b5df-a61c-4f77-9724-41ca9cde3654"
}
```
> **Example Request - GBP**

```shell
--request POST "https://playlive.railsbank.com/v1/customer/transactions"
--header "Content-Type: application/json"
--header "Accept: application/json"
--header "Authorization: API-Key <<yourAPI-Key>>"
{
    "payment_type": "payment-type-UK-FasterPayments",
    "reference": "this is a test payment",
    "amount": "3",
    "ledger_from_id": "6630b391-c5ce-46c1-9d23-a82a9e27f82d",
    "beneficiary_id": "8763b5df-a61c-4f77-9724-41ca9cde3654"
}
```
> **Example Response**

```shell
{
  "transaction_id": "8763b5df-a61c-4f77-9724-41ca9cde3654"
}
```
`POST "https://playlive.railsbank.com/v1/customer/transactions"`

- Once you have funded a ledger and you have a beneficiary, the first thing to do is send some money to them.
- Currently, we support sending money in both GBP, via the UK Faster Payments Scheme, and EUR, via the Sepa Step2 Scheme.
- When sending GBP:
  - The funds will be transferred immediately. They are usually processed by the receiving bank within minutes, but the scheme rules allow up to 3 hours fo rthis process to take place.
  - The reference field is **mandatory**. The reference can be 1-18 characters, including all upper and lowercase letters in the latin alphabet plus some special characters: (`[a-zA-Z0-9/?:().+#=!%&*<>;{@]{1,18}`).
- When sending EUR:
  - If you send the money before **10 AM GMT**, it will have arrived by **3 PM**.
  - If you send the money after, the funds will be processed and received at midnight.
  - The reference field can be 35 characters.
- **Note:**
  - When sending money in **PlayLive** you can only make transactions under £5.
  - When sending money to a beneficiary in **Play**, the money will not be received in the beneficiary account. This is because beneficiaries are envisioned to be entities holding accounts outside of Railsbank and it is impossible to send the virtual funds of Play to the outside world.

### The Standard Transaction Object
| Attribute                                                                             | Description |
|:--------------------------------------------------------------------------------------|:--|
| `amount` <br> _number_, required                                                      | The amount to be sent |
| `beneiciary_account_id` <br>  _string_, optional                                      | The `account_id` of the beneficiary account to which you want the funds to be transferred. <br> _Use if you want to send funds to a non-default beneficiary account_ |
| `beneiciary_id` <br> _string_, required (unless `beneiciary_account_id` is specified) | The UUID of the beneficiary to which you want the funds to be sent. <br> _Funds will be sent to the beneficiary's default account_ |
| `ledger_from_id` <br>  _string_, required                                             | The UUID of the ledger to be debited |
| `payment_credit_date` <br> _string_, optional                                         | Allows you to specify the date that you want the payment to be credited on <br> _YYYY-MM-DD_ |
| `payment_type` <br> _string_ , required                                               | The type of payment i.e. the scheme by which the payment will be sent (see below) |
| `reference` <br> _string_ , required                                                  | The payment reference |
| `who_pays_charges` <br>  _string_, optional                                           | Information about the payer of any charges <br> _Allowed Values:_ receiver-pays, sender-pays, shared |
| `cut_off_time_implementation` <br> _string_, optional                                 | Extra detail about the need for the transaction to be processed <br> _Allowed Values:_ strict, loose |
| `transaction_meta` <br>  _object_, optional                                           | Extra information you want to input in the form of custom fields |

### Payment Types
| Type                                    | Description                        |
|:----------------------------------------|:-----------------------------------|
| `payment-type-inter-ledger`             | A payment between two ledgers      |
| `payment-type-UK-FasterPayments`        | A GBP payment via UK Faster Payments |
| `payment-type-EU-SEPA-Step2`            | A EUR payment via SEPA Step2       |
| `payment-type-fx`                       | A convert-and-send payment via Currency Cloud |
| `payment-type-Global-SWIFT`             | A payment via SWIFT                |
| `payment-type-UK-BACS`                  | A GBP BACS Payment                 |
| `payment-type-EU-SEPA-Target2`          | A EUR payment via SEPA Target 2    |
| `payment-type-virtual-debit-card-debit` | A debit card payment               |

## Send to a Ledger
> **Example Request**

```shell
--request POST "https://playlive.railsbank.com/v1/customer/transactions"
--header "Content-Type: application/json"
--header "Accept: application/json"
--header "Authorization: API-Key <<yourAPI-Key>>"
{
    "amount": "3",
    "ledger_from_id": "6630b391-c5ce-46c1-9d23-a82a9e27f82d",
    "ledger_to_id": "8763b5df-a61c-4f77-9724-41ca9cde3654",
    "transaction_meta": {
      "my_transaction_id": "08575937"
    }
}
```
> **Example Response**

```shell
{
  "transaction_id": "8763b5df-a61c-4f77-9724-41ca9cde3654"
}
```

`POST "https://playlive.railsbank.com/v1/customer/transactions/inter-ledger"`

- Often you will want to send money between your ledgers. For instance, you might want to 'sweep' fees to a particular fees ledger, or loan customers money from a loan ledger.
- You cannot move money between ledgers with different assets: money can't move from a GBP ledger to a EUR ledger.
- Inter-ledger transactions have to be between ledgers created using the same API-Key.
- If they are in **PlayLive** they still need to be under £5.
- To make an interledger transfer you need a ledger with funds on it, and a ledger to send the funds to.
- The funds will be transferred instantaneously. If they don't arrive in the ledger and the transaction status is `accepted`, the funds are almost certainly in the Partner Quarantine queue.

### The Inter-ledger Transaction Object

| Attribute                                   | Description                    |
|:--------------------------------------------|:-------------------------------|
| `amount` <br> _number_, required            | The amount to be sent          |
| `ledger_from_id` <br> _string_, required    | The ledger from which funds will be sent |
| `ledger_to_id` <br> _string_, required      | The ledger to which funds will be sent |
| `transaction_meta` <br>  _object_, optional | Extra information you want to input in the form of custom fields |
