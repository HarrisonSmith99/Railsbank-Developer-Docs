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
  curl
    --request POST https://live.railsbank.com/v1/customer/endusers
    --header 'Content-Type: application/json'
    --header 'Accept: application/json'
    --header 'Authorization: API-Key <<yourliveapikey>>'

  {
    "person": {
         "name": "John Smith",
         }
  }
  ```
  > To which the API will respond with an `enduser_id` like this:

  ```javascript
  {
      "enduser_id": "5b054129-9139-4300-b40a-2e1154e1edb8"
  }
  ```
  - While you can see our Onboard an Enduser tutorial to learn how to onboard a basic enduser, in this one we're going to briefly and explicitly understand the small changes that need to take place for the enduser to be able to hold a **Railsbank Debit Card**.
  - So, to onboard a **card-holding** enduser, assuming you have purchased the **Railsbank-Direct-Debit-1** product, you need to paste the following into your terminal or testing tool, making sure to replace the example values for your own.

  - You can Fetch enduser to retrieve all the information about the enduser - including their card details, once they've been created!
