# Basket Item Quantity Validation

## Behaviour
Quantity for any basket item must be validated before being accepted.
Quantity must be greater than 0.
Quantity must not exceed 100.
If validation fails the API must reject the request with an appropriate error response and message.
Valid requests must continue to work exactly as before.
Do not introduce a new validation library.
Use existing error handling patterns already present in the service.
The validation must happen server-side.

## Acceptance Criteria
Given a basket update request is submitted
When quantity is 0
Then the API returns a 400 response with a human-readable error message
Given a basket update request is submitted
When quantity is 101
Then the API returns a 400 response with a human-readable error message
Given a basket update request is submitted
When quantity is 1
Then the request succeeds and the basket is updated normally
Given a basket update request is submitted
When quantity is 100
Then the request succeeds and the basket is updated normally
Given a basket update request is submitted
When quantity is 50
Then the request succeeds and the basket is updated normally

## Success Criteria
Sending quantity 0 returns a 400 response.
Sending quantity 101 returns a 400 response.
Sending quantity 1 and quantity 100 succeed normally.
Error response includes a human-readable message describing the constraint.
No new validation library is introduced.
Validation occurs server-side using existing error handling patterns.

## asIs
src/Basket.API/Model/BasketItem.cs:1
src/Basket.API/Grpc/BasketService.cs:1
src/Basket.API/Repositories/RedisBasketRepository.cs:1
