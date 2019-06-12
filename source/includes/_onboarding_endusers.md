# Onboarding Endusers

  > **Endpoints**

  ```shell
  POST /v1/customer/endusers
  GET /v1/customer/endusers/{{ENDUSER_ID}}
  PUT /v1/customer/endusers/{{ENDUSER_ID}}
  ```

  - **Endusers** are the private individuals or companies that use Railsbank's capabilities; they are the customers of our customers.

  - In the following example we shall onboard two endusers:
    - John Smith (a private individual, also called a **person**)
    - Numaro Ltd. (a corporation, also called a **company**)

  - The code in the following sections are examples of payloads for creating the two types of endusers: **Person** and **Company**.

  - The single endpoint is conditional on the type of enduser. Only one of the two fields is allowed to exist in the request body:

    - If the enduser is a private individual, you will just need to include the fields for `person`.
    - If the enduser is a company, you will just need to include the fields from `company`.


  - The minimum data required is simply the name of the `person` or `company`.

  - Whilst a name technically allows you to create an enduser, for compliance reasons, you must include the following fields:

  - If it's a person:
      - Name
      - Address
      - Country of residence

  - If it's a company:
    - Ultimate beneficial owner information
    - If the trading address is different from the registered address, you will need both

  - Please note that the `name` (or `trading name` in the case of a company) of your enduser has a 50 character maximum limit.

## Onboard an Individual Enduser

> **Example Request**

```shell
  --request POST "https://playlive.railsbank.com/v1/customer/endusers"
  --header "Content-Type: application/json"
	--header "Accept: application/json"
	--header "Authorization: API-Key <<yourAPI-Key>>"

  {
    "person": {
         "name": "John Smith",
         "email": "johnsmith@gmail.com",
         "date_of_birth": "1970-11-05",
         "telephone": "+44 22 626 2626",
         "address": {
           "address_refinement": "Apartment 42",
           "address_number": "29",
           "address_street": "Acacia Road",
           "address_city": "London",
           "address_postal_code": "FX20 7XS",
           "address_iso_country": "GBR"
         },
         "address_history": [
           {
             "address_refinement": "Apartment 77",
             "address_number": "42",
             "address_street": "Kirschbaumstraße",
             "address_city": "Berlin",
             "address_postal_code": "12059",
             "address_iso_country": "DE",
             "address_start_date": "2000-09-02",
             "address_end_date": "2002-12-20"
           }
         ],
         "nationality": [
           "British"
         ],
         "country_of_residence": [
           "GBR"
         ],
         "date_onboarded": "2015-11-21"
       },
        "enduser_meta": {
         "foo": "baa",
        "our_salesforce_reference": "http://na1.salesforce.com/5003000000D8cuI"
    }
  }
  ```
  > **Example Curl Request**

   ```bash
     curl -X POST "https://playlive.railsbank.com/v1/customer/endusers" -H "accept: application/json" -H "Authorization: API-Key <<yourAPI-Key>>" -H "Content-Type: application/json" -d "{ \"person\": { \"name\": \"John Smith\", \"email\": \"johnsmith@gmail.com\", \"date_of_birth\": \"1970-11-05\", \"telephone\": \"+44 22 626 2626\", \"address\": { \"address_refinement\": \"Apartment 42\", \"address_number\": \"29\", \"address_street\": \"Acacia Road\", \"address_city\": \"London\", \"address_postal_code\": \"FX20 7XS\", \"address_iso_country\": \"GBR\" }, \"address_history\": [ { \"address_refinement\": \"Apartment 77\", \"address_number\": \"42\", \"address_street\": \"Kirschbaumstraße\", \"address_city\": \"Berlin\", \"address_postal_code\": \"12059\", \"address_iso_country\": \"DE\", \"address_start_date\": \"2000-09-02\", \"address_end_date\": \"2002-12-20\" } ], \"nationality\": [ \"British\" ], \"country_of_residence\": [ \"GBR\" ], \"date_onboarded\": \"2015-11-21\" }, \"enduser_meta\": { \"foo\": \"baa\", \"our_salesforce_reference\": \"http://na1.salesforce.com/5003000000D8cuI\" } }"
   ```
  > **Example Response**

  ```shell
  {
      "enduser_id": "5b054129-9139-4300-b40a-2e1154e1edb8"
  }
  ```
