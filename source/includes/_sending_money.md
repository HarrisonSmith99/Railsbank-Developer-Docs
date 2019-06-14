# Sending Money
> **Endpoints**

```shell
`POST /v1/customer/transactions`
`POST /v1/customer/transactions/inter-ledger`
`POST /v1/customer/transactions/{{TRANSACTION_ID}}/invoices`
`POST /v1/customer/transactions/bulk`
`POST /v1/customer/transactions/manual-debit`
`POST /v1/customer/transactions/manual-credit`
`GET /v1/customer/transactions`
`GET /v1/customer/transactions/{{TRANSACTION_ID}}`
`PUT /v1/customer/transactions/{{TRANSACTION_ID}}`

```

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

## Send to another Ledger
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

## Send Multiple Transactions
> **Example Request**

```shell
--request POST "https://playlive.railsbank.com/v1/customer/transactions/bulk"
--header "Content-Type: application/json"
--header "Accept: application/json"
--header "Authorization: API-Key <<yourAPI-Key>>"
[
  {
    "payment_credit_date": "2000-11-05",
    "payment_type": "payment-type-EU-SEPA-Step2",
    "ledger_id": "753fa673-66b4-4c94-9ddb-f9f4b5c1e9a3",
    "who_pays_charges": "shared",
    "reference": "this is a test payment",
    "amount": "0",
    "ledger_from_id": "753fa673-66b4-4c94-9ddb-f9f4b5c1e9a3",
    "beneficiary_id": "6630b391-c5ce-46c1-9d23-a82a9e27f82d",
    "transaction_meta": {
      "foo": "bar"
    },
    "beneficiary_account_id": "c91b339e-57d7-41ea-a805-8966ce8fe4ed",
    "cut_off_time_implementation": "loose"
  },
  {
    "payment_credit_date": "2000-11-05",
    "payment_type": "payment-type-EU-SEPA-Step2",
    "ledger_id": "8763b5df-a61c-4f77-9724-41ca9cde3654",
    "who_pays_charges": "shared",
    "reference": "this is a test payment",
    "amount": "0",
    "ledger_from_id": "c91b339e-57d7-41ea-a805-8966ce8fe4ed",
    "beneficiary_id": "8763b5df-a61c-4f77-9724-41ca9cde3654",
    "transaction_meta": {
      "foo": "bar"
    },
    "beneficiary_account_id": "6630b391-c5ce-46c1-9d23-a82a9e27f82d",
    "cut_off_time_implementation": "loose"
  },
  {
    "payment_credit_date": "2000-11-05",
    "payment_type": "payment-type-EU-SEPA-Step2",
    "ledger_id": "753fa673-66b4-4c94-9ddb-f9f4b5c1e9a3",
    "who_pays_charges": "shared",
    "reference": "this is a test payment",
    "amount": "0",
    "ledger_from_id": "c91b339e-57d7-41ea-a805-8966ce8fe4ed",
    "beneficiary_id": "8763b5df-a61c-4f77-9724-41ca9cde3654",
    "transaction_meta": {
      "foo": "bar"
    },
    "beneficiary_account_id": "c91b339e-57d7-41ea-a805-8966ce8fe4ed",
    "cut_off_time_implementation": "loose"
  }
]
```
> **Example Response**

```shell
[
  {
    "transaction_id": "6630b391-c5ce-46c1-9d23-a82a9e27f82d"
  },
  {
    "transaction_id": "753fa673-66b4-4c94-9ddb-f9f4b5c1e9a3"
  },
  {
    "transaction_id": "8763b5df-a61c-4f77-9724-41ca9cde3654"
  },
  {
    "transaction_id": "753fa673-66b4-4c94-9ddb-f9f4b5c1e9a3"
  }
]
```
`POST "https://playlive.railsbank.com/v1/customer/transactions/bulk"`

- This endpoint allows you to send multiple transactions to multiple beneficiaries with one request.
- Each transaction in the array has the same object as a standard transaction.
- You can send different types of payment in the same request. For instance, you can send both EUR and GBP payments.
- The API will respond with an array of transaction ids in the order of the transactions POSTed.

## Fetch a Transaction
> **Example Request**

```shell
--request GET "https://playlive.railsbank.com/v1/customer/transactions/{{TRANSACTION_ID}}"
--header "Content-Type: application/json"
--header "Accept: application/json"
--header "Authorization: API-Key <<yourAPI-Key>>"
```
> **Example Response - GBP**

