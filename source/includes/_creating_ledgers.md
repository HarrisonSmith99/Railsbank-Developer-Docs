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
`POST "https://playlive.railsbank.com/v1/customer/ledgers"`

- This endpoint issues a ledger, assigning it to a particular holder.
- When GBP ledgers are issued the `uk_account_number` and `uk_sort_code` are automatically assigned, however, when EUR ledgers are issued, the `iban` and `bic_swift` are not automatically assigned – the `POST v1/customer/ledgers/{{LEDGER_ID}}/assign-iban` endpoint is required to do this.
- Check out the `ledger_meta` field which allows you to add custom fields to the ledger, for instance, your own account number.

### The Ledger Object
| Attribute                                                         | Description |
|:------------------------------------------------------------------|:---------|
| `holder_id` <br> _string_, required                               | The UUID of the holder of the ledger, either an `enduser_id` or the `customer_id` |
| `asset_class` <br> _string_, required                             | The class of the asset being held in the ledger, for a normal GBP or EUR ledger, use `currency` <br> _Allowed Values:_ commodity, currency |
| `asset_type` <br> _string_, required                              | The type of asset being held in the ledger, for a normal ledger this means the currency <br> _Allowed Values:_ gbp, eur, aud, chf, cad, sek, usd, nok, nzd, jpy |
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

## Issue a Virtual Ledger
> **Example Request**

```shell
  --request POST "https://playlive.railsbank.com/v1/customer/ledgers/virtual"
  --header "Content-Type: application/json"
	--header "Accept: application/json"
	--header "Authorization: API-Key <<yourAPI-Key>>"
  {
  "asset_class": "commodity",
  "asset_type": "gold",
  "holder_id": "c91b339e-57d7-41ea-a805-8966ce8fe4ed",
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
`POST "https://playlive.railsbank.com/v1/customer/ledgers/virtual"`

- Virtual Ledgers are ledgers that hold 'virtual' assets.
- Assets currently supported are gold, goldbloc, and whisky.
- They are designed to reflect an amount of the physical asset that an enduser might hold - in a vault, for instance.
- The assets can be manually credited and debited onto the ledgers.
- You can move the assets between virual ledgers using the inter-ledger function.
- You cannot convert assets on virtual ledgers into real assets on real ledgers.

### The Virtual Ledger Object
| Attribute                             | Description                          |
|:--------------------------------------|:-------------------------------------|
| `holder_id` <br> _string_, required   | The UUID of the holder of the ledger, either an `enduser_id` or the `customer_id` |
| `asset_type` <br> _string_, required  | The type of asset being held in the ledger, in this case, the commodity <br> _Allowed Values:_ gold, whisky, goldbloc |
| `asset_class` <br> _string_, required | The class of the asset being held in the ledger, for virtual ledgers, use `commodity` <br> _Allowed Values:_ commodity, currency |
| `ledger_meta` <br> _object_, optional | Extra information you want to add to the ledger in the form of custom fields |

## Assign a Ledger an IBAN

> **Example Request**

```shell
 --request POST "https://playlive.railsbank.com/v1/customer/ledgers/{{LEDGER_ID}}/assign-iban"
 --header "Content-Type: application/json"
 --header "Accept: application/json"
 --header "Authorization: API-Key <<yourAPI-Key>>"
```
> **Example Response**

```shell
{
 "ledger_id": "6630b391-c5ce-46c1-9d23-a82a9e27f82d"
}
```
`POST "https://playlive.railsbank.com/v1/customer/ledgers/{{LEDGER_ID}}/assign-iban"`

- EUR ledgers are not automatically assigned an `iban` or `bic_swift` code upon creation.
- To do so, you need to save the `ledger_id` the API responded with when you created the ledger, wait a couple of seconds for the ledger to be created, before calling this endpoint.
- Our IBAN is reachable via the SEPA Step2 Payment Scheme.
- The assigning of the ledger may take a few seconds or it could take a few hours: it is dependent upon the partner bank. Usually, it takes seconds.

## Fetching Individual Ledgers
> **Example Request: By UUID**

```shell
 --request GET "https://playlive.railsbank.com/v1/customer/ledgers/{{LEDGER_ID}}"
 --header "Content-Type: application/json"
 --header "Accept: application/json"
 --header "Authorization: API-Key <<yourAPI-Key>>"
