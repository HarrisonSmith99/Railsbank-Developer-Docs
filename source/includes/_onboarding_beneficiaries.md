# Onboarding Beneficiaries
- A **beneficiary** is an entity who *holds* an account to which money is sent to from a Railsbank ledger.
- A beneficiary can either be: a **person** or a **company**
- Beneficiaries are be held by either an **enduser** or the **customer**.
- Once the beneiciary is made, the holder of the beneficiary can send money from their ledgers to one of their beneficiaries.
- Just as is the case in the real world, a beneficiary can have multiple accounts - for instance a GBP account and a EUR account. The account to which you want to send money to must be set as the `default` account.
- It is not possible to delete a beneficiary. please be mindful when creating them.
- Rules similar to those that apply to endusers apply to creating beneficiaries: beneficiaries can either bet individuals - `person` - or corporates - `company`. Different payloads are required for each

## Create an Individual Beneficiary
> **Example Request GBP Bank Account**

```shell
--request POST "https://playlive.railsbank.com/v1/customer/beneficiaries"
--header "Content-Type: application/json"
--header "Accept: application/json"
--header "Authorization: API-Key <<yourAPI-Key>>"

{
  "uk_account_number": "556311XX",
  "uk_sort_code": "0400XX",
  "holder_id": "{{enduser_id}}",
  "asset_class": "currency",
  "asset_type": "gbp",
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
- When creating a beneficiary, the account details you use will be those that fall under the beneficiary's `default_account` automatically. If you want to add an account for the beneficiary you can easily do so using the **Add account** endpoint below.
- **Remember** When you send money to a beneficiary, the funds will be transferred to the `default_account`. If you want to send the funds to a different beneficiary account, make that account the default using the **Default an account** endpoint.
- If you are creating a beneficiary with a UK bank account, you **do not** need to provide the account `iban` or `bic_swift`, only the `uk_account_number` and `uk_sort_code`.
- We support creating beneficiaries for all of the G10 currencies because these are the currencies we allow you to **convert-and-send** money to. To add a non-GBP or EUR account, use the `default_account` object which allows you to specify the type of bank code being used etc.
  - If you are unsure of the required account details for a **convert-and-send** transaction, refer to [this helpful guide](https://www.currencycloud.com/support/payment-guides/) and note that we use the **Priority Payment** method.

### The Individual Beneficiary Object

| Attribute                                                                              | Description |
|:---------------------------------------------------------------------------------------|:--|
| `holder_id` <br> _string_, required                                                    | The UUID of the holder of the beneficiary, either an `enduser_id` or the `customer_id` |
| `asset_class` <br> _string_, required                                                  | Used for GBP and EUR beneficiaries. The class of the asset being held in the account, for a normal bank account use `currency` <br> _Allowed Values:_ commodity, currency |
| `asset_type` <br> _string_, required                                                   | Used for GBP and EUR beneficiaries. The type of asset being held in the account, for a normal account this means the currency <br> _Allowed Values:_ gbp, eur, aud, chf, cad, sek, usd, nok, nzd, jpy |
| `bank_country` <br> _string_, optional                                                 | Used for GBP and EUR beneficiaries. The country in which the beneficiary's bank account is held. |
| `bank_name` <br> _string_, optional                                                    | Used for GBP and EUR beneficiaries. The name of the bank holding the beneficiary account. |
| `uk_account_number` <br> _string_, required for GBP beneficiaries                      | The account number of the beneficiary account <br> _Eight integers_ |
| `uk_sort_code` <br> _string_, required for GBP beneficiaries                           | The sort code of the beneficiary account. Identifies the bank and the branch <br> _Six integers_ |
| `iban` <br> _string_, required for EUR beneficiaries                                   | The IBAN of the beneficiary account |
| `bic_swift` <br> _string_, required for EUR beneficiaries                              | The SWIFT Branch Identifier Code of the beneficiary account |
| `default_account` <br> _object_, required for non-GBP/EUR beneficiaries                | The account to which funds will be sent |
| `default_account.account_number` <br> _string_, required                               | The account number of the beneficiary account |
| `default_account.asset_class` <br> _string_, required                                  | Used for GBP and EUR beneficiaries. The class of the asset being held in the account, for a normal bank account use `currency` <br> _Allowed Values:_ commodity, currency |
| `default_account.asset_type` <br> _string_, required                                   | Used for GBP and EUR beneficiaries. The type of asset being held in the account, for a normal account this means the currency <br> _Allowed Values:_ gbp, eur, aud, chf, cad, sek, usd, nok, nzd, jpy |
| `default_account.bank_code` <br> _string_, required                                    | The bank code of the beneficiary account. Allows us to identify the bank and branch |
| `default_account.bank_code_type` <br> _string_, required                               | The type of bank code of the beneficiary account. <br> _Allowed Values:_ routing-number, aba, sort-code, zengin-code, bsb |
| `default_account.bank_country` <br> _string_, required                                 | The country in which the beneficiary's bank account is held. |
| `default_account.bank_name` <br> _string_, optional                                    | The name of the bank in which the beneficiary's bank account is held. |
| `default_account.iban` <br> _string_, required for convert-and-send if applicable      | The IBAN of the beneficiary account |
| `default_account.bic_swift` <br> _string_, required for convert-and-send if applicable | The SWIFT Branch Identifier Code of the beneficiary account |
| `person` <br> _object_, required (for individuals)                                     | Information about individual |
| `person.name` <br> _string_, required                                                  | Full Name  <br> _Limited to 70 chars_ |
| `person.country_of_residence` <br> _string_, required                                  | Country code <br>  _ISO 8601 format_ |
| `person.date_of_birth` <br> _string_, optional                                         | Date of birth  <br> _YYYY-MM-DD_ |
| `person.nationality` <br> _string_, optional                                           | Nationality |
| `person.pep` <br> _boolean_, required (if applicable)                                  | Politically Exposed Person <br>  _Does not need to be inside ""_ |
| `person.pep_type` <br> _string_, optional                                              | Extent of exposure <br>  _Allowed Values: `direct`, `close-associate`, `former-pep`, `family-member`_ |
| `person.pep_notes` <br> _string_, optional                                             | Extra detail |
| `person.social_security_number` <br> _string_, optional                                | SSN |
| `person.telephone` <br> _string_, optional                                             | Phone number |
| `person.tin` <br> _string_, optional                                                   | Tax Identification Number |
| `person.tin_type` <br> _string_, optional                                              | Type of Tax Identification Number |
| `person.address` <br> _object_, required                                               | current address information |
| `person.address.address_city` <br> _string_, required                                  | City that enduser lives in |
| `person.address.address_iso_country`<br> _string_, required                            | Country code <br>  _ISO 8601 format_ |
| `person.address.address_number` <br> _string_, required                                | House or building number on street |
| `person.address.address_postal_code` <br> _string_, required                           | Postal or zip code |
| `person.address.address_refinement` <br> _string_, optional                            | Extra detail, e.g. house name |
| `person.address.address_region` <br> _string_, required                                | Region in country |
| `person.address.address_street` <br> _string_, required                                | Street the beneficiary lives on |
| `person.address_history` <br> _array of objects_, optional                             | Previous address information. Can include multiple addresses in the array. |
| `person.address_history.address_start_date` <br> _string_, optional                    | Date at which the person began living at the address <br> _YYYY-MM-DD_ |
| `person.address_history.address_end_date` <br> _string_, optional                      | Date at which the person stopped living at the address <br> _YYYY-MM-DD_ |
| `person.address_history.address_city` <br> _string_, optional                          | City that beneficiary lived in |
| `person.address_history.address_iso_country` <br> _string_, optional                   | Country code <br>  _ISO 8601 format_ |
| `person.address_history.address_number` <br> _string_, optional                        | House or building number on street |
| `person.address_history.address_postal_code` <br> _string_, optional                   | Postal or zip code |
| `person.address_history.address_refinement` <br> _string_, optional                    | Extra detail, e.g. house name |
| `person.address_history.address_region` <br> _string_, optional                        | Region in country |
| `person.address_history.address_street` <br> _string_, optional                        | Street the beneficiary lived on |
| `beneficiary_meta` <br> _object_, optional                                             | Any extra, custom fields that you want to include about the beneficiary |

## Create a Corporate Beneficiary

> **Example Request EUR Bank Account**

```shell
--request POST "https://playlive.railsbank.com/v1/customer/beneficiaries"
--header "Content-Type: application/json"
--header "Accept: application/json"
--header "Authorization: API-Key <<yourAPI-Key>>"

