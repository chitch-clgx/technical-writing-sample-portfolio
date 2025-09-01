### Writing Sample: Lender Integrations Guide

* **Topic:** API Resource Descriptions for Third-Party System Integration
* **Audience:** Software developers and system administrators responsible for integrating a third-party system with a centralized order management platform.
* **Goal:** To provide a clear, comprehensive guide for interacting with the API, detailing authentication, request/response structures, and specific endpoint functionalities.

---

### I. Document Overview

This integration guide provides detailed information on API communications, covering both inbound and outbound data from integrating third-party systems. This document is published in a collapsible format to allow users to focus on specific content by expanding and collapsing sections as needed. It is recommended that users utilize the document navigation tree for easy access to information.

### II. Interface

#### **Introduction**

The API is an XML-based interface that allows third-party systems to place orders, receive orders, and monitor status updates. The API uses two namespaces: `Integration.xsd` for communication context that is independent of the platform, and `OrderPlatform.xsd` for context specific to the platform. All posts to the endpoint are synchronous and will generate a **Standard Response** unless otherwise specified.

#### **XML Schema: Essential Information**

When integrating with the API, parties should avoid using blank XML tags unless they have the `minOccurs=1` attribute. This is because other instances of blank tags may cause issues if the system expects a specific format for a tag that is not required. It is considered best practice to validate XML against the XSD namespaces before posting to the API to catch potential errors before an order is rejected.

When processing a final status update, your service should provide a positive acknowledgment before attempting to download any included files. Attempting to download files prematurely can lead to a timeout, causing the system to repost the data.

#### **API Configuration**

API credentials, such as login and password, are managed within the **Administration/Interface** tab of the platform. This is also where users can specify the URLs for the platform to post data to their third-party system and select which file types to include in a final status update.

Client API URLs must be public and secure (`https`). The security certificate must be issued by a Certificate Authority (CA) that participates in the Microsoft Trusted Root Certificate Program and cannot be self-signed. The client's landing page must support either Basic Authentication or an XML ID and password.

#### **Authentication**

The platform's digital gateway uses **OAuth 2.0 Client ID/Secret** to identify the vendor. To receive a token, clients must send a Basic Authorization header with a base64 encoded "ClientId:ClientSecret" to the token endpoint.

* **UAT Endpoint:** `POST https://api-uat.example-digital-gateway.com/order-gateway-oauth2/token?grant_type=client_credentials`
* **Production Endpoint:** `POST https://api-prod.example-digital-gateway.com/order-gateway-oauth2/token?grant_type=client_credentials`

The response will contain the OAuth token, which should then be included in all subsequent API calls by passing it as a Bearer token in the **Authorization** header.

---

### III. API Resource Descriptions

The following sections detail the requests and responses for each individual API endpoint, along with specific data point requirements for integration.

#### **3.0 Create Order**

This is a post originating from a third-party system that sends order data into the platform using `CreateOrderRq` XML.

#### **3.1 Create Order Request Description**

A `CreateOrderRq` XML post includes the main nodes `Header` and `Order`. The `Header` element is a standard request header that must accompany each API request. The `Order` element contains all of the specific order details.

#### **3.2 CreateOrderRq XML Tables**

The following tables define the data points available in the `CreateOrderRq` XML, including those required to create an order and those that are optional.

**Table 3.1 CreateOrderRq: Order element**

| Element | Description | Type | Required | Notes |
| :--- | :--- | :--- | :--- | :--- |
| **Order** | Root node | Complex (`CreateOrderRqType`) | | |
| **GroupID** | Primary Key (PK) for Group associated with order | int | Optional | The full list of PKs for all client Groups will be provided by the platform. |
| **ProcComments** | Processor comments | string | 1024 Optional | |
| **OrderInfo** | Info related to the order | Complex (`OrderInfoType`) | Required | See OrderInfo table for documentation. |
| **PropertyAddress** | Info related to property address | Complex (`AddressInfoType`) | Required | See PropertyAddress table for documentation. |
| **PropertyTypeId** | Identifier for type of property (i.e., condominium, etc.) | `PropertyTypeId` | Required | |
| **LoanPurposeId** | Identifier for loan purpose (i.e., refinance, etc.) | `LoanPurposeId` | Required | |
| **OccupancyTypeId** | Identifier for property’s occupancy type (i.e., investor, etc.) | `OccupancyTypeId` | Required | |
| **LoanAmount** | Requested loan amount | decimal | Optional | |
| **ServiceId** | Identifier for requested service type | `ServiceId` | Optional | **Required when ProductOrderType is not submitted.** |
| **ServiceProviderID** | Identifier for service provider | string | Optional | |
| **ElectronicDeliveryConsent** | Enables the platform to electronically deliver documents to designated recipients | boolean | Optional | Pass 1 for consent, 0 or omit otherwise. |

#### **3.3 CreateOrderRq Sample XML: Minimum Data**