```
> **Example Request: By IBAN**

```shell
 --request GET "https://playlive.railsbank.com/v1/customer/ledgers/by-iban?iban={{YOUR_IBAN}}"
 --header "Content-Type: application/json"
 --header "Accept: application/json"
 --header "Authorization: API-Key <<yourAPI-Key>>"
```
> **Example Request: By UK Account Number and Sort Code**

```shell
 --request GET "https://playlive.railsbank.com/v1/customer/ledgers/by-uk-bank-account?uk_account_number={{YOUR_UK_ACCOUNT_NUMBER}}&uk_sort_code={{YOUR_UK_SORT_CODE}}"
 --header "Content-Type: application/json"
 --header "Accept: application/json"
 --header "Authorization: API-Key <<yourAPI-Key>>"
```

> **Example Response**

```shell
  {
     "iban": "GB47PAYR040052123397XX",
     "last_modified_at": "2019-06-03T10:26:17.646Z",
     "ledger_primary_use_types": [
         "ledger-primary-use-types-payments"
     ],
     "ledger_id": "5cf4f5c9-4307-4e51-92c6-098e55ccb8bf",
     "ledger_holder": {
         "customer_id": "5b9f7e0a-634b-441f-a755-e42c0b214c69",
         "company": {
             "name": "Railsbank"
         }
     },
     "ledger_who_owns_assets": "ledger-assets-owned-by-me",
     "uk_sort_code": "0400XX",
     "partner_ref": "payrnet",
     "holder_id": "5b9f7e0a-634b-441f-a755-e42c0b214c69",
     "partner_id": "58fe2ce1-b90b-4868-b40d-cb3bc43395d9",
     "ledger_t_and_cs_country_of_jurisdiction": "GB",
     "bic_swift": "PAYRGB2LXXX",
     "ledger_status": "ledger-status-ok",
     "amount": 0,
     "created_at": "2019-06-03T10:26:17.646Z",
     "partner_product": "PayrNet-GBP-1",
     "partner": {
         "partner_id": "58fe2ce1-b90b-4868-b40d-cb3bc43395d9",
         "company": {
             "name": "PayrNet"
         },
         "partner_ref": "payrnet"
     },
     "ledger_iban_status": "ledger-iban-status-ok",
     "asset_type": "gbp",
     "uk_account_number": "123397XX",
     "asset_class": "currency",
     "ledger_type": "ledger-type-omnibus"
 }
