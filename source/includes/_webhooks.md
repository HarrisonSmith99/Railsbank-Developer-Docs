# Webhooks
> **Endpoints**

```shell
`POST /v1/customer/webhook`
`POST /v1/customer/webhook/update-secret`
`GET /v1/customer/webhook`
`GET /v1/customer/webhook/failed-to-deliver`
`DELETE /v1/customer/webhook`
`GET /v1/customer/notifications`
```

- We understand that it's not ideal to have to poll our GET endpoints to detect changes in a ledger or a transaction, so we have a webhook that will send HTTP notifications to your chosen URL when certain events occur.
- Use the **Webhook** capability to receive real-time notifications.
- With Railsbank you only have to set **one** webhook url: all of the notifications will be sent there.
- With the url you must also supply a `secret`, which allows you to identify that the notifications you are receiving are coming from Railsbank.
- Ensure your SSL Certificate is issued by a Trusted Authority or has the full chain included.

## Types of Notification
> **Entity Ready to Use**

```shell
{
  "enduser_id" : "5d0385ac-4ae5-4eae-be6b-fa126c0a6bfc",
  "owner" : "5be950c7-ef99-43d7-a0c7-96da0106a68d",
  "type" : "entity-ready-to-use",
  "created_at" : "2019-06-14T11:32:06.279Z",
  "notification_id" : "5d0385b6-d5b0-47ab-a57d-ab1fede836b4",
  "secret" : "927741"
}
```
> **Ledger Changed**

```shell
{
  "owner" : "5be950c7-ef99-43d7-a0c7-96da0106a68d",
  "ledger_id" : "5ca22664-edd8-443e-9c56-5bb9a1c79a79",
  "type" : "ledger-changed",
  "secret" : "927741",
  "transaction_id" : "5d0385b2-b496-4396-9f36-f4f0c14d4660",
  "created_at" : "2019-06-14T11:32:15.046Z",
  "notification_id" : "5d0385bf-0409-48a2-8906-2d6d106e0372",
}
```

> **Entity FW Quarantine**

```shell
{
  "transaction_id" : "5d0385b5-1ffe-4039-8494-0163a51e2e21",
  "owner" : "5be950c7-ef99-43d7-a0c7-96da0106a68d",
  "type" : "entity-fw-quarantine",
  "created_at" : "2019-06-14T11:32:05.194Z",
  "notification_id" : "5d0385b5-3aa7-4b9a-af0a-93f1d7fd9b73",
  "secret" : "927741"
}
```
> **Firewall Finished**

```shell
{
  "beneficiary_id" : "5d0385af-20d6-4a9b-9578-191752f4a585",
  "beneficiary_status" : "beneficiary-status-quarantine",
  "owner" : "5be950c7-ef99-43d7-a0c7-96da0106a68d",
  "type" : "beneficiary-firewall-finished",
  "created_at" : "2019-06-14T11:31:59.695Z",
  "notification_id" : "5d0385af-6779-45f0-96e6-54592efb9487",
  "secret" : "927741"
}
```

> **Card Awaiting Activation, Activated, and Card Suspended**

```shell
{
  "card_id" : "5d038588-5006-4eaf-8235-c67676b77db5",
  "owner" : "5be950c7-ef99-43d7-a0c7-96da0106a68d",
  "type" : "card-awaiting-activation",
  "created_at" : "2019-06-14T11:31:21.944Z",
  "notification_id" : "5d038589-e16a-460d-84ee-9f9b12064fed",
  "secret" : "927741"
}

{
  "card_id" : "5d038588-5006-4eaf-8235-c67676b77db5",
  "owner" : "5be950c7-ef99-43d7-a0c7-96da0106a68d",
  "type" : "card-activated",
  "created_at" : "2019-06-14T11:31:32.568Z",
  "notification_id" : "5d038594-5de6-4402-9ba8-94628b3c546f",
  "secret" : "927741"
}

{
  "card_id" : "5d038588-5006-4eaf-8235-c67676b77db5",
  "owner" : "5be950c7-ef99-43d7-a0c7-96da0106a68d",
  "type" : "card-suspended",
  "created_at" : "2019-06-14T11:31:45.891Z",
  "notification_id" : "5d0385a1-2d64-4498-9e28-c250513f13e0",
  "secret" : "927741"
}
```
> **Transaction declined**