```shell
{
        "payment_type": "payment-type-UK-FasterPayments",
        "transaction_type": "transaction-type-send",
        "transaction_status": "transaction-status-accepted",
        "transaction_info": {},
        "reference": "xyz",
        "amount": 8.08,
        "ledger_from_id": "5bed3df1-9fb6-4c23-8d61-814a0e61113c",
        "transaction_id": "5cff65c2-71aa-42c0-b620-0211c7a891c2",
        "created_at": "2019-06-11T08:26:42.607Z",
        "partner_product": "PayrNet-GBP-1",
        "beneficiary_id": "5cf52d84-04ce-4de6-8f35-bc11295afd3b",
        "transaction_printout": {
            "ultimatesenderaddress": {
                "address_iso_country": "GB"
            },
            "beneficiaryiban": "sort-code - 2314XX/134598XX",
            "pspofsendername": "PayrNet",
            "pspaccountlocation": "PAYRGB2LXXX",
            "pspofsenderphysicallocation": "GB",
            "pspaccounttandcscountryofjurisdiction": "GB",
            "ultimatesendername": "Railsbank",
            "pspofsenderaddress": {
                "address_refinement": "Kemp House",
                "address_number": "152",
                "address_street": "City Road",
                "address_city": "London",
                "address_postal_code": "EC1V 2NX",
                "address_iso_country": "GB"
            },
            "paymentonbehalfoftype": "POO",
            "pspofultimatebenename": "Starling",
            "ultimatesenderaccountnumber": "GB24PAYR04005213694938",
            "beneficiaryname": "Railsbank Technology Limited",
            "paymentpartytype": "third_party"
        },
        "asset_type": "gbp",
        "asset_class": "currency",
        "transaction_meta": {
            "info": "2018-02-21T16:05:13.016Z | 0.19"
        },
        "invoices": [],
        "beneficiary_account_id": "5cf52d84-0faa-4104-bf6c-395896f3b04b"
    },
    {
        "payment_type": "payment-type-UK-FasterPayments",
        "transaction_type": "transaction-type-send",
        "payment_info": {
            "domesticInstructionAccount": {
                "sortCode": "2314XX",
                "accountNumber": "134598XX",
                "accountName": "Railsbank Technology Limited"
            },
            "reference": "xyz",
            "currencyAndAmount": {
                "currency": "GBP",
                "minorUnits": 79
            },
            "type": "SIP"
        },
        "transaction_status": "transaction-status-accepted",
        "transaction_info": {},
        "reference": "xyz",
        "amount": 0.79,
        "ledger_from_id": "5bed4559-a448-43ba-a826-5900d6766457",
        "transaction_id": "5cff660e-037c-4371-a2ef-a0c42bf5c1d9",
        "created_at": "2019-06-11T08:27:58.143Z",
        "partner_product": "PayrNet-GBP-1",
        "beneficiary_id": "5cf52d84-04ce-4de6-8f35-bc11295afd3b",
        "transaction_printout": {
            "ultimatesenderaddress": {
                "address_iso_country": "GB"
            },
            "beneficiaryiban": "sort-code - 231470/13459843",
            "pspofsendername": "PayrNet",
            "pspaccountlocation": "PAYRGB2LXXX",
            "pspofsenderphysicallocation": "GB",
            "pspaccounttandcscountryofjurisdiction": "GB",
            "ultimatesendername": "Railsbank",
            "pspofsenderaddress": {
                "address_refinement": "Kemp House",
                "address_number": "152",
                "address_street": "City Road",
                "address_city": "London",
                "address_postal_code": "EC1V 2NX",
                "address_iso_country": "GB"
            },
            "paymentonbehalfoftype": "POO",
            "pspofultimatebenename": "Starling",
            "ultimatesenderaccountnumber": "GB62PAYR04005218848640",
            "beneficiaryname": "Railsbank Technology Limited",
            "paymentpartytype": "third_party"
        },
        "asset_type": "gbp",
        "asset_class": "currency",
        "transaction_meta": {
            "info": "2018-02-21T16:05:13.016Z | 0.19"
        },
        "invoices": [],
        "beneficiary_account_id": "5cf52d84-0faa-4104-bf6c-395896f3b04b"
    }
```

> **Example Response - EUR**

