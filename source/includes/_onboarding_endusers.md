# Onboarding Endusers

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

  - Meanwhile, all possible fields can be found in the [Dev Docs](https://docs.railsbank.com/).

  - Please note that the `name` (or `trading name` in the case of a company) of your enduser has a 50 character maximum limit.

## Onboard a Private Individual

> When copy-and-pasting, make sure to change the data to that of your own test enduser.

```shell
curl
  --header 'Content-Type: application/json'
	--header 'Accept: application/json'
	--header 'Authorization: API-Key <<yourapikey>>'
  --request POST
  --data
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
  https://playlive.railsbank.com/v1/customer/endusers
  ```

  > The response will return an `enduser_id` like this:

  ```JSON
  {
      "enduser_id": "5b054129-9139-4300-b40a-2e1154e1edb8"
  }
  ```
  - In the following examples, we will be using the **PlayLive** environment. To use these examples in a **Play** or **Live** environment, simply change the Authorization `API-Key` to the correct Key and change the base URL to `https://play.railsbank.com` or `https://live.railsbank.com` respectively.

## Onboard a Company

  > Once again, just copy-and-paste, changing the details to your test company as you go.

  ```shell
  curl   
    --header 'Content-Type: application/json'
  	--header 'Accept: application/json'
  	--header 'Authorization: API-Key <<yourapikey>>'
    --request POST
    --data
    {
      "company": {
      "name": "Example Company",
      "trading_name": "Example Company Ltd.",  //SEE NOTES
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
      "directors": [                           //SEE NOTES
        {
          "date_appointed": "1990-01-01",
          "job_title": "CEO",
          "is_also_ubo": false,
          "person": {
            "country_of_residence": [
              "USA"
            ],
            "pep": false,                      //SEE NOTES
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
  https://playlive.railsbank.com/v1/customer/endusers
  ```
  > The response will return an `enduser_id` like this:

  ```JSON
  {
      "enduser_id": "5b054129-9139-4300-b40a-2e1154e1edb8"
  }
  ```

  - If the `trading_address` is different from `registered_address`, you need both (otherwise, just include registration address)
  - If there is a director who holds 10% or more of the stakes, then you must include the `director` details
  - If `pep` details are applicable you must include them

## Fetch an Enduser

  > Paste the following into your terminal or testing tool, the API will respond with something like the payload below, making sure to change `{{enduser_id}}` to the id of the enduser you wish to fetch.

  ```shell
  curl   
  --header 'Content-Type: application/json'
  --header 'Accept: application/json'
  --header 'Authorization: API-Key <<yourapikey>>'
  --request GET https://playlive.railsbank.com/v1/customer/endusers/{{enduser_id}}
  ```
  ```JSON
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
  - This simple API call will return all the information about an enduser: the ledgers they hold, their beneficiaries, their status (they could be in quarantine) and their details.

## Update an Enduser
  > Now, to update the enduser we simply use the following call and include the keys that we want to add or change in the request body. In this case:

  > **add** `trading_address`

  > **change** `post_code`

  ```shell
  curl
    --header 'Content-Type: application/json'
  	--header 'Accept: application/json'
  	--header 'Authorization: API-Key <<yourapikey>>'
    --request PUT  
    --data
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
    https://playlive.railsbank.com/v1/customer/endusers/{{enduser_id}}
  ```
  > If the key is a nested, you should include the parent in the request body field

  > If done successfully, you will be given the your existing `enduser_id`.
  ```JSON
  {
      "enduser_id": "5c54418e-8bbf-4a66-b54a-XXXXXXXXXXXX"
  }
  ```

  - You only need to include the keys and values that you want to change.
  - Updating a non-existent enduser will return an error `"Invalid enduser id."`
  - You can update both **person** and **company** endusers.


  Let us imagine the following hypothetical but practical scenarios:

### Scenario 1 - Adding a new key of information

  - Imagine an existing company enduser you onboarded now has a different **trading address** from the address that they registered from (also called the **registration address**)
  - From a compliance perspective, you will need to provide both the `trading_adresses` and `registration_address` information. However, when you onboarded that enduser, you only included the latter

### Scenario 2 - Editing an existing key of information

  - The same existing company enduser incorrectly provided their post-code and it needs to be changed.