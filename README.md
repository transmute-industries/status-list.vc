# status-list.vc

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

## Validate a credential with status

```sh
transmute w3c credential validate \
--verifiable-credential 0/index.json
```

... change status ...

... validate again ...