```shell
{
    "payment_type": "payment-type-EU-SEPA-Step2",
    "transaction_type": "transaction-type-send",
    "payment_info": {
        "credit-transfer-transaction-information*": [
            {
                "debtor-account": {
                    "identification": {
                        "iban": "GB65PAYR00997700000339"
                    }
                },
                "debtor-agent": {
                    "financial-institution-identification": {
                        "bic": "PAYRGB21"
                    }
                },
                "creditor-account": {
                    "identification": {
                        "iban": "DE75700111106050110069"
                    }
                },
                "creditor": {
                    "name": "Railsbank Technology Limited",
                    "postal-address": {
                        "country": "DE"
                    }
                },
                "payment-type-information": {
                    "service-level": {
                        "code": "SEPA"
                    }
                },
                "remittance-information": {
                    "unstructured*": [
                        "xyz"
                    ]
                },
                "creditor-agent": {
                    "financial-institution-identification": {
                        "bic": "DEKTDE7GXXX"
                    }
                },
                "interbank-settlement-amount": {
                    "value": 1.1,
                    "currency": "EUR"
                },
                "payment-identification": {
                    "end-to-end-identification": "NOTPROVIDED",
                    "transaction-identification": "174682e83ae04fa5a8fb2b099cdcbe9b"
                },
                "charge-bearer": "SLEV",
                "debtor": {
                    "name": "Railsbank",
                    "postal-address": {
                        "country": "GB"
                    }
                }
            }
        ],
        "group-header": {
            "message-identification": "174682e83ae04fa5a8fb2b099cdcbe9b",
            "creation-date-time": "2019-06-11T08:40:38.163Z",
            "settlement-information": {
                "settlement-method": "CLRG"
            },
            "total-interbank-settlement-amount": {
                "value": 1.1,
                "currency": "EUR"
            },
            "interbank-settlement-date": "2019-06-11",
            "instructing-agent": {
                "financial-institution-identification": {
                    "bic": "PAYRGB21"
                }
            },
            "instructed-agent": {
                "financial-institution-identification": {
                    "bic": "CMBRFR2B"
                }
            }
        }
    },
    "transaction_status": "transaction-status-accepted",
    "transaction_info": {},
    "reference": "xyz",
    "amount": 1.1,
    "ledger_from_id": "5c9e28c4-8db2-4a9e-b73d-45dabe2f41d3",
    "transaction_id": "5cff6659-bbc5-4923-adc5-3e0e7b6404b2",
    "created_at": "2019-06-11T08:29:13.060Z",
    "partner_product": "PayrNet-EUR-1",
    "beneficiary_id": "5cf52ed8-7879-4adc-a9a6-476f0aa5fdf8",
    "transaction_printout": {
        "ultimatesenderaddress": {
            "address_iso_country": "GB"
        },
        "beneficiaryiban": "DE757001111060501100XX",
        "pspofsendername": "PayrNet",
        "pspaccountlocation": "PAYRGB21",
        "pspofsenderphysicallocation": "GB",
        "pspaccounttandcscountryofjurisdiction": "GB",
        "ultimatesendername": "Railsbank",
        "pspofsenderaddress": {
            "address_refinement": "Kemp House",
            "address_number": "152",
            "address_street": "City Road",
            "address_city": "London",
            "address_postal_code": "EC1V 2NX",
            "address_iso_country": "GB"
        },
        "paymentonbehalfoftype": "POO",
        "pspofultimatebenename": "Arkea",
        "ultimatesenderaccountnumber": "GB65PAYR009977000003XX",
        "beneficiaryname": "Railsbank Technology Limited",
        "paymentpartytype": "third_party"
    },
    "asset_type": "eur",
    "asset_class": "currency",
    "transaction_meta": {
        "info": "2018-02-21T16:05:13.016Z | 0.19"
    },
    "invoices": [],
    "beneficiary_account_id": "5cf52ed8-7e87-4cb4-aa88-702a0b1c19e4"
}
```
> **Example Response - INTER-LEDGER**

```shell
{
        "payment_type": "payment-type-inter-ledger",
        "transaction_type": "transaction-type-inter-ledger-omnibus",
        "transaction_status": "transaction-status-accepted",
        "ledger_to_id": "5cf4f5c9-4307-4e51-92c6-098e55ccb8bf",
        "transaction_info": {},
        "amount": 0.1,
        "ledger_from_id": "5cf4f5b7-5943-4c87-b597-cd330110ecff",
        "transaction_id": "5cfe73b5-4148-4b04-af06-e6c6a5053c89",
        "created_at": "2019-06-10T15:13:57.782Z",
        "partner_product": "PayrNet-GBP-1",
        "transaction_printout": {
            "ultimatesenderaddress": {
                "address_iso_country": "GB"
            },
            "beneficiaryiban": "GB47PAYR040052123397XX",
            "pspofsendername": "Railsbank",
            "pspaccountlocation": "PAYRGB2LXXX",
            "pspofsenderphysicallocation": "GB",
            "pspaccounttandcscountryofjurisdiction": "GB",
            "ultimatesendername": "Railsbank",
            "pspofsenderaddress": {
                "address_iso_country": "GB"
            },
            "pspofultimatebenephysicallocation": "GB",
            "pspofultimatebeneaddress": {
                "address_iso_country": "GB"
            },
            "paymentonbehalfoftype": "POO",
            "pspofultimatebenename": "Railsbank",
            "ultimatesenderaccountnumber": "GB69PAYR040052071860XX",
            "beneficiaryname": "Railsbank",
            "paymentpartytype": "interledger"
        },
        "asset_type": "gbp",
        "asset_class": "currency",
        "invoices": []
    }
```

`GET "https://playlive.railsbank.com/v1/customer/transactions/{{TRANSACTION_ID}}"`

