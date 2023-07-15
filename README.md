# status-list.vc

[Read the spec](https://github.com/w3c/vc-status-list-2021).

- https://status-list.vc
- https://status-list.vc/0
- https://status-list.vc/1
- https://status-list.vc/.well-known/did.json

A website for testing W3C Verifiable Credentials.

```sh
dig status-list.vc +nostats +nocomments +nocmd
```

## Create Private Signing Key

```sh
transmute key generate \
--alg ES384 \
--output private.signing.jwk.json
```

## Export Public Verification Key

```sh
transmute key export \
--input  private.signing.jwk.json \
--output public.verifying.jwk.json
```

## Create Issuer

```sh
transmute w3c controller create \
--issuer-key ./public.verifying.jwk.json \
--controller .well-known/did.json
```

(... find replace did:jwk -> did:web )

## Create a Status List Credential

```sh
transmute w3c status-list create \
--id https://status-list.vc/1 \
--issuer did:web:status-list.vc \
--valid-from 2032-07-15T12:00:00.992Z \
--purpose revocation \
--length 8 \
--claimset 1/claimset.json
```

## Secure a Status List Credential

```sh
transmute w3c credential issue \
--issuer-key private.signing.jwk.json \
--issuer-kid did:web:status-list.vc#0 \
--claimset  1/claimset.json \
--verifiable-credential 1/index.json
```

## Update a Status

```sh
transmute w3c status-list update \
--issuer-key private.signing.jwk.json \
--verifiable-credential 1/index.json \
--index 0 \
--status true
```

## Add a status to credential

```sh
transmute w3c status-list add \
--id https://status-list.vc/1 \
--type StatusList2021Entry \
--purpose revocation \
--index 0 \
--claimset 0/claimset.json
```

## Issue a credential with a status

```sh
transmute w3c credential issue \
--issuer-key private.signing.jwk.json \
--issuer-kid did:web:status-list.vc#0 \
--claimset  0/claimset.json \
--verifiable-credential 0/index.json
```

## Update multiple statuses

```sh
transmute w3c status-list update \
--issuer-key private.signing.jwk.json \
--verifiable-credential 0/index.json \
--index 0 \
--status false
```

```sh
transmute w3c status-list update \
--issuer-key private.signing.jwk.json \
--verifiable-credential 1/index.json \
--index 1 \
--status true
```

## Validate multiple statuses

```sh
transmute w3c credential validate \
--verifiable-credential 0/index.json
```

If verifiable, this will product the following validation output, which can be further processed with policies.

```json
{
  "issuer": {
    "kid": "urn:ietf:params:oauth:jwk-thumbprint:sha-256:0qeohpP4Y41iaeG-JBBSD59HfLXZf4Ki6WnANozOeFM",
    "kty": "EC",
    "crv": "P-384",
    "alg": "ES384",
    "x": "ruGe2V1Eq5TW_lZquDda-pse0bAWKSFwb-UNeXLSuMnwNOFghVxgZIvgDoZRdLfb",
    "y": "mjhbxan_hDnNP2tlIfYf_zGvfZ174qAWwWOlYiHB62KJu9Y8IQ7CiL425WwQ3aVK"
  },
  "credentialStatus": {
    "valid": true,
    "https://status-list.vc/0#0": {
      "suspension": false,
      "list": {
        "@context": [
          "https://www.w3.org/ns/credentials/v2"
        ],
        "id": "https://status-list.vc/0",
        "type": [
          "VerifiableCredential",
          "StatusList2021Credential"
        ],
        "issuer": "did:web:status-list.vc",
        "validFrom": "2032-07-15T12:00:00.992Z",
        "credentialStatus": [
          {
            "id": "https://status-list.vc/0#0",
            "type": "StatusList2021Entry",
            "statusPurpose": "suspension",
            "statusListIndex": "0",
            "statusListCredential": "https://status-list.vc/0"
          },
          {
            "id": "https://status-list.vc/1#1",
            "type": "StatusList2021Entry",
            "statusPurpose": "revocation",
            "statusListIndex": "1",
            "statusListCredential": "https://status-list.vc/1"
          }
        ],
        "credentialSubject": {
          "id": "https://status-list.vc/0",
          "type": "StatusList2021",
          "statusPurpose": "suspension",
          "encodedList": "H4sIAAAAAAAAA2MAAI3vAtIBAAAA"
        }
      }
    },
    "https://status-list.vc/1#1": {
      "revocation": true,
      "list": {
        "@context": [
          "https://www.w3.org/ns/credentials/v2"
        ],
        "id": "https://status-list.vc/1",
        "type": [
          "VerifiableCredential",
          "StatusList2021Credential"
        ],
        "issuer": "did:web:status-list.vc",
        "validFrom": "2032-07-15T12:00:00.992Z",
        "credentialSubject": {
          "id": "https://status-list.vc/1#list",
          "type": "StatusList2021",
          "statusPurpose": "revocation",
          "encodedList": "H4sIAAAAAAAAAzsAAD0tZkkBAAAA"
        }
      }
    }
  }
}
```