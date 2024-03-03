# OpenID Federation Research and Education Implementation Profile


This document outlines a trust infrastructure
based on OpenID Federation 1.0, specifically tailored for the
Research and Education ecosystem,
according to the requirements, properties and components defined in the document
[General Architecture](OpenID4RS-General-Architecture.md).

In this model, trust is established and managed through a federated system,
leveraging the OpenID Federation protocol. 

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
    + TA Subordinate Statement (what TA says about Marco) -- Marco Entity Configuration
    |
    + TA Subordinate Statement (what TA says about Alice) -- Alice (Intermediate)
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
            "policy_uri": "https://rp.example.edu/policy",
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
    "authority_hints": ["https://accreditation-body.example.edu"]
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


### Federation Entity Metadata

The **federation_entity** metadata section within an Entity Configuration provides essential information about the federation entity, including its operational endpoints and organizational details. Below is a documentation of each element within the federation_entity metadata, presented in a markdown table format.

| Element | Description |
|----------------------------|-----------------------------------------------------------------------------------------------|
| **federation_resolve_endpoint** | The endpoint where the cached federation resolution requests can be sent to resolve entity statements and trust chains. See the dedicated section below. |
| **organization_name** | The official name of the organization that operates the federation entity. |
| **homepage_uri** | The URL of the organization's homepage, providing more information about the entity. |
| **policy_uri** | The URL where the federation entity's policy documents can be found. |
| **logo_uri** | The URL of an image file representing the organization's logo. |
| **contacts** | A list of email addresses or other contact information for reaching out to the organization. |