```

- Fetch a ledger to access the fundamental information about a ledger: the account details and balance.
- We advise that you add the `/wait` parameter on the end of the url. This means the API will wait until the ledger is in a 'rest' state, meaning it is usable.
  - This is useful because while the creation of a ledger is practically atomic, the assigning of an IBAN or an account number and sort code takes a couple of seconds. Wait allows them to be assigned before the ledger information is returned to you.

### Endpoints

`GET /v1/customer/ledgers/{{ledger_id}}/wait`

Get a ledger based on its UUID. This will allow you to discover the account details that can be used in the following endpoints.

`GET /v1/customer/ledgers/by-iban?iban={{YOUR_IBAN}}`

Get a ledger based on its IBAN.

`GET /v1/customer/ledgers/by-uk-bank-account?uk_account_number={{YOUR_UK_ACCOUNT_NUMBER}}&uk_sort_code={{YOUR_UK_SORT_CODE}}`

Get a ledger based on its uk account details.

### New Fields

| Attribute                            | Description                           |
|:-------------------------------------|:--------------------------------------|
| `amount` <br> _number_               | The ledger balance                    |
| `ledger_status` <br> _string_        | The status of the ledger <br> _See below of Allowed Values_ |
| `last_modified_at` <br> _string_     | The timestamp of the last time the ledger was changed in some way - either a PUT was executed to update information or the ledger was credited or debited |
| `created_at` <br> _string_           | The timestamp of when the ledger was created |
| `iban` <br> _string_                 | The iban assigned to the ledger       |
| `bic_swift` <br> _string_            | The SWIFT Business Identifier Code e.g. PAYRGB21XXX <br> _4 letters (bank code), 2 letters (ISO 3166-1 country code), 2 letters or digits (location code), 3 letters or digits (branch code, optional, 'XXX' for primary office)_ |
| `ledger_iban_status` <br> _string_   | The status of the iban <br> _Allowed Values:_ ledger-iban-status-pending, entity-status-unable-to-advance, ledger-iban-status-declined, ledger-iban-status-ok |
| `uk_account_number` <br> _string_    | The UK account number <br> _eight integers_ |
| `uk_sort_code` <br> _string_         | The UK sort code - identifies the bank branch <br> _six integers_ |
| `partner` <br> _object_              | Information regarding the partner the ledger was created by |
| `partner.partner_ref` <br> _string_  | The reference of the partner          |
| `partner.partner_id` <br> _string_   | The UUID of the partner               |
| `partner.company` <br> _object_      | Object to hold the name of the partner |
| `partner.company.name` <br> _string_ | The name of the Partner               |
| `ledger_holder` <br> _object_        | Information regarding the ledger holder |

### Ledger Statuses
| Message                      | Description                                   |
|:-----------------------------|:----------------------------------------------|
| `ledger-status-missing-data` | Extra data required to create the ledger. Often, this occurs if the holder of the ledger is missing the data required to generate a ledger. |
| `ledger-status-pending`      | The ledger is being created. To avoid fetching an ledger in this state, use our `/wait` parameter. |
| `ledger-status-error`        | The ledger has not been created properly. This occurs if the proposed holder of the ledger is in a state in which they are unable to hold the ledger, for instance, pending. |
| `ledger-status-ok`           | The ledger is ready to receive and send money to and from. You will receive a `type: entity-ready-to-use` webhook. |
| `ledger-status-declined`     | The ledger has been declined. For instance if the `"ledger_t_and_cs_country_of_jurisdiction":` is not acceptable to our compliance team. |

## Fetch Ledger Entries
> **Example Request**

```shell
 --request GET "https://playlive.railsbank.com/v1/customer/ledgers/{{LEDGER_ID}}/entries"
 --header "Content-Type: application/json"
 --header "Accept: application/json"
 --header "Authorization: API-Key <<yourAPI-Key>>"
