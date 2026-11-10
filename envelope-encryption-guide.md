# Envelope Encryption with Vaultline Key Service

*Sample documentation by Kumar Gaurav. Vaultline is a fictional product created for portfolio purposes.*

## About this guide

This guide shows application developers how to protect data at rest with envelope encryption by using the Vaultline Key Service REST API (v2). By the end, you will be able to encrypt and decrypt a file, rotate a master key, and handle common errors.

**Audience:** Backend developers familiar with REST APIs and symmetric encryption. **Time to complete:** 20 minutes. **API version:** 2.4

## How envelope encryption works

Instead of sending your data to the key service, you encrypt it locally with a short-lived *data key*. Vaultline protects that data key by *wrapping* (encrypting) it under a *master key* that never leaves the service.

1. Your application requests a data key from Vaultline.
2. Vaultline returns the data key in two forms: plaintext and wrapped.
3. Your application encrypts the data with the plaintext key, then discards that key.
4. Your application stores the ciphertext together with the wrapped key.
5. To decrypt, your application sends the wrapped key to Vaultline and receives the plaintext key back.

This approach keeps large payloads inside your environment, limits exposure of the master key, and lets you rotate the master key without re-encrypting your data.

## Before you begin

Make sure you have:

- A Vaultline account with a master key. This guide uses a key named `payments-prod`.
- An API token with the `keys:generate` and `keys:decrypt` permissions.
- Python 3.9 or later, with the required libraries installed: `pip install requests cryptography`
- Your token stored in an environment variable named `VAULTLINE_TOKEN`.

> **Warning:** Never hard-code API tokens in source files or commit them to version control. Use an environment variable or a secrets manager.

## Step 1: Generate a data key

Send a `POST` request to the data-keys endpoint of your master key.

```bash
curl -X POST https://api.vaultline.example.com/v2/keys/payments-prod/data-keys \
  -H "Authorization: Bearer $VAULTLINE_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"key_spec": "AES_256"}'
```

A successful request returns `201 Created`:

```json
{
  "key_id": "payments-prod",
  "key_version": 3,
  "plaintext_key": "q3Jt...base64...",
  "wrapped_key": "AQIDAHh...base64...",
  "request_id": "req_8f2a91"
}
```

Both key values are Base64-encoded. Record `key_version`, because you need it if you rotate keys later.

## Step 2: Encrypt your data locally

Use the plaintext key to encrypt your data with AES-256-GCM, then remove the key from memory.

```python
import os, base64, json, requests
from cryptography.hazmat.primitives.ciphers.aead import AESGCM

API = 'https://api.vaultline.example.com/v2'
HEADERS = {'Authorization': f"Bearer {os.environ['VAULTLINE_TOKEN']}"}

# Request a data key
resp = requests.post(
    f'{API}/keys/payments-prod/data-keys',
    headers=HEADERS,
    json={'key_spec': 'AES_256'},
    timeout=10,
)
resp.raise_for_status()
key = resp.json()

# Encrypt locally
plaintext_key = base64.b64decode(key['plaintext_key'])
nonce = os.urandom(12)  # Never reuse a nonce with the same key
data = open('invoice.pdf', 'rb').read()
ciphertext = AESGCM(plaintext_key).encrypt(nonce, data, b'invoice-v1')

# Discard the plaintext key
del plaintext_key
```

> **Note:** Python does not guarantee that deleting a variable erases it from memory. For high-assurance workloads, use a library that supports secure memory handling.

## Step 3: Store the encrypted package

Save the ciphertext, nonce, wrapped key and key version together. You need all four to decrypt later.

```python
package = {
    'ciphertext': base64.b64encode(ciphertext).decode(),
    'nonce': base64.b64encode(nonce).decode(),
    'wrapped_key': key['wrapped_key'],
    'key_version': key['key_version'],
}
json.dump(package, open('invoice.enc.json', 'w'))
```

## Step 4: Decrypt your data

Send the wrapped key to Vaultline to recover the plaintext key.

```python
package = json.load(open('invoice.enc.json'))

resp = requests.post(
    f'{API}/keys/payments-prod/decrypt',
    headers=HEADERS,
    json={'wrapped_key': package['wrapped_key']},
    timeout=10,
)
resp.raise_for_status()

plaintext_key = base64.b64decode(resp.json()['plaintext_key'])
data = AESGCM(plaintext_key).decrypt(
    base64.b64decode(package['nonce']),
    base64.b64decode(package['ciphertext']),
    b'invoice-v1',
)
```

The additional data (`invoice-v1`) must match the value used during encryption, or decryption fails.

## API reference

### Generate a data key

`POST /v2/keys/{key_id}/data-keys`

| Parameter | Type | Required | Description |
| --- | --- | --- | --- |
| `key_id` | string (path) | Yes | Name of the master key. |
| `key_spec` | string (body) | Yes | Data key type. Supported values: `AES_128`, `AES_256`. |

### Decrypt a wrapped key

`POST /v2/keys/{key_id}/decrypt`

| Parameter | Type | Required | Description |
| --- | --- | --- | --- |
| `key_id` | string (path) | Yes | Name of the master key that wrapped the data key. |
| `wrapped_key` | string (body) | Yes | Base64-encoded wrapped key from the generate response. |

### Rotate a master key

`POST /v2/keys/{key_id}/rotate`

Creates a new key version. New data keys use the new version. Earlier versions stay available for decryption, so existing data keeps working.

```json
{ "key_id": "payments-prod", "key_version": 4, "status": "active" }
```

## Error codes

| HTTP status | Error code | Meaning | Action |
| --- | --- | --- | --- |
| 400 | `invalid_key_spec` | The `key_spec` value is not supported. | Use `AES_128` or `AES_256`. |
| 401 | `invalid_token` | The token is missing, expired or malformed. | Request a new token. |
| 403 | `permission_denied` | The token lacks the required permission. | Add `keys:generate` or `keys:decrypt`. |
| 404 | `key_not_found` | The master key does not exist. | Check the `key_id` spelling. |
| 409 | `key_disabled` | The master key is disabled. | Re-enable the key, then retry. |
| 429 | `rate_limited` | Too many requests. | Retry with exponential backoff. |
| 503 | `service_unavailable` | Temporary service issue. | Retry after a short delay. |

## Security best practices

- **Rotate master keys at least every 12 months,** or immediately if you suspect exposure.
- **Use a unique data key for each object.** Do not reuse one data key across files.
- **Grant least privilege.** Give services that only encrypt the `keys:generate` permission, not `keys:decrypt`.
- **Log key operations,** but never log plaintext keys or decrypted data.
- **Set request timeouts** and retry failed calls with backoff, so key-service issues do not stall your application.

## Troubleshooting

| Symptom | Likely cause | Fix |
| --- | --- | --- |
| `InvalidTag` error on decrypt | The additional data, nonce or ciphertext does not match what was used to encrypt. | Confirm all three values are unchanged and stored together. |
| `403 permission_denied` on decrypt | The token only has `keys:generate`. | Issue a token with `keys:decrypt`. |
| Old files fail after rotation | The old key version was disabled or deleted. | Re-enable the old version, or re-wrap the data key. |

## Release notes: version 2.4

**New**

- Added the `AES_128` key specification for lower-latency workloads.
- Added `request_id` to all responses to simplify support requests.

**Changed**

- The `decrypt` endpoint now returns `409 key_disabled` instead of `404` when a key is disabled.

**Deprecated**

- The `/v1/encrypt` endpoint will be removed on 31 March 2027. Migrate to envelope encryption as described in this guide.