```shell
{
  "transaction_id" : "5d03851d-013a-41b5-821b-4071e743061a",
  "owner" : "5be950c7-ef99-43d7-a0c7-96da0106a68d",
  "type" : "transaction-declined",
  "created_at" : "2019-06-14T11:29:33.912Z",
  "notification_id" : "5d03851d-86e4-410e-814d-8b68d51ce55b",
  "secret" : "927741"
}
```



- **All** notifications will include:
  - `owner` - your customer id
  - `type` - the type of notification
  - `created_at` - when the notification was created

### Notifications

| **Type**                          | **Description**                          |
|:----------------------------------|:-----------------------------------------|
| `:entity-ready-to-use`            | An entity is created successfully and ready to use <br>• the notification will  include the id of the entity |
| `:ledger-changed`                 | A ledger has changed been credited or debited (includes if it is credited due to a send-money transaction being declined) <br>• the notification will include the `ledger_id` of the changed ledger and the `transaction_id` of the transaction that caused the change |
| `:enduser-firewall-finished`      | A Compliance Firewall check is performed on an enduser entity <br>• the notification will include the id of the enduser and their status |
| `:beneficiary-firewall-finished`  | A Compliance Firewall check is performed on a beneficiary entity <br>• the notification will include the id of the beneficiary and their status |
| `:entity-fw-quarantine`           | An entity has been put into quarantine for breaching a firewall rule <br>• the notification will  include the id of the entity that hit the quarantine |
| `:entity-fw-warning`              |                                          |
| `:entity-fw-missing-data`         | An entity has not been created successfully due to missing firewall data |
| `:transaction-declined`           | A transaction was declined (we will have updated the `transaction_status`) <br> The notification will include the `transaction_id` of the declined transaction which you can fetch to see the `failure_reasons`. |
| `:card-awaiting-activation`       | A new card has been created and awaiting activation. <br> the `card_id` will be included in the notification. |
| `:card-activated`                 | A card has been activated. <br> the `card_id` will be included in the notification. |
| `:card-failed-to-activate`        | A card failed to activate. <br> the `card_id` will be included in the notification. |
| `:card-suspended`                 | A card was suspended <br> the `card_id` will be included in the notification. |
| `:card-failed-to-suspend`         | A card suspension call failed. <br> the `card_id` will be included in the notification. |
| `:card-failed`                    | A card was not created successfully <br> the `card_id` will be included in the notification. |
| `:card-fraud-rule-breach`         | A card broke one of your fraud rules <br> the `card_id` will be included in the notification. |
| `:direct-debit-mandate-activated` | Direct debit related activity:<br> successfully created a mandate |
| `:direct-debit-mandate-failed`    | Direct debit related activity:<br> creating a mandate has failed |
| `:direct-debit-mandate-cancelled` | Direct debit related activity:<br> a mandate has been cancelled |
| `:direct-debit-payment-created`   | Direct debit related activity:<br> a mandate payment is being created |
| `:direct-debit-payment-paid`      | Direct debit related activity:<br> a mandate payment is successful |
| `:direct-debit-payment-failed`    | Direct debit related activity:<br> a mandate payment has failed |
| `:direct-debit-payment-refunded`  | Direct debit related activity:<br> payment has been refunded |
| `:webhook-test`                   | The notification sent upon the successful creation of the webhook or updating of a secret. |


## Set up a Webhook
> **Example Request**

```shell
--request POST "https://playlive.railsbank.com/v1/customer/webhook"
--header 'Content-Type: application/json'
--header 'Accept: application/json'
--header 'Authorization: API-Key yourapikey'
{
  "webhook_url": "https://webhook.example.net/foo",
  "webhook_secret": "some_secret_key_sent_with_every_event"
}
```

`POST "https://playlive.railsbank.com/v1/customer/webhooks"`

- This endpoint allows you to set your webhook.
- Make sure to include both the URL you want the notifications sent to, and the secret you want to use to verify that the notifications are your particular notifications.
- If the webhook is set-up successfully, the API will return a 200 HTTP response with no payload.
- When you create your webhook you will receive a notification of ` "type" : "webhook-test"`.

