# Onboarding Beneficiaries
> **Endpoints**

```shell
POST /v1/customer/beneficiaries
POST /v1/customer/beneficiaries/{{BENEFICIARY_ID}}/accounts
GET /v1/customer/beneficiaries/{{BENEFICIARY_ID}}
GET /v1/customer/beneficiaries/{{BENEFICIARY_ID}}/accounts
GET /v1/customer/beneficiaries/{{BENEFICIARY_ID}}/accounts/{{ACCOUNT_ID}}
PUT /v1/customer/beneficiaries/{{BENEFICIARY_ID}}
PUT /v1/customer/beneficiaries/{{BENEFICIARY_ID}}/accounts/{{ACCOUNT_ID}}
PUT /v1/customer/beneficiaries/{{BENEFICIARY_ID}}/accounts/{{ACCOUNT_ID}}/make-default
````

- A **beneficiary** is an entity who *holds* an account to which money is sent to from a Railsbank ledger.
- A beneficiary can either be: a **person** or a **company**
- Beneficiaries are be held by either an **enduser** or the **customer**.
- Once the beneiciary is made, the holder of the beneficiary can send money from their ledgers to one of their beneficiaries.
- Just as is the case in the real world, a beneficiary can have multiple accounts - for instance a GBP account and a EUR account. The account to which you want to send money to must be set as the `default` account.
- It is not possible to delete a beneficiary. please be mindful when creating them.
- Rules similar to those that apply to endusers apply to creating beneficiaries: beneficiaries can either bet individuals - `person` - or corporates - `company`. Different payloads are required for each

## Create an Individual Beneficiary
> **Example Request - GBP Bank Account**

```shell
--request POST "https://playlive.railsbank.com/v1/customer/beneficiaries"
--header "Content-Type: application/json"
--header "Accept: application/json"
--header "Authorization: API-Key <<yourAPI-Key>>"
{
  "holder_id": "{{enduser_id}}",
  "asset_class": "currency",
  "asset_type": "gbp",
  "default_account": {
    "account_number": "45564658",
    "bank_country": "GB",
    "bank_code": "040004",
    "bank_name": "Monzo",
    "asset_type": "gbp",
    "asset_class": "currency",
    "bank_code_type": "sort-code"
  },
  "person": {
    "date_onboarded": "2015-11-21",
    "email": "johnsmith@example.net",
    "name": "John Smith",
    "address": {
      "address_refinement": "Apartment 42",
      "address_number": "29",
      "address_street": "Acacia Road",
      "address_city": "London",
      "address_region": "Essex",
      "address_postal_code": "FX20 7XS",
      "address_iso_country": "GB"
    },
    "country_of_residence": [
      "GB"
    ],
    "telephone": "+44 22 626 2626",
    "date_of_birth": "1970-11-05",
    "nationality": [
      "British"
    ]
  },
  "beneficiary_meta": {
    "foo": "baa",
    "our_salesforce_reference": "http://na1.salesforce.com/5003000000D8cuI"
  }
}
```
> **Example Response**

```shell
{
    "beneficiary_id": "5b054129-9139-4300-b40a-2e1154e1edb8"
}
```

`POST "https://playlive.railsbank.com/v1/customer/beneficiaries"`

- To create a beneficiary you need to know either an `enduser_id` or your `customer_id`. If the customer is the holder of the beneficiary it signifies that it is the customer who will be sending the money.
- Of course, you must also know the account details of the beneficiary.
- The minimum data required to create an individual beneficiary is the person's `name` (first and last name) and full address, including the `address_iso_country` (we support iso-alpha-2 and alpha-3).
- The metadata object also exists for beneficiaries, allowing you to add custom fields to your liking.
- **Remember** When you send money to a beneficiary, the funds will be transferred to the `default_account`. If you want to send the funds to a different beneficiary account, add an account and make that account the default using the **Default an account** endpoint.
- If you are creating a beneficiary with a UK bank account, you **do not** need to provide the account `iban` or `bic_swift`, only the `account_number` and `bank_code`, which will be the sort-code.
- We support creating beneficiaries for all of the G10 currencies because these are the currencies we allow you to **convert-and-send** money to. To add an account, use the `default_account` object which allows you to specify the type of bank code being used etc.
  - If you are unsure of the required account details for a **convert-and-send** transaction, refer to [this helpful guide](https://www.currencycloud.com/support/payment-guides/) and note that we use the **Priority Payment** method.

### The Individual Beneficiary Object

| Attribute                                                                                      | Description |
|:-----------------------------------------------------------------------------------------------|:--|
| `holder_id` <br> _string_, required                                                            | The UUID of the holder of the beneficiary, either an `enduser_id` or the `customer_id` |
| `asset_class` <br> _string_, required                                                          | Used for GBP and EUR beneficiaries. The class of the asset being held in the account, for a normal bank account use `currency` <br> _Allowed Values:_ commodity, currency |
| `asset_type` <br> _string_, required                                                           | Used for GBP and EUR beneficiaries. The type of asset being held in the account, for a normal account this means the currency <br> _Allowed Values:_ gbp, eur, aud, chf, cad, sek, usd, nok, nzd, jpy |
| `bank_country` <br> _string_, optional                                                         | The country in which the beneficiary's bank account is held. |
| `bank_name` <br> _string_, optional                                                            | The name of the bank holding the beneficiary account. |
| `default_account` <br> _object_, required for non-GBP/EUR beneficiaries                        | The account to which funds will be sent |
| `default_account.account_number` <br> _string_, required                                       | The account number of the beneficiary account. |
| `default_account.asset_class` <br> _string_, required                                          | Used for GBP and EUR beneficiaries. The class of the asset being held in the account, for a normal bank account use `currency` <br> _Allowed Values:_ commodity, currency |
| `default_account.asset_type` <br> _string_, required                                           | Used for GBP and EUR beneficiaries. The type of asset being held in the account, for a normal account this means the currency <br> _Allowed Values:_ gbp, eur, aud, chf, cad, sek, usd, nok, nzd, jpy |
| `default_account.bank_code` <br> _string_, required if applicable                              | The bank code of the beneficiary account. Allows us to identify the bank and branch |
| `default_account.bank_code_type` <br> _string_, required if applicable                         | The type of bank code of the beneficiary account. Will be sort-code for a UK account <br> _Allowed Values:_ routing-number, aba, sort-code, zengin-code, bsb |
| `default_account.bank_country` <br> _string_, required                                         | The country in which the beneficiary's bank account is held. |
| `default_account.bank_name` <br> _string_, optional                                            | The name of the bank in which the beneficiary's bank account is held. |
| `default_account.iban` <br> _string_, required for EUR and convert-and-send if applicable      | The IBAN of the beneficiary account |
| `default_account.bic_swift` <br> _string_, required for EUR and convert-and-send if applicable | The SWIFT Branch Identifier Code of the beneficiary account |
| `person` <br> _object_, required (for individuals)                                             | Information about individual |
| `person.name` <br> _string_, required                                                          | Full Name  <br> _Limited to 70 chars_ |
| `person.country_of_residence` <br> _string_, required                                          | Country code <br>  _ISO 8601 format_ |
| `person.date_of_birth` <br> _string_, optional                                                 | Date of birth  <br> _YYYY-MM-DD_ |
| `person.nationality` <br> _string_, optional                                                   | Nationality |
| `person.pep` <br> _boolean_, required (if applicable)                                          | Politically Exposed Person <br>  _Does not need to be inside ""_ |
| `person.pep_type` <br> _string_, optional                                                      | Extent of exposure <br>  _Allowed Values: `direct`, `close-associate`, `former-pep`, `family-member`_ |
| `person.pep_notes` <br> _string_, optional                                                     | Extra detail |
| `person.social_security_number` <br> _string_, optional                                        | SSN |
| `person.telephone` <br> _string_, optional                                                     | Phone number |
| `person.tin` <br> _string_, optional                                                           | Tax Identification Number |
| `person.tin_type` <br> _string_, optional                                                      | Type of Tax Identification Number |
| `person.address` <br> _object_, required                                                       | current address information |
| `person.address.address_city` <br> _string_, required                                          | City that enduser lives in |
| `person.address.address_iso_country`<br> _string_, required                                    | Country code <br>  _ISO 8601 format_ |
| `person.address.address_number` <br> _string_, required                                        | House or building number on street |
| `person.address.address_postal_code` <br> _string_, required                                   | Postal or zip code |
| `person.address.address_refinement` <br> _string_, optional                                    | Extra detail, e.g. house name |
| `person.address.address_region` <br> _string_, required                                        | Region in country |
| `person.address.address_street` <br> _string_, required                                        | Street the beneficiary lives on |
| `person.address_history` <br> _array of objects_, optional                                     | Previous address information. Can include multiple addresses in the array. |
| `person.address_history.address_start_date` <br> _string_, optional                            | Date at which the person began living at the address <br> _YYYY-MM-DD_ |
| `person.address_history.address_end_date` <br> _string_, optional                              | Date at which the person stopped living at the address <br> _YYYY-MM-DD_ |
| `person.address_history.address_city` <br> _string_, optional                                  | City that beneficiary lived in |
| `person.address_history.address_iso_country` <br> _string_, optional                           | Country code <br>  _ISO 8601 format_ |
| `person.address_history.address_number` <br> _string_, optional                                | House or building number on street |
| `person.address_history.address_postal_code` <br> _string_, optional                           | Postal or zip code |
| `person.address_history.address_refinement` <br> _string_, optional                            | Extra detail, e.g. house name |
| `person.address_history.address_region` <br> _string_, optional                                | Region in country |
| `person.address_history.address_street` <br> _string_, optional                                | Street the beneficiary lived on |
| `beneficiary_meta` <br> _object_, optional                                                     | Any extra, custom fields that you want to include about the beneficiary |

## Create a Corporate Beneficiary

> **Example Request - EUR Bank Account**

```shell
--request POST "https://playlive.railsbank.com/v1/customer/beneficiaries"
--header "Content-Type: application/json"
--header "Accept: application/json"
--header "Authorization: API-Key <<yourAPI-Key>>"

