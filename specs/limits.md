# Catalog Item Availability Endpoint

## context
The eShop Catalog service manages product listings for the AdventureWorks store.
Each catalog item already tracks an AvailableStock integer field in the database.
Currently consumers must fetch the full item detail payload to determine stock status.
This creates unnecessary data transfer when only availability information is needed.
A lightweight dedicated endpoint is required to solve this.

## behaviorFunctional
The endpoint must accept a single catalog item ID as a route parameter.
The endpoint must return whether the item is currently available for purchase.
The endpoint must return the current stock quantity of the item.
An item is considered available when its AvailableStock is greater than zero.
An item is considered unavailable when its AvailableStock is zero or less.
If the requested item ID does not exist the endpoint must return a not-found response.
The endpoint must be read-only and must not modify any data.
The endpoint must reuse existing data access patterns already present in Catalog.API.
The endpoint must follow the URL structure and response conventions of existing Catalog.API endpoints.
The stock quantity returned must reflect the current value stored in the database.
No new database columns or model fields are required for this feature.

## constraints
Do not duplicate data access logic — reuse existing repository or context patterns.
Do not modify the existing CatalogItem model.
Do not alter any existing catalog endpoints.
Do not introduce any new dependencies or packages.
The endpoint must be registered consistently with how other Catalog.API endpoints are registered.
Response field names must follow the camelCase convention used in existing API responses.

## outOfScope
Reserving or locking stock is out of scope.
Real-time stock updates or webhooks are out of scope.
Availability logic based on anything other than AvailableStock is out of scope.
Authentication or authorization on this endpoint is out of scope.

## testCases
Given a GET request to the availability endpoint
When the item ID exists and AvailableStock is greater than 0
Then the response is 200
And available is true
And stock equals the current AvailableStock value

Given a GET request to the availability endpoint
When the item ID exists and AvailableStock is exactly 0
Then the response is 200
And available is false
And stock is 0

Given a GET request to the availability endpoint
When the item ID does not exist in the database
Then the response is 404

Given a GET request to the availability endpoint
When the item ID exists and AvailableStock is 1
Then the response is 200
And available is true
And stock is 1

Given existing catalog endpoints such as GET /api/catalog/items
When the availability endpoint is added
Then all existing endpoints continue to return correct responses

Given the availability endpoint is called
When the response is returned
Then the response shape is exactly { "available": true/false, "stock": integer }
And no additional fields are included

## definitionOfDone
A new read-only endpoint exists that accepts a catalog item ID.
GET request with a valid item ID returns 200 with available and stock fields.
GET request with an unknown item ID returns 404.
available is true when AvailableStock is greater than 0.
available is false when AvailableStock is 0.
stock reflects the exact AvailableStock value from the database.
Response shape is { "available": bool, "stock": int }.
No existing catalog endpoints are broken.
No data access logic is duplicated.
No new packages or dependencies are introduced.
The endpoint is registered using the same pattern as existing Catalog.API endpoints.

## asIs
src/Catalog.API/Apis/CatalogApi.cs:1
src/Catalog.API/Model/CatalogItem.cs:1
src/Catalog.API/Infrastructure/CatalogContext.cs:1
src/Catalog.API/Services/CatalogServices.cs:1
src/Catalog.API/Program.cs:1