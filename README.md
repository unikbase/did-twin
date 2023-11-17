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
twin-did          = "did:twin:" twin-specific-idstring

twin-specific-idstring= hyphenReplacedDTMI ":" instanceID

hyphenReplacedDTMI= "dtmi" "-" 1*segment
segment           = 1*(ALPHA / DIGIT / "-")

instanceID                = 1*(ALPHA / DIGIT / "-" / "." / "_" / "~")

; ALPHA and DIGIT are as defined by RFC 5234, Appendix B.1
```

### Example

An example Twin DID:

```
did:twin:dtmi-com-unikbase-DigitalPassport-1:f47ac10b-58cc-4372-a567-0e02b2c3d479
```

### Digital Twin Model Identifier

Twin DIDs contains hyphen replaced [Digital Twin Model Identifier](https://azure.github.io/opendigitaltwins-dtdl/DTDL/v3/DTDL.v3.html#digital-twin-model-identifier) of the digital twin.

The DTMI colons (:) and semicolons (;) are replaced by hyphens (-). 

For example, `dtmi:com:unikbase:DigitalPassport;1` would become `dtmi-com-unikbase-DigitalPassport-1`.

## CRUD Operations

### Create (Register)
To create a twin DID, the user or system must:

* Determine the DTMI for the digital twin.
* Generate a UUID for the specific instance.
* Format the DID as per the provided structure.

### Read (Resolve)

Resolving a twin DID would return a DID document associated with the DID. 
The resolver should recognize the twin method and parse the <hyphenReplacedDTMI> to understand the digital twin's model type and the specific instance.

DTC to provide an set of endpoint ?

### Update

Updates to the DID document associated with a twin DID can be made by authorized parties, typically the entity controlling the digital twin.
The exact mechanism would depend on the underlying infrastructure chosen to host these DIDs.

### Delete/Revoke

The deletion or revocation of a twin DID would involve marking the DID as inactive or removing its association. 
The exact process would depend on the underlying infrastructure.

## Status

This document is a work in progress draft.

## References

1.  Decentralized Identifiers (DIDs) v0.11 https://w3c-ccg.github.io/did-spec

2.  ABNF https://tools.ietf.org/html/rfc5234