```
> **Example Response**

```shell
[
  {
    "created_at": "2000-01-01T00:00:00.000Z",
    "ledger_entry_id": "bb8b2428-f94c-41df-8e82-a895ab4d6ac8",
    "transaction_id": "6630b391-c5ce-46c1-9d23-a82a9e27f82d",
    "amount": "0",
    "ledger_entry_type": "debit"
  },
  {
    "created_at": "2000-01-01T00:00:00.000Z",
    "ledger_entry_id": "8763b5df-a61c-4f77-9724-41ca9cde3654",
    "transaction_id": "8763b5df-a61c-4f77-9724-41ca9cde3654",
    "amount": "3",
    "ledger_entry_type": "credit"
  },
  {
    "created_at": "2000-01-01T00:00:00.000Z",
    "ledger_entry_id": "c91b339e-57d7-41ea-a805-8966ce8fe4ed",
    "transaction_id": "6630b391-c5ce-46c1-9d23-a82a9e27f82d",
    "amount": "5",
    "ledger_entry_type": "credit"
  },
  {
    "created_at": "2000-01-01T00:00:00.000Z",
    "ledger_entry_id": "bb8b2428-f94c-41df-8e82-a895ab4d6ac8",
    "transaction_id": "bb8b2428-f94c-41df-8e82-a895ab4d6ac8",
    "amount": "10",
    "ledger_entry_type": "credit"
  }
]
```

`GET /v1/customer/ledgers/{{LEDGER_ID}}/entries`

`GET /v1/customer/ledgers/{{LEDGER_ID}}/entries?starting_at_date=2018-09-09`

`GET /v1/customer/ledgers/{{LEDGER_ID}}/entries?starting_at_date=2018-09-09`

- This endpoint returns the transactions performed on a given ledger, providing a starting point for generating a statement for a ledger.
- It returns an array of transactions within the specified time range.
- For optimal performance, we return limited data about the transactions.
- Use either the date-based parameters or the id-based parameters, not both.
- When using parameters, don't place the strings within quotes.

| Parameter                        | Description                               |
|:---------------------------------|:------------------------------------------|
| `starting_at_date` <br> _string_ | Any transaction before this date will not be shown in the array <br> _YYYY-MM-DD_ |
| `to_date` <br> _string_          | Any transactions after this date will not be shown <br> _YYYY-MM-DD_ |
| `items_per_page` <br> _integer_  | The number of items returned in each page. A hyperlink to the next page will be found in the response headers |
| `last_seen_id` <br> _string_     | The entries returned will have all been created after the `ledger_entry_id` whose UUID is the `last_seen_id` |

### Ledger Entry Attribute

| Attribute                         | Description                              |
|:----------------------------------|:-----------------------------------------|
| `created_at` <br> _string_        | The timestamp of when the transaction was created |
| `ledger_entry_id` <br> _string_   | The UUID of the particular ledger entry  |
| `transaction_id` <br> _string_    | The UUID of the transaction that made the entry. Can be fetched using `GET v1/customer/transactions/{{TRANSACTION_ID}}` |
| `amount` <br> _string_            | The amount credited or debited           |
| `ledger_entry_type` <br> _string_ | The type of entry <br> _debit or credit_ |

## Fetch Multiple Ledgers

> **Example Request**

```shell
 --request GET "https://playlive.railsbank.com/v1/customer/ledgers?holder_id={{CUSTOMER_ID}}"
 --header "Content-Type: application/json"
 --header "Accept: application/json"
 --header "Authorization: API-Key <<yourAPI-Key>>"
