# OpenID Federation Research and Education Implementation Profile


This document outlines a trust infrastructure
based on OpenID Federation 1.0, specifically tailored for the
Research and Education ecosystem,
according to the requirements, properties and components defined in the document
[General Architecture](OpenID4RS-General-Architecture.md).

In this model, trust is established and managed through a federated system,
leveraging the OpenID Federation protocol. This protocol enables secure and
seamless communication between various independent entities, such as
research institutions, libraries, publishers, and data repositories.

The architecture is designed to facilitate trust and interoperability
between these entities, allowing them to share resources and collaborate
effectively. It does so by defining and enforcing policies for
identity verification, access control, and data protection.

At the heart of this architecture is the concept of a Trust Chain,
a series of signed artifacts that represent the relationships and
permissions between entities. This chain is built and maintained
through a process known as Federation Entity Discovery.

The model also incorporates optional Trust Marks, which federations
can define and require based on their specific needs. These marks
serve as additional indicators of trustworthiness, and compliance 
to specific purposes, within the ecosystem.

This architecture, incorporating the concept of multilateral federation, 
aims to provide a robust and flexible framework for establishing 
trust in the ecosystem. It promotes collaboration and resource sharing 
in a decentralized context, enabling multiple entities to engage in 
trust relationships simultaneously.


## Summary

1. The OpenID Federation Standard
2. Terminology and Acronyms
3. A Federation For Digital Identities And Authorizations
4. General Operation Within the Federation
5. Configuration of Participants
6. Accreditation of Participants
7. Federation Endpoints
8. Trust Marks e Policies
9. Differences between OpenID Federation 1.0 and OpenID4RS
10. Security Considerations and Current Best Practices
11. Examples


