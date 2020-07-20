# DIPLOME-ORCL DID Method Specification

## Author

-   Diplome Team: <https://www.diplo-me.eu/about-us/index.html>

## Preface

The diplome-orcl DID method specification conforms to the requirements specified in 
the [DID specification](https://w3c-ccg.github.io/did-core/), currently published by the 
W3C Credentials Community Group. For more information about DIDs and DID method specifications, 
please see the [DID Primer](https://github.com/WebOfTrustInfo/rebooting-the-web-of-trust-fall2017/blob/master/topics-and-advance-readings/did-primer.md)

## Abstract

Decentralized Identifiers (DIDs, see [1]) within Diplome ecosystem enables verifiable, decentralized digital identification, designed to be compatible with any distributed ledger or network.
In the Diplome community, the Certification Authorities are entities entitled to issue qualification titles, thus are certified issuers of digital certifications. 
Certification Authorities are entities sub-sets of a more general group defined as 'Organizations': in Diplome, certified organizations are listed in an openly accessible registry that enables any verifier to check the authenticity and characteristics of such organization.
This registry is managed by an oracle (Diplome oracle smart contract) that is the the verifier of CA issuers. 

The described DID method details the verification process of an Organization identity on Diploem ethereum-like oracle.

For a reference implementation of this DID method specification see [2].

## JSON-LD Context Definition
>Note, this DID method specification uses the `Secp256k1VerificationKey2018`, `Secp256k1SignatureAuthentication2018` types and an `ethereumAddress` instead of a `publicKeyHex`.
>
>The definition of the ethr DID JSON-LD context is:
>
>  {
>    "@context":
>    {
>      "ethereumAddress": "https://github.com/decentralized-identity/ethr-did-resolver#ethereumAddress",
>      "Secp256k1VerificationKey2018": "https://github.com/decentralized-identity/ethr-did-resolver#Secp256k1VerificationKey2018",
>      "Secp256k1SignatureAuthentication2018": "https://github.com/decentralized-identity/ethr-did-resolver#Secp256k1VerificationKey2018",
>    }
>  }

## DID Method Name

The namestring that shall identify this DID method is: `diplome-orcl`

A DID that uses this method MUST begin with the following prefix: `did:diplome-orcl`. Per the DID specification, this string 
MUST be in lowercase. The remainder of the DID, after the prefix, is specified below.

## DID Schema

A Decentralized Identifier is defined as a [RFC3986](https://tools.ietf.org/html/rfc3986)
Uniform Resource Identifier, with a format based on the generic DID schema. Fore more
information you can refer to the
[original documentation](https://w3c.github.io/did-core/#generic-did-syntax).

```abnf
did = "did:" method-name ":" method-specific-id
method-name        = 1*method-char
method-char        = %x61-7A / DIGIT
method-specific-id = *idchar *( ":" *idchar )
idchar             = ALPHA / DIGIT / "." / "-" / "_"
```

Example of a simple Decentralized Identifier (DID).

```
did:diplome-orcl:5ee2c54db8b1d13dbc2a6629
```

Expanding on the previous definitions the diplome-orcl DID Method specification use the
following format.

```abnf
did                = "did:diplome-orcl:" specific-idstring
specific-idstring  = 12hexOctet
hexOctet = hexDigit hexDigit
hexDigit = "0" / "1" / "2" / "3" / "4" / "5" / "6" / "7" / "8" / "9" / "a" / "b" / "c" / "d" / "e" / "f"
```

> Note: it is assumed that the DID is anchored on the Diplome DLT network. 

## CRUD Operation Definitions

### Create (Register Organization)

In order to create a `diplome-orcl` DID, a Certificate Authority (Organization), key pair, needs to be generated together with related ethereum wallet. At this point, one interaction with Diplome network is needed in order to register Organization on the Oracle CAVerification.

Oracle CAVerification is available on Diplome DLT at address [ TO BE UPDATED ].
In order to create a new Certification Authority it's needed to invoke the relevant CAVerifier (Oracle Smart Contract) addCA functions as defined by the ERC725 standard:

 * `function addCA(address newCaAddress, string memory caDescription, string memory caPubkey, string memory caDid, string memory caWhedId, uint caExistFrom, uint caExistTo) public onlyDiplome inState(State.Active);`

>Note: for security constraints only Diplome Organization can create new Certification Authorities

### Read

The Certification Autority can be verified as resolution of diplome-orcl DID by using read only functions and contract events on CAVerifier (Oracle Smart Contract).

In order to verify existency and availability of an CA, it can be invoked the function:

 * `function isCAVerified(string calldata caDid) external view inState(State.Active) returns (bool);`

In order to retrieve details about Certification Authority, it can be invoked the function:

 * `function getCaDetails(string calldata caDid) external view returns (string memory name,bool active,string memory pubkey,string memory did,string memory whedId, uint existFrom, uint existTo);`

### Update

The Certification Authority may be updated by invoking the relevant CAVerifier (Oracle Smart Contract) functions as defined by the ERC725 standard:

 * `function updateCA(string memory caDid, string memory caDescription, bool active, string memory caPubkey, address caAddress, string memory caWhedId, uint caExistFrom, uint caExistTo) public onlyDiplome inState(State.Active);`
 * `function updateCADeqarDetails(string memory caDid, string memory caDeqarID, string memory caQFEHEALevels, string memory caDeqarPermalink) public onlyDiplome inState(State.Active);`

>Note: these methods are written in the Solidity language. Ethereum smart contracts are executed as binary code running in the Ethereum Virtual Machine (EVM). For security constraints only Diplome Organization can update Certification Authorities

### Delete (Revoke) 

Revoking the Certification Authority can be supported by executing the metod to disable de CA:

* `function disableCA(string memory caDid) public onlyDiplome inState(State.Active);`

>Note: for security constraints only Diplome Organization can delete (revoke) Certification Authorities


## DID Resolution

The simplest mechanism to resolve a particular DID instance to the latest published
version of its corresponding DID Document is using the agent's HTTP interface.

The resolution and data retrieval is done by performing a __GET__ request of the form:

`https://wallet.diplo-me.eu/diplome/organization/diplome-orcl/{{subject}}`

For example:

```bash
curl -v https://wallet.diplo-me.eu/diplome/organization/diplome-orcl/5ee2c54db8b1d13dbc2a6629
```

If the subject is valid, and information has been published to the network, the
response will be latest version available of its corresponding DID Document encoded
in JSON-LD with a __200__ status code. If no information is available the response
will be a JSON encoded error message with a __404__ status code.

## References

 **[1]** <https://w3c-ccg.github.io/did-core/>

 **[2]** <https://github.com/decentralized-identity/ethr-did-resolver>