{
  "holder_id": "{{enduser_id}}",
  "asset_class": "currency",
  "asset_type": "eur",
  "default_account": {
    "iban": "GB4402005678901234567890",
    "bank_country": "GB",
    "bank_name": "Barclays",
    "bic_swift": "SPSRSKBAXXX",
    "asset_type": "eur",
    "asset_class": "currency"
  },
  "company": {
  	"name": "John's Ice Creams Ltd",
  	"address": {
      "address_refinement": "Apartment 42",
      "address_number": "29",
      "address_street": "Acacia Road",
      "address_city": "London",
      "address_region": "Essex",
      "address_postal_code": "FX20 7XS",
      "address_iso_country": "GB"
    },
    "country_of_residence": [
      "GB"
    ],
    "address_history": [
      {
        "address_refinement": "Apartment 77",
        "address_number": "42",
        "address_street": "Kirschbaumstraße",
        "address_city": "Berlin",
        "address_region": "California",
        "address_postal_code": "12059",
        "address_iso_country": "DE",
        "address_start_date": "2000-09-02",
        "address_end_date": "2002-12-20"
      }
    ],
    "email": "icecream@example.net",
    "telephone": "+44 22 626 2626"
  },
  "beneficiary_meta": {
    "foo": "baa",
    "our_salesforce_reference": "http://na1.salesforce.com/5003000000D8cuI"
  }
}