`POST "https://playlive.railsbank.com/v1/customer/endusers"`

  - This endpoint allows you to onboard an enduser (your customer) who is an individual.
  - Make sure, at a minimum, to include the name and full address of the user to ensure they are not blocked by compliance.
  - Check out the `enduser_meta` field which allows you to add custom fields to the enduser.

    <a href="javascript:void(0);"onclick="javascript:toggledarkbox()">Expand Table</a>

### The Individual Enduser Object
| Attribute                                                            | Description |
|:---------------------------------------------------------------------|:------|
| `person` <br> _object_, required (for individuals)                   | Information about individual |
| `person.name` <br> _string_, required                                | Full Name  <br> _Limited to 70 chars_ |
| `person.country_of_residence` <br> _string_, required                | Country code <br>  _ISO 8601 format_ |
| `person.date_of_birth` <br> _string_, optional                       | Date of birth  <br> _YYYY-MM-DD_ |
| `person.nationality` <br> _string_, optional                         | Nationality |
| `person.pep` <br> _boolean_, required (if applicable)                | Politically Exposed Person <br>  _Does not need to be inside ""_ |
| `person.pep_type` <br> _string_, optional                            | Extent of exposure <br>  _Allowed Values: `direct`, `close-associate`, `former-pep`, `family-member`_ |
| `person.pep_notes` <br> _string_, optional                           | Extra detail |
| `person.social_security_number` <br> _string_, optional              | SSN   |
| `person.telephone` <br> _string_, optional                           | Phone number |
| `person.tin` <br> _string_, optional                                 | Tax Identification Number |
| `person.tin_type` <br> _string_, optional                            | Type of Tax Identification Number |
| `person.address` <br> _object_, required                             | current address information |
| `person.address.address_city` <br> _string_, required                | City that enduser lives in |
| `person.address.address_iso_country`<br> _string_, required          | Country code <br>  _ISO 8601 format_ |
| `person.address.address_number` <br> _string_, required              | House or building number on street |
| `person.address.address_postal_code` <br> _string_, required         | Postal or zip code |
| `person.address.address_refinement` <br> _string_, optional          | Extra detail, e.g. house name |
| `person.address.address_region` <br> _string_, required              | Region in country |
| `person.address.address_street` <br> _string_, required              | Street the enduser lives on |
| `person.address_history` <br> _array of objects_, optional           | Previous address information. Can include multiple addresses in an array |
| `person.address_history.address_start_date` <br> _string_, optional  | Date at which the person began living at the address |
| `person.address_history.address_end_date` <br> _string_, optional    | Date at which the person stopped living at the address |
| `person.address_history.address_city` <br> _string_, optional        | City that enduser lived in |
| `person.address_history.address_iso_country` <br> _string_, optional | Country code <br>  _ISO 8601 format_ |
| `person.address_history.address_number` <br> _string_, optional      | House or building number on street |
| `person.address_history.address_postal_code` <br> _string_, optional | Postal or zip code |
| `person.address_history.address_refinement` <br> _string_, optional  | Extra detail, e.g. house name |
| `person.address_history.address_region` <br> _string_, optional      | Region in country |
| `person.address_history.address_street` <br> _string_, optional      | Street the enduser lived on |
| `enduser_meta` <br> _object_, optional                               | Any extra, custom fields that you want to include about the enduser |

<<script type="text/javascript">
  hidden=false;
  function toggledarkbox() {
    if (hidden == false) {
      $(".content table").css("overflow", "visible")
      hidden = true
    }else{
      $(".content table").css("overflow", "auto")
      hidden = false
    }
}
</script>

