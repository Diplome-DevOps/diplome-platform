
# DID Method
The present document describes the "diplome-orcl" DID Method specification.
The ***diplome-orcl*** DID method specification conforms to the requirements specified in the [DID specification](https://w3c-ccg.github.io/did-core/), currently published by the  W3C Credentials Community Group. For more information about DIDs and DID method specifications, 
please see the [DID Primer](https://github.com/WebOfTrustInfo/rebooting-the-web-of-trust-fall2017/blob/master/topics-and-advance-readings/did-primer.md)


Author:
-   Diplo*me* Tech Team: <https://www.diplo-me.eu/about-us/index.html>

### 1. Decentralized Identifiers
Decentralized Identifiers (DIDs, see [1]) within Diplo*me* ecosystem enables verifiable, decentralized digital identification, designed to be compatible with any distributed ledger or network.
In the Diplo*me* community, *Certification Authorities* (CA) are entities entitled to issue qualification titles, thus are certified issuers of digital certifications. 
Certification Authorities are entities sub-sets of a more general group defined as '*Organizations*': in Diplo*me*, certified organizations are listed in an open, accessible registry that enables any verifier to check the authenticity and characteristics of such organization.
This registry is managed by an oracle within Diplo*me* ecosystem (Diplo*me* oracle smart contract) that is the the verifier of CA issuers. 

The described DID method describes the verification process of an Organization identity on Diplo*me* ethereum-like oracle.

## 2. DID Method Specification
The method specification provides all the technical considerations, guidelines and recommendations produced for the design and deployment of the DID method implementation.

### 2.1 DID Document
A Decentralized Identifier, regardless of its particular method, can be resolved to a standard resource describing the subject. This resource is called a [DID Document](https://w3c-ccg.github.io/did-spec/#did-documents), and typically contains, among other relevant details, cryptographic material that enables authentication and verification of the DID subject.

The document is a Linked Data structure that ensures a high degree of flexibility while facilitating the process of acquiring, parsing and using the contained information. For the moment, the suggested encoding format for the document is [JSON-LD](https://www.w3.org/TR/json-ld/). Other formats could be used in the future.

> The term Linked Data is used to describe a recommended best practice for exposing
  sharing, and connecting information on the Web using standards, such as URLs,
  to identify things and their properties. When information is presented as Linked
  Data, other related information can be easily discovered and new information can be
  easily linked to it. Linked Data is extensible in a decentralized way, greatly
  reducing barriers to large scale integration. 

At the very least, the document must include the DID subject it's referring to under the `id` key.

```json
{
  "@context": "https://www.w3.org/ns/did/v1",
  "id": "did:diplome-orcl:5ee2c54db8b1d13dbc2a6629"
}
```

Details that are included in the DID Document are:

- [Created](https://w3c-ccg.github.io/did-spec/#created-optional):
  Timestamp of the original creation.
- [Updated](https://w3c-ccg.github.io/did-spec/#updated-optional):
  Timestamp of the most recent change.
- [Public Keys](https://w3c-ccg.github.io/did-spec/#public-keys):
  Public keys are used for digital signatures, encryption and other cryptographic operations, which in turn are the basis for purposes such as authentication, secure communication, etc.
- [Authentication](https://w3c-ccg.github.io/did-spec/#authentication):
  List the enabled mechanisms by which the DID subject can cryptographically prove that they are, in fact, associated with a DID Document.
- [Services](https://w3c-ccg.github.io/did-spec/#service-endpoints):
  In addition to publication of authentication and authorization mechanisms, the other primary purpose of a DID Document is to enable discovery of service endpoints for the subject. A service endpoint may represent any type of service the subject wishes to advertise, including decentralized identity management services for further discovery, authentication, authorization, or interaction. 
- [Proof](https://w3c-ccg.github.io/did-spec/#proof-optional):
  Cryptographic proof of the integrity of the DID Document according its subject.
    
Additionally, the DID Document may include any other fields deemed relevant for the particular use case or implementation.

Example of a more complete, and useful, DID Document.
```json
{
  "@context": [
    "https://www.w3.org/ns/did/v1",
    "https://w3id.org/security/v1"
  ],
  "id": "did:diplome-orcl:5ee2c54db8b1d13dbc2a6629",
  "created": "2019-03-09T15:44:15Z",
  "updated": "2019-03-09T15:44:15Z",
  "publicKey": [
    {
      "id": "did:diplome-orcl:5ee2c54db8b1d13dbc2a6629",
      "type": "Ed25519VerificationKey2018",
      "controller": "did:diplome-orcl:5ee2c54db8b1d13dbc2a6629",
      "publicKeyBase58": "A2nuvNvK0tyykNx40uJnxStj3tppYvfBGu3F13rGjA9B"
    }
  ],
  "authentication": [
    "did:diplome-orcl:5ee2c54db8b1d13dbc2a6629"
  ],
  "proof": {
    "@context": [
      "https://w3id.org/security/v1"
    ],
    "type": "Ed25519Signature2018",
    "creator": "did:diplome-orcl:5ee2c54db8b1d13dbc2a6629",
    "created": "2019-03-09T15:44:16Z",
    "domain": "https://wallet.diplo-me.eu/",
    "nonce": "a9hnhcnvjvqvmqh+gs7u0xkz6itr99l507",
    "proofValue": "A4QnsJKAvfZPR20g2LCYD5b9vkMEcA1O1RU2CxEDZ/AtUnmZRXOajwPfpQdNPA/6LBhFyjBh4czZu5l6saK+a5=="
  }
}
```

### 2.2 DID Method Name
The namestring that shall identify this DID method is: `diplome-orcl`

A DID that uses this method MUST begin with the following prefix: `did:diplome-orcl`. Per the DID specification, this string MUST be in lowercase. The remainder of the DID, after the prefix, is specified below.

### 2.3 DID Schema
A Decentralized Identifier is defined as a [RFC3986](https://tools.ietf.org/html/rfc3986) Uniform Resource Identifier, with a format based on the generic DID schema. Fore more information you can refer to the [original documentation](https://w3c.github.io/did-core/#generic-did-syntax).

```abnf
did                 = "did:" method-name ":" method-specific-id
method-name         = 1*method-char
method-char         = %x61-7A / DIGIT
method-specific-id  = *idchar *( ":" *idchar )
idchar              = ALPHA / DIGIT / "." / "-" / "_"
```

Example of a simple Decentralized Identifier (DID).

```
did:diplome-orcl:5ee2c54db8b1d13dbc2a6629
```

Expanding on the previous definitions the *diplome-orcl* DID Method specification use the following format.

```abnf
did                 = "did:diplome-orcl:" specific-idstring
specific-idstring   = 12hexOctet
hexOctet            = hexDigit hexDigit
hexDigit            = "0" / "1" / "2" / "3" / "4" / "5" / "6" / "7" / "8" / "9" / "a" / "b" / "c" / "d" / "e" / "f"
```

> Note: it is assumed that the DID is anchored on the Diplome DLT network. 

## 3. Client Operations
> To enable the full functionality of DIDs and DID Documents on a particular  distributed ledger or network (called the target system), a DID method  specification MUST specify how each of the following CRUD operations is performed by a client. Each operation MUST be specified to the level of detail necessary to build and test interoperable client implementations with the target system.

The following sections provide detailed descriptions and examples of all required CRUD base operations and some more advanced use cases.

### 3.1 CRUD Operations
Basic operations enabling the users to create, read, update and delete identifier instances.

### 3.1 Create (Register Organization)
In order to create a `diplome-orcl` DID, a Certificate Authority (Organization), key pair, needs to be generated together with related ethereum wallet. At this point, one interaction with Diplo*me* network is needed in order to register Organization on the Oracle CAVerification.

Oracle CAVerification is available on Diplo*me* DLT at a given address, shared with the verifier during the share-and-verify process. In any case the address is deemed to be public: [ TO BE UPDATED ].
In order to create a new Certification Authority the authorized governing body needs to invoke the relevant CAVerifier (Oracle Smart Contract) addCA functions as defined by the ERC725 standard:

 * `function addCA(address newCaAddress, string memory caDescription, string memory caPubkey, string memory caDid, string memory caWhedId, uint caExistFrom, uint caExistTo) public onlyDiplome inState(State.Active);`

>Note: for security and trust management constraints as for today only Diplo*me* Organization can create new Certification Authorities.

### 3.2 Read
The Certification Authority can be verified as resolution of *diplome-orcl* DID by using read only functions and contract events on CAVerifier (Oracle Smart Contract).

In order to verify existence and availability of a CA, the function below must be invoked:

```eth
    function isCAVerified(string calldata caDid) external view inState(State.Active) returns (bool);
```

In order to retrieve details about Certification Authority, the function below must be invoked:
```eth
    function getCaDetails(string calldata caDid) external view returns (string memory name,bool active,string memory pubkey,string memory did,string memory whedId, uint existFrom, uint existTo);
```

### 3.3 Update
Any Certification Authority details may be updated by invoking the relevant CAVerifier (Oracle Smart Contract) functions as defined by the ERC725 standard:

```eth
    function updateCA(string memory caDid, string memory caDescription, bool active, string memory caPubkey, address caAddress, string memory caWhedId, uint caExistFrom, uint caExistTo) public onlyDiplome inState(State.Active);
 ```   
```eth
    function updateCADeqarDetails(string memory caDid, string memory caDeqarID, string memory caQFEHEALevels, string memory caDeqarPermalink) public onlyDiplome inState(State.Active);
```

>Note: these methods are written in the Solidity language. Ethereum smart contracts are executed as binary code running in the Ethereum Virtual Machine (EVM). For security and trust management constraints as for today only Diplo*me* Organization can update Certification Authorities

### 3.4 Delete (Revoke) 
Revoking a Certification Authority can be supported by executing the corresponding method that will disable de CA:

```eth
    function disableCA(string memory caDid) public onlyDiplome inState(State.Active);
```    

>Note: for security and trust management constraints as for today only Diplo*me* Organization can delete (revoke) Certification Authorities

## 4. DID Resolution
The simplest mechanism to resolve a particular DID instance to the latest published version of its corresponding DID Document is using the agent's HTTP interface.

The resolution and data retrieval is done by performing a __GET__ request of the form:

`https://wallet.diplo-me.eu/diplome/organization/diplome-orcl/{{subject}}`

For example:

```bash
curl -v https://wallet.diplo-me.eu/diplome/organization/diplome-orcl/5ee2c54db8b1d13dbc2a6629
```

If the subject is valid, and information has been published to the network, the response will be latest version available of its corresponding DID Document encoded in JSON-LD with a __200__ status code. 
If no information is available the response
will be a JSON encoded error message with a __404__ status code.

# References

 **[1]** <https://w3c-ccg.github.io/did-core/>

 **[2]** <https://github.com/decentralized-identity/ethr-did-resolver>