```
> **Example Response**

```shell
{
    "beneficiary_id": "5b054129-9139-4300-b40a-2e1154e1edb8"
}
```

`POST "https://playlive.railsbank.com/v1/customer/beneficiaries"`

- To create a beneficiary you need to know either an `enduser_id` or your `customer_id`. If the customer is the holder of the beneficiary it signifies that it is the customer who will be sending the money.
- Of course, you must also know the account details of the beneficiary.
- The minimum data required to create an corporate beneficiary is the company's `trading_name` and full address, including the `address_iso_country` (we support iso-alpha-2 and alpha-3).
  - If the `trading_address` is different from `registered_address`, you need to provide both (otherwise, just include registration address)
  - If there is a director who holds 10% or more of the stakes, you must include the director's details. If the director is pep, you must state it.
- The metadata object also exists for beneficiaries, allowing you to add custom fields to your liking.
- **Remember** When you send money to a beneficiary, the funds will be transferred to the `default_account`. If you want to send the funds to a different beneficiary account, add an account and make that account the default using the **Default an account** endpoint.
- If you are creating a beneficiary with a UK bank account, you **do not** need to provide the account `iban` or `bic_swift`, only the `account_number` and `bank_code`, which will be the sort-code.
- We support creating beneficiaries for all of the G10 currencies because these are the currencies we allow you to **convert-and-send** money to. To add an account, use the `default_account` object which allows you to specify the type of bank code being used etc.
  - If you are unsure of the required account details for a **convert-and-send** transaction, refer to [this helpful guide](https://www.currencycloud.com/support/payment-guides/) and note that we use the **Priority Payment** method.

### The Corporate Beneficiary Object
| Attribute                                                                                      | Description |
|:-----------------------------------------------------------------------------------------------|:--|
| `holder_id` <br> _string_, required                                                            | The UUID of the holder of the beneficiary, either an `enduser_id` or the `customer_id` |
| `asset_class` <br> _string_, required                                                          | Used for GBP and EUR beneficiaries. The class of the asset being held in the account, for a normal bank account use `currency` <br> _Allowed Values:_ commodity, currency |
| `asset_type` <br> _string_, required                                                           | Used for GBP and EUR beneficiaries. The type of asset being held in the account, for a normal account this means the currency <br> _Allowed Values:_ gbp, eur, aud, chf, cad, sek, usd, nok, nzd, jpy |
| `bank_country` <br> _string_, optional                                                         | Used for GBP and EUR beneficiaries. The country in which the beneficiary's bank account is held. |
| `bank_name` <br> _string_, optional                                                            | Used for GBP and EUR beneficiaries. The name of the bank holding the beneficiary account. |
| `default_account` <br> _object_, required for non-GBP/EUR beneficiaries                        | The account to which funds will be sent |
| `default_account.account_number` <br> _string_, required                                       | The account number of the beneficiary account |
| `default_account.asset_class` <br> _string_, required                                          | The class of the asset being held in the account, for a normal bank account use `currency` <br> _Allowed Values:_ commodity, currency |
| `default_account.asset_type` <br> _string_, required                                           | The type of asset being held in the account, for a normal account this means the currency <br> _Allowed Values:_ gbp, eur, aud, chf, cad, sek, usd, nok, nzd, jpy |
| `default_account.bank_code` <br> _string_, required  if applicable                             | The bank code of the beneficiary account. Allows us to identify the bank and branch |
| `default_account.bank_code_type` <br> _string_, required if applicable                         | The type of bank code of the beneficiary account. <br> _Allowed Values:_ routing-number, aba, sort-code, zengin-code, bsb |
| `default_account.bank_country` <br> _string_, required                                         | The country in which the beneficiary's bank account is held. |
| `default_account.bank_name` <br> _string_, optional                                            | The name of the bank in which the beneficiary's bank account is held. |
| `default_account.iban` <br> _string_, required for EUR and convert-and-send if applicable      | The IBAN of the beneficiary account |
| `default_account.bic_swift` <br> _string_, required for EUR and convert-and-send if applicable | The SWIFT Branch Identifier Code of the beneficiary account |
| `company` <br> _object_, required (for companies)                                              | Information about the company |
| `company.name` <br> _string_,  required                                                        | The name of the company |
| `company.trading_name` <br> _string_,  required                                                | The full trading name of the company |
| `company.corporate_entity_type` <br> _string_,  optional                                       | The type of company, for instance, a non-profit |
| `company.date_onboarded` <br> _string_, optional                                               | The date the you onboarded the company ad your customer <br> _YYYY-MM-DD_ |
| `company.eu_vat` <br> _string_, optional                                                       | The Value Added Tax number for a European company |
| `company.vat` <br> _string_, optional                                                          | The Value Added Tax number for a British company |
| `company.incorporation_locality` <br> _string_, optional                                       | The country in which the company is incorporated <br>  _ISO 8601 format_ |
| `company.industry` <br> _string_, optional                                                     | The industry the company works within |
| `company.legal_entry_number` <br> _string_, optional                                           | The legal entity number of the company <br> To be changed to `legal_entity_number` |
| `company.regulated_financial_service_firm` <br> _boolean_, optional                            | Is the company regulated? <br> _Allowed Values:_ true, false |
| `company.web_site` <br> _string_, optional                                                     | The website URL of the company |
| `company.type` <br> _string_, optional                                                         | The type of company |
| `company.type_other` <br> _string_, optional                                                   | We have a long list of valid company types. If you receive an error when generating a company due to the `type` being invalid, use this field instead. |
| `company.tin` <br> _string_, optional                                                          | The Tax Identification Number of the company |
| `company.registration_number` <br> _string_, optional                                          | The registration number of the company |
| `company.registration_address` <br> _array of objects_, required                               | The registration address(es) of the company: we use an array incase there are multiple |
| `company.registration_address.address_city` <br> _string_, required                            | City that beneficiary is registered in |
| `company.registration_address.address_iso_country`<br> _string_, required                      | Country code <br>  _ISO 8601 format_ |
| `company.registration_address.address_number` <br> _string_, required                          | House or building number on street |
| `company.registration_address.address_postal_code` <br> _string_, required                     | Postal or zip code |
| `company.registration_address.address_refinement` <br> _string_, optional                      | Extra detail, e.g. house name |
| `company.registration_address.address_region` <br> _string_, required                          | Region in country |
| `company.registration_address.address_street` <br> _string_, required                          | Street the beneficiary is registered on |
| `company.registration_address_history` <br> _array of objects_, optional                       | Previous registration address(es) of the company: we use an array incase there are multiple| |
| `company.registration_address_history.address_start_date` <br> _string_, optional              | Date the company began trading for the address. <br> _YYYY-MM-DD_ |
| `company.registration_address_history.address_end_date` <br> _string_, optional                | Date the company stopped trading for the address. <br> _YYYY-MM-DD_ |
| `company.registration_address_history.address_city` <br> _string_, optional                    | City that beneficiary was registered in |
| `company.registration_address_history.address_iso_country` <br> _string_, optional             | Country code <br>  _ISO 8601 format_ |
| `company.registration_address_history.address_number` <br> _string_, optional                  | House or building number on street |
| `company.registration_address_history.address_postal_code` <br> _string_, optional             | Postal or zip code |
| `company.registration_address_history.address_refinement` <br> _string_, optional              | Extra detail, e.g. house name |
| `company.registration_address_history.address_region` <br> _string_, optional                  | Region in country |
| `company.registration_address_history.address_street` <br> _string_, optional                  | Street the beneficiary was registered on |
| `company.regulatory_licenses` <br> _array of objects_, optional                                | The regulatory licenses the company holds |
| `company.regulatory_licenses.regulatory_license_body` <br> _string_, optional                  | The issuer of the regulatory license the company holds |
| `company.regulatory_licenses.regulatory_license_reference_number` <br> _string_, optional      | The reference number of the regulatory license the company holds |
| `company.regulatory_licenses.regulatory_license_type` <br> _string_, optional                  | The type of regulatory license the company holds <br> _Allowed Values:_ GB-EMI, GB-API, Other |
| `company.listed_on_stock_exchange` <br> _boolean_, optional                                    | Is the company listed on an exchange? |
| `company.stock_exchanges` <br> _array of objects_, optional                                    | The stock_exchange(s) the company is listed on |
| `company.stock_exchanges.stock_exchange_name` <br> _string_, optional                          | The name of the stock_exchange |
| `company.stock_exchanges.stock_exchange_ticker` <br> _string_, optional                        | The ticker of the company on the stock_exchange: an abbreviation used to uniquely identify publicly traded shares of a particular stock on a particular stock market |
| `company.trading_address` <br> _array of objects_, required                                    | The trading address(es) of the company: we use an array incase there are multiple |
| `company.trading_address.address_city` <br> _string_, required                                 | City that beneficiary trades in |
| `company.trading_address.address_iso_country`<br> _string_, required                           | Country code <br>  _ISO 8601 format_ |
| `company.trading_address.address_number` <br> _string_, required                               | House or building number on street |
| `company.trading_address.address_postal_code` <br> _string_, required                          | Postal or zip code |
| `company.trading_address.address_refinement` <br> _string_, optional                           | Extra detail, e.g. house name |
| `company.trading_address.address_region` <br> _string_, required                               | Region in country |
| `company.trading_address.address_street` <br> _string_, required                               | Street the beneficiary trades on |
| `company.trading_address_history` <br> _array of objects_, optional                            | Previous registration address(es) of the company: we use an array incase there are multiple |
| `company.trading_address_history.address_start_date` <br> _string_, optional                   | Date the company began trading for the address. <br> _YYYY-MM-DD_ |
| `company.trading_address_history.address_end_date` <br> _string_, optional                     | Date the company stopped trading for the address. <br> _YYYY-MM-DD_ |
| `company.trading_address_history.address_city` <br> _string_, optional                         | City that enduser was trading in |
| `company.trading_address_history.address_iso_country` <br> _string_, optional                  | Country code <br>  _ISO 8601 format_ |
| `company.trading_address_history.address_number` <br> _string_, optional                       | House or building number on street |
| `company.trading_address_history.address_postal_code` <br> _string_, optional                  | Postal or zip code |
| `company.trading_address_history.address_refinement` <br> _string_, optional                   | Extra detail, e.g. house name |
| `company.trading_address_history.address_region` <br> _string_, optional                       | Region in country |
| `company.trading_address_history.address_street` <br> _string_, optional                       | Street the beneficiary was trading on |
| `company.directors` <br> _array of objects_, required if director exists                       | Information regarding the directors of the company |
| `company.directors.company` <br> _object_, required if director is a company                   | Information about a different company who is a director of the enduser (company) <br> The fields are the same as for a corporate enduser |
| `company.directors.person` <br> _object_, required if director is a person                     | Information about a person who is a director of the company <br> The fields are the same as for an individual enduser (detailed above) |
| `company.directors.date_appointed` <br> _string_, required                                     | Date the director was appointed <br> _YYYY-MM-DD_ |
| `company.directors.is_also_ubo` <br> _boolean_, required                                       | Is the director an Ultimate Beneficiary Owner <br> _Allowed Values:_ true, false |
| `company.directors.job_title` <br> _string_, optional                                          | The job title of the director |
| `company.ultimate_beneficial_owners` <br> _array of objects_, optional                         | Information regarding the UBOs of the company |
| `company.ultimate_beneficial_owners.company` <br> _object_, optional                           | Information about a different company who is a UBO of the enduser (company) <br> The fields are the same as for a corporate enduser |
| `company.ultimate_beneficial_owners.person` <br> _object_, optional                            | Information about a person who is a UBO of the company <br> The fields are the same as for an individual enduser (detailed above) |
| `company.ultimate_beneficial_owners.percentage_holding` <br> _number_, optional                | The percentage of the company that the UBO owns |
| `enduser_meta` <br> _object_, optional                                                         | Any extra, custom fields that you want to include about the enduser |

## Fetch a Beneficiary
> **Example Request**

```shell
  --request GET "https://playlive.railsbank.com/v1/customer/beneficiaries/{{BENEFICIARY_ID}}"
  --header "Content-Type: application/json"
  --header "Accept: application/json"
  --header "Authorization: API-Key <<yourapikey>>"
