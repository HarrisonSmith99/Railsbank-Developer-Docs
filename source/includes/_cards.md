# Cards

  - The **Railsbank Debit Card** solution allows our customers to issue **Mastercard Debit Cards** against Railsbank ledgers with just a few easy API calls, exercising the **Issue Cards** cabability.
  - Cards can be both virtual and physical, and - quite uniquely - a virtual card can be transformed into a physical card while retaining it's **card number (PAN)**.
  - The card design can also be customised to your taste.
  - Due to the limits of **playLive** and the beauty of the innovation, this cabalility is only available for customers with a **Live API Key**.
  - Customers with a **Live API Key** must have also purchased the `Railsbank-Debit-Card-1` partner product.
  - This can easily be done by contacting [support](mailto:support@railsbank.com).

## Onboard a Card-holding Enduser

  > Note the change of the base url from playlive to live.

  ```shell
  curl --request POST https://live.railsbank.com/v1/customer/endusers
    --header 'Content-Type: application/json'
    --header 'Accept: application/json'
    --header 'Authorization: API-Key <<yourliveapikey>>'
    --request POST
    --data
  {
    "person": {
         "name": "John Smith",
         }
  }
  https://live.railsbank.com/v1/customer/endusers
  ```
  > To which the API will respond with an `enduser_id` like this:

  ```JSON
  {
      "enduser_id": "5b054129-9139-4300-b40a-2e1154e1edb8"
  }
  ```
  - While you can see our Onboard an Enduser tutorial to learn how to onboard a basic enduser, in this one we're going to briefly and explicitly understand the small changes that need to take place for the enduser to be able to hold a **Railsbank Debit Card**.
  - So, to onboard a **card-holding** enduser, assuming you have purchased the **Railsbank-Direct-Debit-1** product, you need to paste the following into your terminal or testing tool, making sure to replace the example values for your own.

  - You can Fetch enduser to retrieve all the information about the enduser - including their card details, once they've been created!

## Issue a Card

> Once you've got the `ledger_id` simply paste it into the following payload, paste the payload into your terminal or testing tool and let us do the rest.

```shell
curl
	--header 'Content-Type: application/json'
	--header 'Accept: application/json'
	--header 'Authorization: API-Key <<yourliveapikey>>'
  --request POST
  --data
{
  "ledger_id": "{{ledger_id}}",
  "partner_product": "Railsbank-Debit-Card-1",
  "card_program": "Railsbank Card Program"
}
https://live.railsbank.com/v1/customer/cards
```
> The API will respond with the `card_id`, which will look something like this:

```JSON
{
  "card_id": "6630b391-c5ce-46c1-9d23-a82a9e27f82d"
}
```
  - Once you've created your **card-holding** enduser, carefully go through our #docTextSection:bsbkPCZM69Akzjebo tutorial - it'll only take a minute or so - to create your **SEPA Step 2** connected bank account to which your card is going to be attched.
  - When issuing the ledger, make sure to do so in the **Live** environment with your **Live API Key** and the `enduser_id` of the **card-holding** enduser you've created.