## Fetch your Webhook

> **Example Request**

```shell
--request GET "https://playlive.railsbank.com/v1/customer/webhook"
--header 'Content-Type: application/json'
--header 'Accept: application/json'
--header 'Authorization: API-Key yourapikey'
```

> **Example Response**

```shell
{
  "webhook_url": "https://webhook.example.net/foo",
  "webhook_secret": "some_secret_key_sent_with_every_event"
}
```
`GET "https://playlive.railsbank.com/v1/customer/webhook"`

- This endpoint allows you to fetch your webhook to see the URL and the secret.
- Because we only allow you to create one webhook at a time, you do not need to specify a UUID.

## Update your Webhook Secret

> **Example Request**

```shell
--request POST "https://playlive.railsbank.com/v1/customer/webhook/update-secret"
--header 'Content-Type: application/json'
--header 'Accept: application/json'
--header 'Authorization: API-Key yourapikey'
{
  "webhook_secret": "some_secret_key_sent_with_every_event"
}
```
`POST "https://playlive.railsbank.com/v1/customer/webhook/update-secret"`

- This endpoint allows you to update the secret attached to your webhook notifications without changing the URL they are sent to.
- This helps you to improve the security of your notifications.
- The secret is sent with every event, allowing you to verify that the events.
- There will be no response body, only the HHTP code.
- If the webhook secret is updated successfully, the API will return a 200 HTTP response with no payload.
- When you update your webhook secret you will receive a notification of ` "type" : "webhook-test"`.

## Delete your Webhook

> **Example Request**

```shell
--request DELETE "https://playlive.railsbank.com/v1/customer/webhook"
--header 'Content-Type: application/json'
--header 'Accept: application/json'
--header 'Authorization: API-Key yourapikey'
```
`DELETE "https://playlive.railsbank.com/v1/customer/webhook"`

- This simple endpoint allows you to delete your webhook.
- No notifications will be sent to the URL once you call this endpoint.
- The URL cannot be reset once deleted.
- There will be no response body, only the HHTP code.

## Fetch Failed Notifications
> **Example Request**

```shell
--request GET "https://playlive.railsbank.com/v1/customer/webhook/failed-to-deliver"
--header 'Content-Type: application/json'
--header 'Accept: application/json'
--header 'Authorization: API-Key yourapikey'
```

> **Example Response**

```shell
[
    {
        "enduser_id": "5aa9032a-fd2a-4e03-a901-3930a1e2609a",
        "enduser_status": "enduser-status-ok",
        "owner": "5a30ffea-fe4b-4bd3-8a09-4798130dcd8d",
        "type": "enduser-firewall-finished",
        "created_at": "2018-03-14T11:10:36.472Z",
        "notification_id": "5b329ff8-3558-438c-ada8-6b744e81ba80"
    },
    {
        "enduser_id": "5aa90ecc-f581-4441-b788-c2cdc12a7654",
        "enduser_status": "enduser-status-ok",
        "owner": "5a30ffea-fe4b-4bd3-8a09-4798130dcd8d",
        "type": "enduser-firewall-finished",
        "created_at": "2018-03-14T12:00:14.992Z",
        "notification_id": "5b329ff8-1658-486c-bcc9-f7e0e1779e3d"
    },
    {
        "ledger_id": "5aa91194-4872-4019-961d-560a705cca80",
        "transaction_id": "5aa91326-86df-4d22-9ef5-5068260881ff",
        "owner": "5a30ffea-fe4b-4bd3-8a09-4798130dcd8d",
        "type": "ledger-changed",
        "created_at": "2018-03-14T12:18:56.727Z",
        "notification_id": "5b329ff8-092e-4fe8-8fae-c65af4771ef5"
    },
    {
        "ledger_id": "5aa91187-8bab-4eed-b41e-a262d1be50f1",
        "transaction_id": "5aa91326-86df-4d22-9ef5-5068260881ff",
        "owner": "5a30ffea-fe4b-4bd3-8a09-4798130dcd8d",
        "type": "ledger-changed",
        "created_at": "2018-03-14T12:18:56.727Z",
        "notification_id": "5b329ff8-1822-47c7-977b-ac9b4b8ab2aa"
    },
]
```

