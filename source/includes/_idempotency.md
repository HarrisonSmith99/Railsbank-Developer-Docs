Transient errors can happen, particulary where networking issues may be involved.

The current best-practice on this is to use idempotency keys. As an introduction to these, we recommend reading Stripe's [idempotency blog post](https://stripe.com/blog/idempotency). It's well worth the seven minutes read.

Using this technique, you'll be able to send the same request over and over again, safe that (until the key expires 24 hours after first seen) the action won't be repeated.

Simply add an `Idempotency-Key` header with a unique value to each `POST`/`PUT`/`DELETE` call you make to take advantage of this method.

### Additional notes
 * In cases where your first call with same idempotency key is still being calculated, you'll see a returned status of `202`.
 * If the response to this call contains information like an API key (which you probably want to avoid), the returned status for the call with the same idempotency key will be `400`.
