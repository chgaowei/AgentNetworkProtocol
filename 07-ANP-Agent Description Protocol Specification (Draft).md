# Agent Description Protocol Specification (Draft)

## Abstract

This specification defines the Agent Description (AD), a standardized protocol for describing agents. Based on JSON-LD format and combining schema.org vocabulary with custom vocabularies, it provides a unified way to describe agents.

The protocol's core components include:
1. Using JSON-LD as the base data format, supporting linked data and semantic web features
2. Defining core vocabularies for agent basic information, products, services, and interfaces
3. Adopting the did:wba method as a unified security mechanism for cross-platform authentication
4. Supporting interoperability with other protocols (such as OpenAPI, JSON-RPC)

This specification aims to enhance interoperability and communication efficiency between agents, providing foundational support for building agent networks.

## Introduction

Agent Description (AD) serves as an entry point for agents. Other agents can obtain information about an agent's name, affiliated entity, functions, products, services, interaction APIs, or protocols based on its AD. This information enables data communication and collaboration between agents.

As AI capabilities continue to advance and their natural language understanding improves, theoretically, pure natural language without fixed specification requirements could be used to describe agent information. However, this would increase communication costs between agents and reduce consistency in information understanding between agents. Therefore, to improve communication efficiency between agents, a specification for describing agents is needed to ensure consistency in information exchange between agents.

## Overview

