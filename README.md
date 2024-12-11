# documentation-bruno-collection

A Bruno collection for testing our [public APIs](https://developers.trustpilot.com), owned by the API team.

Public calls are based on public information that any consumer or business can find on Trustpilot without the use of an API.

Private calls are not accessible to everyone. They're only accessible to businesses with a Trustpilot business profile.

## Prerequisites

- [Bruno](https://www.usebruno.com/) client installed on your machine
- A Trustpilot API key and secret (obtain from Trustpilot Business, see article on [how to use Trustpilot APIs](https://support.trustpilot.com/hc/en-us/articles/207309867-How-to-use-Trustpilot-APIs))

## Setup

This setup uses OAuth2 grant type: client_credentials by default

1. Clone this repository (or download as zip and unzip)
2. Launch Bruno client
3. Open the collection within Bruno client
4. Update the environment variables:

- `businessUnitId` - unique identifier of the business unit
- `baseUrl` - base URL for Trustpilot API environment (default is https://api.trustpilot.com/v1)
- `invitationsBaseUrl` - base URL for Trustpilot invitations API environment (default is https://invitations-api.trustpilot.com/v1)
- `apikey` - api key for the business application
- `apisecret` - api secret for the (above) business application

5. Query/Path Params as environment variables (these can be configured here for ease of use as they are used frequently throughout the endpoints)

- `domainUrl` - domain url
- `consumerId` - Unique identifer for consumer
- `conversationId` - Unique identifer for conversation
- `reviewId` - Unique identifer for a review
- `commentId` - Unique identifer for a specfic comment
- `sku` - Stock keeping unit (unique identifer for specific product)
- `authorBusinessUserId` - Business User Id for the author of a review
- `token` - Param storing token used in the revoke token endpoint

## Make API request

1. Select endpoint
2. Update any path and query parameters
3. Execute request

## Authentication

The Bruno collection has been configured to handle token authentication for you. On your first request (and prior to any following request), a script will fetch a valid token based on the environment variables you've configured, and populate the request accordingly. When a token expires, the script will fetch a new token automatically before the next request. The script can be found in the collection configuration tab.

## Optional setup for using OAuth2 grant type: password

If you would instead like to use `grant type: password`:

1. Update the following environment variables:

- `grantType` - add `password`
- `username` - Trustpilot business username
- `password` - Trustpilot business password

The background script will now automatically fetch a valid token with the updated grant type.

## Secrets

Secrets are the equivalent of passwords. Secrets are not to be shared with anyone. Do not untick the 'Secret' checkbox when configuring environment variables - this will ensure your secret value(s) are not committed to the collection's files.

## Guidance

<details>
<summary>Business Units</summary>

#### What is a Business Unit?

A business unit is the fundamental organisational concept in Trustpilot that serves as the collection point for all content related to a website. Specifically:

- It contains all your company's:
  - Reviews
  - Ratings
  - Company responses
- It's associated with your domain name(s):
  - One main domain name (primary name)
- Each business unit has a unique identifier (Business Unit ID or BUID)
- A business user can have access to one or multiple business units. If you have multiple domains, each will have its own Business Unit ID
- The Business Unit ID is required for:
  - Getting reviews
  - Creating invitations
  - Managing product reviews
  - And many other API endpoints

#### How to Find Your Business Unit ID:

Use the Find Business Unit endpoint:

```
GET https://api.trustpilot.com/v1/business-units/find
```

Example request:

```
https://api.trustpilot.com/v1/business-units/find?name=example.com
```

The response will look like this:

```json
{
    "links": [...],
    "id": "507f191e810c19729de86aaa",  // This is your Business Unit ID
    "displayName": "Your Company Name",
    "name": {
        "identifying": "example.com",
        "referring": [
            "example.com",
            "www.example.com"
        ]
    },
    "websiteUrl": "http://www.example.com",
    "country": "US",
    "numberOfReviews": {
        "total": 14,
        "usedForTrustScoreCalculation": 12,
        "oneStar": 2,
        "twoStars": 3,
        "threeStars": 2,
        "fourStars": 5,
        "fiveStars": 2
    },
    "status": "active",
    "score": {
        "trustScore": 4.6,
        "stars": 4.5
    }
}
```

- The "id" field in the response is your Business Unit ID (BUID)
- This ID is essential and required for many other API operations
- Using the wrong Business Unit ID in API calls may result in 403 Forbidden errors

</details>

<details>
<summary>Categories</summary>

#### What is a Category?

Categories in Trustpilot are classifications for business units that help organise and group similar businesses together. Each business unit can be associated with multiple categories, and categories can have parent-child relationships.

- Category names are translated based on the locale parameter
- Categories are country-specific

#### How to Find Categories:

List All Categories:
The simplest way to find categories is to use the List Categories endpoint:

Endpoint: `GET https://api.trustpilot.com/v1/categories`

Example response:

```json
{
  "categories": [
    {
      "categoryId": "pet_store",
      "displayName": "Pet Store",
      "name": "pet_store"
    }
  ]
}
```

Get Specific Category Details:
Once you have a categoryId, you can get more details about it:

Endpoint: `GET https://api.trustpilot.com/v1/categories/{categoryId}`

Example response:

```json
{
  "businessUnitCount": 42,
  "parentId": "animals_pets",
  "displayName": "Pet Store",
  "categoryId": "pet_store",
  "name": "pet_store"
}
```

List Business Units in a Category:
You can also see what businesses are in a specific category:

Endpoint: `GET https://api.trustpilot.com/v1/categories/{categoryId}/business-units`

</details>

<details>
<summary>Reviews</summary>

#### What is a Review?

A review in Trustpilot is feedback provided by a consumer about a business or product. Reviews can be either:

1. Service Reviews - Reviews about the overall business/service
2. Product Reviews - Reviews about specific products

- Reviews can have different states: active, reported, etc.
- Private review endpoints provide more detailed information but require authentication
- Reviews can be filtered by various criteria like stars, language, date
- Some reviews may have company replies
- Reviews can be verified or unverified
- Reviews can have tags associated with them
- Reviews can be liked by other consumers
- Product reviews have additional fields like SKU and product details

#### How to Find Reviews:

There are several ways to find reviews depending on your needs:

Get Business Unit Reviews:

```
GET https://api.trustpilot.com/v1/business-units/{businessUnitId}/reviews
```

Parameters:

- businessUnitId (Required): Your business unit ID
- stars (Optional): Filter by star rating
- language (Optional): Filter by language
- page (Optional, default: 1)
- perPage (Optional, default: 20, max: 100)
- orderBy (Optional): Sort order

Get Business Unit Private Reviews:

```
GET https://api.trustpilot.com/v1/private/business-units/{businessUnitId}/reviews
```

This endpoint requires Business User OAuth Token and provides additional private information like:

- Consumer ID
- Reference ID
- Referral email

For Product Reviews:

```
GET https://api.trustpilot.com/v1/product-reviews/business-units/{businessUnitId}/reviews
```

Parameters:

- businessUnitId (Required)
- productUrl or sku (At least one required)

Get a Single Review by ID:

```
GET https://api.trustpilot.com/v1/reviews/{reviewId}
```

Required:

- reviewId: The ID of the specific review

Response example:

```json
{
  "id": "507f191e810c19729de86aaa",
  "stars": 5,
  "title": "My review",
  "text": "This shop is great",
  "language": "da",
  "createdAt": "2013-09-07T13:37:00",
  "updatedAt": "2013-09-07T13:37:00",
  "numberOfLikes": 10,
  "isVerified": true,
  "companyReply": {
    "text": "This is our reply.",
    "createdAt": "2013-09-07T13:37:00",
    "updatedAt": "2013-09-07T13:37:00"
  },
  "consumer": {
    "displayLocation": "Frederiksberg, DK",
    "numberOfReviews": 1,
    "displayName": "John Doe",
    "id": "507f191e810c19729de8asd4"
  }
}
```

</details>

<details>
<summary>Consumers</summary>

#### What is a Consumer?

A consumer in Trustpilot is a user who interacts with the consumer site (e.g. to write a review). Consumer profiles contain information such as:

- Display name
- Profile information
- Number of reviews
- Location (if provided)
- Profile image (if they have one)
- Language/locale preferences

Consumer IDs are typically obtained from review data rather than searched directly

- Consumer privacy is important - not all information is publicly available
- Consumer profiles may have different levels of completeness depending on what information they've provided
- Some endpoints require authentication while others are public
- Consumers can have verified or unverified reviews
- Consumer display names must be valid according to Trustpilot's rules

#### How to Find Consumer Information:

Get Consumer Profile:

```
GET https://api.trustpilot.com/v1/consumers/{consumerId}/profile
```

Required:

- consumerId: The ID of the consumer

Response includes:

```json
{
  "city": "",
  "about": "",
  "displayName": "Test Consumer",
  "locale": "en-US",
  "gender": "",
  "country": "US",
  "id": "781faa232895a4f79f109999",
  "createdAt": null,
  "hasImage": false,
  "birthYear": 0.0,
  "profileImage": {
    "image24x24": {
      "url": null,
      "width": 0.0,
      "height": 0.0
    },
    "image35x35": {...},
    "image64x64": {...},
    "image73x73": {...}
  }
}
```

Get Consumer Profile with Reviews Count and Weblinks:

```
GET https://api.trustpilot.com/v1/consumers/{consumerId}
```

This provides additional information including:

- Number of reviews
- Web links
- Profile information

Get Consumer's Reviews:

```
GET https://api.trustpilot.com/v1/consumers/{consumerId}/reviews
```

Parameters:

- consumerId (Required)
- stars (Optional): Filter by star rating
- language (Optional): Filter by language
- businessUnitId (Optional): Filter reviews for specific business
- page (Optional)
- perPage (Optional)
- orderBy (Optional)
- includeReportedReviews (Optional)

Get Multiple Consumer Profiles in Bulk:

```
POST https://api.trustpilot.com/v1/consumers/profile/bulk
```

Request body:

```json
{
  "consumerIds": [
    "id1",
    "id2"
  ]
}
```

#### Privacy Considerations:

- Email addresses and other private information are not publicly available
- Some consumer information is only available through private endpoints
- Consumer profiles respect privacy settings set by the consumers themselves

</details>

<details>
<summary>Conversations</summary>

#### What is a Conversation?

A conversation is a thread attached to a product review that allows business users and consumers to interact through comments. Conversations are only created if a business replies to the consumer’s product review.

#### How to Create a Conversation:

1. First, you need to create a conversation for a specific product review using the "Create product review conversation" endpoint:

Endpoint: `POST https://api.trustpilot.com/v1/private/product-reviews/{reviewId}/create-conversation`

Required:

- reviewId: The ID of the product review you want to start a conversation for

The response will look like:

```json
{
  "conversationId": "507f191e810c19729de86989"
}
```

2. Once you have the conversationId, you can create comments in the conversation using the "Create comment" endpoint:

Endpoint: `POST https://api.trustpilot.com/v1/private/conversations/{conversationId}/comments`

Required:

- conversationId: The ID received from the previous step
- Request body:

```json
{
  "content": "Your comment text here"
}
```

The response will include the comment details:

```json
{
  "commentId": "507f191e810c19729de86989",
  "createdAt": "2013-09-07T13:37:00",
  "author": {
    "id": "507f191e810c19729de86778",
    "type": "businessUser"
  },
  "content": "Your comment text here"
}
```

- Conversations can be set to either public or private state
- If set to private, third parties won't see the conversation
- You can manage the conversation state using the "Set conversation state" endpoint
- You can later retrieve conversation details using the Get conversation endpoints (both public and private versions available)
- Comments can be updated after creation using the Update comment endpoint

</details>

<details>
<summary>SKUs</summary>

#### What is an SKU?

An SKU (Stock Keeping Unit) is a unique identifier for a product in Trustpilot's system. SKUs are used to:

- Link product reviews to specific products
- Track product information
- Manage product review invitations
- Get product review summaries
- SKUs should be unique within your business unit
- SKUs are case-sensitive
- You can batch process multiple SKUs in many endpoints
- SKUs can be used to:

  - Get product reviews
  - Get review summaries
  - Create review invitations
  - Manage product information

#### How to Find SKUs:

Get Products List:

```
GET https://api.trustpilot.com/v1/private/business-units/{businessUnitId}/products
```

Required:

- businessUnitId: Your business unit ID

Optional parameters:

- skus: Filter for specific SKUs
- groupId: Filter by group ID
- page (default: 1)
- perPage (default: 10000)

Response example:

```json
{
  "products": [
    {
      "id": "507f191e810c19729de86909",
      "sku": "Prod123",
      "googleMerchantCenterProductId": "Product_DK_1007653571_2874605123",
      "title": "Toy car",
      "link": "http://myshop.com/products/toy-car",
      "imageLink": "http://myshop.com/products/images/toy-car.jpg",
      "processedImages": [
        {
          "type": "100pxWide",
          "url": "https://product-reviews-images.trustpilot.com/5837640412df3f0aabf9989a_100pxWide.png"
        }
      ],
      "businessUnitId": "507f191e810c19729de86909",
      "price": "99.95",
      "gtin": "3200000003774",
      "mpn": "HSC0424PC",
      "brand": "ToyProducer",
      "currency": "EUR",
      "description": "A metal toy car"
    }
  ],
  "isLastPage": false
}
```

Create/Update Products:
You can also create or update products (which will create SKUs) using:

```
POST https://api.trustpilot.com/v1/private/business-units/{businessUnitId}/products
```

Request body example:

```json
{
  "products": [
    {
      "sku": "Prod123",
      "googleMerchantCenterProductId": "Product_DK_1007653571_2874605123",
      "title": "Toy car",
      "link": "http://myshop.com/products/toy-car",
      "imageLink": "http://myshop.com/products/images/toy-car.jpg",
      "price": "99.95",
      "currency": "USD",
      "gtin": "3200000003767",
      "mpn": "HSC0424PC",
      "brand": "ToyProducer",
      "description": "A very soft shoe built for walking long distances.",
      "productCategoryGoogleId": "1267",
      "groupId": "123456"
    }
  ]
}
```

</details>

<details>
<summary>Product Reviews vs Service Reviews</summary>

#### Service Reviews

A service review is feedback about the overall business/company experience.

Characteristics:

- Reviews the entire business or service experience
- Associated with the Business Unit as a whole
- Can be invited via email or service review invitation link
- Cannot have product-specific details
- Contributes to the overall TrustScore of the business
- Shows up on the main business profile

#### Service Review Endpoints:

Get service reviews:

```
GET https://api.trustpilot.com/v1/business-units/{businessUnitId}/reviews
```

#### Product Reviews

A product review is feedback about a specific product purchased from the business.

Characteristics:

- Reviews a specific product (identified by SKU)
- Contains product-specific information
- Can include product attributes and ratings
- Can have product images attached
- Associated with specific products in your catalog
- Can be part of a conversation thread
- Can include specific product attributes ratings
- Doesn't directly affect the overall TrustScore

#### Product Review Endpoints:

Get product reviews:

```
GET https://api.trustpilot.com/v1/product-reviews/business-units/{businessUnitId}
```

Key Differences in API Handling:

Data Structure:
Service Reviews include:

```json
{
  "stars": 5,
  "title": "Great service",
  "text": "This shop is great.",
  "consumer": {...},
  "businessUnit": {...}
}
```

Product Reviews include:

```json
{
  "stars": 4,
  "content": "This product was nice",
  "product": {
    "id": "507f191e810c19729de86909",
    "productUrl": "http://www.mycompanystore.com/products/12345.htm",
    "name": "Metal Toy Car",
    "sku": "ABC-1234",
    "brand": "Acme"
  },
  "attributeRatings": [
    {
      "attributeId": "attributes.default.quality",
      "attributeName": "Quality",
      "rating": 4
    }
  ]
}
```

#### Review Management:

##### Service Reviews:

- Can be replied to directly
- Can be tagged
- Can be reported

##### Product Reviews:

- Require creating a conversation first to reply
- Can have multiple comments in the conversation
- Can include product-specific attributes
- Can have attachments (like product images)

</details>