```

> **Example Response**

```shell
{
    "iban": "GB69TRWI231470134598XX",
    "last_modified_at": "2019-06-03T14:24:04.347Z",
    "beneficiary_status": "beneficiary-status-ok",
    "uk_sort_code": "2314XX",
    "company": {
        "name": "Railsbank Technology Limited",
        "registration_address": {
            "address_refinement": "Transferwise",
            "address_number": "56",
            "address_street": "Shoreditch High Street",
            "address_city": "London",
            "address_postal_code": "E1 6JJ",
            "address_iso_country": "GB"
        }
    },
    "screening_monitored_search": false,
    "holder_id": "5b9f7e0a-634b-441f-a755-e42c0b214c69",
    "beneficiary_holder": {
        "customer_id": "5b9f7e0a-634b-441f-a755-e42c0b214c69",
        "company": {
            "name": "Railsbank"
        }
    },
    "bic_swift": "TRWIGB22XXX",
    "created_at": "2019-06-03T14:24:04.347Z",
    "beneficiary_id": "5cf52d84-04ce-4de6-8f35-bc11295afd3b",
    "asset_type": "gbp",
    "uk_account_number": "134598XX",
    "asset_class": "currency",
    "default_account": {
        "iban": "GB69TRWI231470134598XX",
        "last_modified_at": "2019-06-03T14:24:04.347Z",
        "account_number": "134598XX",
        "bank_code": "2314XX",
        "bic_swift": "TRWIGB22XXX",
        "created_at": "2019-06-03T14:24:04.347Z",
        "asset_type": "gbp",
        "asset_class": "currency",
        "account_id": "5cf52d84-0faa-4104-bf6c-395896f3b04b",
        "bank_code_type": "sort-code"
    }
}
```

`GET "https://playlive.railsbank.com/v1/customer/beneficiaries/{{BENEFICIARY_ID}}"`

- Fetching a beneficiary is much like fetching anything else in the API.
- We use a data-enhancing service to add extra detail to accounts: for instance, you will see that GBP accounts have an `iban` which we have determined using the data you provided when creating the beneficiary.
- If extra data is required for the beneficiary to be created successfully, you will see it in the `missing_data` object.
- We recommend using the `/wait` parameter to get the API to respond with the beneficiary in a REST state - to wait until the beneficiary is not in `status-pending` due to being processed by the API.


### New Fields

| Attribute                              | Description                         |
|:---------------------------------------|:------------------------------------|
| `beneficiary_holder` <br> _object_     | Information about the holder of the beneficiary |
| `beneficiary_id` <br> _string_         | The UUID of the beneficiary         |
| `beneficiary_status` <br> _string_     | The state of the beneficiary        |
| `created_at` <br> _string_             | The timestamp of when the beneficiary was created |
| `entity_type` <br> _string_            | The type of beneficiary <br> _Allowed Values:_ person, company |
| `last_modified_at` <br> _string_       | The timestamp of when the beneficiary was last modified |
| `missing_data` <br> _array of strings_ | Information regarding fields required for the beneficiary to be usable |


### Beneficiary Statuses

| Message                           | Description                              |
|:----------------------------------|:-----------------------------------------|
| `beneficiary-status-missing-data` | Extra data is required for the beneficiary to be created, for instance, the `uk_sort_code` of the beneficiary account. |
| `beneficiary-status-pending`      | The beneficiary is being processed. To avoid fetching an beneficiary in this state, use our `/wait` parameter. |
| `beneficiary-status-quarantine`   | The beneficiary has broken a customer firewall rule and fallen into the customer quarantine queue. You will receive a `type: entity-fw-quarantine` webhook and a `type: beneficiary-firewall-finished` webhook. |
| `beneficiary-status-ok`           | The beneficiary is ready to receive transactions. You will receive a `type: entity-ready-to-use` webhook and a `type: beneficiary-firewall-finished` webhook if you have set and firewall rules up. |
| `beneficiary-status-declined`     | The beneficiary has been declined by our system. This is usually because the beneficiary has broken a partner firewall rule and been rejected by our compliance team, for instance, if they are from a country we don't deal with, like North Korea. |

## Fetch Multiple Beneficiaries
> **Example Request**

```shell
--request GET "https://playlive.railsbank.com/v1/customer/beneficiaries?holder_id={{ENDUSER_ID}}"
--header "Content-Type: application/json"
--header "Accept: application/json"
--header "Authorization: API-Key <<yourapikey>>"
```
. **Example Response**

```shell
[
    {
        "iban": "GB91SRLG04005205393196",
        "last_modified_at": "2018-10-01T12:39:31.713Z",
        "beneficiary_status": "beneficiary-status-ok",
        "uk_sort_code": "040052",
        "screening_monitored_search": false,
        "holder_id": "5bb20baa-4f4d-4914-b16b-d4346bda8fea",
        "beneficiary_holder": {
            "enduser_id": "5bb20baa-4f4d-4914-b16b-d4346bda8fea",
            "person": {
                "name": "Sarah Jane Smith"
            }
        },
        "person": {
            "country_of_residence": [
                "GB"
            ],
            "address_history": [
                {
                    "address_start_date": "2000-09-02",
                    "address_postal_code": "12059",
                    "address_iso_country": "FR",
                    "address_region": "Lile",
                    "address_refinement": "Apartment 77",
                    "address_street": "Kirschbaumstraße",
                    "address_end_date": "2002-12-20",
                    "address_city": "Paris",
                    "address_number": "42"
                }
            ],
            "date_onboarded": "2015-11-21",
            "email": "blackhole@railsbank.com",
            "name": "TEST-oct1-18",
            "address": {
                "address_refinement": "Apartment 8",
                "address_number": "6",
                "address_street": "Harold Road",
                "address_city": "London",
                "address_postal_code": "SE19 3PU",
                "address_region": "London",
                "address_iso_country": "FR"
            },
            "telephone": "+44 22 626 2626",
            "date_of_birth": "1970-11-05",
            "nationality": [
                "British"
            ]
        },
        "bic_swift": "SRLGGB2LXXX",
        "created_at": "2018-10-01T12:39:31.713Z",
        "beneficiary_meta": {
            "foo": "baa",
            "our_salesforce_reference": "http://na1.salesforce.com/5003000000D8cuI"
        },
        "beneficiary_id": "5bb21583-94a6-451b-af62-9f7187dc8723",
        "asset_type": "gbp",
        "uk_account_number": "05393196",
        "asset_class": "currency",
        "default_account": {
            "iban": "GB91SRLG04005205393196",
            "last_modified_at": "2018-10-01T12:39:31.713Z",
            "account_number": "05393196",
            "bank_code": "040052",
            "bic_swift": "SRLGGB2LXXX",
            "created_at": "2018-10-01T12:39:31.713Z",
            "asset_type": "gbp",
            "asset_class": "currency",
            "account_id": "5bb21583-b094-4ee9-8573-fdfa4e113a2b",
            "bank_code_type": "sort-code"
        }
    },
    {
        "iban": "GB91SRLG04005205393196",
        "last_modified_at": "2018-10-01T12:44:18.354Z",
        "beneficiary_status": "beneficiary-status-ok",
        "uk_sort_code": "040052",
        "screening_monitored_search": false,
        "holder_id": "5bb20baa-4f4d-4914-b16b-d4346bda8fea",
        "beneficiary_holder": {
            "enduser_id": "5bb20baa-4f4d-4914-b16b-d4346bda8fea",
            "person": {
                "name": "Sarah Jane Smith"
            }
        },
        "person": {
            "country_of_residence": [
                "GB"
            ],
            "address_history": [
                {
                    "address_start_date": "2000-09-02",
                    "address_postal_code": "12059",
                    "address_iso_country": "GB",
                    "address_region": "Lile",
                    "address_refinement": "Apartment 77",
                    "address_street": "Kirschbaumstraße",
                    "address_end_date": "2002-12-20",
                    "address_city": "Paris",
                    "address_number": "42"
                }
            ],
            "date_onboarded": "2015-11-21",
            "email": "blackhole@railsbank.com",
            "name": "TEST-oct1-18",
            "address": {
                "address_refinement": "Apartment 8",
                "address_number": "6",
                "address_street": "Harold Road",
                "address_city": "London",
                "address_postal_code": "SE19 3PU",
                "address_region": "London",
                "address_iso_country": "GB"
            },
            "telephone": "+44 22 626 2626",
            "date_of_birth": "1970-11-05",
            "nationality": [
                "British"
            ]
        },
        "bic_swift": "SRLGGB2LXXX",
        "created_at": "2018-10-01T12:44:18.354Z",
        "beneficiary_meta": {
            "foo": "baa",
            "our_salesforce_reference": "http://na1.salesforce.com/5003000000D8cuI"
        },
        "beneficiary_id": "5bb216a2-6bd7-4c83-aef3-62b988089a3c",
        "asset_type": "gbp",
        "uk_account_number": "05393196",
        "asset_class": "currency",
        "default_account": {
            "iban": "GB91SRLG04005205393196",
            "last_modified_at": "2018-10-01T12:44:18.354Z",
            "account_number": "05393196",
            "bank_code": "040052",
            "bic_swift": "SRLGGB2LXXX",
            "created_at": "2018-10-01T12:44:18.354Z",
            "asset_type": "gbp",
            "asset_class": "currency",
            "account_id": "5bb216a2-935a-46e9-a246-5568ca883774",
            "bank_code_type": "sort-code"
        }
    }
]
```
`GET "https://playlive.railsbank.com/v1/customer/beneficiaries"`

`GET "https://playlive.railsbank.com/v1/customer/beneficiaries?holder_id={{ENDUSER_ID}}"`

`GET "https://playlive.railsbank.com/v1/customer/beneficiaries?holder_id={{ENDUSER_ID}}&items_per_page=25"`

- You can fetch multiple beneficiaries.
  - You can filter the response from this endpoint by `holder_id`, allowing you to retrieve the beneficiaries held by you as the customer or by a particular enduser.
  - Use either the date-based parameters or the id-based parameters, not both.
  - When using parameters, don't place the strings within quotes.
  - To add a parameter simply use the `&` symbol.
- The response will be an array of beneficiaries containing all the information about each beneficiary.

### Parameters

| Parameter                        | Description                               |
|:---------------------------------|:------------------------------------------|
| `holder_id` <br> _string_        | The holder of the beneficiary: an enduser or a customer |
| `starting_at_date` <br> _string_ | Any beneficiary created before this date will not be shown in the array <br> _YYYY-MM-DD_ |
| `items_per_page` <br> _integer_  | The number of items returned in each page. A hyperlink to the next page will be found in the response headers |
| `created_at` <br> _string_       | The date that the list of beneficiaries will begin at: any beneficiary created before this date will not be returned. <br> _YYYY-MM-DD_ |
| `last_seen_id` <br> _string_     | The beneficiaries returned will have all been created after the beneficiary whose UUID is the `last_seen_id` |


## Add an Beneficiary Account
> **Example Request - EUR Bank Account**

```shell

