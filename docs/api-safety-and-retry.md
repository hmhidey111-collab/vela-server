# API safety and retry guidance

## Signed-XDR submission

Clients must sign only the transaction returned by the payment-intent flow
after checking the sender, recipient, asset, amount, memo, network, and
timeout. The server validates the signed envelope against the persisted
payment intent before forwarding it to Horizon. Clients must never send
secret keys, seed phrases, or unsigned private data to the API.

Use testnet-only accounts for development and inspect the returned public
error code rather than parsing provider-specific text.

## Rate limits and retries

The server applies the configured throttler to API requests. Clients should
honor `429` responses and use bounded exponential backoff with jitter. Retry
only idempotent reads or requests carrying the same documented request ID;
never blindly retry a signed payment submission without first reading its
current payment status.

Network errors and `5xx` responses may be retried with a bounded budget. A
successful response, a validation error, an authentication error, or a
payment state conflict should be surfaced to the user instead of retried.

Live Horizon/RPC checks are explicit workflow-dispatch operations and are not
part of pull-request verification.