This metadata give the administative informations, federation role and common capabilities of the entity within the federated identity ecosystem, according to the common federation role types. 

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
  "sub": "https://leaf.example.edu",
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
      "issuer": "https://https://leaf.example.edu",
      "organization_name": "Example OP"
    },
    "oauth_client": {
      "organization_name": "Example RP"
    }
  },
  "metadata_policy": {
    "openid_provider": {
      "id_token_signing_alg_values_supported": {
        "subset_of": ["RS256", "RS384", "RS512"]
      },
      "op_policy_uri": {
        "regexp": "^https://[\\w-]+\\.example\\.edu/[\\w-]+\\.html"
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

In the OpenID Federation process, a Relying Party (RP) embarks on a systematic journey to discover and validate Identity Providers (OPs) within the federation. This journey begins at the Trust Anchor, where the RP uses a listing endpoint to filter through all intermediates and OPs. For each entity identified, the RP fetches Subordinate Statements to trace the trust chain, continuing this process until it reaches the leaf entities, which are the OPs. At this stage, the RP fetches the Entity Configuration of each OP.

This process allows the RP to navigate the entire federation, ensuring that it collects and validates the trust chain for each OP. The culmination of this process is the creation of the Trust chains related to all the OPs and the inclusion of these into a discovery page. This page includes each OP, enriched with validated information and logos obtained through their respective trust chains, thus providing a comprehensive and secure overview of available Identity Providers within the federation.

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
**Sequence Diagram x.** [This diagram](http://www.plantuml.com/plantuml/svg/VP6nQiCm48PtFyMHfSbGkdSe2MaB6Og54pfrKQsBVI2VWdHEyTlth7HGGgW76UJl_twTl4vYeuo3hqxwffPEbWKM3Vg9k0EZczC2R8B6N1E7E2Q13RTzY1auRAw17Gl60HrPmGpiFcu0Xoma4vWOpkGmmh8sgupMfgeQ0uylQds6TvIs1qz9vYE58rZleTGdiEmqb2eVmWcE8G9QZPHC1VfafnxaZdC_VeVeY6VTqwn2TZUwsXIIscH9tdv8y7OPPHluIDqOhL3WkvL53-n5rm5PbccLQye67VtVJ0wK4wgaI3Twla_hoSsXoy_QzfQuw2d87RT-TAWVG95NIcquOvp0s0w_-8UaX_EVp2cXZY6Fc2_UXoxawwhMkew3_mK0) shows how a Relying Party navigates the federation, collecting and validating the trust chain for each Identity Provider (OP), and then creates a discovery page including each OP using the information and logo obtained through their trust chain.

This comprehensive process ensures that trust within the federation is not only declared but also rigorously verified and enforced, allowing entities within the federation to confidently rely on the authenticity and integrity of each other's claims.

### How the AS/OP registers a new Client/RP

The process of an Authorization Server (AS) or OpenID Provider (OP) registering a new Client or Relying Party (RP) often involves automatic client registration. This process allows clients to be dynamically registered with the AS/OP without manual intervention. Below the steps an AS/OP follows to register a new Client/RP:

1. **Client Request**: The RP sends a request to the AS/OP. This request is a protocol specific request providing at least the entity ID in the form of HTTPs URL.
2. **Validation**: The AS/OP discovers the trust with the Client usinf the federation discovery process.
3. **Registration Confirmation**: If the Trust Chains is successful, the AS/OP accepts the request.

```
@startuml
participant "Relying Party (RP)" as RP
participant "Authorization Server/OpenID Provider (AS/OP)" as ASOP

RP -> ASOP: Send a request\nwith RP's HTTP URL entity id
ASOP -> ASOP: Validate the trust (Federation Discovery Process) and registers the RP
alt If validation is successful
    ASOP -> RP: Return the response
else If validation fails
    ASOP -> RP: Return error response
end
@enduml
```
**Sequence Diagram x.** This [sequence diagram](http://www.plantuml.com/plantuml/svg/TP3FIpin4CNl-Ik6NhxjuKll7gI58XQ4XhHwyX9sPzs1cAmpavNrhtUsLQY2WVmWUP_t7jc8HjRwcjnWEpKqnAom29WcoY_WxNA2PV2h1KI1u7_AjbNxmlGHbKg68_A8l3uCcFSxy5n6Qf5XkJsk3ry6s-F1EnSy_ByzdsyCoYr4O7ohAFgStqbxo_adS7ywUNWC3u1PoLfGwmovGfzYeZOgWlOskOh2yWujyb9dHz8KApJDLHeKMRcO5FOTZ1Tm5f60r6P-xMfoOECx8rX2GAsSp_wCCfGiw309_ZBf8YNv2qRcmbTuRjt65lloJm00) illustrates the automatic client registration process.


#### Federation Discovery Process

The sequence diagram starts with the OP fetching the RP's entity configuration to identify authority hints, which are pointers to entities that can issue statements about the RP. The OP then follows these hints, collecting subordinate statements from intermediate entities and validating each one. The process continues until the OP reaches the Trust Anchor, whose statement is also fetched and validated. Finally, the OP compiles the validated trust chain and checks for its expiry. If the trust chain is valid and unexpired, the OP proceeds with the federation process; otherwise, the process is aborted with an error.

Note: While this section exemplifies the journey of discovery from the perspective of an OpenID Provider (OP), it's important to understand that this approach can be applied to every kind of entity type within the federation. 


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
    OP -> IE: Fetch Entity Configuration -> get federation_fetch_api URL
    IE -> OP: Fetch Subordinate Statement
    OP -> OP: Validate the previous statement\nusing the Federation Entity Keys\nprovided in the Subordinate Statement
end

OP -> OP: Validate Trust Chain

alt If Trust Chain is valid and unexpired
    OP -> OP: Proceed with Federation Process
else
    OP -> OP: Abort Process with Error
end

OP -> OP: Applies Policies
OP -> OP: Derive RP's final metadata

@enduml
```
**Sequence Diagram x.** The [sequence diagram](https://www.plantuml.com/plantuml/uml/TPB1Qjmm48RlUeeXbwQ7la0F9KkofQO56oVjAH1KQxmUa4TYDDfctxvoXC4n6ryiPhxv_tzitIMredds9fOt3HGjAzoq4RbvW4x2cHmAtBRTvnkm2ThkFTYZln2Ve2l52zps5OD-XpMiA3CwiefmP2KbJ6zaStnFFabE2WSUfY1lmDF1cBQ3Bz-Aw5VuZZfCvVGfLPaBt0SUwPJ5AWKs_HlP5h97pSBNgOtWyLN53iKnQt5Sq1_4cc2KC5UV3mpMZxEVDCQ7464C0cY7QTSsK9xgtXpNTQvbN8WJAepei5PUnWL-iP7WT_zh4IeIDSmgz9Z_1d74LX4UrIhEkB6iv1_hoIs0JWXHy4mX9qWh_Smv4P-MRX5TlTU8F_6IdZdMpHqGBzm7jiZEc2k-zG_VJvRO6EiLcd5R0qfmNbYmx20plaOITBijEWa3eeD_fDCcPwsdPD0dt8qSIaXTWJfu50co3tg8qHCcw8AdeHmshGSKEcEz5YEnzJ2ZMcVL6dDNPFBi_mC0) about an OP building the Trust chain related to an RP.


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
Sequence Diagram x. This [sequence diagram](http://www.plantuml.com/plantuml/svg/TP11ImCn48Nl-HL3B-f1lFSWBUiM5WJBjdWIR9hCTWUpapKpKVlltMv1MR4v967uvirxin2rINCNJ3-yvAYth31xHDl0TRs-UrhVpC0Ad0fJf6B5rA4dgmWbAocXZ6nLJibjogAmPDV6D0BRfJ7ZXDlxKNWEArJNZlyhEOiTeOZSq24dgnJvXw_AETJudb4KDdaVaoS-ETZeS7V8AY-SXVW0EuavENn5zdqarfsfoj-9DMfEV47261iKgWRErelM4WC996JyjFk01eOpYJ11Dzav56bo24UeJqte3GRvhwzYqd67HR0Yv6o05NeynuO74XS_C1qDil-xjMSRo5l5IoccTD6YUvlOBBzw6fGNo3vtuHi0) illustrates the streamlined process of renewing a trust chain through Fast Renewal. By directly fetching and validating the necessary subordinate statements and, if applicable, the trust anchor statement, entities can efficiently maintain their trust relationships within the federation. 

This method significantly reduces the overhead and complexity involved in re-establishing trust chains, provided the conditions for its application are met.

## 9. Federation Endpoints

In the context of OpenID Federation, the operation of trust is central to the interaction between various entities within the federation. Each entity, by virtue of participating in the federation, acts as a federation entity involved in trust evaluation processes. This encompasses a wide range of activities from verifying the authenticity of other entities to ensuring compliance with federation policies.

However, within this ecosystem, certain entities such as the Trust Anchor, its Intermediates, and Trust Mark Issuers play a pivotal role in the accreditation process. These entities have the authority to accredit or endorse other entities within the federation, thereby holding a significant position in the trust hierarchy. To facilitate their operations and interactions within the federation, these entities are required to expose specialized endpoints. These endpoints are designed to support the unique functions these entities perform, ranging from issuing trust marks to validating entity credentials.

The table below outlines the specialized endpoints required by entities with accreditation power within the OpenID Federation, where each name represent the key name within the `federation_entity` JSON Object, contained in the Entity Configuration `metadata` parameter. Please keep in mind that the Entity Configuration is available at the endpoint `.well-known/openid-federation`, since the `.well-known/openid-federation` is a mandatory endpoint required for all entities within the OpenID Federation. This `.well-known/openid-federation` endpoint serves as a foundational element for federation operations such as the discovery process, ensuring that Entity Configurations are discoverable and accessible in a standardized manner and is provided in a well-known location.


| Name | Entity Type | Description |
|-------------------------------|----------------|-----------------------------------------------------------------------------|
| **federation_list_endpoint** | Trust Anchor, Intermediates | Endpoint for publishing the list of accredited entities (Subordinates) in the form of JSON Array.  |
| **federation_fetch_endpoint** | Trust Anchor, Intermediates | Endpoint for retrieving accreditation information and updates for a specific subject, in the form Subordinate Statement. |
| **federation_trust_mark_endpoint** | Trust Mark Issuers | Endpoint for issuing and managing Trust Marks to accredited entities. |
| **federation_trust_mark_status_endpoint** | Trust Mark Issuers | Endpoint for validation the non revocation of a trust mark issued for a particular subject. |
| **federation_trust_mark_list_endpoint** | Trust Mark Issuers | Endpoint for publishing the list of trust marked entities about a particular Trust Mark, the result is a JSON Array. |
| **federation_historical_keys_endpoint** | Trust Anchor | Endpoint for publishing the signed status of historical cryptographic public keys that are no longer in use, including details on whether they are expired or revoked, along with the reasons for their revocation. |
| **federation_resolve_endpoint** | All entities | Endpoint for publishing the resolved metadata and trust chain for entities with whom trust has been established and maintained over time. | 

These specialized endpoints are essential for the operation and management of trust within the federation. They enable entities with accreditation power to efficiently perform their roles, ensuring that trust and compliance are maintained across the federation. By providing these endpoints, the federation supports a robust infrastructure for trust management, accreditation, and policy enforcement, which is crucial for the secure and seamless operation of the federation ecosystem.

Below is an example of an decoded Entity Configuration, where the JWT header and payload are represented as JSON objects, that includes a metadata section with a `federation_entity` type. This example also illustrates how the previously discussed federation endpoints are represented within the configuration, assuming that the entity is a TA or an Intermediate and also a Trust Mark Issuer:

````
{
  "iss": "https://entity.example.com",
  "sub": "https://entity.example.com",
  "metadata": {
    "federation_entity": {
      "federation_list_endpoint": "https://example-entity.com/federation/list",
      "federation_fetch_endpoint": "https://example-entity.com/federation/fetch",
      "federation_trust_mark_endpoint": "https://example-entity.com/federation/trust-mark",
      "federation_trust_mark_status_endpoint": "https://example-entity.com/federation/trust-mark-status",
      "federation_trust_mark_list_endpoint": "https://example-entity.com/federation/trust-mark-list",
      "federation_historical_keys_endpoint": "https://example-entity.com/federation/historical-keys",
      "federation_resolve_endpoint": "https://example-entity.com/federation/resolve"
    }
  },
  "jwks": {
    "keys": [
      {
        "kty": "RSA",
        "use": "sig",
        "kid": "1",
        "n": "0vx7agoebGcQSuuPiLJXZptN9nndrQmbXEPCRfceaC7mkxr8v...",
        "e": "AQAB"
      }
    ]
  },
  "authority_hints": ["https://trust-anchor.example.com", "https://intermediate.other-federation.example.org"],
  "iat": 1600000000,
  "exp": 1631536000
}
````

## 10. Federation Endpoints Errors

For each federation endpoint, the specific errors are defined within the federation specifications. These detailed definitions ensure that implementers can accurately handle errors and provide consistent responses across different federation entities.

### Trust Chain

Imagine a chain of trust, like a relay race:

    - It starts with Entity Configuration, like the starting point.
    - There can be zero or more Subordinate Statements, like runners passing the baton. These statements are issued by Intermediates (think of them as middle runners) about other entities called Subordinates (like the next runner in line).
    - Finally, there's a statement issued by the Trust Anchor (the end of the race). This statement is either about the last Intermediate or the final entity (the Leaf Entity).
    - This chain always ends with the Entity Configuration of the Trust Anchor, like crossing the finish line, even though it might not always be shown in the details.

Below is an example of a Trust Chain, represented as a JSON Array. It contains entity statements beginning with the Entity Configuration (at position 0) about the subject of the trust evaluation. It includes subordinate statements and concludes with the Trust Anchor's Entity Configuration. Each statement is linked to its sibling statement by the `sub`, `iss`, `authority_hints`, `jwks`, and the cryptographic signature, which can be verified using the provided JWK.

```
[
      "eyJhbGciOiJSUzI1NiIsImtpZCI6ImMzWnlUR1ZTY0RNeFowd3dka1pQVVZZMFMyRkVRMnBYVjE4dFVFd3ROVlZIV21WVVEwTmlTM2xVU1EiLCJ0eXAiOiJlbnRpdHktc3RhdGVtZW50K2p3dCJ9.eyJleHAiOjE2OTY1ODMzMzQsImlhdCI6MTY5NjI4MzMzNCwiaXNzIjoiaHR0cHM6Ly9jcmVkZW50aWFsX2lzc3Vlci5leGFtcGxlLm9yZyIsInN1YiI6Imh0dHBzOi8vY3JlZGVudGlhbF9pc3N1ZXIuZXhhbXBsZS5vcmciLCJqd2tzIjp7ImtleXMiOlt7Imt0eSI6IlJTQSIsImtpZCI6ImMzWnlUR1ZTY0RNeFowd3dka1pQVVZZMFMyRkVRMnBYVjE4dFVFd3ROVlZIV21WVVEwTmlTM2xVU1EiLCJlIjoiQVFBQiIsIm4iOiIwNmZfWUd0ejhyY29WQWZSUnlNbldYbmNteVRFc3lmUHh6OVlXdlRjT0c1WGtIQ244c1ZCa3hjVENleDFvdWtxZVlVLU0xcUE3ZXE4RmNMRDJHUEVxVHNITDBRNVNMLXVYSUlYd2hSSmx3bWpRNWh1dEdnV3ptMUVOc0g0SENyV3EwNmJGLTRpUFFpUVUxRm1kVXdxVTI5clVqTnlmeUlLTmd1cFJPZ2p1Y3ozcWYtWE1ZdU5SRnFFcld0b3N0dHE1bm4zdFlSNWh5UWpVWnVwNHZPUnFhUUROMUxiVEk5d2cxRzlxRjlxQUxDcGk0WlNmclJyWEphZDUtamwtS1VOdFM1cG5hbGlHRFF2a3AxTnJCaXBJT19haTFvR21BMTlHd1E4VGpNVkNFc3ZXUXpKcTVrd0N1eHVKZTlBODE1RzRuU2I1WFp1RXZ6anhNU1M4b3hpLVEifV19LCJtZXRhZGF0YSI6eyJvcGVuaWRfY3JlZGVudGlhbF9pc3N1ZXIiOnsiandrcyI6eyJrZXlzIjpbeyJrdHkiOiJSU0EiLCJraWQiOiJSMlJ6UlhBMFJWQnlkekZPVkcxZmRXUlRNVFozWVRSbU5uRTFWM0ZmTUUxb01VWkxla2xpWTFOWU9BIiwiZSI6IkFRQUIiLCJuIjoiNUhfWGg3eGdEV1R4UVZiSnFtT0d1cm9rRThrcjJlMUtnTVdjWU9BNzRPXzFQWGQydWdqeUlxOXQxbVZQU3V3eC10eVNrMlBLcGpwLS1XckhuM0E0VUtKa3VSMTF6aG1kTEJzVThUT0JCdTVNWjhhdER1amZSd0lMWGRLc0VYa2x2YUI2UExUNHpkWm9kZ0NzMDVLeTJlNXNiNXo2X0NpRHFnVVZuV1BtSkxNa2dwQnRaLWtNZF9sYjlTb29abGxmVUdsVGtzYXVKMl9nVlEtVnBGVU1YWWpvSmpOeDk3ZXVrYVluUkVvQ0MzVGFfLThiY1Jvc2x4MnhySWJ1X1VHVnFpcGVOM05QLW1lZmY5VlRaV1lNM2dtb2x3dXBuTUNYWGlpa2NSNVVTTFZnMGVfZ3o2T2ZvUlZHS0FXSXBSUkxUejJhaXF1a1ZIWmRaWDl0Tm16MG13In1dfX0sImZlZGVyYXRpb25fZW50aXR5Ijp7Im9yZ2FuaXphdGlvbl9uYW1lIjoiT3BlbklEIENyZWRlbnRpYWwgSXNzdWVyIGV4YW1wbGUiLCJob21lcGFnZV91cmkiOiJodHRwczovL2NyZWRlbnRpYWxfaXNzdWVyLmV4YW1wbGUub3JnL2hvbWUiLCJwb2xpY3lfdXJpIjoiaHR0cHM6Ly9jcmVkZW50aWFsX2lzc3Vlci5leGFtcGxlLm9yZy9wb2xpY3kiLCJsb2dvX3VyaSI6Imh0dHBzOi8vY3JlZGVudGlhbF9pc3N1ZXIuZXhhbXBsZS5vcmcvc3RhdGljL2xvZ28uc3ZnIiwiY29udGFjdHMiOlsidGVjaEBjcmVkZW50aWFsX2lzc3Vlci5leGFtcGxlLm9yZyJdfX0sImF1dGhvcml0eV9oaW50cyI6WyJodHRwczovL2ludGVybWVkaWF0ZS5laWRhcy5leGFtcGxlLm9yZyJdfQ.WM0dOdLOhC9UTRi_y6lwwu6PmUWv1wc5f8MhKP2qz6b1haa-eyf4Y60iZ2SDeFeg105wHwPSKN8AfRoNyqubOGZMAFmP-sYBeqZatNsGNWjFEHzI9dJwx1oBab5_TDoULarpKSdvuub4NBOBTKFUSDffBG6jzlaK5JiQzMR30-jqSt9qPPWrJepyLQvyFxmvn94OiNQQwJ0oYB1MecJ7r8gmQMCAH4LixD6amKQoCuN0UeN7iEHTs3z_8ZUDihQ7BHf0dtwDCmG_G7xvaNkLPkG6cP2e2-l5kcYbNaUaK0IsX2yB0BkoQOOHlT-_M2TC2Zzg6Nmmw8WpTeraMrSkIQ",
      "eyJhbGciOiJSUzI1NiIsImtpZCI6IlpsSndObTQ1VDJsVWNYbEhUR2cxWldobGJsZGpVV2xsY21RdFNVbFFXWHBIUzFKRVEwOHlUV1Z2TkEiLCJ0eXAiOiJlbnRpdHktc3RhdGVtZW50K2p3dCJ9.eyJleHAiOjE2OTY1ODMzMzQsImlhdCI6MTY5NjI4MzMzNCwiaXNzIjoiaHR0cHM6Ly9pbnRlcm1lZGlhdGUuZWlkYXMuZXhhbXBsZS5vcmciLCJzdWIiOiJodHRwczovL2NyZWRlbnRpYWxfaXNzdWVyLmV4YW1wbGUub3JnIiwiandrcyI6eyJrZXlzIjpbeyJrdHkiOiJSU0EiLCJraWQiOiJjM1p5VEdWU2NETXhaMHd3ZGtaUFVWWTBTMkZFUTJwWFYxOHRVRXd0TlZWSFdtVlVRME5pUzNsVVNRIiwiZSI6IkFRQUIiLCJuIjoiMDZmX1lHdHo4cmNvVkFmUlJ5TW5XWG5jbXlURXN5ZlB4ejlZV3ZUY09HNVhrSENuOHNWQmt4Y1RDZXgxb3VrcWVZVS1NMXFBN2VxOEZjTEQyR1BFcVRzSEwwUTVTTC11WElJWHdoUkpsd21qUTVodXRHZ1d6bTFFTnNINEhDcldxMDZiRi00aVBRaVFVMUZtZFV3cVUyOXJVak55ZnlJS05ndXBST2dqdWN6M3FmLVhNWXVOUkZxRXJXdG9zdHRxNW5uM3RZUjVoeVFqVVp1cDR2T1JxYVFETjFMYlRJOXdnMUc5cUY5cUFMQ3BpNFpTZnJSclhKYWQ1LWpsLUtVTnRTNXBuYWxpR0RRdmtwMU5yQmlwSU9fYWkxb0dtQTE5R3dROFRqTVZDRXN2V1F6SnE1a3dDdXh1SmU5QTgxNUc0blNiNVhadUV2emp4TVNTOG94aS1RIn1dfX0.GxUAOCfVHTyBvJzq9BIV76fX-bRqIVcL-Y36B8NLO17lE0rkfDQQGUh-Vh1JmF_MF3Q4QhXjW-agXoKST3q9x5nDoYHTleQ-10Lw9qiU9I6K8mn1JMNXahzNaX_MLOA7R3_O0-HhxtARIEKpiGQTjup_f5PyqBLrwcERxhnbg0YrpgsvVQHC1SUgXiMtKxMZDWLUtk7q8YaNNhJ4nuEV2M1ZWcEVFloWYKWBJbJvmZ9jqsop6svMDOqusih3sNGWKZ9XmPqtUeGR8o1vsKsOk0WKVnEMH9ESGu54pUJahX05jd4UmTPOzfRJ61k9-_te6xNZUwJqtU9wJ--IrHz7ig",
      "eyJhbGciOiJSUzI1NiIsImtpZCI6IlpXRlRRbWhmVFdaSVRuRlRZM0ZTV2pKdU5HMWZWV05hZWxkNmNtUjFRa0pEYlhaWlRYQm1hM1JWUVEiLCJ0eXAiOiJlbnRpdHktc3RhdGVtZW50K2p3dCJ9.eyJleHAiOjE2OTY1ODMzMzQsImlhdCI6MTY5NjI4MzMzNCwiaXNzIjoiaHR0cHM6Ly90cnVzdC1hbmNob3IuZXhhbXBsZS5vcmciLCJzdWIiOiJodHRwczovL2ludGVybWVkaWF0ZS5laWRhcy5leGFtcGxlLm9yZyIsImp3a3MiOnsia2V5cyI6W3sia3R5IjoiUlNBIiwia2lkIjoiWmxKd05tNDVUMmxVY1hsSFRHZzFaV2hsYmxkalVXbGxjbVF0U1VsUVdYcEhTMUpFUTA4eVRXVnZOQSIsImUiOiJBUUFCIiwibiI6ImtSQUlzLVRBblF6T29lc195RW9oaDNUeGlaWFlPRC1xekI2T25JalZCaVI0WFNGbkw4djhRQ0IxamJiSlZTQ1hBZmdObTlBZjdVRDNBX0E2T1pQOUtacldSUk00NEp0TVAzZmxVSU5CQ2xzNFBrdVd2RklmWEtaaWVUNGhBMzNYdUlTSndqT2NlNnRrbUZTUl9wQmI3S0JHbnB4d3dnRlJjeUM2RU1VQWtWUGdQRl9mbXM3NEEyTF83TGVaNzh2X053VUhsY2VxVHB1OUx6VTRuTTd1RTZQY0JPSmJweXI2SFFuUTR1VHN6WThsWHVmT3pHZ2IzVXNDNmRnQWVVVERIZlFROE4xYXV2bkJUNmR0SHNHNWMxS2NCT2QzLVp6ZTZ5WVE5c2JLUlUtWVoydDZKdUJIbS1xLTVOamxiRWVwNDAwbFgxSXlKdG9Tc3F0d3lCX1djdyJ9XX19.CvBvN0aG-r13UG4uITH72tC5CbAG0rT4qYQ5wwHOtGE021etZFQd40RFnQT5e-Gy_Y8Wiin-Zmc1hWW2rVyZ1RRInjYGUt26QI6ujR-5w9Y_LHVp-6RzYEF0lg9otpAyszQE4hf5qBZYAj8t39FvCYWTYVci6mtpovJQ380ha8I4QL__fZtEgDLQ-7VKS58nN1DOVdcIICMMfxpDR81bkY5i5Qxcy7AaZTN6xxE2SlCO-pKKub0jBUtnug20-BL2YgcPhLFOYWfj2cuyapOA9Omwu6CPhmZHgsL1P2oK_f4jA9JxNDYcV0losDbD86r8Wg3anM2lVM5BTHkiUr2grg",
      "eyJhbGciOiJSUzI1NiIsImtpZCI6IlpXRlRRbWhmVFdaSVRuRlRZM0ZTV2pKdU5HMWZWV05hZWxkNmNtUjFRa0pEYlhaWlRYQm1hM1JWUVEiLCJ0eXAiOiJlbnRpdHktc3RhdGVtZW50K2p3dCJ9.eyJleHAiOjE2OTY1ODMzMzQsImlhdCI6MTY5NjI4MzMzNCwiaXNzIjoiaHR0cHM6Ly90cnVzdC1hbmNob3IuZXhhbXBsZS5vcmciLCJzdWIiOiJodHRwczovL3RydXN0LWFuY2hvci5leGFtcGxlLm9yZyIsImp3a3MiOnsia2V5cyI6W3sia3R5IjoiUlNBIiwia2lkIjoiWldGVFFtaGZUV1pJVG5GVFkzRlNXakp1TkcxZlZXTmFlbGQ2Y21SMVFrSkRiWFpaVFhCbWEzUlZRUSIsImUiOiJBUUFCIiwibiI6InhPQnN0YnJUUE9SQ3JnTXowQ254Skk4cjd4TkwwYjZITExKY05NSkNQbGl0SmUzNUZUdGw1bVdMVXFoeFdiRXFYYnpVLW80VHd1ZHA5NHFrckI3Zks5cEk0bDBldEoxMFZTbTQ0dXNiakxoMTUwVXFlSnlmQmdyMzRWbWhINWo3ZFMzS3V5bjNvOEdER2ZjNDVjcmdsR3lNOUgzbFp6SGl6SXdncDVzSUhCeGFPRWQ1eXlOdXhBLXFFQU4zaFIxQi1LQ2JYaWVDRVRrcWNMWU1zX2psRHVsTVE0MzI5TEgwNkhaclhINElfUHVnWXd0WldUN0VqZUR6c3Y2Ny14TFFhOEg3cnV1N3JVME1FREl0TkVpS3ljdVk5b0pLWWtrX2hBQzFMNkt2VkNmeXpVOGVvem9KQzM4eGhXUklNTXJZd2hiSmFaa3dUdDZCRDByalNNRUp6USJ9XX0sIm1ldGFkYXRhIjp7ImZlZGVyYXRpb25fZW50aXR5Ijp7ImZlZGVyYXRpb25fZmV0Y2hfZW5kcG9pbnQiOiJodHRwczovL3RydXN0LWFuY2hvci5leGFtcGxlLm9yZy9mZXRjaCIsImZlZGVyYXRpb25fcmVzb2x2ZV9lbmRwb2ludCI6Imh0dHBzOi8vdHJ1c3QtYW5jaG9yLmV4YW1wbGUub3JnL3Jlc29sdmUiLCJmZWRlcmF0aW9uX2xpc3RfZW5kcG9pbnQiOiJodHRwczovL3RydXN0LWFuY2hvci5leGFtcGxlLm9yZy9saXN0Iiwib3JnYW5pemF0aW9uX25hbWUiOiJUQSBleGFtcGxlIiwiaG9tZXBhZ2VfdXJpIjoiaHR0cHM6Ly90cnVzdC1hbmNob3IuZXhhbXBsZS5vcmcvaG9tZSIsInBvbGljeV91cmkiOiJodHRwczovL3RydXN0LWFuY2hvci5leGFtcGxlLm9yZy9wb2xpY3kiLCJsb2dvX3VyaSI6Imh0dHBzOi8vdHJ1c3QtYW5jaG9yLmV4YW1wbGUub3JnL3N0YXRpYy9sb2dvLnN2ZyIsImNvbnRhY3RzIjpbInRlY2hAdHJ1c3QtYW5jaG9yLmV4YW1wbGUub3JnIl19fSwiY29uc3RyYWludHMiOnsibWF4X3BhdGhfbGVuZ3RoIjoxfX0.ZenICRAm4zrd65A3tsSN77VLjheJAK09s8sBXQVo2l3r7RrN8pXFKqulZebxfSALF4am58Ry8GeK2N1ZK7B1GlfskAnnIINfi-IpjqquvPW_MXrSo0bLoXnnmruHJ1D0KbONbwdZaOZg4oJRo2PBU009pojheUwcRcNDujCRiQimsvEYlL4YLwpPHjEmt46WviPESnZCgez_oEdoXkNjN3PsOta9de_abjkASYdGgAlBTIbUjAutmErbubjKptxpvycibBLuJ58nkiQZ_KesLRPnDY9hbPf0ZMbtCB_gx8yLlQ9-8nE5_fuxICz1uKR4536txsQFmrCvireBchaZ1A"
  ]
```

## 11. Differences between OpenID Federation 1.0 and OpenID4RS

TBD.

Highlights the key differences and improvements of the OpenID4RS profile over the standard OpenID Federation 1.0, emphasizing enhancements tailored for the Research and Education ecosystem.

## 13. Security Considerations and Current Best Practices

TBD.

Discusses the security considerations and best practices within the federation, aiming to ensure the integrity, confidentiality, and availability of federated services.

## 14. Examples
Provides practical examples of federation scenarios, configurations, and implementations, offering insights into real-world applications of the OpenID Federation.

## 15. Become a Provider
Guides entities through the process of becoming a provider within the OpenID Federation, detailing the requirements, steps, and benefits of participation.

