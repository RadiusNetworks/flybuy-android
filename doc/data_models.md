# FlyBuy Data Models

Several data objects are used and returned by the FlyBuy SDK. These include:

- [FlyBuy Data Models](#flybuy-data-models)
  - [Customer Info](#customer-info)
  - [Customer](#customer)
  - [Site](#site)
  - [Order](#order)
    - [Customer State ENUM Values](#customer-state-enum-values)
    - [Order State ENUM Values](#order-state-enum-values)
    - [Pickup Window](#pickup-window)
  - [Errors](#errors)

## Customer Info

The `CustomerInfo` type is used to update customer information. It is also returned in the `Order` for the customer information for an order.

```kotlin
data class CustomerInfo (
    var name: String,
    var carType: String,
    var carColor: String,
    var licensePlate: String,
    var phone: String?
)
```

## Customer

The `Customer` type provide details of the current customer using `FlyBuy.customer.current` and is returned after creating a customer or logging in.

```kotlin
class Customer {
    val id: Int
    val apiToken: String
    val createdAt: String
    val updatedAt: String
    val deletedAt: String?
    val email: String?
    val name: String
    val phone: String?
    val carType: String?
    val carColor: String?
    val licensePlate: String?
}
```

## Site

The `Site` type provides details for a particular site.

```kotlin
class Site {
    val id: Int
    val name: String?
    val phone: String?
    val streetAddress: String?
    val fullAddress: String?
    val locality: String?
    val region: String?
    val country: String?
    val postalCode: String?
    val latitude: String?
    val longitude: String?
    val coverPhotoUrl: String?
    val iconUrl: String?
    val instructions: String?
    val description: String?
    val partnerIdentifier: String?
}
```

## Order

The `Order` type provides details for a order.

```kotlin
class Order{
    val id: Int
    var orderState: OrderState
    var customerState: CustomerState
    val partnerIdentifier: String?
    val pickupWindow: PickupWindow?
    val pickupType: String?
    val etaAt: Instant?
    val redeemedAt: Instant?
    val customerRatingValue: Int?
    val customerRatingComments: String?

    val site: Site
    val customer: CustomerInfo
}
```

### Customer State ENUM Values

`CustomerState` provides the current status of the customer.

| Value       | Description                                                             |
|-------------|-------------------------------------------------------------------------|
| `created`   | Customer has claimed their order                                        |
| `enRoute`   | Order tracking has started and the customer is on their way             |
| `nearby`    | Customer is close to the site                                           |
| `arrived`   | Customer has arrived on site                                            |
| `waiting`   | Customer is in a pickup area or has manually indicated they are waiting |
| `completed` | Order is complete                                                       |

### Order State ENUM Values

`OrderState` provide the state of the order from the merchant's perspective. 

| Value       | Description                                                             |
|-------------|-------------------------------------------------------------------------|
| `created`   | Order has been created                                                  |
| `ready`     | Order is ready for customer to claim                                    |
| `delayed`   | Order has been delayed by merchant after customer arrives               |
| `cancelled` | Merchant has cancelled order                                            |
| `completed` | Order is complete                                                       |
| `gone`      | Returned by API when order does not exist                               |

### Pickup Window

The `PickupWindow` type provides the time range when an order is expected to be picked up. It will be `null` if there is no pickup window, i.e., ASAP. If there is just a pickup time, the `start` and `end` will be the same.

```kotlin
class PickupWindow {
    val start: Instant
    val end: Instant
}
```

## Errors

If there is an error for any SDK method, the callback will return a `SdkError` object. 

```kotlin
class SdkError {
    val type: ResponseEventType
    val code: Int
               
    // User friendly error message (in English)
    fun userError(): String
}
```

```kotlin
enum class ResponseEventType {
    FAILED,
    SUCCESS,
    NO_CONNECTION
}
```
