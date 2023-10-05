# did-twin
Twin DID Method

[![](https://img.shields.io/badge/Status-Draft-orange.svg?style=flat-square)](#Status)

This document specifies the Twin method
[DID Method](https://w3c-ccg.github.io/did-spec/#specific-did-method-schemes) [`did:twin`].

This specification conforms to the requirements specified in the DID specification currently published by the W3C
Credentials Community Group. For more information about DIDs and DID method specifications, please see
[DID Primer](https://git.io/did-primer) and [DID Specification](https://w3c-ccg.github.io/did-spec).

## Method Name

The namestring that shall identify this DID method is: `twin`

A DID that uses this method **MUST** begin with the following prefix: `did:twin:`. Per the DID specification,
this prefix MUST be in lowercase. The format of remainder of the DID, after this prefix, is specified below in
the section on [Method Specific Identifiers](#method-specific-identifiers).

## Method Specific Identifiers

Twin DIDs conform with [the Generic DID Scheme](https://w3c-ccg.github.io/did-spec/#the-generic-did-scheme)
described in the DID spec. The format of the `twin-specific-idstring` is described below in
[ABNF](https://tools.ietf.org/html/rfc5234):

```
twin-did               = "did:twin:" twin-specific-idstring

twin-specific-idstring = 1*(provider ":") idstring

provider                    = 1*providerchar
providerchar                = %x61-7A / DIGIT ; 61-7A is a-z in US-ASCII

idstring                = 28*31(base58char)
base58char              = "1" / "2" / "3" / "4" / "5" / "6" / "7" / "8" / "9" / "A" / "B" / "C"
                          / "D" / "E" / "F" / "G" / "H" / "J" / "K" / "L" / "M" / "N" / "P" / "Q"
                          / "R" / "S" / "T" / "U" / "V" / "W" / "X" / "Y" / "Z" / "a" / "b" / "c"
                          / "d" / "e" / "f" / "g" / "h" / "i" / "j" / "k" / "m" / "n" / "o" / "p"
                          / "q" / "r" / "s" / "t" / "u" / "v" / "w" / "x" / "y" / "z"
```

### Generating a unique idstring

A unique `idstring` is created as follows:

1.  Generate a public/private keypair, using one of the methods in the
    [Linked Data Cryptographic Suite Registry](https://w3c-ccg.github.io/ld-cryptosuite-registry/).
2.  Hash the public key from step 1 using one of the hashing algorithms supported by
    [multihash](https://multiformats.io/multihash/).
3.  Truncate the hash from step 2 to the lower 20 bytes.
4.  Prepend the [multihash prefix](https://github.com/multiformats/multicodec/blob/master/table.csv)
    for the algorithm chosen in step 2 (the length part of a multihash is not included because length of the hashed
    value is always 20 bytes per step 3)
5.  [Base58](https://en.wikipedia.org/wiki/Base58) encode the value from step 4 using the
    [Bitcoin alphabet](https://en.bitcoinwiki.org/wiki/Base58#Alphabet_Base58).

### Example

An example Twin DID:

```
did:twin:unikbase:2PCd14L1pLMpfSfpgKe2HyYZFu2pf
```

### Providers

Twin DIDs contan a colon separated provider path as a prefix to an DID, for example:

```
did:twin:unikbase:2PCd14L1pLMpfSfpgKe2HyYZFu2pf
```

a provider name **MUST** include only lowercase letters or digits.

## CRUD Operations

### Create/Register

Twin Clients can create/register an entity in the Liquichain Network by submitting a
[Verifiable Claim](https://www.w3.org/TR/verifiable-claims-data-model) as a transaction.
The [issuer](https://www.w3.org/TR/verifiable-claims-data-model) and the
[subject](https://www.w3.org/TR/verifiable-claims-data-model) of this claim are the same DID that is being registered.

Here is an example of such a claim:

```js
{
	"@context": "https://w3id.org/security/v1",
	"id": "did:twin:unikbase:2Mm9pLRQwueo7FJUvBoDW7QKGBXTX/credentials/1",
	"type": ["Credential"],
	"issuer": "did:eth:2Mm9pLRQwueo7FJUvBoDW7QKGBXTX",
	"issued": "2018-11-15",
	"claim": {
		"id": "did:twin:unikbase:2Mm9pLRQwueo7FJUvBoDW7QKGBXTX",
		"document": {
			"@context": "https://w3id.org/did/v1",
			"id": "did:eth:2Mm9pLRQwueo7FJUvBoDW7QKGBXTX",
			"publicKey": [{
				"id": "did:eth:2Mm9pLRQwueo7FJUvBoDW7QKGBXTX#key1",
				"type": "RsaVerificationKey2018",
				"owner": "did:eth:2Mm9pLRQwueo7FJUvBoDW7QKGBXTX",
				"publicKeyPem": "-----BEGIN PUBLIC KEY...END PUBLIC KEY-----\r\n"
			}],
			"authentication": [{
				"type": "RsaSignatureAuthentication2018",
				"publicKey": "did:eth:2Mm9pLRQwueo7FJUvBoDW7QKGBXTX#key1"
			}],
			"service": [{
				"id": "did:eth:2Mm9pLRQwueo7FJUvBoDW7QKGBXTX/credentials",
				"type": "CredentialRepositoryService",
				"serviceEndpoint": "..."
			}]
		}
	},
	"signature": {
		"type": "RsaSignature2018",
		"created": "2018-11-15T21:06:01.546Z",
		"creator": "did:eth:2Mm9pLRQwueo7FJUvBoDW7QKGBXTX#key1",
		"domain": "json-ld.org",
		"nonce": "e8283e888657e23b07f9c9eaf4efe80b49eff76b",
		"signatureValue": "bd16879C...aD9AF1FaCa"
	}
}
```

The [DID Document](https://w3c-ccg.github.io/did-spec/#did-documents) representing the entity is included in the body
of the claim.

This transaction is approved and the entity is registered if all of these conditions are true:

1.  if the `did` in the `issuer`, `claim.id` and `claim.document.id` fields match
2.  if the `claim.document.publicKey` contains at least one public key that when run through the process
    described in the section on [Generating a unique idstring](#generating-a-unique-idstring) results in the exact DID
    from the `issuer`, `claim.id` and `claim.document.id` fields.
3.  if the signature on the claim is by one of the public keys mentioned in the DID document as approved for
    authentication.

### Read

Twin Clients can read a DID document by sending a query request for a DID.

For example a query for `did:twin:unikbase:2Mm9pLRQwueo7FJUvBoDW7QKGBXTX` would return:

```
{
	"@context": "https://w3id.org/did/v1",
	"id": "did:twin:unikbase:2Mm9pLRQwueo7FJUvBoDW7QKGBXTX",
	"publicKey": [{
		"id": "did:eth:2Mm9pLRQwueo7FJUvBoDW7QKGBXTX#key1",
		"type": "RsaVerificationKey2018",
		"owner": "did:eth:2Mm9pLRQwueo7FJUvBoDW7QKGBXTX",
		"publicKeyPem": "-----BEGIN PUBLIC KEY...END PUBLIC KEY-----\r\n"
	}],
	"authentication": [{
		"type": "RsaSignatureAuthentication2018",
		"publicKey": "did:eth:2Mm9pLRQwueo7FJUvBoDW7QKGBXTX#key1"
	}],
	"service": [{
		"id": "did:eth:2Mm9pLRQwueo7FJUvBoDW7QKGBXTX/credentials",
		"type": "CredentialRepositoryService",
		"serviceEndpoint": "..."
	}]
}
```

### Update

Twin Clients can update a DID document by submitting a
[Verifiable Claim](https://www.w3.org/TR/verifiable-claims-data-model) as a transaction.
The [issuer](https://www.w3.org/TR/verifiable-claims-data-model) and the
[subject](https://www.w3.org/TR/verifiable-claims-data-model) of this claim are the same DID that is being updated.

```js
{
	"@context": "https://w3id.org/security/v1",
	"id": "did:twin:unikbase:2Mm9pLRQwueo7FJUvBoDW7QKGBXTX/credentials/2",
	"type": ["Credential"],
	"issuer": "did:eth:2Mm9pLRQwueo7FJUvBoDW7QKGBXTX",
	"issued": "2018-11-16",
	"claim": {
		"id": "did:twin:2Mm9pLRQwueo7FJUvBoDW7QKGBXTX",
		"document": {
			"@context": "https://w3id.org/did/v1",
			"id": "did:twin:unikbase:2Mm9pLRQwueo7FJUvBoDW7QKGBXTX",
			"authentication": [{
				"type": "Ed25519VerificationKey2018",
				"publicKey": "did:twin:2Mm9pLRQwueo7FJUvBoDW7QKGBXTX#key2"
			}],
		}
	},
	"signature": {
		"type": "RsaSignature2018",
		"created": "2018-11-16T06:37:28.733Z",
		"creator": "did:eth:2Mm9pLRQwueo7FJUvBoDW7QKGBXTX#key1",
		"domain": "json-ld.org",
		"nonce": "79459f19c93f57fe18715d62f93f85e02aa01f4a",
		"signatureValue": "911653da61a0...11b0be8eaa4"
	}
}
```

This transaction is approved and the entity is updated if all these conditions are true:

1.  if the `did` in the `issuer`, `claim.id` and `claim.document.id` fields match.
2.  if the signature on the claim is by one of the public keys mentioned in the DID document as approved for
    authentication.

### Delete/Revoke

Twin Clients can revoke a DID document by submitting a
[Verifiable Claim](https://www.w3.org/TR/verifiable-claims-data-model) as a transaction.
The [issuer](https://www.w3.org/TR/verifiable-claims-data-model) and the
[subject](https://www.w3.org/TR/verifiable-claims-data-model) of this claim are the same DID that is being revoked.

The document field is set to `null`.

```js
{
	"@context": "https://w3id.org/security/v1",
	"id": "did:twin:unikbase:2Mm9pLRQwueo7FJUvBoDW7QKGBXTX/credentials/3",
	"type": ["Credential"],
	"issuer": "did:eth:2Mm9pLRQwueo7FJUvBoDW7QKGBXTX",
	"issued": "2018-11-16",
	"claim": {
		"id": "did:twin:unikbase:2Mm9pLRQwueo7FJUvBoDW7QKGBXTX",
		"document": null
	},
	"signature": {
		"type": "RsaSignature2018",
		"created": "2018-11-16T06:53:34.162Z",
		"creator": "did:eth:2Mm9pLRQwueo7FJUvBoDW7QKGBXTX#key1",
		"domain": "json-ld.org",
		"nonce": "1c4e4834bea6c6cc5f13d5370e8c3cff85234139",
		"signatureValue": "73b251cb9612...0e446e81a56"
	}
}
```

This transaction is approved and the entity is deleted if all these conditions are true:

1.  if the `did` in the `issuer` and `claim.id` fields match.
2.  if the signature on the claim is by one of the public keys mentioned in the DID document as approved for
    authentication.

## Status

This document is a work in progress draft.

## References

1.  Decentralized Identifiers (DIDs) v0.11 https://w3c-ccg.github.io/did-spec

2.  ABNF https://tools.ietf.org/html/rfc5234

3.  Multihash - Self-describing hashes https://multiformats.io/multihash/

4.  The Multihash Data Format https://tools.ietf.org/html/draft-multiformats-multihash-00

5.  Multihash Labels https://github.com/multiformats/multicodec/blob/master/table.csv

6.  Base58 Encoding https://en.wikipedia.org/wiki/Base58

7.  Bitcoin Base58 Alphabet https://en.bitcoinwiki.org/wiki/Base58#Alphabet_Base58

8.  Linked Data Cryptographic Suite Registry https://w3c-ccg.github.io/ld-cryptosuite-registry

9.  Verifiable Claims https://www.w3.org/TR/verifiable-claims-data-model

10. JSON-LD 1.0 - A JSON-based Serialization for Linked Data https://www.w3.org/TR/json-ld