```xml
<?xml version="1.0" encoding="utf-8"?>
<CreateOrderRq xmlns:xsi="[http://www.w3.org/2001/XMLSchema-instance](http://www.w3.org/2001/XMLSchema-instance)" xmlns:xsd="[http://www.w3.org/2001/XMLSchema](http://www.w3.org/2001/XMLSchema)" xmlns="[http://www.example.com/OrderPlatform](http://www.example.com/OrderPlatform)">
  <Header>
    <SourceApp xmlns="[http://www.example.com/Integration.xsd](http://www.example.com/Integration.xsd)">Client_System</SourceApp>
    <TargetApp xmlns="[http://www.example.com/Integration.xsd](http://www.example.com/Integration.xsd)">OrderPlatform</TargetApp>
    <AppInstance xmlns="[http://www.example.com/Integration.xsd](http://www.example.com/Integration.xsd)">SAMPLE_PORT</AppInstance>
    <AppInstanceLogin xmlns="[http://www.example.com/Integration.xsd](http://www.example.com/Integration.xsd)">user_name</AppInstanceLogin>
    <AppInstancePwd xmlns="[http://www.example.com/Integration.xsd](http://www.example.com/Integration.xsd)">P@ssw0rd123!</AppInstancePwd>
    <Timestamp xmlns="[http://www.example.com/Integration.xsd](http://www.example.com/Integration.xsd)">2025-09-01T11:30:00.000</Timestamp>
    <MessageId xmlns="[http://www.example.com/Integration.xsd](http://www.example.com/Integration.xsd)">1234abcd-5678-efgh-9101-ijkl2345mnop</MessageId>
  </Header>
  <Order>
    <OrderInfo>
      <LoanNumber>987654321</LoanNumber>
      <Channel>1</Channel>
    </OrderInfo>
    <PropertyAddress>
      <RequireNormalization>true</RequireNormalization>
      <UnstructuredStreetAddress>123 Main St, Apt 45</UnstructuredStreetAddress>
      <StreetNo/>
      <Prefix/>
      <Street/>
      <Suffix/>
      <UnitNo/>
      <City>Testville</City>
      <County>Test County</County>
      <State>MS</State>
      <Zip>38655</Zip>
    </PropertyAddress>
    <PropertyTypeId>CONDO</PropertyTypeId>
    <LoanPurposeId>Refi</LoanPurposeId>
    <OccupancyTypeId>OWN</OccupancyTypeId>
    <ProductOrderType>AppraisalURAR</ProductOrderType>
    <ProductOrderDetails>
      <PropertyValuationMethodType>TraditionalAppraisal</PropertyValuationMethodType>
      <LivingUnitExcludingADUCount>1</LivingUnitExcludingADUCount>
      <RentScheduleIndicator>0</RentScheduleIndicator>
      <PUDIndicator>false</PUDIndicator>
    </ProductOrderDetails>
  </Order>
</CreateOrderRq>

#### **3.4 Create Order Response Description**

The `CreateOrderRs` is a synchronous call that is expected to return a `FolderId` and `DocId` for future reference. The third-party system should store this `FolderId` and `DocId` in its own records. The response may also contain a processing error and message.

**Table 3.2 CreateOrderRs: Tracking element**

| Element | Description | Type | Required | Notes |
| :--- | :--- | :--- | :--- | :--- |
| **Tracking** | Passes order details used for tracking between a third-party system and the platform | Complex (`OrderTrackingType`) | | |
| **FolderId** | Number associated with a folder for tracking purposes | string | 20 Required | |
| **DocId** | Number associated with a document for tracking purposes | string | 20 Required | The DocId is also used for status updates. |
| **LoanNumber** | Echo back of the loan number received in the request | string | 60 Required | |
| **SubPortId** | Sub-port ID of the newly created document | string | 20 Optional | |
| **IsAVM** | Denotes whether the order is an Automated Valuation Model (AVM) order | boolean | Optional | |

#### **3.5 CreateOrderRs Sample XML**

```xml
<?xml version="1.0" encoding="utf-8"?>
<CreateOrderRs xmlns:xsi="[http://www.w3.org/2001/XMLSchema-instance](http://www.w3.org/2001/XMLSchema-instance)" xmlns:xsd="[http://www.w3.org/2001/XMLSchema](http://www.w3.org/2001/XMLSchema)" xmlns="[http://www.example.com/OrderPlatform](http://www.example.com/OrderPlatform)">
  <Header>
    <Code xmlns="[http://www.example.com/Integration.xsd](http://www.example.com/Integration.xsd)">OK</Code>
    <Message xmlns="[http://www.example.com/Integration.xsd](http://www.example.com/Integration.xsd)">Order created successfully.</Message>
    <MessageId xmlns="[http://www.example.com/Integration.xsd](http://www.example.com/Integration.xsd)">1234abcd-5678-efgh-9101-ijkl2345mnop</MessageId>
  </Header>
  <Tracking>
    <FolderId>20250901-1234</FolderId>
    <DocId>20250901-1234-1</DocId>
    <LoanNumber>987654321</LoanNumber>
    <SubPortId>123456</SubPortId>
    <IsAVM>false</IsAVM>
  </Tracking>
</CreateOrderRs>