```

> **Example Response**

```shell
[
  {
      "iban": "GB22PAYR00997700000143",
      "last_modified_at": "2018-10-04T09:23:27.552Z",
      "ledger_primary_use_types": [
          "ledger-primary-use-types-payments"
      ],
      "ledger_id": "5bb5dc0f-800e-418d-8642-f3af85fb7e0c",
      "ledger_holder": {
          "customer_id": "5b9f7e0a-634b-441f-a755-e42c0b214c69",
          "company": {
              "name": "Railsbank"
          }
      },
      "ledger_who_owns_assets": "ledger-assets-owned-by-me",
      "partner_ref": "payrnet",
      "holder_id": "5b9f7e0a-634b-441f-a755-e42c0b214c69",
      "partner_id": "58fe2ce1-b90b-4868-b40d-cb3bc43395d9",
      "ledger_t_and_cs_country_of_jurisdiction": "GB",
      "bic_swift": "PAYRGB21",
      "ledger_status": "ledger-status-ok",
      "amount": 0.5,
      "created_at": "2018-10-04T09:23:27.552Z",
      "partner_product": "PayrNet-EUR-1",
      "partner": {
          "partner_id": "58fe2ce1-b90b-4868-b40d-cb3bc43395d9",
          "company": {
              "name": "PayrNet"
          },
          "partner_ref": "payrnet"
      },
      "ledger_iban_status": "ledger-iban-status-ok",
      "asset_type": "eur",
      "asset_class": "currency",
      "ledger_type": "ledger-type-single-user"
  },
  {
      "last_modified_at": "2018-10-16T09:31:05.026Z",
      "ledger_primary_use_types": [
          "ledger-primary-use-types-payments"
      ],
      "ledger_id": "5bc5afd9-bade-410d-add4-79b4ac56079f",
      "ledger_holder": {
          "customer_id": "5b9f7e0a-634b-441f-a755-e42c0b214c69",
          "company": {
              "name": "Railsbank"
          }
      },
      "ledger_who_owns_assets": "ledger-assets-owned-by-me",
      "partner_ref": "payrnet",
      "holder_id": "5b9f7e0a-634b-441f-a755-e42c0b214c69",
      "partner_id": "58fe2ce1-b90b-4868-b40d-cb3bc43395d9",
      "ledger_t_and_cs_country_of_jurisdiction": "GB",
      "ledger_status": "ledger-status-ok",
      "amount": 0.1,
      "created_at": "2018-10-16T09:31:05.026Z",
      "partner_product": "PayrNet-EUR-1",
      "partner": {
          "partner_id": "58fe2ce1-b90b-4868-b40d-cb3bc43395d9",
          "company": {
              "name": "PayrNet"
          },
          "partner_ref": "payrnet"
      },
      "asset_type": "eur",
      "asset_class": "currency",
      "ledger_type": "ledger-type-single-user"
  }
]
```
`GET /v1/customer/ledgers`

`GET /v1/customer/ledgers?holder_id={{CUSTOMER_ID}}`

`GET /v1/customer/ledgers?partner_product=PayrNet-GBP-1`

`GET /v1/customer/ledgers?holder_id={{CUSTOMER_ID}}&partner_product=PayrNet-GBP-1`

`GET /v1/customer/ledgers?created_at=2018-09-01`

`GET /v1/customer/ledgers?holder_id={{CUSTOMER_ID}}&partner_product=PayrNet-GBP-1&created_at=2018-09-01`

`GET /v1/customer/ledgers?last_seen_id={{LEDGER_ID}}`

`GET /v1/customer/ledgers?items_per_page=25`

- This endpoint allows you to fetch multiple normal ledgers by the holder of the ledger and/or the partner product of the ledger.
- For instance, you might want to see all the GBP ledgers held by a particular enduser, or all the EUR ledgers held by the customer.
- The response can also be constricted to a particular timeframe or a `last_seen_id`. It can also be paginated to shorten the response time.
- All parameters are optional - you can simply fetch all the ledgers created using your API-Key in the given environment.
- At the same time, all parameters can be used. To add a parameter simply use the `&` symbol.
  - Use either the date-based parameters or the id-based parameters, not both.
  - When using parameters, don't place the strings within quotes.
- The response will be an array of ledger objects in the same format as if they had all been fetched individually.
- The response will be in the same format no matter the number of parameters used.

| Parameter                       | Description                                |
|:--------------------------------|:-------------------------------------------|
| `holder_id` <br> _string_       | The holder of the ledgers: an enduser or a customer |
| `partner_product` <br> _string_ | The partner product the ledgers call under <br> _Allowed Values:_ ExampleBank-GBP-1, ExampleBank-EUR-1, PayrNet-GBP-1, PayrNet-GBP-2, PayrNet-EUR-1 |
| `items_per_page` <br> _integer_ | The number of items returned in each page. A hyperlink to the next page will be found in the response headers |
| `created_at` <br> _string_      | The date that the list of ledgers will begin at: any ledger created before this date will not be returned. <br> _YYYY-MM-DD_ |
| `last_seen_id` <br> _string_    | The ledgers returned will have all been created after the ledger whose UUID is the `last_seen_id` |

## Fetch Recently Modified Ledgers
> **Example Request**

```shell
 --request GET "https://playlive.railsbank.com/v1/customer/ledgers/changed?from_date="2018-09-09""
 --header "Content-Type: application/json"
 --header "Accept: application/json"
 --header "Authorization: API-Key <<yourAPI-Key>>"