## Onboard a Corporate Enduser

  > **Example Request**

  ```shell
    --request POST "https://playlive.railsbank.com/v1/customer/endusers"
    --header "Content-Type: application/json"
  	--header "Accept: application/json"
  	--header "Authorization: API-Key <<yourapikey>>"
    {
      "company": {
            "name": "Example Company",
            "trading_name": "Example Company Ltd.",  
            "web_site": "www.website.com",
            "industry": "Financial Services",
            "listed_on_stock_exchange": false,
            "registration_address":
        {
           "address_refinement": "Floor 15",
           "address_number": "20",
           "address_street": "The Strand",
           "address_city": "London",
           "address_postal_code": "SS8 9JH",
           "address_iso_country": "GBR"
        },
      "directors": [                         
        {
          "date_appointed": "1990-01-01",
          "job_title": "CEO",
          "is_also_ubo": false,
          "person": {
            "country_of_residence": [
              "USA"
            ],
            "pep": false,                
            "email": "johnsmith@gmail.com",
            "name": "John Smith",
            "social_security_number": "090606",
            "telephone": "+44 98 765 4321",
            "date_of_birth": "1981-02-03",
            "nationality": [
              "American"
            ]
          }
        }
      ]
    }
  }
  ```
  > **Example Curl Request**

  ```shell
      curl -X POST "https://playlive.railsbank.com/v1/customer/endusers" -H "accept: application/json" -H "Authorization: API-Key <<yourAPI-Key>>" -H "Content-Type: application/json" -d "{ \"company\": { \"name\": \"Example Company\", \"trading_name\": \"Example Company Ltd.\", \"web_site\": \"www.website.com\", \"industry\": \"Financial Services\", \"listed_on_stock_exchange\": false, \"registration_address\": { \"address_refinement\": \"Floor 15\", \"address_number\": \"20\", \"address_street\": \"The Strand\", \"address_city\": \"London\", \"address_postal_code\": \"SS8 9JH\", \"address_iso_country\": \"GBR\" }, \"directors\": [ { \"date_appointed\": \"1990-01-01\", \"job_title\": \"CEO\", \"is_also_ubo\": false, \"person\": { \"country_of_residence\": [ \"USA\" ], \"pep\": false, \"email\": \"johnsmith@gmail.com\", \"name\": \"John Smith\", \"social_security_number\": \"090606\", \"telephone\": \"+44 98 765 4321\", \"date_of_birth\": \"1981-02-03\", \"nationality\": [ \"American\" ] } } ] } }"
  ```
  > **Example Response**

  ```shell
  {
      "enduser_id": "5b054129-9139-4300-b40a-2e1154e1edb8"
  }
  ```
`POST "https://playlive.railsbank.com/v1/customer/endusers"`

  - This endpoint allows you to onboard an enduser (your customer) who is a company.
  - These endusers are businesses that are also your customers if you are a B2B business.
  - Check out the `enduser_meta` field which allows you to add custom fields to the enduser.
  - If the `trading_address` is different from `registered_address`, you need both (otherwise, just include registration address)
  - If there is a director who holds 10% or more of the stakes, then you must include the `director` details
  - If `pep` details are applicable you must include them

