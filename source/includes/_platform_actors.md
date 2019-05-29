# Platform Actors

## Customers


You, the company with access to Railsbank API Keys and capabilities, integrating them into your app or business in order to provide global banking to your customers.

## Endusers


A user of Railsbank capabilities, endusers can be a company or a person and are your customers- our customer's customer.

Bear in mind, the enduser does not access the Railsbank API. Just the Customer. The enduser uses whatever interface the Customer provides for the enduser to interact with the Railsbank API.

## Beneficiaries


A beneficiary is simply a company or person to which money can be sent to from a Railsbank ledger. Both endusers and customers can 'hold' beneficiaries, and  multiple accounts can be held by one beneficiary.

If an enduser creates a beneficiary, for instance, the beneficiary is added to the list of all the enduser's other beneficiaries. When the enduser wants to transfer money to a particular beneficiary, they simply select the `beneficiary_id` from the list and add it to the transaction payload.
