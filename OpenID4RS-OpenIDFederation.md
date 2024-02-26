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

According to the model defined within this document, the 
trust infrastructure is designed to facilitate trust and interoperability
between these entities, allowing them to share resources and collaborate
effectively. It does so by defining and enforcing policies for
identity verification, access control, and data protection.

At the heart of this architecture is the concept of a trust chain,
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

- **Descendant**: Entity indirectly linked to a superior entity. Eg: Marco (Leaf) accredited by Alice (Intermediate) is Descendant of the Trust Anchor where Alice is Subordinate of the Trust Anchor or its Descendant, if accredited by another Intermediate.

### Components

- **Entity Configuration**: The set of parameters and settings that claims the entity's roles, capabilities, and requirements within the federation. This configuration is crucial for the entity's successful integration and operation within the federated ecosystem.

- **Subordinate Statement**: A signed artifact issued by an Intermediate or Trust Anchor, asserting the trustworthiness and role of a subordinate entity within the federation. These statements are key components of the trust chain, enabling entities to establish and verify trust relationships.

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
**Figure 1.** __How can Carlo and Marco establish trust with each other under this hierarchical structure?__


At the heart of the figure above is the relationship between two entities that interacts in the field of the digital identities, which empower relying parties (RP) and OpenID Providers (OP) to authenticate each other mutually and authorize user authentication through designated authentication protocols. The OpenID Federation model obviates the necessity for pre-established registrations, easing the process of trust establishment between RPs and OPs.

Additionally, the OpenID Federation framework facilitates the establishment of trust and subsequent authorizations for OAuth 2.0 clients, authorization servers, and resource servers.

```
Trust Anchor Entity Configuration (what TA says about itself)
    |
    + TA Subordinate Statement  (what TA says about Marco) -- Marco Entity Configuration
    |
    + TA Subordinate Statement  (what TA says about Alice)-- Alice (Intermediate)
         |
         + Alice Subordinate Statement  (what Alice says about Carlo) -- Carlo (Relying Party)
```
**Figure 2.** __How the trust relations ships are represented though cryptographically signed statements__


## 4. Entity Configuration

By publishing the Entity Configuration, an entity publishes the parameters and settings that describe its roles, capabilities, and requirements within the federation. The Entity Configuration is a Federation Metadata in Jose format, signed by an entity regarding itself, and published at the web endpoint `.well-known/openid-federation`.

Below is a simplified, non-normative example of an Entity Configuration, where the JWT header and payload are represented as decoded plaintext:

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

Below a brief description of each parameter contained within the Entity Configuration despite the role of the federation entity, the following parameters are REQUIRED for all the entities types, be they Trust Ancors, Intermediates or Leaves. These parameters are defined as "Entity Configuration Common Parameters".

| Claim              | Description                                                                                   |
|--------------------|-----------------------------------------------------------------------------------------------|
| `iss`              | String. Identifier of the issuing Entity.                                                     |
| `sub`              | String. Identifier of the Entity to which it is referred. Both `iss` and `sub` contain the same value (URL). |
| `iat`              | UNIX Timestamp with the time of generation of the JWT.                                        |
| `exp`              | UNIX Timestamp with the expiry time of the JWT.                                               |
| `jwks`             | Federatioin Entity Keys, a JSON Web Key Set (JWKS) containing the public part of the signing keys of the Entity to be used for the Trust and Federation operations.  |
| `metadata`         | JSON Object. Contains Metadata according to the schema of that type. Possible types include `openid_relying_party`, `openid_provider`, `federation_entity`, `oauth_authorization_server`, and `oauth_resource`. |


In addition to the Entity Configuration Common Parameters, the Trust Anchor MAY contain the following paramenters, which their requirement may vary by each of them.

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
This section describes the structure of entity statements within the federation, which are crucial for proving a relationship with a subordinate entity.

By publishing the Subordinate Statement, an entity publishes the public keys and settings regarding a subject, its Subordinate within the federation. The Subordinate Statement enconded in Jose format, signed by an entity regarding another entity (`iss` != `sub`), and published at the web endpoint called federation fetch endpoint.

> How the federation fetch endpoint is known?

The federation fetch endpoint is known by the Entity Configuration of the subject entity, where the `federation_fetch_endpoint` parameter is defined in the `metadata.federation_entity` parameter.

