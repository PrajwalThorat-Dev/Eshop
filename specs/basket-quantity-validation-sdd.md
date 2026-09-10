# Basket Item Quantity Validation

## context
The eShop Basket service allows authenticated users to manage their shopping cart.
Each basket item contains a product reference and a quantity chosen by the user.
Currently the Basket API accepts any integer value as a quantity with no validation.
This means a user could submit a quantity of 0, a negative number, or an unreasonably
large number without any rejection from the server.
Server-side validation is required to enforce a sensible quantity range before
the basket is persisted.

## behaviorFunctional
Quantity for any basket item must be validated before the basket is accepted.
Quantity must be greater than 0.
Quantity must not exceed 100.
If validation fails the API must reject the request immediately.
The rejection response must include a human-readable message describing the constraint.
The rejection must use an appropriate HTTP error status.
Valid requests must continue to work exactly as before.
Validation must happen server-side regardless of any client-side checks.
The validation must apply to every basket item in the request, not just the first.
If multiple items have invalid quantities all violations must be caught.

## constraints
Do not introduce a new validation library or NuGet package.
Use existing error handling patterns already present in the Basket service.
Do not modify the BasketItem model's data type for quantity.
Do not change the public API contract — request and response shapes remain the same.
The valid range is 1 to 100 inclusive on both ends.

## outOfScope
Per-product quantity limits based on catalog data are out of scope.
Stock availability checks during basket update are out of scope.
Client-side validation is out of scope.
Validation of basket item price or product ID is out of scope.

## testCases
Given a basket update request is submitted
When quantity of an item is 0
Then the API returns a 400 response
And the response includes a human-readable error message describing the constraint

Given a basket update request is submitted
When quantity of an item is -1
Then the API returns a 400 response
And the response includes a human-readable error message describing the constraint

Given a basket update request is submitted
When quantity of an item is 101
Then the API returns a 400 response
And the response includes a human-readable error message describing the constraint

Given a basket update request is submitted
When quantity of an item is 1
Then the request succeeds
And the basket is updated normally

Given a basket update request is submitted
When quantity of an item is 100
Then the request succeeds
And the basket is updated normally

Given a basket update request is submitted
When quantity of an item is 50
Then the request succeeds
And the basket is updated normally

Given a basket update request is submitted with multiple items
When one item has quantity 0 and another has quantity 5
Then the API returns a 400 response
And the error message references the invalid item

Given a basket update request is submitted
When all items have quantities between 1 and 100 inclusive
Then the basket is persisted and the response is successful

## definitionOfDone
Submitting quantity 0 returns a 400 response with a descriptive error message.
Submitting quantity -1 returns a 400 response with a descriptive error message.
Submitting quantity 101 returns a 400 response with a descriptive error message.
Submitting quantity 1 succeeds and the basket is updated.
Submitting quantity 100 succeeds and the basket is updated.
Submitting quantity 50 succeeds and the basket is updated.
Error response includes a human-readable message describing the valid range.
No new validation library or NuGet package is introduced.
Validation occurs server-side using existing error handling patterns.
No change to the BasketItem model data type.
No change to the public request or response shape.
All existing basket operations continue to function correctly.

## asIs
src/Basket.API/Model/BasketItem.cs:1
src/Basket.API/Grpc/BasketService.cs:1
src/Basket.API/Repositories/RedisBasketRepository.cs:1
src/Basket.API/Program.cs:1
