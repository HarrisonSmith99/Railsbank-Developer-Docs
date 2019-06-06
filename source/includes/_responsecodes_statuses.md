# Response Codes and Statuses

## HTTPD Status Codes

We consistently use [standardized HTTPD Status](https://tools.ietf.org/html/rfc7231#section-6) codes.

| Code | Text                  | Description                                   |
|:-----|:----------------------|:----------------------------------------------|
| 200  | OK                    | Success!                                      |
| 202  | Accepted              | Accepted by us, but processing is not yet complete |
| 304  | Not Modified          | There was no new data to return.              |
| 400  | Bad Request           | The request was invalid or cannot be otherwise served. An accompanying error message will explain further. Requests without authentication are considered invalid and will yield this response. |
| 401  | Unauthorized          | Missing or incorrect authentication credentials. |
| 403  | Forbidden             | The request is understood, but it has been refused or access is not allowed. An accompanying error message will explain why. |
| 404  | Not Found             | The URI requested is invalid or the resource requested, such as a user, does not exist. Also returned when the requested format is not supported by the requested method. |
| 410  | Gone                  | This resource is gone. Used to indicate that an API endpoint has been turned off. |
| 429  | Too Many Requests     | Returned when a request cannot be served due to rate limiting |
| 500  | Internal Server Error | Something is broken. Please post to the developer forums with additional details of your request, so the Railsbank team can investigate. |
| 502  | Bad Gateway           | Railsbank is down or being upgraded.          |
| 503  | Service Unavailable   | The Railsbank servers are up, but overloaded with requests. Try again later. |
| 504  | Gateway timeout       | The Railsbank servers are up, but the request couldn’t be serviced due to some failure within our stack. Try again later. |


## Error Messages

When the Railsbank API returns error messages, it does so in JSON format. For example, an error might look like this:

``{"errors":[{"message":"Sorry, that page does not exist","code":34}]}``

## Status Codes

The statuses for the various parts of Railsbank will normally follow a standardized pattern where `foo-status-ok` or `foo-status-accepted` are good. Other states are not there yet and may be worthy of investigation (depending on your desired outcome).

These lists detail the states and an outline of the order things pass through the Railsbank State Machine

### Sending money, Inter-Ledger, and Convert-and-Send (FX)
| Message                           | Description                              |
|:----------------------------------|:-----------------------------------------|
| `transaction-status-missing-data` | Extra data required to complete the transaction. For instance, if the beneficiary does not have enough account information for the transaction to be completed. |
| `transaction-status-pending`      | Transaction is being processed. We do not have a `/wait` parameter for transactions as they can be processed for a longer period of time. We advise using a webhook to notify you when processing has been completed. |
| `transaction-status-quarantine`   | The transaction has fallen into your customer quarantine queue. You will receive a `type: entity-fw-quarantine` webhook and a `type: transaction-firewall-finished` webhook. **We recommend setting your own firewall rules to prevent any of your transactions falling into the partner quarantine queue and therefore out of your control.** |
| `transaction-status-accepted`     | The transaction has been accepted by the Railsbank platform and the funds have left the ledger. YOu will receive a `type: ledger-changed` webhook. Please note that this doesn't mean they have arrived at the beneficiary account: due to regulation, we cannot inform customers when a transaction lands in the partner quarantine queue, so if a transaction is `accepted`, it may be in the partner quarantine and it can be `declined`. If it is declined you will receive a `type: transaction-declined` webhook. |
| `transaction-status-declined`     | Transaction has ben declined. When you fetch the transaction you will see the `failure_reasons`, for instance: `insufficient-funds`. You will receive a `type: transaction-declined` webhook. |


### Receiving money
- `transaction-status-error` - There has been an error receiving the transaction.
- `transaction-status-pending` - Transaction is being processed. We do not have a `/wait` parameter for transactions as they can be processed for a longer period of time. We advise using a webhook to notify you when processing has been completed.
- `transaction-status-quarantine` - The transaction has fallen into your customer quarantine queue. You will receive a `type: entity-fw-quarantine` webhook.
- `transaction-status-accepted` - The transaction has been accepted by the Railsbank platform and the funds have left the ledger. YOu will receive a `type: ledger-changed` webhook. Please note that this doesn't mean they have arrived at the beneficiary account: due to regulation, we cannot inform customers when a transaction lands in the partner quarantine queue, so if a transaction is `accepted`, it may be in the partner quarantine and it can be `declined`. If it is declined you will receive a `type: transaction-declined` webhook.
- `transaction-status-declined` - Transaction has ben declined. When you fetch the transaction you will see the `failure_reasons`, for instance: `insufficient-funds`. You will receive a `type: transaction-declined` webhook.

### Endusers

- `enduser-status-missing-data` - Extra data is required for the enduser to be created, for instance, the name of the enduser.
- `enduser-status-pending` - The enduser is being processed. To avoid fetching an enduser in this state, use our `/wait` parameter.
- `enduser-status-quarantine` - The enduser has broken a customer firewall rule and fallen into the customer quarantine queue. You will receive a `type: entity-fw-quarantine` webhook and a `type: enduser-firewall-finished` webhook.
- `enduser-status-ok` - The enduser is ready to use: to add ledgers and beneficiaries to and send and receive transactions. You will receive a `type: entity-ready-to-use` webhook and a `type: enduser-firewall-finished` webhook if you have set and firewall rules up.
- `enduser-status-declined`- The enduser has been declined by our system. This is usually because the enduser has broken a partner firewall rule and been rejected by our compliance team, for instance, if they are from a country we don't deal with, like North Korea.


### Beneficiaries
- `beneficiary-status-missing-data` - Extra data is required for the beneficiary to be created, for instance, the `uk_sort_code` of the beneficiary account.
- `beneficiary-status-pending` - The beneficiary is being processed. To avoid fetching an beneficiary in this state, use our `/wait` parameter.
- `beneficiary-status-quarantine` - The beneficiary has broken a customer firewall rule and fallen into the customer quarantine queue. You will receive a `type: entity-fw-quarantine` webhook and a `type: beneficiary-firewall-finished` webhook.
- `beneficiary-status-ok` - The beneficiary is ready to receive transactions. You will receive a `type: entity-ready-to-use` webhook and a `type: beneficiary-firewall-finished` webhook if you have set and firewall rules up.
- `beneficiary-status-declined`- The beneficiary has been declined by our system. This is usually because the beneficiary has broken a partner firewall rule and been rejected by our compliance team, for instance, if they are from a country we don't deal with, like North Korea.

### Ledgers
- `ledger-status-missing-data` - Extra data required to create the ledger. Often, this occurs if the holder of the ledger is missing the data required to generate a ledger.
- `ledger-status-pending` - The ledger is being created. To avoid fetching an ledger in this state, use our `/wait` parameter.
- `ledger-status-error` - The ledger has not been created properly. This occurs if the proposed holder of the ledger is in a state in which they are unable to hold the ledger, for instance, pending.
- `ledger-status-ok` - The ledger is ready to receive and send money to and from. You will receive a `type: entity-ready-to-use` webhook.
- `ledger-status-declined` - The ledger has been declined. For instance if the `"ledger_t_and_cs_country_of_jurisdiction":` is not acceptable to our compliance team.

### Cards
- `card-status-created` - The card has been successfully created.
- `card-status-failed` - The card has failed to create for one of the reasons below. You will receive a `type: card-failed` webhook.
  - Possible failure reasons:
    - `card-not-active` - is set when card status is not equal to active
    - `contact-support` - is set when we get recommendation to decline from paymentology
    - `fx-issue` - is returned in one of these cases:
    contributed-price-not-set, contributed-price-suspended or contributed-price-expired
    - `card-rules-breached` - added if any or card rules are breached
    - `partner-error` - happens when we have insufficient-funds-float-ledger
    - `insufficient-funds` - when we have insufficient funds in ~customer~ card ledge
- `card-status-suspending` - The card is in the process of being suspended. Use the `/wait` parameter to wait until it is in a REST state (in this case, suspended).
- `card-status-suspended` - The card has been suspended by the enduser. You will receive a `type: card-suspended` webhook.
- `card-status-awaiting-activation` - The card has been created but not activated. You will receive a `type: card-awaiting-activation` webhook.
- `card-status-activating` - The card is being activated. Use the `/wait` parameter to wait until it is in a REST state (in this case, activated).
- `card-status-active` - The card is active and ready to be used. You will receive a `type: card-activated` webhook.
