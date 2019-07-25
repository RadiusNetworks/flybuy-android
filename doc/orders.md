# Orders

Examples are in Kotlin.

- [Fetch Claimed Orders](#fetch-claimed-orders)
- [Fetch Unclaimed Orders](#fetch-unclaimed-orders)
- [Observe Orders](#observe-orders)
- [Claim Orders](#claim-orders)
- [Create Order](#create-order)
- [Update Orders](#update-orders)

## <span id="fetch-claimed-orders">Fetch Claimed Orders</span>

Fetch the latest claimed orders with the server. An order is claimed if it is associated with the current customer.

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

## <span id="fetch-unclaimed-orders">Fetch Unclaimed Orders</span>

```kotlin
FlyBuy.orders.fetch(redemptionCode) { order, sdkError ->
    // Update order claim view with order details here
}
```

## <span id="observe-orders">Observe Orders</span>

Set up observers to get updates about orders.

If you are using Android Jetpack, you can observe orders using `LiveData` streams of orders.

```kotlin
val openOrders = FlyBuy.orders.openLiveData
val closedOrders = FlyBuy.orders.closedLiveData
```

## <span id="claim-orders">Claim Orders</span>

To claim an order for the current customer, the app should call the `claim` method.

```kotlin
val customerInfo = CustomerInfo (
    name = "Marty McFly",
    carType = "DeLorean",
    carColor = "Silver",
    licensePlate = "OUTATIME"
)
FlyBuy.orders.claim(redemptionCode, customerInfo) { order, sdkError ->
    // If sdkError == null, order has been claimed
}
```

## <span id="create-order">Create Order</span>

To create an order for the current customer, the app should call the `create` method. Note that a customer will be created if there isn't a customer currently logged in.

```kotlin
val createOrderInfo = CreateOrderInfo (
    siteID = 1,
    partnerIdentifier = "1234",
    name = "Marty McFly",
    carType = "DeLorean",
    carColor = "Silver",
    licensePlate = "OUTATIME"
)
FlyBuy.orders.create(createOrderInfo) { order, sdkError ->
    // If sdkError == null, order has been created
}
```

## <span id="update-orders">Update Orders</span>

Orders are always updated with an order event. The order object cannot be updated directly.

```kotlin
FlyBuy.orders.event(order, CustomerState.WAITING) { order, sdkError ->
    // If sdkError == null, order has been updated
}
```

#### Order Event Attributes

| Attribute       | Description               |
|-----------------|---------------------------|
| `order`         | Order data                |
| `customerState` | Customer state ENUM value |

#### Customer State ENUM Values

| Value         | Description                                                             |
|---------------|-------------------------------------------------------------------------|
| `CREATED`     | Order has been created                                                  |
| `EN_ROUTE`    | Order tracking has started and the customer is on their way             |
| `NEARBY`      | Customer is close to the site                                           |
| `ARRIVED`     | Customer has arrived on site                                            |
| `WAITING`     | Customer is in a pickup area or has manually indicated they are waiting |
| `COMPLETED`   | Order is complete                                                       |