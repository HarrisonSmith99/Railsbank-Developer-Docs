# Authentication

> To authorize, use this code:

```shell
# With shell, you can just pass the correct header with each request
curl "api_endpoint_here"
  -H "Authorization: API-Key <<your_API_Key>>"
```

Railsbank uses API keys to allow access to the API. You can register for an API key on our [website](http://railsbank.com).

Railsbank expects the API key to be included in all API requests to the server in a header that looks like the following:

`Authorization: API-Key <<your_API_key>>`