We use [JSON-LD](https://www.w3.org/TR/json-ld11/) (JavaScript Object Notation for Linked Data) as the format for agent descriptions. JSON-LD is a standard format for representing linked data and semantic web data. It is based on JSON (JavaScript Object Notation) syntax and allows embedding links in JSON objects to represent relationships and structures between data.

This way, we can store all information about an agent as multiple files and then use the agent description document to link these files together. The agent description document serves as an entry point for the agent, allowing other agents to obtain all information about the agent based on this description document.

To improve consistency in agents' understanding of description documents, we have defined a series of standard vocabularies. Additionally, when describing resources such as products and services, we recommend using schema.org vocabularies to enhance document understanding consistency. If schema.org vocabularies cannot meet requirements, users can also define custom vocabularies. Another advantage of using schema.org vocabularies is that the data can be processed directly by code, rather than only by AI.

JSON-LD also needs to be used in conjunction with other protocols. For example, interface description protocols can use OpenAPI described in YAML format or RPC protocols described in JSON-RPC format.

Here is an example of an agent description document:

```json
{
  "@context": {
    "@vocab": "https://schema.org/",
    "did": "https://w3id.org/did#",
    "ad": "https://agent-network-protocol.com/ad#"
  },
  "@type": "ad:AgentDescription",
  "@id": "https://agent-network-protocol.com/agents/smartassistant",
  "name": "SmartAssistant",
  "did": "did:wba:example.com:user:alice",
  "owner": {
    "@type": "Organization",
    "name": "Hangzhou Bit Intelligence Technology Co., Ltd.",
    "@id": "https://agent-network-protocol.com"
  },
  "description": "SmartAssistant is an intelligent agent solution for individuals and enterprises, providing various natural language processing and cross-platform connectivity capabilities.",
  "version": "1.0.0",
  "created": "2024-12-31T12:00:00Z",
  "securityDefinitions": {
    "didwba_sc": {
      "scheme": "didwba",
      "in": "header",
      "name": "Authorization"
    }
  },
  "security": "didwba_sc",
  "products": [
    {
      "@type": "Product",
      "name": "AI Assistant Pro",
      "description": "A high-end AI assistant offering advanced customization services.",
      "@id": "https://agent-network-protocol.com/products/ai-assistant-pro"
    },
    {
      "@type": "Product",
      "name": "Agent Connector",
      "description": "A cross-platform connectivity solution for intelligent agents.",
      "@id": "https://agent-network-protocol.com/products/agent-connector"
    }
  ],
  "interfaces": [
    {
      "@type": "ad:NaturalLanguageInterface",
      "protocol": "YAML",
      "url": "https://agent-network-protocol.com/api/nl-interface.yaml",
      "description": "A YAML file for interacting with the intelligent agent through natural language."
    },
    {
      "@type": "ad:PurchaseInterface",
      "protocol": "YAML",
      "url": "https://agent-network-protocol.com/api/purchase-interface.yaml",
      "description": "A YAML file for interacting with the intelligent agent through purchase."
    },
    {
      "@type": "ad:APIInterface",
      "protocol": "JSON-RPC 2.0",
      "url": "https://agent-network-protocol.com/api/api-interface.json",
      "description": "A JSON-RPC 2.0 interface for programmatic interaction with the intelligent agent."
    }
  ]
}
```

Here is an example of a product description:
```json
{
  "@context": {
    "@vocab": "https://schema.org/"
  },
  "@type": "Product",
  "@id": "https://agent-network-protocol.com/products/ai-assistant-pro",
  "name": "AI Assistant Pro",
  "description": "A high-end AI assistant offering advanced customization services.",
  "version": "2.1.3",
  "brand": {
    "@type": "Brand",
    "name": "SmartTech"
  },
  "offers": {
    "@type": "Offer",
    "price": "99.99",
    "priceCurrency": "USD",
    "availability": "https://schema.org/InStock",
    "url": "https://agent-network-protocol.com/store/ai-assistant-pro"
  }
}
```

## Namespaces

In this specification, vocabulary terms related to agent descriptions are always presented in their compact form. Their expanded form can be accessed through the namespace IRI https://agent-network-protocol.com/ad#.

## AD Information Model

### Overview

The AD information model is built on the vocabulary at https://agent-network-protocol.com/ad# and schema.org's vocabulary.

The "ad" namespace defines key vocabularies for agent description, while schema.org defines common vocabularies for agent description. These vocabularies can be customized by users and can be automatically recognized by AI.

### Vocabulary Definitions

#### Core Vocabulary Definitions

##### Agent
An agent is an abstraction of a physical or virtual entity, whose metadata and interfaces are described through an Agent Description (AD) document. A virtual entity can be a combination of one or more agents.

Table 1: Agent Level Vocabulary Terms

| Vocabulary Term | Description | Required | Type |
|----------------|-------------|----------|------|
| @context | JSON-LD keyword for defining shorthand names (terms) used in the AD document. | Required | anyURI or Array |
| @type | JSON-LD keyword for adding semantic tags (or types) to objects. | Optional | string or Array of string |
| @id | Agent identifier in URI [RFC3986] format (e.g., stable URIs, temporary and mutable URIs, URIs with local IP addresses, URNs, etc.). | Optional | anyURI |
| did | Decentralized Identifier (DID) of the agent, used for uniquely identifying the agent's identity. | Optional | string |
| name | Provides human-readable name based on default language (e.g., text for UI display). | Required | string |
| description | Provides additional (human-readable) information based on default language. | Optional | string |
| version | Provides version information. | Optional | VersionInfo |
| created | Provides creation time information for the AD instance. | Optional | dateTime |
| modified | Provides last modification time information for the AD instance. | Optional | dateTime |
| owner | Provides agent owner information. Can be a person or organization. | Optional | Person or Organization |
| products | List of all products provided by the agent. | Optional | Array of Product |
| services | List of all services provided by the agent. | Optional | Array of Service |
| interfaces | All interface definitions provided by the agent. | Optional | Array of Interface |
| security | Collection of security definition names, selected from securityDefinitions. All security requirements must be met when accessing resources. | Required | string or Array of string |
| securityDefinitions | Collection of named security configurations (definitions only). Only applied when used in security name-value pairs. | Required | Map of SecurityScheme |

For @context, AD instances define the following rules:

1. The @context name-value pair must include the anyURI https://agent-network-protocol.com/ad# to identify the document as an AD document.
2. When @context is an array, it can contain multiple anyURI or Map type elements. It is recommended to include all name-value pairs in one Map.
3. Maps in the @context array can contain name-value pairs where values are anyURI type namespace identifiers and names are terms or prefixes representing that namespace.
4. One Map in the @context array should contain a name-value pair defining the AD default language, where the name is the term @language and the value is a language tag conforming to [BCP47] (e.g., en, zh-CN, zh-TW, etc.).

##### Interface
Interfaces define ways to interact with the agent. Basic interface types include:

1. APIInterface: API interface for interacting with the agent through specific API protocols
2. NaturalLanguageInterface: Natural language interface for interacting with the agent through natural language
3. PurchaseInterface: Purchase interface for interacting with the agent through specific purchase protocols

Table 5: Interface Level Vocabulary Terms

| Vocabulary Term | Description | Required | Type |
|----------------|-------------|----------|------|
| @type | Interface type. | Required | string |
| @id | Unique identifier for the interface. | Required | anyURI |
| name | Interface name. | Required | string |
| description | Detailed description of the interface. | Required | string |
| protocol | Protocol used by the interface, currently supports YAML, JSON-RPC 2.0. | Required | string |
| url | URL of the interface definition document. | Required | anyURI |
| version | Interface version information. | Optional | string |
| security | Security requirements for the interface. | Optional | SecurityScheme |

### Security Mechanism

The Agent Description Protocol currently uses the did:wba method as its security mechanism. The did:wba method is a Web-based Decentralized Identifier (DID) specification designed to meet the needs of cross-platform identity authentication and agent communication.

Other authentication schemes may be extended in the future as needed.

#### DIDWBASecurityScheme

Metadata describing the configuration of a security mechanism based on the did:wba method. The value assigned to the scheme name must be defined in a vocabulary included in the agent description.

For all security schemes, any keys, passwords, or other sensitive information directly providing access MUST NOT be stored in the AD and should instead be shared and stored out-of-band via other mechanisms. The purpose of an AD is to describe how to access an agent if and only if a Consumer already has authorization, and is not meant to be used to grant that authorization.

Security schemes generally require additional authentication parameters, such as digital signatures. The location of this information is indicated by the value associated with name, often in combination with the value associated with in. The value associated with in can take one of the following values:

- header: The parameter will be given in a header provided by the protocol, with the name of the header provided by the value of name. In the did:wba method, authentication information is passed through the Authorization header.
- query: The parameter will be appended to the URI as a query parameter, with the name of the query parameter provided by name.
- body: The parameter will be provided in the body of the request payload, with the data schema element used provided by name.
- cookie: The parameter is stored in a cookie identified by the value of name.
- uri: The parameter is embedded in the URI itself, which is encoded in the relevant interaction using a URI template variable defined by the value of name.
- auto: The location is determined as part of the protocol, or negotiated. If a SecurityScheme's in field is set to auto, then the name field SHOULD NOT be set.

Table 2: Security Scheme Level Vocabulary Terms

| Vocabulary Term | Description | Required | Type |
|----------------|-------------|----------|------|
| @type | JSON-LD keyword to add semantic tags to objects. | Optional | string or Array of string |
| description | Provides additional (human-readable) information. | Optional | string |
| scheme | Identifier of the security mechanism. | Required | string |
| in | Location of the authentication parameter. | Required | string |
| name | Name of the authentication parameter. | Required | string |

Here is an example of a security configuration using the did:wba method:

```json
{
    "securityDefinitions": {
        "didwba_sc": {
            "scheme": "didwba",
            "in": "header",
            "name": "Authorization"
        }
    },
    "security": "didwba_sc"
}
```

Security configuration in AD is required. Security definitions must be activated through the security member at the agent level. This configuration is the security mechanism required for interacting with the agent.

When security appears at the top level of the AD document, it means that all resources must be accessed using this security mechanism for authentication. When it appears within a specific resource, it means that the resource can only be accessed when this security mechanism is satisfied. If the security specified at the top level differs from that specified within the resource, the security specified within the resource takes precedence.

## Common Definition Standardization

For a specific product or service, such as a cup of coffee or a toy, we can use a subset of schema.org's Product properties to define a specific type that clarifies how the product should be described. This way, all agents can use unified definitions when constructing product data, facilitating interoperability between different agents.

A similar approach can be used for interfaces. For example, with product purchase interfaces, we can define a unified purchase interface specification that all agents can use, enabling interoperability between different agents.