# Cards

  - The **Railsbank Debit Card** solution allows our customers to issue **Mastercard Debit Cards** against Railsbank ledgers with just a few easy API calls, exercising the **Issue Cards** cabability.
  - Cards can be both virtual and physical, and - quite uniquely - a virtual card can be transformed into a physical card while retaining it's **card number (PAN)**.
  - The card design can also be customised to your taste.
  - Due to the limits of **playLive** and the beauty of the innovation, this cabalility is only available for customers with a **Live API Key**.
  - Customers with a **Live API Key** must have also purchased the `Railsbank-Debit-Card-1` partner product.
  - This can easily be done by contacting [support](mailto:support@railsbank.com).

## Onboard a Card-holding Enduser

  > **Note the change of the base url from playlive to live.**

  ```shell
  curl  
    --request POST "https://playlive.railsbank.com/v1/customer/endusers"
    --header "Content-Type: application/json"
  	--header "Accept: application/json"
  	--header "Authorization: API-Key <<yourapikey>>"
    --data
        "{
            "person": {
                 "name": "John Smith",
                },
            "address": {  
                  "address_refinement": "Apartment 42",
                  "address_number": "29",
                  "address_street": "Acacia Road",
                  "address_city": "London",
                  "address_postal_code": "FX20 7XS",
                  "address_iso_country": "GBR"
                }
          }"
  ```
  > **To which the API will respond with an `enduser_id` like this:**

  ```JSON
  {
      "enduser_id": "5b054129-9139-4300-b40a-2e1154e1edb8"
  }
  ```
  - While you can see our Onboard an Enduser tutorial to learn how to onboard a basic enduser, in this one we're going to briefly and explicitly understand the small changes that need to take place for the enduser to be able to hold a **Railsbank Debit Card**.
  - So, to onboard a **card-holding** enduser, assuming you have purchased the **Railsbank-Direct-Debit-1** product, you need to paste the following into your terminal or testing tool, making sure to replace the example values for your own.
  - You can Fetch enduser to retrieve all the information about the enduser, including their card details, once they've been created!

### Note
  - You can only create endusers of the type associated to your card programme: so if your card programme only allows individual people, only endusers of type "person" will be able to hold cards.

## Issue a Card

  > **Once you've got the `ledger_id` simply paste it into the following payload, paste the payload into your terminal or testing tool and let us do the rest.**

  ```shell
  curl
    --request POST "https://live.railsbank.com/v1/customer/cards"
    --header "Content-Type: application/json"
    --header "Accept: application/json"
    --header "Authorization: API-Key <<yourapikey>>"
    --data
      "{
        "ledger_id": "{{ledger_id}}",
        "partner_product": "Railsbank-Debit-Card-1",
        "card_program": "Railsbank Card Program"
      }"

  ```
  > **The API will respond with the `card_id`, which will look something like this:**

  ```JSON
  {
    "card_id": "6630b391-c5ce-46c1-9d23-a82a9e27f82d"
  }
  ```
  - Once you've created your **card-holding** enduser, carefully go through our Issue a ledger tutorial - it'll only take a minute or so - to create the bank account to which your card is going to be attched.
  - When issuing the ledger, make sure to do so in the **Live** environment with your **Live API Key** and the `enduser_id` of the **card-holding** enduser you've created.

## Fetch a Card

  > **Simply paste the following into your terminal or testing tool, making sure to substitute your enduser's `card_id` in.**

  ```shell
  curl
    --request GET "https://live.railsbank.com/v1/customer/cards/{{card_id}}"
  	--header "Content-Type: application/json"
  	--header "Accept: application/json"
  	--header "Authorization: API-Key <<yourliveapikey>>"
  ```
  > **The API will respond with a payload smilar to the example below. The possible statuses of a card are: `active`, `created`, and `disabled`.**

  ```JSON
  {
    "card_id": "8763b5df-a61c-4f77-9724-41ca9cde3654",
    "card_status": "card-status-created",
    "card_program": "Railsbank Card Program"
    "customer_id": "c91b339e-57d7-41ea-a805-8966ce8fe4ed",
    "partner_product": "Railsbank-Debit-Card-1",
    "ledger_id": "6630b391-c5ce-46c1-9d23-a82a9e27f82d",
    "temp_card_image_url": "237564rh43ht43"
  }
  ```
    - Fetching a card is much like 'fetching' anything else with the API.
    - In this case, all you need to know is the `card_id`.

## Fetch a Card using its token

  > **Simply paste the following into your terminal or testing tool, making sure to substitute your enduser's `card_token` in.**

  ```shell
  curl
    --request GET "https://live.railsbank.com/v1/customer/cards/{{card_token}}"
    --header "Content-Type: application/json"
    --header "Accept: application/json"
    --header "Authorization: API-Key <<yourliveapikey>>"
  ```

  > **The API will respond with a payload smilar to the example below. The possible statuses of a card are: `active`, `created`, and `disabled`.**

  ```JSON
  {
      "ledger_id": "5cda9171-f2e3-4442-82f1-884c877df9e5",
      "card_token": "473894665",
      "card_id": "5cdab638-ad74-47c3-a918-15e1876dcfcf",
      "card_type": "virtual",
      "card_rules": [],
      "created_at": "2019-05-14T12:36:08.493Z",
      "partner_product": "Railsbank-Debit-Card-1",
      "card_design": "design one",
      "card_status": "card-status-awaiting-activation",
      "card_programme": "rbbe-313-gbp-person"
  }
  ```
  - A few seconds after a card is created, a token is assigned to it.
  - This token can also be used to fetch the card.
  - If you are fetching the card using this endpoint after generation you will need to wait a few seconds before the token is added to the card, at which point you must fetch the card using the `card_id` in order to discover the token.
  - The card token can be used to validate physical cards. It will be nine integers on the back of the card, so when your customer receives the card, before you activate it, you can check that they are using the correct card by checking the token number.

