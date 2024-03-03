# General Architecture of Trust, Authentication and Authorization Infrastructure for Research and Education

This document defines the general architecture for trust and authorization infrastructure, designed to facilitate access to protected resources, including services and personal data. This document is developed by the Research and Education community, even if it is drawed on advancements in SAML2 and OAuth 2.0 technologies, it aims to be technology-agnostic. It focuses on the key components and principles necessary for managing digital identities and authorizing entities across various domains and organizations.

This document provides detailed requirements and design considerations offering a systematic approach to understanding complex trust relationships and authorization patterns, crafting an infrastructure capable of supporting and scaling these relationships efficiently.

By thoroughly examining the requirements and according to their satisfaction, it defines the necessary core components, policies, patterns that enable entities within the Research and Scholarship ecosystem to establish and maintain trusted and authorized relationships.

The ultimate objective of this document is to equip stakeholders with a comprehensive framework for implementing a trust and authorization model. This model aims to enable secure, efficient, and collaborative exchanges within a broad, cross-organizational and cross-domain ecosystem.

## Summary

The architecture is designed around two core components:

1. **Trust**: Involves the assessment and validation of entities' identities and their compliance with specific standards or regulations. This process includes organizations, natural or legal persons, services, or devices, and encompasses identity verification, compliance evaluation, accreditation, and the verification of compliance proofs. Trust establishes the foundation for determining an entity's eligibility to perform certain actions or fulfill specific roles within a regulated framework.
2. **Authorization**: Focuses on the processes of authenticating entities and authorizing their access to protected resources or services. This process includes the issuance of tokens, access control, and the enforcement of policies that govern the use of resources.

The document is organized into the following sections:

- Terminology
- Background and Problem Statement
- Requirements
- General Properties of the Infrastructure
- Trust Infrastructure Components
- Authorization Infrastructure Component
- Digital Identities Component
- Implementation Considerations
- Conclusion
- References


## 2. Terminology
This section defines key terms and acronyms used throughout the document.


| Term                   | Description                                                                                                                                                                                                                      |
|------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Trust Model**        | Principles and guidelines defining how trust is established, maintained, and validated within the ecosystem, particularly concerning digital identities and entity authorization.                                                |
| **Infrastructure of Trust**| The underlying physical and digital systems, protocols, and technologies that enable and support the establishment and management of trust among entities, in accordance with the Trust Model.                                    |
| **Trust Framework**    | Normative text that includes policies, administrative and accreditation protocols, and technologies designed to manage the trust infrastructure and the trust relationships.                                                      |
| **Policy Classes**     | Sets of rules, behaviors, and procedures for managing and enforcing compliance within the ecosystem.                                                                                                                             |
| **Trust Evaluation**   | The process of verifying and assessing the trustworthiness of entities, credentials, and the data exchanged.                                                                                                                     |
| **Trust Establishment**| The process of verifying adherence to agreed-upon standards and protocols between entities within the ecosystem.                                                                                                                  |
| **Authorization Server**| A server that issues tokens to clients after successfully authenticating an entity and obtaining authorization, facilitating access control.                                                                                      |
| **Identity Provider (IdP)**| An entity that creates, maintains, and manages identity information for users and provides authentication services to relying parties within a digital ecosystem. The Identity Provider verifies the identity of a principal and asserts that identity to other parties, facilitating secure access and interactions.|
| **User**               | An individual or entity that utilizes a system or service to perform actions or access resources. In the context of digital ecosystems, a user typically interacts with systems through authentication and authorization processes, often represented or managed by digital identities.|
| **Resource Server**    | A server that hosts protected resources and is capable of accepting and responding to protected resource requests using access tokens.                                                                                            |
| **Client**             | An application or entity making protected resource requests on behalf of the resource owner and with its authorization.                                                                                                           |
| **Relying Party**      | An entity that relies on the validation of assertions provided by another entity to establish a trust relationship or to authorize access.                                                                                        |
| **Trust Mark**         | A symbol or indicator issued by a trusted authority that signifies an entity has met certain criteria of trustworthiness, compliance, or security standards. Trust marks serve as visible tokens of assurance in digital transactions, helping to establish confidence among parties in an online environment.|

### 2.2 Acronyms

| Acronym | Full Form               |
|---------|-------------------------|
| TM      | Trust Mark              |
| TF      | Trust Framework         |
| AS      | Authorization Server    |
| RS      | Resource Server         |
| RP      | Relying Party           |
| IDP     | Identity Provider       |


## Background and Problem Statement

This section provides context, including a brief explanation of the Research and Education
ecosystem, the need for a trust model, and any relevant existing
systems or protocols.

### Research and Scholarship Ecosystem

...

### Existing Systems and Protocols

...

### Trust Infrastructure Topologies

...

## Requirements