### The Corporate Enduser Object
| Attribute                                                                                 | Description |
|:------------------------------------------------------------------------------------------|:--|
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
| `company.registration_address.address_city` <br> _string_, required                       | City that enduser is registered in |
| `company.registration_address.address_iso_country`<br> _string_, required                 | Country code <br>  _ISO 8601 format_ |
| `company.registration_address.address_number` <br> _string_, required                     | House or building number on street |
| `company.registration_address.address_postal_code` <br> _string_, required                | Postal or zip code |
| `company.registration_address.address_refinement` <br> _string_, optional                 | Extra detail, e.g. house name |
| `company.registration_address.address_region` <br> _string_, required                     | Region in country |
| `company.registration_address.address_street` <br> _string_, required                     | Street the enduser is registered on |
| `company.registration_address_history` <br> _array of objects_, optional                  | Previous registration address(es) of the company: we use an array incase there are multiple| |
| `company.registration_address_history.address_start_date` <br> _string_, optional         | Date the company began trading for the address. <br> _YYYY-MM-DD_ |
| `company.registration_address_history.address_end_date` <br> _string_, optional           | Date the company stopped trading for the address. <br> _YYYY-MM-DD_ |
| `company.registration_address_history.address_city` <br> _string_, optional               | City that enduser was registered in |
| `company.registration_address_history.address_iso_country` <br> _string_, optional        | Country code <br>  _ISO 8601 format_ |
| `company.registration_address_history.address_number` <br> _string_, optional             | House or building number on street |
| `company.registration_address_history.address_postal_code` <br> _string_, optional        | Postal or zip code |
| `company.registration_address_history.address_refinement` <br> _string_, optional         | Extra detail, e.g. house name |
| `company.registration_address_history.address_region` <br> _string_, optional             | Region in country |
| `company.registration_address_history.address_street` <br> _string_, optional             | Street the enduser was registered on |
| `company.regulatory_licenses` <br> _array of objects_, optional                           | The regulatory licenses the company holds |
| `company.regulatory_licenses.regulatory_license_body` <br> _string_, optional             | The issuer of the regulatory license the company holds |
| `company.regulatory_licenses.regulatory_license_reference_number` <br> _string_, optional | The reference number of the regulatory license the company holds |
| `company.regulatory_licenses.regulatory_license_type` <br> _string_, optional             | The type of regulatory license the company holds <br> _Allowed Values:_ GB-EMI, GB-API, Other |
| `company.listed_on_stock_exchange` <br> _boolean_, optional                               | Is the company listed on an exchange? |
| `company.stock_exchanges` <br> _array of objects_, optional                               | The stock_exchange(s) the company is listed on |
| `company.stock_exchanges.stock_exchange_name` <br> _string_, optional                     | The name of the stock_exchange |
| `company.stock_exchanges.stock_exchange_ticker` <br> _string_, optional                   | The ticker of the company on the stock_exchange: an abbreviation used to uniquely identify publicly traded shares of a particular stock on a particular stock market |
| `company.trading_address` <br> _array of objects_, required                               | The trading address(es) of the company: we use an array incase there are multiple |
| `company.trading_address.address_city` <br> _string_, required                            | City that enduser trades in |
| `company.trading_address.address_iso_country`<br> _string_, required                      | Country code <br>  _ISO 8601 format_ |
| `company.trading_address.address_number` <br> _string_, required                          | House or building number on street |
| `company.trading_address.address_postal_code` <br> _string_, required                     | Postal or zip code |
| `company.trading_address.address_refinement` <br> _string_, optional                      | Extra detail, e.g. house name |
| `company.trading_address.address_region` <br> _string_, required                          | Region in country |
| `company.trading_address.address_street` <br> _string_, required                          | Street the enduser trades on |
| `company.trading_address_history` <br> _array of objects_, optional                       | Previous registration address(es) of the company: we use an array incase there are multiple |
| `company.trading_address_history.address_start_date` <br> _string_, optional              | Date the company began trading for the address. <br> _YYYY-MM-DD_ |
| `company.trading_address_history.address_end_date` <br> _string_, optional                | Date the company stopped trading for the address. <br> _YYYY-MM-DD_ |
| `company.trading_address_history.address_city` <br> _string_, optional                    | City that enduser was trading in |
| `company.trading_address_history.address_iso_country` <br> _string_, optional             | Country code <br>  _ISO 8601 format_ |
| `company.trading_address_history.address_number` <br> _string_, optional                  | House or building number on street |
| `company.trading_address_history.address_postal_code` <br> _string_, optional             | Postal or zip code |
| `company.trading_address_history.address_refinement` <br> _string_, optional              | Extra detail, e.g. house name |
| `company.trading_address_history.address_region` <br> _string_, optional                  | Region in country |
| `company.trading_address_history.address_street` <br> _string_, optional                  | Street the enduser was trading on |
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

## Fetch an Enduser

  > **Example Request**

  ```shell
    --request GET "https://playlive.railsbank.com/v1/customer/endusers/{{enduser_id}}"
    --header "Content-Type: application/json"
  	--header "Accept: application/json"
  	--header "Authorization: API-Key <<yourapikey>>"
  ```

  > **Example Curl Request**

  ```shell
      curl -X GET "https://playlive.railsbank.com/v1/customer/endusers/{{enduser_id}}" -H "accept: application/json" -H "Authorization: API-Key <<yourAPI-Key>>"
  ```

  > **Example Response**

  ```shell
  {
    "enduser_id": "5b068bd4-ba1c-4b56-a23c-78e0ad2509c9",
    "enduser_status": "enduser-status-ok",
    "ledgers": [],
    "beneficiaries": [],
    "person": {
        "country_of_residence": [
            "GB"
        ],
        "address_history": [
            {
                "address_refinement": "Apartment 77",
                "address_number": "42",
                "address_street": "Kirschbaumstraße",
                "address_city": "Berlin",
                "address_postal_code": "12059",
                "address_iso_country": "DE",
                "address_start_date": "2000-09-02",
                "address_end_date": "2002-12-20"
            }
        ],
        "date_onboarded": "2015-11-21",
        "email": "johnsmith@gmail.com",
        "name": "John Smith",
        "address": {
            "address_refinement": "Apartment 42",
            "address_number": "29",
            "address_street": "Acacia Road",
            "address_city": "London",
            "address_postal_code": "FX20 7XS",
            "address_iso_country": "GB"
        },
        "telephone": "+44 22 626 2626",
        "date_of_birth": "1970-11-05",
        "nationality": [
            "British"
        ]
    },
    "enduser_meta": {
        "foo": "baa",
        "our_salesforce_reference": "http://na1.salesforce.com/5003000000D8cuI"
    },
    "screening_monitored_search": false
  }
  ```