--request POST "https://playlive.railsbank.com/v1/customer/beneficiaries/{{BENEFICIARY_ID}}/accounts"
--header "Content-Type: application/json"
--header "Accept: application/json"
--header "Authorization: API-Key <<yourapikey>>"

{
  "iban": "GB44020056789012345678XX",
  "account_number": "45564658",
  "bank_country": "GB",
  "bank_code": "040004",
  "bank_name": "Monzo",
  "bic_swift": "SPSRSKBAXXX",
  "asset_type": "gbp",
  "asset_class": "currency",
  "bank_code_type": "sort-code"
}
```

> **Example Response**

```shell
{
  "account_id": "bb8b2428-f94c-41df-8e82-a895ab4d6ac8"
}
```

`POST "https://playlive.railsbank.com/v1/customer/beneficiaries/{{BENEFICIARY_ID}}/accounts"`

- We understand that often, your beneficiaries will have multiple bank accounts, perhaps even in multiple currencies.
Use this endpoint to add an account to a beneficiary.
- You don't need to specify that this will be the default account – if it will be – because there is a separate endpoint for that.
  - If you are unsure of the required account details for a **convert-and-send** transaction, refer to [this helpful guide](https://www.currencycloud.com/support/payment-guides/) and note that we use the **Priority Payment** method.

### The Account Object

| Attribute                                                                      | Description |
|:-------------------------------------------------------------------------------|:--|
| `account_number` <br> _string_, required                                       | The account number of the beneficiary account |
| `asset_class` <br> _string_, required                                          | The class of the asset being held in the account, for a normal bank account use `currency` <br> _Allowed Values:_ commodity, currency |
| `asset_type` <br> _string_, required                                           | The type of asset being held in the account, for a normal account this means the currency <br> _Allowed Values:_ gbp, eur, aud, chf, cad, sek, usd, nok, nzd, jpy |
| `bank_code` <br> _string_, required if applicable                              | The bank code of the beneficiary account. Allows us to identify the bank and branch |
| `bank_code_type` <br> _string_, required if applicable                         | The type of bank code of the beneficiary account. <br> _Allowed Values:_ routing-number, aba, sort-code, zengin-code, bsb |
| `bank_country` <br> _string_, required                                         | The country in which the beneficiary's bank account is held. |
| `bank_name` <br> _string_, optional                                            | The name of the bank in which the beneficiary's bank account is held. |
| `iban` <br> _string_, required for EUR and convert-and-send if applicable      | The IBAN of the beneficiary account |
| `bic_swift` <br> _string_, required for EUR and convert-and-send if applicable | The SWIFT Branch Identifier Code of the beneficiary account |


## Fetch Beneficiary Accounts
> **Example Request - Multiple Accounts**

```shell
  --request GET "https://playlive.railsbank.com/v1/customer/beneficiaries/{{BENEFICIARY_ID}}/accounts"
  --header "Content-Type: application/json"
  --header "Accept: application/json"
  --header "Authorization: API-Key <<yourapikey>>"