1. CR-001: The system must support the integration of multiple Identity Providers (IdPs), belonging to different organizations and domains, to accommodate diverse digital identities.
2. CR-002: The trust model shall enable the evaluation and validation of entities' compliance with predefined standards and regulations.
3. CR-003: Authorization mechanisms must enforce access control based on roles, permissions, and policies defined within the Trust Framework.
4. CR-004: The infrastructure must ensure data privacy and security, adhering to applicable laws and standards such as GDPR or HIPAA.
5. CR-005: The system shall provide mechanisms for issuing, validating, and revoking Trust Marks to entities meeting specific trustworthiness criteria.
6. CR-006: Trust and authorization processes must support scalability to manage an increasing number of entities without compromising performance.
7. CR-007: The architecture must facilitate interoperability among different systems, protocols, and technologies within the ecosystem.
8. CR-008: Entities must be able to delegate authority or responsibilities securely, supporting distributed trust mechanisms.
9. CR-009: The system should enable transitive trust, allowing entities to trust each other based on common trusted intermediaries.
10. CR-010: All interactions within the ecosystem must ensure the integrity, confidentiality, and authenticity of exchanged data.
11. CR-011: The infrastructure should support the autonomy of entities, allowing them to define and manage their own trust and authorization policies.
12. CR-012: The system must provide a user-friendly interface for users to manage their digital identities and access permissions.
13. CR-013: Authorization Servers (AS) must implement protocols for secure token issuance, validation, and revocation.
14. CR-014: Resource Servers (RS) must be capable of handling protected resource requests in compliance with access tokens issued by Authorization Servers.
15. CR-015: The architecture must include mechanisms for continuous monitoring and auditing to detect and respond to security threats promptly.
16. CR-016: The system must implement mechanisms to assess and assign levels of assurance (LoA) to the identity of authenticated entities, such as users, based on the strength and reliability of the authentication process.
17. CR-017: Identity Providers (IdPs) must be capable of providing verifiable evidence supporting the assigned level of assurance for each authenticated entity.
18. CR-018: The trust model shall incorporate criteria for evaluating the level of assurance required for access to specific resources or services, ensuring appropriate security measures are enforced.
19. CR-019: Authorization decisions must take into account the level of assurance of the entity's identity, restricting access to resources or services based on the required assurance levels.
20. CR-020: The infrastructure must support dynamic adjustment of access rights and permissions based on changes to an entity's level of assurance over time.

## General Properties

This section outlines the foundational characteristics that the trust and authorization infrastructure must embody to effectively manage digital identities and authorize entities within the ecosystem. These properties ensure the infrastructure is adaptable, secure, and capable of supporting a wide range of interactions among entities. Key properties include:

- Delegation: The technical ability to delegate authority or responsibilities to other entities, facilitating a distributed mechanism for trust and authorization.
- Transitive Trust: The establishment of trust indirectly through a chain of trusted relationships, enabling entities to trust each other based on common authorities and trusted intermediaries.
- Scalability: The infrastructure's design to efficiently manage an increasing number of entities or interactions without a significant increase in complexity for trust management or authorization processes.
- Interoperability: The support for seamless interaction and trust establishment between diverse systems and entities within the ecosystem, ensuring compatibility and integration across different platforms and technologies.
- Flexibility: The adaptability of the infrastructure to various operational and organizational needs, allowing entities to define and adjust their trust relationships, authorization policies, and access controls.
- Autonomy: While part of a federated ecosystem, each entity retains control over its own definitions, configurations, and policies, ensuring independence within the collective framework.
- Security: The incorporation of mechanisms to ensure the integrity, confidentiality, and authenticity of the trust relationships, interactions, and authorization processes within the ecosystem.
- Privacy: The design of the infrastructure to respect and protect the privacy of the entities involved, adhering to principles of minimal disclosure and data protection.


## 4. Trust Infrastructure Components
This section explains the basic principles behind the trust model, such
as the concept of transitive trust and the role of a trusted third party.

### Roles

TBD.

### Technical Components

TBD.

### Accreditation

TBD.

### Interaction between Components

TBD.

### Trust Relationship Management Over Time

TBD.



#### Trust Chain
At the heart of this architecture is the concept of a Trust Chain, a
series of signed artifacts that represent the relationships and
permissions between entities. This chain is built  through a discovery
process. This section explains the concept of the Trust Chain, how it is
established and maintained, and its role in the trust model.


##### Concept of the Trust Chain

At a high level, the Trust Chain operates on the principle of transitive trust,
facilitated by a trusted third party common to all participating independent organizations.

In this model, each organization trusts the third party, often referred to as a Trust Anchor.

When Organization A wants to establish a trust relationship with Organization B,
it relies on the Trust Anchor, which both A and B trust. If the Trust Anchor or its descendant Intermediates vouches for B,
then A can trust B implicitly, even without direct interaction.

This principle allows for the creation of a Trust Chain, where trust is passed
along a chain of organizations, all linked by their common trust in the Trust Anchor.
This chain can extend to any number of organizations, allowing for a scalable
and efficient trust model.

This model reduces the complexity and overhead of establishing trust relationships
between numerous independent organizations, as each organization only needs to
establish and maintain a trust relationship with the Trust Anchor, rather than
with every other organization individually.


### Establishment and Maintenance of the Trust Chain

TBD.

### Role of the Trust Chain in the Trust Model

TBD.


### Establishment and Maintenance of the Trust Chain

TBD.


### Role of the Trust Chain in the Trust Model

TBD.

### Trust Marks
This section explains the concept of Trust Marks, how they are
defined and used, and their role in the trust model.

The model incorporates Trust Marks, which federations can
The model incorporates Trust Marks, which federations can
define and require based on their specific needs. These marks serve
as additional indicators of trustworthiness within the ecosystem.

- Role of Trust Marks in the Trust Model
- Role of Trust Marks in the Trust Model

#### Use of Trust Marks

... use cases ... common usages ...



## Authorization Infrastructure Component

...

## Digital Identities Component

...



## Implementation Considerations
This section discusses any practical considerations for implementing the
trust model, such as technical requirements, potential challenges, and
suggested solutions.

### 9.1 Technical Requirements

TBD.

### 9.2 Potential Challenges

TBD.

### 9.3 Suggested Solutions

TBD.


## 10. Conclusion
This section summarizes the document, reiterates the benefits of the
proposed trust model, and suggests next steps for implementation.

## 11. References
This section lists any sources or additional reading that were
referenced in the document.