## 1. The OpenID Federation Standard
This document defines an implementation profile that is fully compliant with the [OpenID Federation 1.0](https://openid.net/specs/openid-connect-federation-1_0.html) specification.


## 2. Terminology and Acronyms
This section provides a comprehensive list of terms and acronyms used throughout this document, 
offering clarity and understanding of the specific language involved in OpenID Federation.
The terms are subdivided in two categories, by roles within the ecosystem and technical components.

### Roles and Components Definitions

- **Trust Anchor**: The foundational authority in a trust chain, responsible for the root of trust from which all trust relationships are derived. It is the most trusted entity, typically issuing the top-level informations in and about the federation.

- **Intermediate**: Entities in the trust chain that facilitate the extension of trust from the Trust Anchor to leaf entities. They may issue cryptograhical verifiable information to other intermediates or directly to leaf entities, acting as a bridge in the trust chain.

- **Leaf**: The end entities in a trust chain that rely on cryptograhical verifiable information issued by Trust Anchors or Intermediates. Leaf entities are typically specific digital services that participate in the federation, relying on the trust framework established by higher-level entities.

- **Trust Mark Issuer**: An entity authorized to issue Trust Marks within the federation. Trust Marks serve as indicators of compliance or adherence to specific standards, policies, or practices, enhancing the trustworthiness of entities that possess them.

- **Subordinate**: Entity directly accredited by a Trust Anchor or an Intermediate entity. Eg: Marco (Leaf) accredited by Alice (Intermediate) is Subonrdinate of Alice.

- **Descendant**: Entity indirectly linked to a superior entity. Eg: Marco (Leaf) accredited by Alice (Intermediate) is Descendant of the Trust Anchor where Alice is subordinate of the Trust Anchor or its Descendant.

### Components

- **Entity Configuration**: The set of parameters and settings that define an entity's role, capabilities, and requirements within the federation. This configuration is crucial for the entity's successful integration and operation within the federated ecosystem.

- **Subordinate Statement**: A signed artifact issued by an intermediate or Trust Anchor, asserting the trustworthiness and role of a subordinate entity within the federation. These statements are key components of the trust chain, enabling entities to establish and verify trust relationships.

## 3. A Federation For Digital Identities And Authorizations

The OpenID Federation constructs a trust infrastructure composed of authorities and participants. Trust relationships are forged through the OpenID Federation standard, facilitating authorization and authentication processes for accessing specific digital resources. These processes are enabled by protocols, not detailed in this document, which are integrated into the OpenID Federation as metadata. This integration allows for the customization of each participant's requirements and ensures interoperability.

This framework promotes delegation and the transitive extension of trust among the federation's various entities, thereby streamlining trust propagation. OpenID Federation is able to solve the problem represented in the following figure, where the answer is "Through a common Trusted Third Party" that in this case is represented by the Trust Anchor.

```
Trust Anchor
    |
    +-- Marco (Identity Provider)
    |
    +-- Alice (Intermediate)
         |
         +-- Carlo (Relying Party)
```
Figure 1. How can Carlo and Marco establish trust with each other under this hierarchical structure?


At the heart of this system are digital identities, which empower relying parties (RP) and OpenID Providers (OP) to authenticate each other mutually and authorize user authentication through designated authentication protocols.

The OpenID Federation model obviates the necessity for pre-established registrations, easing the process of trust establishment between RPs and OPs.

Additionally, the OpenID Federation framework facilitates the establishment of trust and subsequent authorizations for OAuth 2.0 clients, authorization servers, and resource servers.


## 4. Entity Configuration

Entity Configuration is a a basic component of the OpenID Federation, defining the parameters and settings that describe an entity's role, capabilities, and requirements within the federation. The Entity Configuration is a Federation Metadata in Jose format, signed by an entity regarding itself, and published at the web endpoint `.well-known/openid-federation`.

Below is a simplified, non-normative example of an Entity Configuration, there the JWT header and payload are represented as decoded plaintext:

```
{
    "alg": "RS256",
    "kid": "2HnoFS3YnC9tjiCaivhWLVUJ3AxwGGz_98uRFaqMEEs",
    "typ": "entity-statement+jwt"
}
.
{
    "exp": 1649590602,
    "iat": 1649417862,
    "iss": "https://rp.example.edu",
    "sub": "https://rp.example.edu",
    "jwks": {
        "keys": [
            {
                "kty": "RSA",
                "n": "5s4qi …",
                "e": "AQAB",
                "kid": "2HnoFS3YnC9tjiCaivhWLVUJ3AxwGGz_98uRFaqMEEs"
            }
        ]
    },
    "metadata": {
        "openid_relying_party": {
            "application_type": "web",
            "client_id": "https://rp.example.edu",
            "client_registration_types": [
                "automatic"
            ],
            "jwks": {
                "keys": [
                    {
                        "kty": "RSA",
                        "use": "sig",
                        "n": "1Ta-sE …",
                        "e": "AQAB",
                        "kid": "YhNFS3YnC9tjiCaivhWLVUJ3AxwGGz_98uRFaqMEEs"
                    }
                ]
            },
            "client_name": "Name of an example organization",
            "contacts": [
                "ops@rp.example.edu"
            ],
            "grant_types": [
                "refresh_token",
                "authorization_code"
            ],
            "redirect_uris": [
                "https://rp.example.edu/oidc/rp/callback"
            ],
            "response_types": [
                "code"
            ],
            "subject_type": "pairwise"
        },
        "federation_entity": {
            "federation_resolve_endpoint": "https://rp.example.edu/resolve",
            "organization_name": "EDU OIDC Service Provider",
            "homepage_uri": "https://rp.example.edu",
            "policy_uri": "https://rp.example.it/policy",
            "logo_uri": "https://rp.example.edu/static/logo.svg",
            "contacts": [
               "tech@example.edu"
             ]
        }
    },
    "trust_marks": [
        {
            "id": "https://trust-framework.example.edu/openid_relying_party/profile-A",
            "trust_mark": "eyJh …"
        }
    ],
    "authority_hints": [
        "https://accreditation-body.example.edu"
    ]
}
```

Below a brief description of each parameter contained within the Entity Configuration despite the role of the federation entity, the following parameters are REQUIRED for all the entities typse, be they Trust Ancors, Intermediates or Leaves. These parameters are defined as "Entity Configuration Common Parameters".

| Claim              | Description                                                                                   |
|--------------------|-----------------------------------------------------------------------------------------------|
| `iss`              | String. Identifier of the issuing Entity.                                                     |
| `sub`              | String. Identifier of the Entity to which it is referred. Both `iss` and `sub` contain the same value (URL). |
| `iat`              | UNIX Timestamp with the time of generation of the JWT.                                        |
| `exp`              | UNIX Timestamp with the expiry time of the JWT.                                               |
| `jwks`             | Federatioin Entity Keys, a JSON Web Key Set (JWKS) containing the public part of the signing keys of the Entity to be used for the Trust and Federation operations.  |
| `metadata`         | JSON Object. Contains Metadata according to the schema of that type. Possible types include `openid_relying_party`, `openid_provider`, `federation_entity`, `oauth_authorization_server`, and `oauth_resource`. |


In addition to the Entity Configuration Common Parameters, the Trust Anchor MAY contain the following paramenters, which their requirement may vary by each of them

| Claim              | Description                                                                                   |
|--------------------|-----------------------------------------------------------------------------------------------|
| `constraints`      | JSON Object that describes the defaults constraints to be applied to its Subordinates and Descendants. See the section [TODO ... TBD] for any configuration detail.   |
| `trust_mark_issuers` | JSON Array indicating which Federation authorities are considered trustworthy for issuing specific Trust Marks. |

In addition to the Entity Configuration Common Parameters, Intermediates and Leaves contains the following paramenters:

| Claim              | Description                                                                                   |
|--------------------|-----------------------------------------------------------------------------------------------|
| `authority_hints`  | Array of URLs. Contains a list of URLs of the superior Entities that may issue a Subordinate Statement related to this subject. |
| `trust_marks`      | A JSON Array containing the Trust Marks.    |


### Entity Configurations for Multiple Federations

An important consideration within the OpenID Federation is the multifaceted roles an entity can assume across different federations. For instance, an entity like Marco could function as an OpenID Relying Party and an OpenID Provider within the educational federation, while simultaneously serving as a Trust Anchor in a local federation, where it represents the highest level of authority. This versatility underscores the necessity for a flexible approach to entity configuration validation. Given the potential for an entity's role to shift depending on the federation context, strict validation of an entity's configuration could inadvertently restrict its ability to participate across multiple federations. Therefore, it is crucial to recognize that an entity's configuration may vary significantly, reflecting its diverse roles and responsibilities within different federated environments.


### Considerations on Federation Entity Keys and Metadata Protocol Specific Keys (JWKS)

The Entity Configuration includes top-level JWT keys and specific metadata protocol keys (JWKS) to ensure secure communication and trust within the federation. The top-level JWT keys are used for signing the Entity Configuration itself, providing a layer of security and integrity. These keys should be distinct from the OIDC Core keys, or any other protocol specific keys, to achieve the good practice to specialized different keys for different purposes. Then keys used for Trust and compliance operations are not used for protocol specific operations.

The JWKS contained within each specific metadata type (`openid_relying_party`, `openid_provider`, etc.) represents the public part of the signing keys for that entity's specific protocols.


Each JWK, despite if it is a federation entity key or a protocol specific key, must have a key ID (`kid`) to be properly identified and used for cryptographic operations.

By maintaining separate keys for the federation entity and the specific metadata protocols, the OpenID Federation ensures a robust and secure framework for digital identities and authorizations.


## 5. Subordinate Statement
Describes the structure and purpose of entity statements within the federation, which are crucial for establishing trust and verifying identity.

## 6. Trust Marks
This part elaborates on the optional Trust Marks in the federation, explaining their role as indicators of trustworthiness and compliance within the ecosystem.

## 7. Intermediates
Focuses on the role of intermediates in the trust chain, detailing how they facilitate the establishment and maintenance of trust relationships between entities.

## 8. Trust Negotiation
Explores the process of trust negotiation within the federation, highlighting the mechanisms and protocols involved in establishing trust between entities.

## 9. Federation Endpoints
Details the various endpoints in the federation architecture, explaining their functions and how they support communication and interaction within the federation.

## 10. Federation Errors
This section addresses the common errors that may occur within the federation, providing guidance on identification, understanding, and resolution.

## 11. Differences between OpenID Federation 1.0 and OpenID4RS
Highlights the key differences and improvements of the OpenID4RS profile over the standard OpenID Federation 1.0, emphasizing enhancements tailored for the Research and Education ecosystem.

## 13. Security Considerations and Current Best Practices
Discusses the security considerations and best practices within the federation, aiming to ensure the integrity, confidentiality, and availability of federated services.

- OpenID Federation supports OAuth 2.0 clients, authorization servers, and resource servers in establishing trust and then establishing authorizations, without solely relying on bearer tokens. It requires a demonstration of possession and a discovery process for cryptographic materials. This ensures a higher level of security and identity assurance for the parties involved, leveraging the federation for enhanced security measures in digital identity and authorization management.


## 14. Examples
Provides practical examples of federation scenarios, configurations, and implementations, offering insights into real-world applications of the OpenID Federation.

## 15. Become a Provider
Guides entities through the process of becoming a provider within the OpenID Federation, detailing the requirements, steps, and benefits of participation.