```
> **Example Response**

```shell
[
  {
    "account_id": "8763b5df-a61c-4f77-9724-41ca9cde3654"
  },
  {
    "account_id": "bb8b2428-f94c-41df-8e82-a895ab4d6ac8"
  },
  {
    "account_id": "8763b5df-a61c-4f77-9724-41ca9cde3654"
  },
  {
    "account_id": "c91b339e-57d7-41ea-a805-8966ce8fe4ed"
  }
]
```
> **Example Request - Individual Account**

```shell
  --request GET "https://playlive.railsbank.com/v1/customer/beneficiaries/{{BENEFICIARY_ID}}/accounts/{{ACCOUNT_ID}}"
  --header "Content-Type: application/json"
  --header "Accept: application/json"
  --header "Authorization: API-Key <<yourapikey>>"
```
> **Example Response**

```shell
{
  "iban": "GB44020056789012345678XX",
  "last_modified_at": "1970-01-01T00:00:00.000Z",
  "account_number": "455646XX",
  "bank_country": "GB",
  "bank_name": "Barclays",
  "bic_swift": "SPSRSKBAXXX",
  "created_at": "2000-01-01T00:00:00.000Z",
  "beneficiary_id": "bb8b2428-f94c-41df-8e82-a895ab4d6ac8",
  "asset_type": "eur",
  "asset_class": "currency",
  "account_id": "8763b5df-a61c-4f77-9724-41ca9cde3654"
}
```

`GET "https://playlive.railsbank.com/v1/customer/beneficiaries/{{BENEFICIARY_ID}}/accounts"`

`GET "https://playlive.railsbank.com/v1/customer/beneficiaries/{{BENEFICIARY_ID}}/accounts/{{ACCOUNT_ID}}"`

- With the Railsbank Api you can fetch all the accounts attached to a beneficiary or just a particular account.
- To fetch an individual account you need the `account_id`
- When you fetch multiple accounts they will be returned as an array of accounts. To access their information, you will have to iterate over them and call each individually.

## Update a Beneficiary
> **Example Request**

```shell
--request PUT "https://playlive.railsbank.com/v1/customer/beneficiaries/{{BENEFICIARY_ID}}"
--header "Content-Type: application/json"
--header "Accept: application/json"
--header "Authorization: API-Key <<yourapikey>>"

