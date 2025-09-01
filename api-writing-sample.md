### Writing Sample: Lender Integrations Guide

* **Topic:** API Resource Descriptions for Third-Party System Integration
* **Audience:** Software developers and system administrators responsible for integrating a third-party system with a centralized order management platform.

---

### I. Document Overview

This integration guide provides detailed information on API communications, covering both inbound and outbound data from integrating third-party systems. This document is published in a collapsible format to allow users to focus on specific content by expanding and collapsing sections as needed. It is recommended that users utilize the document navigation tree for easy access to information.

### II. Interface

#### Introduction

The API is an XML-based interface that allows third-party systems to place orders, receive orders, and monitor status updates. The API uses two namespaces: `Integration.xsd` for communication context that is independent of the platform, and `OrderPlatform.xsd` for context specific to the platform. All posts to the endpoint are synchronous and will generate a **Standard Response** unless otherwise specified.

#### XML Schema: Essential Information

When integrating with the API, parties should avoid using blank XML tags unless they have the `minOccurs=1` attribute. This is because other instances of blank tags may cause issues if the system expects a specific format for a tag that is not required. It is considered best practice to validate XML against the XSD namespaces before posting to the API to catch potential errors before an order is rejected.

When processing a final status update, your service should provide a positive acknowledgment before attempting to download any included files. Attempting to download files prematurely can lead to a timeout, causing the system to repost the data.

#### API Configuration

API credentials, such as login and password, are managed within the **Administration/Interface** tab of the platform. This is also where users can specify the URLs for the platform to post data to their third-party system and select which file types to include in a final status update.

Client API URLs must be public and secure (`https`). The security certificate must be issued by a Certificate Authority (CA) that participates in the Microsoft Trusted Root Certificate Program and cannot be self-signed. The client's landing page must support either Basic Authentication or an XML ID and password.

#### Authentication

The platform's digital gateway uses OAuth 2.0 Client ID/Secret to identify the vendor. To receive a token, clients must send a Basic Authorization header with a base64 encoded "ClientId:ClientSecret" to the token endpoint.

* `UAT Endpoint: POST https://api-uat.example-digital-gateway.com/order-gateway-oauth2/token?grant_type=client_credentials`
* `Production Endpoint: POST https://api-prod.example-digital-gateway.com/order-gateway-oauth2/token?grant_type=client_credentials`

The response will contain the OAuth token, which should then be included in all subsequent API calls by passing it as a Bearer token in the Authorization header.

### III. API Resource Descriptions

The following sections detail the requests and responses for each individual API endpoint, along with specific data point requirements for integration.

#### 3.0 Create Order

This is a post originating from a third-party system that sends order data into the platform using `CreateOrderRq` XML.

#### 3.1 Create Order Request Description

A `CreateOrderRq` XML post includes the main nodes `Header` and `Order`. The `Header` element is a standard request header that must accompany each API request. The `Order` element contains all of the specific order details.

#### 3.2 CreateOrderRq XML Tables

The following tables define the data points available in the `CreateOrderRq` XML, including those required to create an order and those that are optional.

**Table 3.1 CreateOrderRq: Order element**

| Element | Description | Type | Required | Notes |
| :--- | :--- | :--- | :--- | :--- |
| Order | Root node | Complex (`CreateOrderRqType`) | | |
| GroupID | Primary Key (PK) for Group associated with order | int | Optional | The full list of PKs for all client Groups will be provided by the platform. |
| ProcComments | Processor comments | string | 1024 Optional | |
| OrderInfo | Info related to the order | Complex (`OrderInfoType`) | Required | See OrderInfo table for documentation. |
| PropertyAddress | Info related to property address | Complex (`AddressInfoType`) | Required | See PropertyAddress table for documentation. |
| PropertyTypeId | Identifier for type of property (i.e., condominium, etc.) | `PropertyTypeId` | Required | |
| LoanPurposeId | Identifier for loan purpose (i.e., refinance, etc.) | `LoanPurposeId` | Required | |
| OccupancyTypeId | Identifier for property’s occupancy type (i.e., investor, etc.) | `OccupancyTypeId` | Required | |
| LoanAmount | Requested loan amount | decimal | Optional | |
| ServiceId | Identifier for requested service type | `ServiceId` | Optional | **Required when ProductOrderType is not submitted.** |
| ServiceProviderID | Identifier for service provider | string | Optional | |
| ElectronicDeliveryConsent | Enables the platform to electronically deliver documents to designated recipients | boolean | Optional | Pass 1 for consent, 0 or omit otherwise. |

*XML samples redacted.*

#### 3.4 Create Order Response Description

The `CreateOrderRs` is a synchronous call that is expected to return a `FolderId` and `DocId` for future reference. The third-party system should store this `FolderId` and `DocId` in its own records. The response may also contain a processing error and message.

**Table 3.2 CreateOrderRs: Tracking element**

| Element | Description | Type | Required | Notes |
| :--- | :--- | :--- | :--- | :--- |
| Tracking | Passes order details used for tracking between a third-party system and the platform | Complex (`OrderTrackingType`) | | |
| FolderId | Number associated with a folder for tracking purposes | string | 20 Required | |
| DocId | Number associated with a document for tracking purposes | string | 20 Required | The DocId is also used for status updates. |
| LoanNumber | Echo back of the loan number received in the request | string | 60 Required | |
| SubPortId | Sub-port ID of the newly created document | string | 20 Optional | |
| IsAVM | Denotes whether the order is an Automated Valuation Model (AVM) order | boolean | Optional | |

*XML samples redacted.*