`GET "https://playlive.railsbank.com/v1/customer/endusers/{{enduser_id}}"`

  - This simple API call will return all the information about an enduser: the ledgers they hold, their beneficiaries, their status (they could be in quarantine) and their details.
  - The only new fields are: the `date_onboarded` which is the date the enduser was onboarded to Railsbank and `enduser_status` which is the state of the enduser.
  - Use the `/wait` parameter to instruct the API to wait for (up to 60 seconds) the enduser to be in a REST state - i.e. not `pending`.

### Enduser Statuses
| Message                       | Description                                  |
|:------------------------------|:---------------------------------------------|
| `enduser-status-missing-data` | Extra data is required for the enduser to be created, for instance, the name of the enduser. |
| `enduser-status-pending`      | The enduser is being processed. To avoid fetching an enduser in this state, use our `/wait` parameter. |
| `enduser-status-quarantine`   | The enduser has broken a customer firewall rule and fallen into the customer quarantine queue. You will receive a `type: entity-fw-quarantine` webhook and a `type: enduser-firewall-finished` webhook. |
| `enduser-status-ok`           | The enduser is ready to use: to add ledgers and beneficiaries to and send and receive transactions. You will receive a `type: entity-ready-to-use` webhook and a `type: enduser-firewall-finished` webhook if you have set and firewall rules up. |
| `enduser-status-declined`     | The enduser has been declined by our system. This is usually because the enduser has broken a partner firewall rule and been rejected by our compliance team, for instance, if they are from a country we don't deal with, like North Korea. |

## Update an Enduser
  > **Example Request**

  > **add** `trading_address`

  > **change** `post_code`

  ```shell
  --request PUT "https://playlive.railsbank.com/v1/customer/endusers/{{enduser_id}}"
  --header "Content-Type: application/json"
	--header "Accept: application/json"
	--header "Authorization: API-Key <<yourapikey>>"
    {
    	"company": {
    		"web_site": "www.correct_company_website.com",
        	"trading_addresses": [{
        	   "address_refinement": "Apartment 5",
    		     "address_number": "21",
    		     "address_street": "Flower Road",
    		     "address_city": "London",
    		     "address_region": "Greater London",
    		     "address_postal_code": "WC1R 7XS",
    		     "address_iso_country": "GB"        
          }]
    	}
    }
  ```
  > **Example Curl Request**

  ```shell
    curl -X PUT "https://playlive.railsbank.com/v1/customer/endusers/{{enduser_id}}" -H "accept: application/json" -H "Authorization: API-Key <<yourAPI-Key>>" -H "Content-Type: application/json" -d "{ \t\"company\": { \t\t\"web_site\": \"www.correct_company_website.com\", \t\"trading_addresses\": [{ \t \"address_refinement\": \"Apartment 5\", \t\t \"address_number\": \"21\", \t\t \"address_street\": \"Flower Road\", \t\t \"address_city\": \"London\", \t\t \"address_region\": \"Greater London\", \t\t \"address_postal_code\": \"WC1R 7XS\", \t\t \"address_iso_country\": \"GB\" }] \t} }"
  ```
  > **Example Response**

  ```shell
  {
      "enduser_id": "5c54418e-8bbf-4a66-b54a-XXXXXXXXXXXX"
  }
  ```
`PUT "https://playlive.railsbank.com/v1/customer/endusers/{{enduser_id}}"`

  - This endpoint allows you to update an enduser.
  - You only need to include the keys and values that you want to change.
  - If the key is a nested, you should include the parent in the request body field. I.e. if you want to update the name of a `person`, you cannot just write `"name": "example_name"`, you must write `"person": {"name": "example_name"}`.
  - Updating a non-existent enduser will return an error `"Invalid enduser id."`
  - You can update both **person** and **company** endusers.

  - Let us imagine the following hypothetical but practical scenarios:

### Scenario 1 - Adding a new key of information

  - Imagine an existing company enduser you onboarded now has a different **trading address** from the address that they registered from (also called the **registration address**)
  - From a compliance perspective, you will need to provide both the `trading_adresses` and `registration_address` information. However, when you onboarded that enduser, you only included the latter

### Scenario 2 - Editing an existing key of information

  - The same existing company enduser incorrectly provided their post-code and it needs to be changed.