{
  "person": {
    "name": "James Smith"
  }
}

```
> **Example Response**

```shell
{
  "beneficiary_id": "bb8b2428-f94c-41df-8e82-a895ab4d6ac8"
}
```

`PUT "https://playlive.railsbank.com/v1/customer/beneficiaries/{{BENEFICIARY_ID}}"`

- This endpoint allows you to update a beneficiary.
- You can update:
  - Any field in the `person` object if the beneficiary is a person, or the `company` object if they are a company.
  - The `default_account` details. This means updating the account details of the account which is the beneficiary account rather hat changing which particular beneficiary account is the default account.
  - The `beneficiary_meta` object.
- In the payload, you only need to supply the field you want to change. If that field is within an object, you must include the parent object too, as in the example.
- You cannot change the type of beneficiary: individual beneficiaries cannot become corporate beneficiaries and vise-versa.

## Update a Beneficiary Account

> **Example Request**

```shell
--request PUT "https://playlive.railsbank.com/v1/customer/beneficiaries/{{BENEFICIARY_ID}}/accounts/{{ACCOUNT_ID}}"
--header "Content-Type: application/json"
--header "Accept: application/json"
--header "Authorization: API-Key <<yourapikey>>"
{
  "bank_code_type": "sort-code",
  "bank_country": "GB",
  "bank_code": "040004",
  "account_number": "09878768",
  "bank_name": "HSBC"
}
```
> **Example Response**

```shell
{
  "beneficiary_id": "bb8b2428-f94c-41df-8e82-a895ab4d6ac8"
}
```

`PUT "https://playlive.railsbank.com/v1/customer/beneficiaries/{{BENEFICIARY_ID}}/accounts/{{ACCOUNT_ID}}"`

- This endpoint allows you to update the account information of a particular beneficiary account, specified by the `account_id` in the endpoint url.
- All the fields can be updated except for the `asset_type`: an account cannot change from holding currency to holding commodity.

### The Account Object

| Attribute                                                                      | Description |
|:-------------------------------------------------------------------------------|:--|
| `account_number` <br> _string_, required                                       | The account number of the beneficiary account |
| `asset_class` <br> _string_, required                                          | The class of the asset being held in the account, for a normal bank account use `currency` <br> _Allowed Values:_ commodity, currency |
| `asset_type` <br> _string_, required                                           | The type of asset being held in the account, for a normal account this means the currency <br> _Allowed Values:_ gbp, eur, aud, chf, cad, sek, usd, nok, nzd, jpy |
| `bank_code` <br> _string_, required if applicable                              | The bank code of the beneficiary account. Allows us to identify the bank and branch |
| `bank_code_type` <br> _string_, required if applicable                         | The type of bank code of the beneficiary account. <br> _Allowed Values:_ routing-number, aba, sort-code, zengin-code, bsb |
| `bank_country` <br> _string_, required                                         | The country in which the beneficiary's bank account is held. |
| `bank_name` <br> _string_, optional                                            | The name of the bank in which the beneficiary's bank account is held. |
| `iban` <br> _string_, required for EUR and convert-and-send if applicable      | The IBAN of the beneficiary account |
| `bic_swift` <br> _string_, required for EUR and convert-and-send if applicable | The SWIFT Branch Identifier Code of the beneficiary account |

## Change Default Account
> **Example Request**

```shell
--request PUT "https://playlive.railsbank.com/v1/customer/beneficiaries/{{BENEFICIARY_ID}}/accounts/{{ACCOUNT_ID}}/make-default"
--header "Content-Type: application/json"
--header "Accept: application/json"
--header "Authorization: API-Key <<yourapikey>>"
```
> **Example Response**

```shell
{
  "beneficiary_id": "bb8b2428-f94c-41df-8e82-a895ab4d6ac8"
}
```

`PUT "https://playlive.railsbank.com/v1/customer/beneficiaries/{{BENEFICIARY_ID}}/accounts/{{ACCOUNT_ID}}/make-default"`

- This simple endpoint allows you to change the account to which funds are transferred to when money is sent to the beneficiary.
- There is no payload.
