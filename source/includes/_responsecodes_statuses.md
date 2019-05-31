# Response Codes and Statuses

## HTTPD Status Codes

We consistently use [standardized HTTPD Status](https://tools.ietf.org/html/rfc7231#section-6) codes.

| Code | Text |	Description |
| ---- | ---- | ---- |
| 200 |	OK |	Success! |
| 202 |	Accepted |	Accepted by us, but processing is not yet complete |
| 304 |	Not Modified |	There was no new data to return. |
| 400 |	Bad Request |	The request was invalid or cannot be otherwise served. An accompanying error message will explain further. Requests without authentication are considered invalid and will yield this response. |
| 401 |	Unauthorized |	Missing or incorrect authentication credentials. |
| 403 |	Forbidden | The request is understood, but it has been refused or access is not allowed. An accompanying error message will explain why. |
| 404 | Not Found | The URI requested is invalid or the resource requested, such as a user, does not exist. Also returned when the requested format is not supported by the requested method. |
| 410 | Gone | This resource is gone. Used to indicate that an API endpoint has been turned off. |
| 429 | Too Many Requests |	Returned when a request cannot be served due to rate limiting |
| 500 |	Internal Server Error |	Something is broken. Please post to the developer forums with additional details of your request, so the Railsbank team can investigate. |
| 502 |	Bad Gateway |	Railsbank is down or being upgraded. |
| 503 |	Service Unavailable |	The Railsbank servers are up, but overloaded with requests. Try again later. |
| 504 |	Gateway timeout |	The Railsbank servers are up, but the request couldn’t be serviced due to some failure within our stack. Try again later. |


## Error Messages

When the Railsbank API returns error messages, it does so in JSON format. For example, an error might look like this:

``{"errors":[{"message":"Sorry, that page does not exist","code":34}]}``

## Status codes for various parts of Railsbank

The statuses for the various parts of Railsbank will normally follow a standardized pattern where `foo-status-ok` or `foo-status-accepted` are good. Other states are not there yet and may be worthy of investigation (depending on your desired outcome).

These lists detail the states and an outline of the order things pass through the Railsbank State Machine

### Sending money
- transaction-status-missing-data
- transaction-status-pending
- transaction-status-quarantine
- **transaction-status-accepted**
- transaction-status-declined
- transaction-status-approved

### Receiving money
- transaction-status-error
- transaction-status-pending
- transaction-status-quarantine
- **transaction-status-accepted**
- transaction-status-declined
- transaction-status-approved

### Interledger
- transaction-status-missing-data
- transaction-status-pending
- transaction-status-quarantine
- **transaction-status-accepted**
- transaction-status-declined

### Foreign Exchange
- transaction-status-error
- transaction-status-missing-data
- transaction-status-pending
- transaction-status-quarantine
- **transaction-status-accepted**
- transaction-status-declined

### Endusers

- enduser-status-missing-data
- enduser-status-pending
- enduser-status-quarantine
- **enduser-status-ok**
- enduser-status-declined


### Beneficiaries
- beneficiary-status-missing-data
- beneficiary-status-pending
- beneficiary-status-quarantine
- **beneficiary-status-ok**
- beneficiary-status-declined

### Ledgers
- ledger-status-pending
- ledger-status-error
- ledger-status-missing-data
- **ledger-status-ok**
- ledger-status-declined
