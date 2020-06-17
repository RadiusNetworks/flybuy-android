# Orders

Examples are in Kotlin. Refer to [Data Models](data_models.md) for details on classes and objects used by the SDK.

- [Fetch Claimed Orders](#fetch-claimed-orders)
- [Fetch Unclaimed Orders](#fetch-unclaimed-orders)
- [Observe Orders](#observe-orders)
- [Claim Orders](#claim-orders)
- [Create Order](#create-order)
- [Update Orders](#update-orders)
  - [Update customer state](#update-customer-state)
  - [Update order state](#update-order-state)
- [Customer Ratings](#customer-ratings)

## <span id="fetch-claimed-orders">Fetch Claimed Orders</span>

Fetch syncs the SDK with the FlyBuy backend to ensure the state changes match between the systems. Call `fetch` optionally when showing a list of orders in the app.

```kotlin
FlyBuy.orders.fetch { orders, sdkError ->
    // Handle orders or deal with error
}
```

Return the cached list of orders for the current user.

```
FlyBuy.orders.all
```

Return the cached list of open orders for the current user.

```
FlyBuy.orders.open
```

Return the cached list of closed orders for the current user.

```
FlyBuy.orders.closed
```

## <span id="fetch-unclaimed-orders">Fetch Orders with Redemption Code</span>

```kotlin
FlyBuy.orders.fetch(redemptionCode) { order, sdkError ->
    // Check error response and update order claim view with order details here
}
```

## <span id="observe-orders">Observe Orders</span>

Set up observers to get updates about orders.

If you are using Android Jetpack, you can observe orders using `LiveData` streams of orders.

```kotlin
val openOrders = FlyBuy.orders.openLiveData
val closedOrders = FlyBuy.orders.closedLiveData
```

You can also observe just the current order.

```kotlin
LiveData<Order> =  FlyBuy.orders.getOrder(orderId)
```

## <span id="claim-orders">Claim Orders</span>

To claim an order for the current customer, the app should call the `claim` method.

```kotlin
// Create the customer info object for person picking up (name is required)
val customerInfo = CustomerInfo (
    name = "Marty McFly",
    carType = "DeLorean",
    carColor = "Silver",
    licensePlate = "OUTATIME",
    phone = "555-555-5555"
)
FlyBuy.orders.claim(redemptionCode, customerInfo) { order, sdkError ->
    // If sdkError == null, order has been claimed
}
```

Optionally, the pickup type can be set when claiming an order. This is only necessary for apps that do not set the pickup type via backend integrations. Refer to [Pickup Types](data_models.md#pickup-type) for possible values.


```kotlin
FlyBuy.orders.claim(redemptionCode, customerInfo, pickupType) { order, sdkError ->
    // If sdkError == null, order has been claimed
}
```

## <span id="create-order">Create Order</span>

To create an order for the current customer, the app should call the `create` method. 

*NOTE: `pickupWindow`, `orderState`, and `pickupType` are optional fields that are not necessary for all integrations.*
```kotlin
val customerInfo = CustomerInfo (
    name = "Marty McFly",
    carType = "DeLorean",
    carColor = "Silver",
    licensePlate = "OUTATIME",
    phone = "555-555-5555"
)
FlyBuy.orders.create(
            siteID = 1,
            partnerIdentifier = "1234",
            customerInfo,
            pickupWindow,
            orderState,
            pickupType
        ) { order, sdkError ->
    // If sdkError == null, order has been created
}
```

Optionally, the `pickupWindow`, `orderState`, and  `pickupType` can be set when creating an order if these are not created via a backend integration. Refer to [Data Models](data_models.md) for possible values.

## <span id="update-orders">Update Orders</span>

Orders are always updated with an order event. The order object cannot be updated directly.

### Update customer state

```kotlin
FlyBuy.orders.event(orderId, CustomerState.WAITING) { order, sdkError ->
    // If sdkError == null, order has been updated
}
```

Refer to [Customer State ENUM Values](data_models.md#customer-state-enum-values) for possible `customerState` values.

### Update order state

```kotlin
FlyBuy.orders.event(orderId, OrderState.CANCELLED) { order, sdkError ->
    // If sdkError == null, order has been updated
}
```

Refer to [Order State ENUM Values](data_models.md#order-state-enum-values) for possible `state` values.

## <span id="rate-orders">Customer Ratings</span>

If you collect customer ratings in your app, you can pass them to FlyBuy. The `rating` should be an integer and `comments` (optional) should be a string:

```kotlin
FlyBuy.orders.rateOrder(orderId = 123, rating = 5, comments = 'Great service') { order, sdkError ->
    // If sdkError == null, rating was successfully submitted
}
```