`GET "https://playlive.railsbank.com/v1/customer/webhook/failed-to-deliver"`

- This endpoint allows you to fetch webhook notification that, for some reason, were not delivered to the URL.
- This won't respond with notifications that weren't delivered because you didn't have a webhook set up.

### Parameters

| Parameter                        | Description                               |
|:---------------------------------|:------------------------------------------|
| `starting_at_date` <br> _string_ | Any notifications created before this date will not be shown in the array <br> _YYYY-MM-DD_ |
| `items_per_page` <br> _integer_  | The number of items returned in each page. A hyperlink to the next page will be found in the response headers |
| `last_seen_id` <br> _string_     | The notifications returned will have all been created after the notification whose UUID is the `last_seen_id` |
| `from_date` <br> _string_        | The date that the list of notifications will start from |
| `end_date` <br> _string_         | The date that the list of notifications will end at |

## Fetch All Notifications
> **Example Request**

```shell
--request GET "https://playlive.railsbank.com/v1/customer/webhook/notifications?from_date=2018-09-19&to_date=2018-09-30"
--header 'Content-Type: application/json'
--header 'Accept: application/json'
--header 'Authorization: API-Key yourapikey'
```

> **Example Response**

```shell
[
    {
        "ledger_id": "5ba3a0a7-f04d-4314-9178-d0331b488862",
        "transaction_id": "5ba3a0d8-e862-4d5b-8c1e-9a8f9c471aea",
        "owner": "5a30ffea-fe4b-4bd3-8a09-4798130dcd8d",
        "type": "ledger-changed",
        "created_at": "2018-09-20T13:30:01.419Z",
        "notification_id": "5ba3a0d9-cfc7-4a36-92a9-b315e8ba61b5"
    },
    {
        "ledger_id": "5ba3a0cc-4bf0-4d62-b493-092f2aa71295",
        "transaction_id": "5ba3a0d8-e862-4d5b-8c1e-9a8f9c471aea",
        "owner": "5a30ffea-fe4b-4bd3-8a09-4798130dcd8d",
        "type": "ledger-changed",
        "created_at": "2018-09-20T13:30:01.419Z",
        "notification_id": "5ba3a0d9-88fb-41e8-8ce5-7eabcd356e82"
    },
    {
        "beneficiary_id": "5ba3a0e3-c642-4e50-af9b-dd2c024f1a73",
        "beneficiary_status": "beneficiary-status-ok",
        "owner": "5a30ffea-fe4b-4bd3-8a09-4798130dcd8d",
        "type": "beneficiary-firewall-finished",
        "created_at": "2018-09-20T13:30:12.077Z",
        "notification_id": "5ba3a0e4-2847-40f6-a93a-0356d81af626"
    },
    {
        "beneficiary_id": "5ba3a0e3-c642-4e50-af9b-dd2c024f1a73",
        "owner": "5a30ffea-fe4b-4bd3-8a09-4798130dcd8d",
        "type": "entity-ready-to-use",
        "created_at": "2018-09-20T13:30:12.095Z",
        "notification_id": "5ba3a0e4-dcf7-4a0e-ae68-b0ad62929250"
    }
]
```

`GET "https://playlive.railsbank.com/v1/customer/webhook/notifications"`

- This endpoint returns all the notifications created by the API due to actions you have taken.
- This includes notifications sent to a webhook or not sent to a webhook (if you did't set one up).

### Parameters

| Parameter                        | Description                               |
|:---------------------------------|:------------------------------------------|
| `starting_at_date` <br> _string_ | Any notifications created before this date will not be shown in the array <br> _YYYY-MM-DD_ |
| `items_per_page` <br> _integer_  | The number of items returned in each page. A hyperlink to the next page will be found in the response headers |
| `last_seen_id` <br> _string_     | The notifications returned will have all been created after the notification whose UUID is the `last_seen_id` |
| `from_date` <br> _string_        | The date that the list of notifications will start from |
| `end_date` <br> _string_         | The date that the list of notifications will end at |