## Fetch all of your cards
  > **Paste the following into your terminal or testing tool and relax.**

  ```shell
  curl
    --request GET "https://live.railsbank.com/v1/customer/cards"
    --header "Content-Type: application/json"
    --header "Accept: application/json"
    --header "Authorization: API-Key <<yourliveapikey>>"
  ```
  > **The API will respond with list of cards and their details similar to the example below:**

  ```JSON
    {
      "card_id": "bb8b2428-f94c-41df-8e82-a895ab4d6ac8",
      "card_status": "card-status-created",
      "customer_id": "bb8b2428-f94c-41df-8e82-a895ab4d6ac8",
      "partner_product": "Railsbank-Debit-Card-1",
      "ledger_id": "bb8b2428-f94c-41df-8e82-a895ab4d6ac8"
    },
    {
      "card_id": "8763b5df-a61c-4f77-9724-41ca9cde3654",
      "card_status": "card-status-active",
      "customer_id": "6630b391-c5ce-46c1-9d23-a82a9e27f82d",
      "partner_product": "Railsbank-Debit-Card-1",
      "ledger_id": "6630b391-c5ce-46c1-9d23-a82a9e27f82d"
    },
    {
      "card_id": "c91b339e-57d7-41ea-a805-8966ce8fe4ed",
      "card_status": "card-status-disabled",
      "customer_id": "bb8b2428-f94c-41df-8e82-a895ab4d6ac8",
      "partner_product": "Railsbank-Debit-Card-1",
      "ledger_id": "bb8b2428-f94c-41df-8e82-a895ab4d6ac8"
    }
  ```

  - If you want to see the details of all the cards you have issued, then this powerful endpoint is for you.

## Activate a Card
  > **Paste the following - substituting your enduser's `card_id` - into your terminal or testing tool. It is that easy.**

  ```shell
  curl
    --request POST "https://live.railsbank.com/v1/customer/cards/{{card_id}}/activate"
    --header "Content-Type: application/json"
    --header "Accept: application/json"
    --header "Authorization: API-Key <<yourliveapikey>>"
  ```
  > **To which the API will once again respond with the `card_id`, only this time that `card_id` will have `status-active`.**

  - A `created` card is cool, but an `activated` is awesome; it has access to global banking.
  - This simple endpoint will **activate** the card.

## Suspend a Card
  > **If you know the `card_id` of the card you want to suspend, paste it, along with the following, into your terminal or testing tool and let us do the rest.**

  ```shell
  curl
    --request POST "https://live.railsbank.com/v1/customer/cards/{{card_id}}/suspend"
    --header "Content-Type: application/json"
    --header "Accept: application/json"
    --header "Authorization: API-Key <<yourliveapikey>>"
  ```
  - The API will respond with your suspended `card_id`. Simple.
  - Naturally, there will come a time when you or your endusers need to `suspend` a card. This endpoint allows you to do so in one easy call.
  - Once you suspended a card, it can be reactivated at any time by using `activate` endpoint. However, if you don't reactivate it, it will remain suspended.

## Fetching Card PINs
  > **Providing your Customer API-Key, use the following endpoint:**

  ```shell
  curl
    --request GET "https://live.railsbank.com/v1/customer/cards/{{card_id}}/pin"
    --header "Content-Type: application/json"
    --header "Accept: application/json"
    --header "Authorization: API-Key <<yourliveapikey>>"
  ```
  > **And API will respond with the PIN:**

  ```python
  {
    "pin": "2477"
  }
  ```
  - You may want to fetch the PIN number for chip-and-PIN transactions. All you need is your `card_id` to obtain it.

  > **So, if you request for PINs before this time, you will receive the error:**

  ```JSON
  {
    "error": "card-pin-not-set"
  }
  ```
### Note
  - Before you fetch you physical card PIN it is important to note:
  - PINs for new cards are generated at 05:05 each day, as part of generating the xml file for the manufacturer.

## Get Card Image
  > **Providing your Customer API-Key, use the following endpoint:**

  ```shell
  curl
    --request GET "https://live.railsbank.com/v1/customer/cards/{{card_id}}/image"
    --header "Content-Type: application/json"
    --header "Accept: application/json"
    --header "Authorization: API-Key <<yourliveapikey>>"
  ```
  > **And API will respond with the URL:**

  ```JSON
  {
    "temp_card_image_url": "http://40.127.00.174:8002/Paymentology/585256_F347DBA257102343AB5132840D231AB3.png"
  }
  ```
  - The endpoint provides a URL which can be used to view the card image.
  - The URL is valid for ten minutes.