- This endpoint allows you to fetch a transaction.
- Please note:
  - When you see `transaction-status-accepted`, this does not necessarily mean that the funds will be transferred to the beneficiary or the ledger. The reason for this is that the transaction could have fallen into our Partner Quarantine Queue if it has broken a firewall rule. We are not allowed to notify customers when this happens due to regulation, and it is possible that the transaction can change from `status-accepted` to `status-declined` if our Compliance Team rejects the transaction. If this occurs you will receive a webhook notification of `type: transaction-declined`.
  - Not all of the fields below will be returned in every fetch transaction call! They are all the possible fields (not including fields that could be sent to us via the payment scheme. Many are dependent on the type of transaction. See the examples to the right for responses for different types of transaction. For an example of our card transactions, see the cards endpoints.

### The Fetched Transaction Object

| Attribute                                                             | Description |
|:----------------------------------------------------------------------|:-----|
| `amount` <br> _number_                                                | The transaction amount |
| `amount_beneficiary_account` <br> _number_                            | The amount credited in the receiver's account in the target currency (for convert-and-send money transactions). |
| `amount_ledger_from` <br> _number_                                    | The amount debited from the sender's ledger in the original currency (for convert-and-send money transactions). |
| `amount_local_currency` <br> _number_                                 | The amount of the local currency transferred (for convert-and-send money transactions). |
| `additional_info` <br> _string_                                       | Any additional information about the transaction. |
| `asset_class` <br> _string_                                           | The class of the asset being sent or received. <br> _Allowed Values:_ commodity, currency |
| `asset_type` <br> _string_                                            | The type of asset being sent or received. <br> _Allowed Values:_ gbp, eur, aud, chf, cad, sek, usd, nok, nzd, jpy |
| `beneficiary_account_id` <br> _string_                                | The Railsbank Beneficiary account ID to be credited. |
| `beneficiary_id` <br> _string_                                        | The receiving Railsbank Beneficiary ID. |
| `card_currency` <br> _string_                                         | The currency on the debit card, if it is a card transaction. |
| `card_entry_method` <br> _string_                                     | The way the card was used, for instance ATM, ecommerce etc. |
| `card_expiry_date` <br> _string_                                      | The card expiry date, if it is a card transaction. |
| `card_rules_breached` <br> _array of strings_                         | The card rules breached, if any, if it is a card transaction. |
| `card_transaction_type` <br> _string_                                 | The type of card transaction. |
| `card_used` <br> _string_                                             | The token number of the card used. |
| `conversion_date` <br> _string_                                       | The date that the convert-and-send funds were converted |
| `conversion_rate` <br> _number_                                       | The rate at which the convert-and-send funds were converted. |
| `created_at` <br> _string_                                            | The date the transaction was created |
| `daily_unique_refence` <br> _string_                                  | A unique daily reference attached to the transaction. |
| `failure_reasons` <br> _array of strings_                             | The failure reasons, if any. <br> _Allowed Values:_ declined-by-compliance-firewall, card-not-active, insufficient-funds, partner-error, card-rules-breached, contact-support, fx-issue. |
| `fixed_side` <br> _string_                                            | Sets the base currency for pair rate direction, which determines how much the base currency is worth as measured against the second currency. It assures if the amount specified will either be debited from the sending ledger or credited in the receiving account. <br> _Allowed Values:_ beneficiary, sender. |
| `invoices` <br> _array of objects_                                    | Attached invoice documents. |
| `invoices.created_at` <br> _string_                                   | The date the invoice was created. |
| `invoices.description` <br> _string_                                  | A description of the invoice. |
| `invoices.document_id` <br> _string_                                  | The id of the invoice document. |
| `ledger_from_id` <br> _string_                                        | The Railsbank Ledger ID to be debited. |
| `ledger_to_id` <br> _string_                                          | The Railsbank ledger ID to be credited. |
| `mcc_description` <br> _string_                                       | The merchant category code description (for card transactions). |
| `merchant_category_code` <br> _string_                                | The merchant category code (for card transactions). |
| `merchant_details` <br> _string_                                      | Extra details about the merchant (for card transactions). |
| `merchant_id` <br> _string_                                           | The merchant's UUID (for card transactions). |
| `merchantbank_id` <br> _string_                                       | The merchant's bank UUID. |
| `missing_data` <br> _array of strings_                                | Any data required for the transaction to be accepted . |
| `partner_product` <br> _string_                                       | The partner product of the sending or receiving ledger. |
| `partner_product_fx` <br> _string_                                    | The convert-and-send partner product, if it is an FX transaction. <br> _Allowed Values:_ PayrNet-FX-1 |
| `payment_info` <br> _object_                                          | Transaction information that will be populated by the payment scheme. See example. |
| `payment_method` <br> _string_                                        | Shows whether the transaction was be sent/received via local (e.g. UKFP) or international (e.g. Swift) payment schemes. |
| `payment_type` <br> _string_                                          | The type of payment: the scheme via which it was transferred . |
| `point_of_sale_country_code` <br> _string_                            | The country code of the country in which the transaction occurred. |
| `point_of_sale_info` <br> _string_                                    | Extra information regarding the point of sale. |
| `point_of_sale_reference` <br> _string_                               | The reference from the point of sale. |
| `reason` <br> _string_                                                | Text the enduser can add to the transaction for the beneficiary. |
| `receipt_id` <br> _string_                                            | The UUID of the transaction receipt, if any. |
| `reference` <br> _string_                                             | The transaction reference. |
| `rejection_reasons` <br> _array of strings_                           | The reason the transaction was rejected, if it was. See below for possible reasons. |
| `return_info` <br> _object_                                           | Information populated by the scheme if the transaction is a return |
| `settlement_date` <br> _string_                                       | The date the transaction was/will be settled. |
| `swift_charge_bearer` <br> _string_                                   | The charge bearer if the transaction is a SWIFT transfer |
| `swift_service_level` <br> _string_                                   | The service level of the SWIFT transaction. |
| `transaction_audit_number` <br> _string_                              | The audit number, if any, of the transaction |
| `transaction_currency` <br> _string_                                  | The currency of the transaction. |
| `transaction_fee` <br> _number_                                       | The fee attached to the transaction, if any. |
| `transaction_id` <br> _string_                                        | The Railsbank UUID of the transaction. |
| `transaction_info` <br> _object_                                      | Extra detail about the transaction. |
| `transaction_info.amount` <br> _number_                               | The amount transferred. |
| `transaction_info.charge_bearer` <br> _string_                        | The bearer, if any, of any charges |
| `transaction_info.clearing_system` <br> _string_                      | The type of clearing system, if any, of the transaction. |
| `transaction_info.clearing_system_proprietary` <br> _string_          | The owner of the clearing system, if any. |
| `transaction_info.created_at` <br> _string_                           | The timestamp of when the transaction was created. |
| `transaction_info.currency` <br> _string_                             | The currency of the transaction. |
| `transaction_info.end_to_end_id` <br> _string_                        | The end to end UUID of the transaction. |
| `transaction_info.instructed_agent` <br> _object_                     | The instructed agent of the transaction. |
| `transaction_info.instructed_agent.bic` <br> _string_                 | The Branch Identifier Code of the instructed agent. |
| `transaction_info.instructing_agent` <br> _object_                    | The instructing agent of the transaction. |
| `transaction_info.instructing_agent.bic` <br> _string_                | The Branch Identifier Code of the instructing agent. |
| `transaction_info.instruction_id` <br> _string_                       | The UUID of the transaction instruction. |
| `transaction_info.local_instrument` <br> _string_                     | The instrument used to perform the transaction. For instance, a debit card. |
| `transaction_info.local_instrument_proprietary` <br> _string_         | The owner of the instrument. |
| `transaction_info.message_id` <br> _string_                           | The UUID of the message. |
| `transaction_info.message_schema` <br> _string_                       | The schema of the message. |
| `transaction_info.purpose` <br> _string_                              | The purpose of the transaction. |
| `transaction_info.purpose_category` <br> _string_                     | The category of the purpose |
| `transaction_info.purpose_category_proprietary` <br> _string_         | The owner of the category. |
| `transaction_info.receiver` <br> _object_                             | Information regarding the receiver of the transaction. |
| `transaction_info.receiver.address` <br> _object_                     | Receiver's address information. |
| `transaction_info.receiver.address.country` <br> _string_             | The country the receiver is based in. |
| `transaction_info.receiver.address.lines` <br> _string_               |      |
| `transaction_info.receiver.bic` <br> _string_                         | The Branch Identifier Code of the receiver. |
| `transaction_info.receiver.birth_city` <br> _string_                  | The birth city. |
| `transaction_info.receiver.birth_country` <br> _string_               | The birth country. |
| `transaction_info.receiver.birth_date` <br> _string_                  | The date of birth. |
| `transaction_info.receiver.birth_province` <br> _string_              | The province of birth. |
| `transaction_info.receiver.id` <br> _string_                          | The UUID of the receiver. |
| `transaction_info.receiver.issuer` <br> _string_                      | The issuing bank of the receiver. |
| `transaction_info.receiver.name` <br> _string_                        | The receiver's name |
| `transaction_info.receiver.scheme` <br> _string_                      | The scheme by which the money was received. |
| `transaction_info.receiver.scheme_proprietary` <br> _string_          | The owner of the scheme. |
| `transaction_info.receiver_account` <br> _object_                     | The account details of the receiver. |
| `transaction_info.receiver_account.iban` <br> _string_                | The IBAN of the receiver. |
| `transaction_info.receiver_agent` <br> _object_                       | The receiving agent, if any. |
| `transaction_info.receiver_agent.bic` <br> _string_                   | The BIC of the receiving agent. |
| `transaction_info.remittance_information_unstructured` <br> _string_  | Information for remittance, if any. |
| `transaction_info.sender` <br> _object_                               | Information about the sender. |
| `transaction_info.sender.address` <br> _object_                       | Sender's address information. |
| `transaction_info.sender.address.country` <br> _string_               | The country the sender is based in. |
| `transaction_info.sender.address.lines` <br> _string_                 |      |
| `transaction_info.sender.bic` <br> _string_                           | The Branch Identifier Code of the sender. |
| `transaction_info.sender.birth_city` <br> _string_                    | The birth city. |
| `transaction_info.sender.birth_country` <br> _string_                 | The birth country. |
| `transaction_info.sender.birth_date` <br> _string_                    | The date of birth. |
| `transaction_info.sender.birth_province` <br> _string_                | The province of birth. |
| `transaction_info.sender.id` <br> _string_                            | The UUID of the sender. |
| `transaction_info.sender.issuer` <br> _string_                        | The issuing bank of the sender. |
| `transaction_info.sender.name` <br> _string_                          | The sender's name |
| `transaction_info.sender.scheme` <br> _string_                        | The scheme by which the money was sent. |
| `transaction_info.sender.scheme_proprietary` <br> _string_            | The owner of the scheme. |
| `transaction_info.sender_account` <br> _object_                       | The account details of the sender. |
| `transaction_info.sender_account.iban` <br> _string_                  | The IBAN of the sender. |
| `transaction_info.sender_agent` <br> _object_                         | The sending agent, if any. |
| `transaction_info.sender_agent.bic` <br> _string_                     | The BIC of the sending agent. |
| `transaction_info.service_level` <br> _string_                        | The service level of the sender. |
| `transaction_info.settlement_account` <br> _object_                   | The settlement account details, if any. |
| `transaction_info.settlement_account.iban` <br> _string_              | The IBAN of the settlement account. |
| `transaction_info.settlement_date` <br> _string_                      | The date the transaction was/will be settled. |
| `transaction_info.settlement_method` <br> _string_                    | The method of settlement. |
| `transaction_info.transaction_id` <br> _string_                       | The transaction UUID. |
| `transaction_info.ultimate_receiver` <br> _object_                    | Information regarding the ultimate receiver of the transaction. |
| `transaction_info.ultimate_receiver.address` <br> _object_            | Ultimate receiver's address information. |
| `transaction_info.ultimate_receiver.address.country` <br> _string_    | The country the ultimate receiver is based in. |
| `transaction_info.ultimate_receiver.address.lines` <br> _string_      |      |
| `transaction_info.ultimate_receiver.bic` <br> _string_                | The Branch Identifier Code of the ultimate receiver. |
| `transaction_info.ultimate_receiver.birth_city` <br> _string_         | The birth city. |
| `transaction_info.ultimate_receiver.birth_country` <br> _string_      | The birth country. |
| `transaction_info.ultimate_receiver.birth_date` <br> _string_         | The date of birth. |
| `transaction_info.ultimate_receiver.birth_province` <br> _string_     | The province of birth. |
| `transaction_info.ultimate_receiver.id` <br> _string_                 | The UUID of the ultimate receiver. |
| `transaction_info.ultimate_receiver.issuer` <br> _string_             | The issuing bank of the ultimate receiver. |
| `transaction_info.ultimate_receiver.name` <br> _string_               | The ultimate receiver's name |
| `transaction_info.ultimate_receiver.scheme` <br> _string_             | The scheme by which the money was received. |
| `transaction_info.ultimate_receiver.scheme_proprietary` <br> _string_ | The owner of the scheme. |
| `transaction_info.ultimate_sender` <br> _object_                      | Information regarding the ultimate sender of the transaction. |
| `transaction_info.ultimate_sender.address` <br> _object_              | Ultimate sender's address information. |
| `transaction_info.ultimate_sender.address.country` <br> _string_      | The country the ultimate sender is based in. |
| `transaction_info.ultimate_sender.address.lines` <br> _string_        |      |
| `transaction_info.ultimate_sender.bic` <br> _string_                  | The Branch Identifier Code of the ultimate sender. |
| `transaction_info.ultimate_sender.birth_city` <br> _string_           | The birth city. |
| `transaction_info.ultimate_sender.birth_country` <br> _string_        | The birth country. |
| `transaction_info.ultimate_sender.birth_date` <br> _string_           | The date of birth. |
| `transaction_info.ultimate_sender.birth_province` <br> _string_       | The province of birth. |
| `transaction_info.ultimate_sender.id` <br> _string_                   | The UUID of the ultimate sender. |
| `transaction_info.ultimate_sender.issuer` <br> _string_               | The issuing bank of the ultimate sender. |
| `transaction_info.ultimate_sender.name` <br> _string_                 | The ultimate sender's name |
| `transaction_info.ultimate_sender.scheme` <br> _string_               | The scheme by which the money was sent. |
| `transaction_info.ultimate_sender.scheme_proprietary` <br> _string_   | The owner of the scheme. |
| `transaction_meta` <br> _object_                                      | An object allowing custom fields that can be created and stored against the transaction. |
| `transaction_printout` <br> _object_                                  | More information about the transaction. Will be populated by the scheme. |
| `transaction_status` <br> _string_                                    | The status of the transaction. See below. |
| `transaction_type` <br> _string_                                      | The type of transaction. |

### Transaction Statuses
| Message                           | Description                              |
|:----------------------------------|:-----------------------------------------|
| `transaction-status-missing-data` | Extra data required to complete the transaction. For instance, if the beneficiary does not have enough account information for the transaction to be completed. |
| `transaction-status-pending`      | Transaction is being processed. We do not have a `/wait` parameter for transactions as they can be processed for a longer period of time. We advise using a webhook to notify you when processing has been completed. |
| `transaction-status-quarantine`   | The transaction has fallen into your customer quarantine queue. You will receive a `type: entity-fw-quarantine` webhook and a `type: transaction-firewall-finished` webhook. **We recommend setting your own firewall rules to prevent any of your transactions falling into the partner quarantine queue and therefore out of your control.** |
| `transaction-status-accepted`     | The transaction has been accepted by the Railsbank platform and the funds have left the ledger. YOu will receive a `type: ledger-changed` webhook. Please note that this doesn't mean they have arrived at the beneficiary account: due to regulation, we cannot inform customers when a transaction lands in the partner quarantine queue, so if a transaction is `accepted`, it may be in the partner quarantine and it can be `declined`. If it is declined you will receive a `type: transaction-declined` webhook. |
| `transaction-status-declined`     | Transaction has ben declined. When you fetch the transaction you will see the `failure_reasons`, for instance: `insufficient-funds`. You will receive a `type: transaction-declined` webhook. |

### Rejection Reasons
| Reason                                                  |
|:--------------------------------------------------------|
| `account-transferred`                                   |
| `beneficiary-sort-code-not-enabled-for-faster-payments` |
| `transaction-currency-different-to-receiver-account`    |
| `beneficiary-sort-code-and-account-number-unknown`      |
| `beneficiary-account-number-invalid`                    |
| `beneficiary-name-does-not-match-beneficiary-account`   |
| `currency-and-amount-invalid`                           |
| `sending-agency-account-stopped`                        |
| `sending-account-transferred`                           |
| `beneficiary-sort-code-invalid`                         |
| `amount-invalid`                                        |
| `rejected-by-receiving-bank-with-unspecified-reason`    |
| `sending-agency-account-closed`                         |
| `insufficient-funds`                                    |
| `transaction-failed-contact-beneficiary `               |
| `currency-invalid`                                      |
| `beneficiary-account-stopped`                           |
| `receiving-agency-account-stopped`                      |
| `account-overdrawn`                                     |
| `syntax-error`                                          |
| `receiving-agency-account-close`                        |
| `additional-remittance-info-invalid`                    |
| `beneficiary-name-missing`                              |
| `account-number-invalid`                                |
| `receiving-account-transferred`                         |
| `receiving-agency-sort-code-and-account-number-unknown` |
| `reference-invalid`                                     |
| `transaction-failed-please-retry`                       |
| `duplicate-fpid2`                                       |
| `terms-and-conditions-limit`                            |
| `sending-agency-sort-code-and-account-number-unknown`   |
| `transaction-failed`                                    |
| `sending-fps-institution-action-required`               |
| `amount-exceeds-account-limit`                          |
| `beneficiary-account-closed`                            |

## Fetch Multiple Transactions
> **Example Request**

```shell
--request GET "https://playlive.railsbank.com/v1/customer/transactions?starting_at_date=2019-06-10"
--header "Content-Type: application/json"
--header "Accept: application/json"
--header "Authorization: API-Key <<yourAPI-Key>>"
```
> **Example Response**

```shell
[
    {
        "payment_type": "payment-type-UK-FasterPayments",
        "transaction_type": "transaction-type-receive",
        "payment_info": {
            "fpsSettlementDate": "2019-06-10",
            "settlementCycleUid": "f2c59b80-32b9-4d69-94c7-251c6075c5a1",
            "destinationAccount": {
                "sortCode": "0400XX",
                "accountNumber": "0718601XX",
                "accountName": "GBP Daily Checks"
            },
            "instructedAmount": {
                "currency": "GBP",
                "minorUnits": 100
            },
            "settlementAmount": {
                "currency": "GBP",
                "minorUnits": 100
            },
            "receivedAt": "2019-06-10T14:59:00.000Z",
            "paymentAccountUid": "5b9f7e0a-634b-441f-a755-e42c0b214c69",
            "paymentBusinessUid": "818a221c-8dca-43c5-9f3c-49d605a97d6d",
            "fpsSettlementCycleId": "CYCLE_002",
            "type": "SIP",
            "sourceAccount": {
                "sortCode": "2314XX",
                "accountNumber": "134598XX",
                "accountName": "railsbank technology limited"
            },
            "returnDetails": {},
            "reference": "Daily-GBP-Checks",
            "fpid": "TW00000000662939301020190610826231470     ",
            "addressUid": "5cf4f5b7-5943-4c87-b597-cd330110ecff",
            "paymentUid": "25080177-dd3c-4da3-9564-2978ae76dfe2"
        },
        "transaction_status": "transaction-status-accepted",
        "ledger_to_id": "5cf4f5b7-5943-4c87-b597-cd330110ecff",
        "transaction_info": {},
        "reference": "Daily-GBP-Checks",
        "amount": 1,
        "transaction_id": "5cfe7035-6035-449c-b6b6-8e52ab3e8b84",
        "created_at": "2019-06-10T14:59:01.186Z",
        "partner_product": "PayrNet-GBP-1",
        "transaction_printout": {
            "beneficiaryiban": "GB69PAYR04005207186010",
            "pspofsendername": "PayrNet",
            "pspofsenderphysicallocation": "GB",
            "ultimatesendername": "railsbank technology limited",
            "pspofsenderaddress": {
                "address_refinement": "Kemp House",
                "address_number": "152",
                "address_street": "City Road",
                "address_city": "London",
                "address_postal_code": "EC1V 2NX",
                "address_iso_country": "GB"
            },
            "paymentonbehalfoftype": "POO",
            "pspofultimatebenename": "Starling",
            "ultimatesenderaccountnumber": "2314XX/13459XX",
            "beneficiaryname": "Railsbank",
            "paymentpartytype": "receive third_party"
        },
        "asset_type": "gbp",
        "asset_class": "currency",
        "invoices": []
    },
    {
        "payment_type": "payment-type-inter-ledger",
        "transaction_type": "transaction-type-inter-ledger-omnibus",
        "transaction_status": "transaction-status-accepted",
        "ledger_to_id": "5cf4f5c9-4307-4e51-92c6-098e55ccb8bf",
        "transaction_info": {},
        "amount": 1,
        "ledger_from_id": "5cf4f5b7-5943-4c87-b597-cd330110ecff",
        "transaction_id": "5cfe707b-fb2b-4a86-bc7e-21ef55c817b3",
        "created_at": "2019-06-10T15:00:11.759Z",
        "partner_product": "PayrNet-GBP-1",
        "transaction_printout": {
            "ultimatesenderaddress": {
                "address_iso_country": "GB"
            },
            "beneficiaryiban": "GB47PAYR040052123397XX",
            "pspofsendername": "Railsbank",
            "pspaccountlocation": "PAYRGB2LXXX",
            "pspofsenderphysicallocation": "GB",
            "pspaccounttandcscountryofjurisdiction": "GB",
            "ultimatesendername": "Railsbank",
            "pspofsenderaddress": {
                "address_iso_country": "GB"
            },
            "pspofultimatebenephysicallocation": "GB",
            "pspofultimatebeneaddress": {
                "address_iso_country": "GB"
            },
            "paymentonbehalfoftype": "POO",
            "pspofultimatebenename": "Railsbank",
            "ultimatesenderaccountnumber": "GB69PAYR040052071860XX",
            "beneficiaryname": "Railsbank",
            "paymentpartytype": "interledger"
        },
        "asset_type": "gbp",
        "asset_class": "currency",
        "invoices": []
    },
]
```

`GET "https://playlive.railsbank.com/v1/customer/transactions`

- This endpoint returns multiple transactions in an array.
- The transactions returned will be in the same format as when you fetch a single transaction.
- You cannot fetch transactions for a particular enduser or ledger: you cannot call `transactions/{{LEDGER_ID}}` or `transactions/{{ENDUSER_ID}}`. To fetch the transactions to a ledger, you need to call `ledgers/{{LEDGER_ID}}/entries` and iterate through the transaction_id's it returns, fetch each transaction sequentially.

<details>
  <summary>Click to expand!</summary>

### Parameters

| Parameter                        | Description                               |
|:---------------------------------|:------------------------------------------|
| `starting_at_date` <br> _string_ | Any transactions created before this date will not be shown in the array <br> _YYYY-MM-DD_ |
| `items_per_page` <br> _integer_  | The number of items returned in each page. A hyperlink to the next page will be found in the response headers |
| `last_seen_id` <br> _string_     | The transactions returned will have all been created after the transaction whose UUID is the `last_seen_id` |
| `from_date` <br> _string_        | The date that the list of transactions will start from |
| `end_date` <br> _string_         | The date that the list of transactions will end at |

</details>

## Update Transactions
> **Example Request**

```shell
--request PUT "https://playlive.railsbank.com/v1/customer/transactions/{{{TRANSACTION_ID}}}"
--header "Content-Type: application/json"
--header "Accept: application/json"
--header "Authorization: API-Key <<yourAPI-Key>>"

{
  "transaction_meta": {
    "foo": "bar"
  }
}
```
> **Example Response**

```shell
{
  "transaction_id": "8763b5df-a61c-4f77-9724-41ca9cde3654"
}
```
`PUT "https://playlive.railsbank.com/v1/customer/transactions/{{TRANSACTION_ID}}`

- This endpoint allows you to update a transaction.
- Currently, the only thing you can update is the `transaction_meta` field.
- This allows you to add custom information to the transaction after it has been sent.

## Upload a Transaction Invoice
> **Example Request**

```shell
--request POST "https://playlive.railsbank.com/v1/customer/transactions/{{TRANSACTION_ID}}/invoices"
--header "Content-Type: application/json"
--header "Accept: application/json"
--header "Authorization: API-Key <<yourAPI-Key>>"
{
  "file": "{{some_file}}",
  "description": "John's March Invoice"
}
```
> **Example Response**

```shell
{
  "transaction_id": "8763b5df-a61c-4f77-9724-41ca9cde3654"
}
```

`POST "https://playlive.railsbank.com/v1/customer/transactions/{{TRANSACTION_ID}}/invoices`

- This endpoint allows you to add an invoice to a transaction in the form of a file.
- You should also add a description, however this field is optional.
- We will generate a `document_id` corresponding to the invoice.

## Credit and Debit Virtual Ledgers

> **Example Request - Credit**

```shell
--request POST "https://playlive.railsbank.com/v1/customer/transactions/manual-credit"
--header "Content-Type: application/json"
--header "Accept: application/json"
--header "Authorization: API-Key <<yourAPI-Key>>"
{
    "amount": "3",
    "ledger_id": "8763b5df-a61c-4f77-9724-41ca9cde3654",
    "transaction_meta": {
      "my_transaction_id": "08575937"
    }
}
```

> **Example Request - Debit**

```shell
--request POST "https://playlive.railsbank.com/v1/customer/transactions/manual-debit"
--header "Content-Type: application/json"
--header "Accept: application/json"
--header "Authorization: API-Key <<yourAPI-Key>>"
{
    "amount": "3",
    "ledger_id": "8763b5df-a61c-4f77-9724-41ca9cde3654",
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

- These endpoints allow you to credit or debit a virtual ledger.
- They are designed to allow you to track of real assets, like gold, outside of Railsbank.
- There are no limits on how much you can credit or debit.
- These transactions can be fetched like any other transaction.

### The Virtual Transaction Object
| Attribute                                   | Description                    |
|:--------------------------------------------|:-------------------------------|
| `amount` <br> _number_, required            | The amount to be sent          |
| `ledger_id` <br> _string_, required         | The ledger to be debited or credited |
| `transaction_meta` <br>  _object_, optional | Extra information you want to input in the form of custom fields |