Below is a simplified, non-normative example of a Subordinate Statement, where the JWT header and payload are represented as decoded plaintext:

```
{
    "alg": "RS256",
    "kid": "3YnC9tjiCaivhWLVUJ3AxwG",
    "typ": "entity-statement+jwt"
}
.
{
  "iss": "https://feide.no",
  "sub": "https://rp.example.edu",
  "iat": 1516239022,
  "exp": 1516298022,
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
    "openid_provider": {
      "issuer": "https://ntnu.no",
      "organization_name": "NTNU"
    },
    "oauth_client": {
      "organization_name": "NTNU"
    }
  },
  "metadata_policy": {
    "openid_provider": {
      "id_token_signing_alg_values_supported": {
        "subset_of": ["RS256", "RS384", "RS512"]
      },
      "op_policy_uri": {
        "regexp": "^https://[\\w-]+\\.example\\.com/[\\w-]+\\.html"
      }
    },
    "oauth_client": {
      "grant_types": {
        "subset_of": ["authorization_code", "client_credentials"]
      }
    }
  }
}
```

The parameters provided in the Subordinate Statements are defined below, ensuring full compliance with the OpenID Federation specification. 


| Parameter Name | State | Description |
|-------------------------------|------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| iss | REQUIRED | The Entity Identifier of the issuer of the Subordinate Statement. |
| sub | REQUIRED | The Entity Identifier of the subject. |
| iat | REQUIRED | The time the statement was issued, represented as seconds from 1970-01-01T0:0:0Z measured in UTC. |
| exp | REQUIRED | Expiration time after which the statement MUST NOT be accepted for processing, represented as seconds from 1970-01-01T0:0:0Z measured in UTC (Unix timestamp). |
| jwks | REQUIRED | A JSON Web Key Set (JWKS) representing the public part of the subject's Federation Entity signing keys. |
| metadata | OPTIONAL | JSON object with protocol-specific claims that overrides the subject's metadata. |
| metadata_policy | OPTIONAL | JSON object that defines a metadata policy. The metadata policy applies to the descendant's metadata, therefore to Subordinate's metadata as well as to all Entities that are Subordinate to it. |
| constraints | OPTIONAL | JSON object that describes constraints that apply to the Trust Chain inm erlations to the Descendants. |
| trust_marks | OPTIONAL | An array of JSON objects, each representing a Trust Mark. |
| trust_mark_issuers | OPTIONAL | A Trust Anchor MAY use this claim to tell which combination of Trust Mark identifiers and issuers are trusted by the federation. |
| trust_mark_owners | OPTIONAL | If a Federation Operator knows that a Trust Mark identifier is owned by an Entity different from the Trust Mark issuer, then that knowledge MUST be expressed in this claim. |
| source_endpoint | OPTIONAL | String containing the fetch endpoint URL from which the Entity Statement was issued. |
This table and example provide a detailed look at the structure and purpose of Subordinate Statements within the OpenID Federation, highlighting their role in establishing trust and verifying identity within the federation.

## 6. Trust Marks

OpenID Federation Trust Marks are a feature within the OpenID Federation framework that serve as indicators of compliance or adherence to specific standards, policies, or practices within a federated identity ecosystem. They are used to enhance the trustworthiness of entities participating in the federation by providing a verifiable way to assert that these entities meet certain criteria or have been certified by recognized authorities.

For example, in the education sector, a Trust Mark could indicate compliance with privacy standards relevant to student data. In healthcare, Trust Marks might be used to signify adherence to health information privacy and security standards.

