# Creating Ledgers

> **Endpoints**

```shell
POST /v1/customer/ledgers
POST /v1/customer/ledgers/virtual
POST /v1/customer/ledgers/{{LEDGER_ID}}/assign
POST /v1/customer/ledgers/{{LEDGER_ID}}/assign-iban
POST /v1/customer/ledgers/{{LEDGER_ID}}/close
GET /v1/customer/ledgers
GET /v1/customer/ledgers/changed
GET /v1/customer/ledgers/entries
GET /v1/customer/ledgers/unassigned
GET /v1/customer/ledgers/{{LEDGER_ID}}
GET /v1/customer/ledgers/by-iban
GET /v1/customer/ledgers/by-uk-bank-account
PUT /v1/customer/ledgers/{{LEDGER_ID}}
```
- Ledgers are digital accounts. They can be held by endusers or customers.
  - There are two `ledger_types`: `single-user`, and `omnibus`. An omnibus account is an account which holds the funds of multiple entities and is usually held by the customer, whereas a single-user account is of course an account holding the finds of only one entity.
- Currently we provide GBP and EUR ledgers, with USD coming later this year.
- One ledger can hold a single asset only. For example, if an enduser wants to hold GBP and EUR, then the enduser needs at least two ledgers: one for GBP, and one for EUR
- Ledgers can also be 'virtual', with 'virtual' funds that can be manually credited and debited to them that are attached to a real world asset. For instance, a gold ledger that reflects a stock of gold in a vault.
- Debit Cards can be attached to ledgers.
- Funds can be sent to, from and between ledgers in the given currency. However, we cannot do inter-ledger transfers between two ledgers holding different currencies.

## Issue a Ledger
> **Example Request**

```shell
  --request POST "https://playlive.railsbank.com/v1/customer/ledgers"
  --header "Content-Type: application/json"
	--header "Accept: application/json"
	--header "Authorization: API-Key <<yourAPI-Key>>"
  {
  "ledger_primary_use_types": [
    "ledger-primary-use-types-payments",
    "ledger-primary-use-types-fees"
  ],
  "ledger_who_owns_assets": "ledger-assets-owned-by-me",
  "holder_id": "753fa673-66b4-4c94-9ddb-f9f4b5c1e9a3",
  "ledger_t_and_cs_country_of_jurisdiction": "US",
  "partner_product": "PayrNet-EUR-1",
  "asset_type": "eur",
  "asset_class": "currency",
  "ledger_type": "ledger-type-single-user",
  "ledger_meta": {
    "foo": "bar"
  }
}
```
> **Example Response**

```shell
{
  "ledger_id": "6630b391-c5ce-46c1-9d23-a82a9e27f82d"
}
```

- This endpoint issues a ledger, assigning it to a particular holder.
- Endpoint: `POST "https://playlive.railsbank.com/v1/customer/ledgers"`
- When GBP ledgers are issued the `uk_account_number` and `uk_sort_code` are automatically assigned, however, when EUR ledgers are issued, the `iban` and `bic_swift` are not automatically assigned – the `POST v1/customer/ledgers/{{LEDGER_ID}}/assign-iban` endpoint is required to do this.
- Check out the `ledger_meta` field which allows you to add custom fields to the ledger, for instance, your own account number.

### The Ledger Object
| Attribute                                                         | Description |
|:------------------------------------------------------------------|:---------|
| `holder_id` <br> _string_, required                               | The UUID of the holder of the ledger, either an `enduser_id` or the `customer_id` |
| `asset_class` <br> _string_, required                             | The class of the asset being held in the ledger, for a normal GBP or EUR ledger, use `currency` <br> _Allowed Values:_ commodity, currency |
| `asset_type` <br> _string_, required                              | The type of asset being held in the ledger, for a normal ledger this means the currency <br> _Allowed Values:_ gbp, eur, aud, chf, cad, sek, usd, nok, nzd, jpy, gold, goldbloc, whisky |
| `partner_product` <br> _string_, required                         | The partner product of the ledger being created: this allows us to call the correct partner for issuing the chosen asset <br> _See below for Allowed Values_ |
| `ledger_type` <br> _string_, required                             | The type of ledger being created <br> _Allowed Values:_ ledger-type-single-user, ledger-type-omnibus |
| `ledger_primary_use_types` <br> _array of strings_, required      | What the ledger is going to be used for. <br> _See below for Allowed Values_ Can have multiple purposes |
| `ledger_who_owns_assets` <br> _string_, required                  | Information regarding the owner of the assets in the ledger <br> _Allowed Values:_ ledger-assets-owned-by-me, ledger-assets-owned-by-other |
| `ledger_t_and_cs_country_of_jurisdiction` <br> _string_, required | The code of the country in which the assets are held <br> _ISO 8601 format_ |
| `ledger_meta` <br> _object_, optional                             | Extra information you want to add to the ledger in the form of custom fields |

### Allowed Values: `partner_product`

| Value               | Explanation                                            |
|:--------------------|:-------------------------------------------------------|
| `ExampleBank-GBP-1` | The partner product to be used in **Play** for GBP ledgers |
| `PayrNet-GBP-1`     | The default partner product to be used in **PlayLive and Live** for GBP ledgers |
| `PayrNet-GBP-2`     | A secondary partner product available to **Live** customers |
| `ExampleBank-EUR-1` | The partner product to be used in **Play** for EUR ledgers |
| `PayrNet-EUR-1`     | The default partner product to be used in **PlayLive and Live** for EUR ledgers |

### Allowed Values: `ledger_primary_use_types`
| Value                                 | Explanation                          |
|:--------------------------------------|:-------------------------------------|
| `ledger-primary-use-types-deposit`    | Used to deposit money: there shouldn't be many transactions to and from this type of ledger |
| `ledger-primary-use-types-payments`   | Used to make payments                |
| `ledger-primary-use-types-settlement` | Used to make settlement payments     |
| `ledger-primary-use-types-float`      | Used as a float account              |
| `ledger-primary-use-types-fees`       | Used to pay fees                     |
| `ledger-primary-use-types-investment` | Used to make investments             |
| `ledger-primary-use-types-collateral` | Used to hold collateral funds        |
| `ledger-primary-use-types-escrow`     | Used for funds held by a third party on behalf of two other parties that are in the process of completing a transaction |