```
> **Example Response**

```shell
{
  "next_request_date": "2020-01-01",
  "ledgers": [
    {
      "ledger_id": "bb8b2428-f94c-41df-8e82-a895ab4d6ac8"
    },
    {
      "ledger_id": "8763b5df-a61c-4f77-9724-41ca9cde3654"
    },
    {
      "ledger_id": "8763b5df-a61c-4f77-9724-41ca9cde3654"
    }
  ]
}
```

`GET /v1/customer/ledgers/changed?from_date="2018-09-09"`

- This endpoint allows you to fetch all the ledgers changed after a specified date, the `from_date` (YYYY-MM-DD).
- By changed, we mean ledgers that have been debited or credited.
- The call is optimized to be extremely fast and as such will only respond the UUIDs of the ledgers that have been changed. Not all their details.
- To fetch their details, iterate over each of them and call `GET v1/customer/ledgers/{{LEDGER_ID}}`.
- In the response is a field called `next_request_date`, this signifies the timestamp of the first actual changed after the specified `from_date`.

## Fetch Unassigned Ledgers
> **Example Request**

```shell
 --request GET "https://playlive.railsbank.com/v1/customer/ledgers/unassigned"
 --header "Content-Type: application/json"
 --header "Accept: application/json"
 --header "Authorization: API-Key <<yourAPI-Key>>"
```
> **Example Response**

```shell
[
    {
      "ledger_id": "bb8b2428-f94c-41df-8e82-a895ab4d6ac8"
    },
    {
      "ledger_id": "8763b5df-a61c-4f77-9724-41ca9cde3654"
    },
    {
      "ledger_id": "8763b5df-a61c-4f77-9724-41ca9cde3654"
    }
]
```

`GET /v1/customer/ledgers/unassigned`

- This endpoint simply fetches the ledgers that do not have a holder.
- The response will be an array of ledger UUIDs. To fetch their details you will need to iterate over each of them and call `GET v1/customer/ledgers/{{LEDGER_ID}}`.
- To assign a ledger to a holder, call `POST /v1/customer/ledgers/{ledger_id}/assign` with the `holder_id` in the request body.

## Assign a Ledger to a Holder
> **Example Request**

```shell
 --request POST "https://playlive.railsbank.com/v1/customer/ledgers/{{LEDGER_ID}}/assign"
 --header "Content-Type: application/json"
 --header "Accept: application/json"
 --header "Authorization: API-Key <<yourAPI-Key>>"

 {
  "holder_id": "bb8b2428-f94c-41df-8e82-a895ab4d6ac8"
}
```
> **Example Response**

```shell
{
  "ledger_id": "753fa673-66b4-4c94-9ddb-f9f4b5c1e9a3"
}
```
`POST /v1/customer/ledgers/{{LEDGER_ID}}/assign`

- This endpoint allows you to assign an unassigned ledger to a holder.
- If you try and assign a ledger that already has a holder to a new holder using this endpoint you will receive `"error": "invalid-state-of-entity"`.

## Update a Ledger
> **Example Request**

```shell
 --request PUT "https://playlive.railsbank.com/v1/customer/ledgers/{{LEDGER_ID}}"
 --header "Content-Type: application/json"
 --header "Accept: application/json"
 --header "Authorization: API-Key <<yourAPI-Key>>"
 {
   "ledger_meta": {
     "custom_account_number"; "09867594"
   }
 }
```
> **Example Response**

```shell
{
  "ledger_id": "753fa673-66b4-4c94-9ddb-f9f4b5c1e9a3"
}
```

`PUT /v1/customer/ledgers/{{LEDGER_ID}}`

- This endpoint allows you to update information on a given ledger.
- At present, we only allow you to update the metadata on a ledger.
  - This includes adding new metadata.
- If you want to update other fields, we recommend closing the ledger and creating a new one.

## Close a Ledger
> **Example Request**

```shell
 --request POST "https://playlive.railsbank.com/v1/customer/{{LEDGER_ID}}/close"
 --header "Content-Type: application/json"
 --header "Accept: application/json"
 --header "Authorization: API-Key <<yourAPI-Key>>"
```
> **Example Response**

```shell
{
  "ledger_id": "753fa673-66b4-4c94-9ddb-f9f4b5c1e9a3"
}
```
`POST /v1/customer/ledgers/{{LEDGER_ID}}/close`

- This endpoint allows you to permanently close a ledger.
- We do not currently allow the disabling and then reopening of ledgers.
- This is supported for any type of normal Railsbank ledger: you cannot close virtual ledgers.
- There's no request body.
- You can't close multiple ledgers at a time with a single endpoint at present.