For detailed information on Trust Marks, including their structure, issuance, and management within a federated identity ecosystem, refer to the [official OpenID Federation specifications](https://openid.net/specs/openid-connect-federation-1_0.html).

## 7. Accreditation Bodies
In the OpenID Federation framework, accreditation bodies can be categorized into three main types: Trust Anchors, Intermediates, and Trust Mark Issuers.

Accreditations bodies have their hierarchy, since the Trust Anchor registers both the intermediates as Subordinates and the Trust Marks Issuers (which are not Subordinates). The Trust Mark Issuers are published within the Trust Anchor's Entity Configuration, in the parameter `trust_mark_issuers`.

## 7.1 Accreditation Bodies Constraints

There are Accreditation Bodies, in the form of Intermediates, allowed to registers only the entities belonging to their domain. When this is true, the trust anchor includes in the subordinate statement regarding to these type of accreditation bodies, the following parameters:

| Parameter Name | State | Description |
|----------------|-------|-------------|
| crit | OPTIONAL | Entity Statements require that the crit (critical) claim be understood and processed. |
| metadata_policy_crit | OPTIONAL | Array of strings specifying critical metadata policy operators other than the standard ones defined that MUST be understood and processed. Only Subordinate Statements MAY include this claim. |


## 8. Trust Establishment and Metadata Exchange

Trust within the OpenID Federation is established through a multi-step process that involves the discovery and collection of entity statements (Leaf's Entity Confgiuration, one or more Subordinate Statements, Trust Anchor Entity Confgiuration), validation of derivating Trust Chain, validation of Trust Marks (if allowed by the Trust Anchor), and the application of metadata policies to obtain the final metadata for an entity. This process ensures that trust is not only declared but also verified and enforced according to the federation's standards and policies.

### Discovery and Collection of Trust Chains
The process begins with the discovery of trust chains. A trust chain is a sequence of Subordinate Statements, starting from a known Trust Anchor down to the entity in question. Each entity in the chain, except for the Trust Anchor, must have a Subordinate Statement issued by its immediate superior in the chain. The discovery process typically involves fetching these statements from well-known endpoints or directories maintained by the federation.

### Validation of the Trust Chain
Once a trust chain is collected, each link in the chain must be validated. This involves verifying the digital signatures on each Subordinate Statement to ensure they were indeed issued by the claimed superior entity and have not been tampered with. Additionally, the validity period of each statement is checked to ensure that none of the statements have expired.

### Validation of Trust Marks
If any entity in the trust chain possesses Trust Marks, these must also be validated. This involves verifying the digital signature on each Trust Mark to ensure it was issued by a recognized Trust Mark Issuer and checking that the Trust Mark is still valid. The Trust Anchor's configuration specifies which Trust Mark Issuers are recognized and trusted within the federation.

### Application of Metadata Policies
Finally, metadata policies specified in the Subordinate Statements are applied. This involves merging the metadata from each entity in the chain, starting from the Trust Anchor down to the entity in question, and applying any metadata policies that modify or restrict the inherited metadata. The result is the final metadata for the entity, which reflects both its own claims and any restrictions or modifications imposed by its superiors in the trust chain.


### How the RP discovers the OPs

In the OpenID Federation process, a Relying Party (RP) embarks on a systematic journey to discover and validate Identity Providers (OPs) within the federation. This journey begins at the Trust Anchor, where the RP uses a listing endpoint to filter through all intermediates and OPs. For each entity identified, the RP fetches Subordinate Statements to trace the trust chain, continuing this process until it reaches the leaf entities, which are the OPs themselves. At this stage, the RP fetches the Entity Configuration of each OP.

This meticulous process allows the RP to navigate the entire federation, ensuring that it collects and validates the trust chain for each OP. The culmination of this process is the creation of the Trust chains related to all the OPs and the inclusion of these into a discovery page. This page includes each OP, enriched with validated information and logos obtained through their respective trust chains, thus providing a comprehensive and secure overview of available Identity Providers within the federation.

```
@startuml
participant "Relying Party" as RP
participant "Trust Anchor" as TA
participant "Intermediate" as IM
participant "Identity Provider (OP)" as OP

RP -> TA: Fetch the list of all Intermediates and OPs
loop for each Intermediate
    RP -> IM: Fetch listing of Subordinates/OPs
end
loop for each OP
    RP -> OP: Fetch OP's Entity Configuration
    RP -> IM: Fetch Subordinate Statement(s) for OP
    RP -> TA: Fetch Subordinate Statement for Intermediate(s)
    RP -> RP: Validate Trust Chain for OP
    RP -> RP: Include OP in Discovery Page\nwith validated information and logo
end

@enduml
```
Sequence Diagram X. [This diagram](http://www.plantuml.com/plantuml/svg/VP6nQiCm48PtFyMHfSbGkdSe2MaB6Og54pfrKQsBVI2VWdHEyTlth7HGGgW76UJl_twTl4vYeuo3hqxwffPEbWKM3Vg9k0EZczC2R8B6N1E7E2Q13RTzY1auRAw17Gl60HrPmGpiFcu0Xoma4vWOpkGmmh8sgupMfgeQ0uylQds6TvIs1qz9vYE58rZleTGdiEmqb2eVmWcE8G9QZPHC1VfafnxaZdC_VeVeY6VTqwn2TZUwsXIIscH9tdv8y7OPPHluIDqOhL3WkvL53-n5rm5PbccLQye67VtVJ0wK4wgaI3Twla_hoSsXoy_QzfQuw2d87RT-TAWVG95NIcquOvp0s0w_-8UaX_EVp2cXZY6Fc2_UXoxawwhMkew3_mK0) shows how a Relying Party navigates the federation, collecting and validating the trust chain for each Identity Provider (OP), and then creates a discovery page including each OP using the information and logo obtained through their trust chain.

This comprehensive process ensures that trust within the federation is not only declared but also rigorously verified and enforced, allowing entities within the federation to confidently rely on the authenticity and integrity of each other's claims.

### How the AS/OP registers a new Client/RP

The process of an Authorization Server (AS) or OpenID Provider (OP) registering a new Client or Relying Party (RP) often involves automatic client registration. This process allows clients to be dynamically registered with the AS/OP without manual intervention.

1. Client Request: The RP sends a request to the AS/OP. This request is a protocol specific request providing only the entity ID in the form of HTTPs URL.
2. Validation: The AS/OP discovers the trust with the Client usinf the federation discovery process.
3. Registration Confirmation: If the Trust Chains is successful, the AS/OP accepts the request.

```
@startuml
participant "Relying Party (RP)" as RP
participant "Authorization Server/OpenID Provider (AS/OP)" as ASOP

RP -> ASOP: Send a request\nwith RP's HTTP URL entity id
ASOP -> ASOP: Validate the trust (Federation Discovery Process)
alt If validation is successful
    ASOP -> RP: Return the response
else If validation fails
    ASOP -> RP: Return error response
end
@enduml
```
Sequence Diagram x. This [sequence diagram](http://www.plantuml.com/plantuml/svg/TP3FIpin4CNl-Ik6NhxjuKll7gI58XQ4XhHwyX9sPzs1cAmpavNrhtUsLQY2WVmWUP_t7jc8HjRwcjnWEpKqnAom29WcoY_WxNA2PV2h1KI1u7_AjbNxmlGHbKg68_A8l3uCcFSxy5n6Qf5XkJsk3ry6s-F1EnSy_ByzdsyCoYr4O7ohAFgStqbxo_adS7ywUNWC3u1PoLfGwmovGfzYeZOgWlOskOh2yWujyb9dHz8KApJDLHeKMRcO5FOTZ1Tm5f60r6P-xMfoOECx8rX2GAsSp_wCCfGiw309_ZBf8YNv2qRcmbTuRjt65lloJm00) illustrates the automatic client registration process.


#### Federation Discovery Process

The sequence diagram starts with the OP fetching the RP's entity configuration to identify authority hints, which are pointers to entities that can issue statements about the RP. The OP then follows these hints, collecting subordinate statements from intermediate entities and validating each one. The process continues until the OP reaches the Trust Anchor, whose statement is also fetched and validated. Finally, the OP compiles the validated trust chain and checks for its expiry. If the trust chain is valid and unexpired, the OP proceeds with the federation process; otherwise, the process is aborted with an error.

```
@startuml
participant "OpenID Provider (OP)" as OP
participant "Relying Party (RP)" as RP
participant "Intermediate Entities" as IE
participant "Trust Anchor" as TA

OP -> RP: Fetch RP's Entity Configuration
RP -> OP: Return Entity Configuration

OP -> RP: Extract Authority Hints from RP's Configuration
RP -> OP: Provide Authority Hints

loop for each Authority Hint
    OP -> IE: Fetch Entity Configuration
    IE -> OP: Fetch Subordinate Statement
    OP -> OP: Validate the previous statement\nusing the Federation Entity Keys\nprovided in the Subordinate Statement
end

OP -> OP: Validate Trust Chain

alt If Trust Chain is Valid and Unexpired
    OP -> OP: Proceed with Federation Process
else
    OP -> OP: Abort Process with Error
end

OP -> OP: Applies Policies
OP -> OP: Derive RP's final metadata

@enduml
```
Sequence Diagram x. The OP building the Trust chain related to an RP.


### How checks a Lead againt any revocations

The sequence diagram below illustrates the federation discovery process from the perspective of an OpenID Provider (OP) verifying an unexpired trust chain for a Relying Party (RP). The process involves fetching the RP's entity configuration, following authority hints to reach the Trust Anchor, collecting all subordinate statements along the way, and validating the trust chain.

#### Trust Chain Fast Renewal

After detailing the federation discovery process, we now turn our attention to the Trust Chain Fast Renewal method. This approach offers a streamlined way to maintain the validity of a trust chain without undergoing the full discovery process again. It's particularly useful for quickly updating trust relationships when minor changes occur or when the trust chain is close to expiration but the overall structure of the federation hasn't changed significantly.

The Trust Chain Fast Renewal process is initiated by fetching the leaf's (RP's or OP's) entity configuration anew. However, unlike the federation discovery process that may involve fetching entity configurations starting from the authority hints, the fast renewal focuses on directly obtaining the subordinate statements. These statements are requested using the source_endpoint provided within them, which points to the location where the statements can be fetched.

#### Conditions for Fast Renewal

Fast Renewal is contingent on two critical conditions:

1. Presence of `source_endpoint`: The process is only possible if the subordinate statements include a `source_endpoint`. This endpoint indicates where the statements can be directly fetched, bypassing the need to traverse the entire trust chain from the top.

2. Unchanged Federation Fetch Endpoint: The authority (Intermediate or Trust Anchor) must not have altered its federation fetch endpoint in its entity configuration. If this endpoint has changed, it implies a potential restructuring of the trust relationships or federation architecture, necessitating a return to the classical federation discovery process to re-establish trust chains accurately.

```
@startuml
participant "Leaf (RP/OP)" as Leaf
participant "Intermediate Entities" as IE
participant "Trust Anchor" as TA

Leaf -> Leaf: Fetch Leaf's Entity Configuration
Leaf -> IE: Request Subordinate Statements\nusing `source_endpoint`
IE -> Leaf: Return Subordinate Statements

alt If Trust Chain is renewable
    Leaf -> Leaf: Trust Chain Successfully Renewed
else
    Leaf -> Leaf: Initiate Classical Federation Discovery
end

Leaf -> Leaf: Validate Subordinate Statements
Leaf -> Leaf: Check Trust Chain Expiry

@enduml
```
Sequence Diagram x. This sequence diagram illustrates the streamlined process of renewing a trust chain through Fast Renewal. By directly fetching and validating the necessary subordinate statements and, if applicable, the trust anchor statement, entities can efficiently maintain their trust relationships within the federation. 

This method significantly reduces the overhead and complexity involved in re-establishing trust chains, provided the conditions for its application are met.

## 9. Federation Endpoints
Details the various endpoints in the federation architecture, explaining their functions and how they support communication and interaction within the federation.

## 10. Federation Errors
This section addresses the common errors that may occur within the federation, providing guidance on identification, understanding, and resolution.

### Trust Chain

Imagine a chain of trust, like a relay race:

    - It starts with Entity Configuration, like the starting point.
    - There can be zero or more Subordinate Statements, like runners passing the baton. These statements are issued by Intermediates (think of them as middle runners) about other entities called Subordinates (like the next runner in line).
    - Finally, there's a statement issued by the Trust Anchor (the end of the race). This statement is either about the last Intermediate or the final entity (the Leaf Entity).
    - This chain always ends with the Entity Configuration of the Trust Anchor, like crossing the finish line, even though it might not always be shown in the details.



## 11. Differences between OpenID Federation 1.0 and OpenID4RS
Highlights the key differences and improvements of the OpenID4RS profile over the standard OpenID Federation 1.0, emphasizing enhancements tailored for the Research and Education ecosystem.

## 13. Security Considerations and Current Best Practices
Discusses the security considerations and best practices within the federation, aiming to ensure the integrity, confidentiality, and availability of federated services.

- OpenID Federation supports OAuth 2.0 clients, authorization servers, and resource servers in establishing trust and then establishing authorizations, without solely relying on bearer tokens. It requires a demonstration of possession and a discovery process for cryptographic materials. This ensures a higher level of security and identity assurance for the parties involved, leveraging the federation for enhanced security measures in digital identity and authorization management.


## 14. Examples
Provides practical examples of federation scenarios, configurations, and implementations, offering insights into real-world applications of the OpenID Federation.

## 15. Become a Provider
Guides entities through the process of becoming a provider within the OpenID Federation, detailing the requirements, steps, and benefits of participation.