{
  "iban": "GB48BARC20172242909977",
  "bic_swift": "BARCGB22",
  "holder_id": "{{enduser_id}}",
  "asset_class": "currency",
  "asset_type": "eur",
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
- When creating a beneficiary, the account details you use will be those that fall under the beneficiary's `default_account` automatically. If you want to add an account for the beneficiary you can easily do so using the **Add account** endpoint below.
- **Remember** When you send money to a beneficiary, the funds will be transferred to the `default_account`. If you want to send the funds to a different beneficiary account, make that account the default using the **Default an account** endpoint.
- If you are creating a beneficiary with a UK bank account, you **do not** need to provide the account `iban` or `bic_swift`, only the `uk_account_number` and `uk_sort_code`.
- We support creating beneficiaries for all of the G10 currencies because these are the currencies we allow you to **convert-and-send** money to. To add a non-GBP or EUR account, use the `default_account` object which allows you to specify the type of bank code being used etc.
  - If you are unsure of the required account details for a **convert-and-send** transaction, refer to [this helpful guide](https://www.currencycloud.com/support/payment-guides/) and note that we use the **Priority Payment** method.

### The Corporate Beneficiary Object
| Attribute                                                                                 | Description |
|:------------------------------------------------------------------------------------------|:--|
| `holder_id` <br> _string_, required                                                       | The UUID of the holder of the beneficiary, either an `enduser_id` or the `customer_id` |
| `asset_class` <br> _string_, required                                                     | Used for GBP and EUR beneficiaries. The class of the asset being held in the account, for a normal bank account use `currency` <br> _Allowed Values:_ commodity, currency |
| `asset_type` <br> _string_, required                                                      | Used for GBP and EUR beneficiaries. The type of asset being held in the account, for a normal account this means the currency <br> _Allowed Values:_ gbp, eur, aud, chf, cad, sek, usd, nok, nzd, jpy |
| `bank_country` <br> _string_, optional                                                    | Used for GBP and EUR beneficiaries. The country in which the beneficiary's bank account is held. |
| `bank_name` <br> _string_, optional                                                       | Used for GBP and EUR beneficiaries. The name of the bank holding the beneficiary account. |
| `uk_account_number` <br> _string_, required for GBP beneficiaries                         | The account number of the beneficiary account <br> _Eight integers_ |
| `uk_sort_code` <br> _string_, required for GBP beneficiaries                              | The sort code of the beneficiary account. Identifies the bank and the branch <br> _Six integers_ |
| `iban` <br> _string_, required for EUR beneficiaries                                      | The IBAN of the beneficiary account |
| `bic_swift` <br> _string_, required for EUR beneficiaries                                 | The SWIFT Branch Identifier Code of the beneficiary account |
| `default_account` <br> _object_, required for non-GBP/EUR beneficiaries                   | The account to which funds will be sent |
| `default_account.account_number` <br> _string_, required                                  | The account number of the beneficiary account |
| `default_account.asset_class` <br> _string_, required                                     | Used for GBP and EUR beneficiaries. The class of the asset being held in the account, for a normal bank account use `currency` <br> _Allowed Values:_ commodity, currency |
| `default_account.asset_type` <br> _string_, required                                      | Used for GBP and EUR beneficiaries. The type of asset being held in the account, for a normal account this means the currency <br> _Allowed Values:_ gbp, eur, aud, chf, cad, sek, usd, nok, nzd, jpy |
| `default_account.bank_code` <br> _string_, required                                       | The bank code of the beneficiary account. Allows us to identify the bank and branch |
| `default_account.bank_code_type` <br> _string_, required                                  | The type of bank code of the beneficiary account. <br> _Allowed Values:_ routing-number, aba, sort-code, zengin-code, bsb |
| `default_account.bank_country` <br> _string_, required                                    | The country in which the beneficiary's bank account is held. |
| `default_account.bank_name` <br> _string_, optional                                       | The name of the bank in which the beneficiary's bank account is held. |
| `default_account.iban` <br> _string_, required for convert-and-send if applicable         | The IBAN of the beneficiary account |
| `default_account.bic_swift` <br> _string_, required for convert-and-send if applicable    | The SWIFT Branch Identifier Code of the beneficiary account |
| `company` <br> _object_, required (for companies)                                         | Information about the company |
| `company.name` <br> _string_,  required                                                   | The name of the company |
| `company.trading_name` <br> _string_,  required                                           | The full trading name of the company |
| `company.corporate_entity_type` <br> _string_,  optional                                  | The type of company, for instance, a non-profit |
| `company.date_onboarded` <br> _string_, optional                                          | The date the you onboarded the company ad your customer <br> _YYYY-MM-DD_ |
| `company.eu_vat` <br> _string_, optional                                                  | The Value Added Tax number for a European company |
| `company.vat` <br> _string_, optional                                                     | The Value Added Tax number for a British company |
| `company.incorporation_locality` <br> _string_, optional                                  | The country in which the company is incorporated <br>  _ISO 8601 format_ |
| `company.industry` <br> _string_, optional                                                | The industry the company works within |
| `company.legal_entry_number` <br> _string_, optional                                      | The legal entity number of the company <br> To be changed to `legal_entity_number` |
| `company.regulated_financial_service_firm` <br> _boolean_, optional                       | Is the company regulated? <br> _Allowed Values:_ true, false |
| `company.web_site` <br> _string_, optional                                                | The website URL of the company |
| `company.type` <br> _string_, optional                                                    | The type of company |
| `company.type_other` <br> _string_, optional                                              | We have a long list of valid company types. If you receive an error when generating a company due to the `type` being invalid, use this field instead. |
| `company.tin` <br> _string_, optional                                                     | The Tax Identification Number of the company |
| `company.registration_number` <br> _string_, optional                                     | The registration number of the company |
| `company.registration_address` <br> _array of objects_, required                          | The registration address(es) of the company: we use an array incase there are multiple |
| `company.registration_address.address_city` <br> _string_, required                       | City that beneficiary is registered in |
| `company.registration_address.address_iso_country`<br> _string_, required                 | Country code <br>  _ISO 8601 format_ |
| `company.registration_address.address_number` <br> _string_, required                     | House or building number on street |
| `company.registration_address.address_postal_code` <br> _string_, required                | Postal or zip code |
| `company.registration_address.address_refinement` <br> _string_, optional                 | Extra detail, e.g. house name |
| `company.registration_address.address_region` <br> _string_, required                     | Region in country |
| `company.registration_address.address_street` <br> _string_, required                     | Street the beneficiary is registered on |
| `company.registration_address_history` <br> _array of objects_, optional                  | Previous registration address(es) of the company: we use an array incase there are multiple| |
| `company.registration_address_history.address_start_date` <br> _string_, optional         | Date the company began trading for the address. <br> _YYYY-MM-DD_ |
| `company.registration_address_history.address_end_date` <br> _string_, optional           | Date the company stopped trading for the address. <br> _YYYY-MM-DD_ |
| `company.registration_address_history.address_city` <br> _string_, optional               | City that beneficiary was registered in |
| `company.registration_address_history.address_iso_country` <br> _string_, optional        | Country code <br>  _ISO 8601 format_ |
| `company.registration_address_history.address_number` <br> _string_, optional             | House or building number on street |
| `company.registration_address_history.address_postal_code` <br> _string_, optional        | Postal or zip code |
| `company.registration_address_history.address_refinement` <br> _string_, optional         | Extra detail, e.g. house name |
| `company.registration_address_history.address_region` <br> _string_, optional             | Region in country |
| `company.registration_address_history.address_street` <br> _string_, optional             | Street the beneficiary was registered on |
| `company.regulatory_licenses` <br> _array of objects_, optional                           | The regulatory licenses the company holds |
| `company.regulatory_licenses.regulatory_license_body` <br> _string_, optional             | The issuer of the regulatory license the company holds |
| `company.regulatory_licenses.regulatory_license_reference_number` <br> _string_, optional | The reference number of the regulatory license the company holds |
| `company.regulatory_licenses.regulatory_license_type` <br> _string_, optional             | The type of regulatory license the company holds <br> _Allowed Values:_ GB-EMI, GB-API, Other |
| `company.listed_on_stock_exchange` <br> _boolean_, optional                               | Is the company listed on an exchange? |
| `company.stock_exchanges` <br> _array of objects_, optional                               | The stock_exchange(s) the company is listed on |
| `company.stock_exchanges.stock_exchange_name` <br> _string_, optional                     | The name of the stock_exchange |
| `company.stock_exchanges.stock_exchange_ticker` <br> _string_, optional                   | The ticker of the company on the stock_exchange: an abbreviation used to uniquely identify publicly traded shares of a particular stock on a particular stock market |
| `company.trading_address` <br> _array of objects_, required                               | The trading address(es) of the company: we use an array incase there are multiple |
| `company.trading_address.address_city` <br> _string_, required                            | City that beneficiary trades in |
| `company.trading_address.address_iso_country`<br> _string_, required                      | Country code <br>  _ISO 8601 format_ |
| `company.trading_address.address_number` <br> _string_, required                          | House or building number on street |
| `company.trading_address.address_postal_code` <br> _string_, required                     | Postal or zip code |
| `company.trading_address.address_refinement` <br> _string_, optional                      | Extra detail, e.g. house name |
| `company.trading_address.address_region` <br> _string_, required                          | Region in country |
| `company.trading_address.address_street` <br> _string_, required                          | Street the beneficiary trades on |
| `company.trading_address_history` <br> _array of objects_, optional                       | Previous registration address(es) of the company: we use an array incase there are multiple |
| `company.trading_address_history.address_start_date` <br> _string_, optional              | Date the company began trading for the address. <br> _YYYY-MM-DD_ |
| `company.trading_address_history.address_end_date` <br> _string_, optional                | Date the company stopped trading for the address. <br> _YYYY-MM-DD_ |
| `company.trading_address_history.address_city` <br> _string_, optional                    | City that enduser was trading in |
| `company.trading_address_history.address_iso_country` <br> _string_, optional             | Country code <br>  _ISO 8601 format_ |
| `company.trading_address_history.address_number` <br> _string_, optional                  | House or building number on street |
| `company.trading_address_history.address_postal_code` <br> _string_, optional             | Postal or zip code |
| `company.trading_address_history.address_refinement` <br> _string_, optional              | Extra detail, e.g. house name |
| `company.trading_address_history.address_region` <br> _string_, optional                  | Region in country |
| `company.trading_address_history.address_street` <br> _string_, optional                  | Street the beneficiary was trading on |
| `company.directors` <br> _array of objects_, required if director exists                  | Information regarding the directors of the company |
| `company.directors.company` <br> _object_, required if director is a company              | Information about a different company who is a director of the enduser (company) <br> The fields are the same as for a corporate enduser |
| `company.directors.person` <br> _object_, required if director is a person                | Information about a person who is a director of the company <br> The fields are the same as for an individual enduser (detailed above) |
| `company.directors.date_appointed` <br> _string_, required                                | Date the director was appointed <br> _YYYY-MM-DD_ |
| `company.directors.is_also_ubo` <br> _boolean_, required                                  | Is the director an Ultimate Beneficiary Owner <br> _Allowed Values:_ true, false |
| `company.directors.job_title` <br> _string_, optional                                     | The job title of the director |
| `company.ultimate_beneficial_owners` <br> _array of objects_, optional                    | Information regarding the UBOs of the company |
| `company.ultimate_beneficial_owners.company` <br> _object_, optional                      | Information about a different company who is a UBO of the enduser (company) <br> The fields are the same as for a corporate enduser |
| `company.ultimate_beneficial_owners.person` <br> _object_, optional                       | Information about a person who is a UBO of the company <br> The fields are the same as for an individual enduser (detailed above) |
| `company.ultimate_beneficial_owners.percentage_holding` <br> _number_, optional           | The percentage of the company that the UBO owns |
| `enduser_meta` <br> _object_, optional                                                    | Any extra, custom fields that you want to include about the enduser |
