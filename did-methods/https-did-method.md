# HTTPS DID Method Specification v0.0.1

October 8th, 2018

Mike Xu <mike.xu@consensy.net>

## Preface

The HTTPS DID method specification conforms to the requirements specified in the [DID specification](https://w3c-ccg.github.io/did-spec/), currently published by the W3C Credentials Community Group. For more information about DIDs and DID method specifications, please see the [DID Primer](https://github.com/WebOfTrustInfo/rebooting-the-web-of-trust-fall2017/blob/master/topics-and-advance-readings/did-primer.md)

## Abstract

DIDs that target a distributed ledger face significant practical challenges in bootstrapping enough meaningful trusted data around identities to incentivize mass adoption. We propose using a new DID method in conjunction with blockchain-based DIDs that allows them to bootstrap trust using a web domain's existing reputation.

## Introduction

>  TODO: What should go here that's different from the abstract?

## Example

```json
{
  "@context": "https://w3id.org/did/v1",
  "id": "did:https:example.com",
  "publicKey": [{
       "id": "did:https:example.com#owner",
       "type": "Secp256k1VerificationKey2018",
       "owner": "did:https:example.com",
       "ethereumAddress": "0xb9c5714089478a327f09197987f16f9e5d936e8a"
  }],
  "authentication": [{
       "type": "Secp256k1SignatureAuthentication2018",
       "publicKey": "did:https:example.com#owner"
  }]
}
```

## Target System

The target system of the HTTPS DID method is the web host that the domain name described by the DID resolves to when queried through the Domain Name System.

## DID Method Name

The namestring that shall identify this DID method is: `https`

A DID that uses this method MUST begin with the following prefix: `did:https`. Per the DID specification, this string MUST be in lowercase.

## DID Method Namespace Specific Identifier

The method specific identifier is a fully qualified domain name that is secured by a TLS/SSL certificate. It is analogous to the common name used in a certificate, and it must not include IP addresses, port numbers, or directories behind the top-level domain extension. The formal rules describing valid domain name syntax are described in [RFC 1035](https://tools.ietf.org/html/rfc1035), [RFC 1123](https://tools.ietf.org/html/rfc1123), and [RFC 2181](https://tools.ietf.org/html/rfc2181).

### Example

```
did:https:w3c-ccg.github.io
```

## JSON-LD Context Definition

> TODO: Once we define the methods of updating the DID document if they need to reference state within the document

## CRUD Operation Definitions

### Create (Register)

> TODO: Current plan is just to generate this from the app configurator, expose it to the user, and present them with instructions to host it on the web server that resolves from the identifier. We will need to come up with a more legit formal process for this before proposing it as a full DID method.

### Read (Resolve)

You must perform the following steps in order to resolve the DID document from an `https` DID:

1. Parse the fully qualified domain name from the identifier
2. Generate an https URL to the expected location of the DID document by prepending "https://" and appending "/.well-known/did.json" to the domain name
3. Perform a `GET` request to the URL using an agent that can successfully negotiate a secure https connection

### Update

> TODO: No way to do this at the moment other than manually updating the hosted document. We will need to come up with a more legit formal process for this before proposing it as a full DID method.

### Delete (Revoke)

> TODO: No way to do this at the moment other than manually deleting the hosted document. We will need to come up with a more legit formal process for this before proposing it as a full DID method.

## Security Considerations

At least TLS 1.2 should be configured to use only strong ciphers suites and to use sufficiently large key sizes. As recommendations may be volatile these days, only the very latest recommendations should be used. However, as a rule of thumb, the following must be used:

- ephemeral keys are to be used
- ECDHE with one of the strong curves {X25519, brainpoolP384r1, NIST P-384, brainpoolP256r1, NIST P-256} shall be used as key exchange
- AESGCM or ChaCha20 with 256 bit large keys shall be used for bulk encryption
- ECDSA with one of the strong curves {brainpoolP384r1, NIST P-384, brainpoolP256r1, NIST P-256} or RSA (at least 3072) shall be used
- Authenticated Encryption with Associated Data (AEAD) shall be used as Mac
- SHA384 or POLY1305 shall be utilized

Examples for strong ciphersuites for now are:
ECDHE-ECDSA-AES256-GCM-SHA384 TLSv1.2 Kx=ECDH Au=ECDSA Enc=AESGCM(256) Mac=AEAD
ECDHE-RSA-AES256-GCM-SHA384 TLSv1.2 Kx=ECDH Au=RSA Enc=AESGCM(256) Mac=AEAD
ECDHE-ECDSA-CHACHA20-POLY1305 TLSv1.2 Kx=ECDH Au=ECDSA Enc=ChaCha20-Poly1305 Mac=AEAD
ECDHE-RSA-CHACHA20-POLY1305 TLSv1.2 Kx=ECDH Au=RSA Enc=ChaCha20-Poly1305 Mac=AEAD
ECDHE-RSA-AES256-GCM-SHA384 TLSv1.2 Kx=ECDH Au=RSA Enc=AESGCM(256) Mac=AEAD
ECDHE-ECDSA-AES256-GCM-SHA384 TLSv1.2 Kx=ECDH Au=ECDSA Enc=AESGCM(256) Mac=AEAD

More infos on hardening TLS can be found here: https://www.owasp.org/index.php/Transport_Layer_Protection_Cheat_Sheet

## Privacy Considerations

> TODO: Not really any that I can think of. This did will mostly be used to represent an organization rather than individual.

## Reference Implementations

The code at [https://github.com/uport-project/https-did-resolver](https://github.com/uport-project/https-did-resolver) is intended to present a reference implementation of this DID method. Any other implementations should ensure that they pass the test suite described in `/src/__tests__` before claiming compatibility