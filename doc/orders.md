# Orders

## Fetch Orders

Fetch the latest orders with the server

```
fun fetchOrders() {
    FlyBuy.orders.fetch { orders, sdkError ->
        // Handle orders or deal with error
    }
}
```

## Claim Order

An order which is created in the FlyBuy service can be "claimed" by the SDK in order to associate it with the current customer.

To get information about an unclaimed order, the app can call `fetch(redemptionCode, callback)`


```kotlin
fun fetchOrder() {
    FlyBuy.orders.fetch(redemptionCode) { order, sdkError ->
        // update order claim view with order details here
    }
}
```

To claim the order for the current customer, the app should call the `claim` method:

```kotlin
fun claimOrder() {
    val customerInfo = CustomerInfo (
                           name = "Marty McFly",
                           carType = "DeLorean",
                           carColor = "Silver",
                           licensePlate = "OUTATIME"
                           )
    FlyBuy.orders.claim(redemptionCode, customerInfo) { order, sdkError ->
        // if sdkError == null, order has been claimed
    }
}
```

After an order is claimed, the app may want to call `FlyBuy.orders.fetch()` to update the list of orders. The newly claimed order should now appear in the list of open orders which is available via `FlyBuy.orders.open`.

## Updating Orders

Orders are always updated with an Order Event. 

```kotlin
fun updateOrder() {
    FlyBuy.orders.event(order, CustomerState.WAITING) { order, sdkError ->
        // if sdkError == null, order has been updated
    }
}
```

#### Order Event attributes

| Attribute | Description            |
| --------- | ---------------------- |
| `order` | The order data |
| `customerState` | Customer state ENUM value |

#### Customer State values

| Value       | Description                                                         |
| ----------- | ------------------------------------------------------------------- |
| `CREATED`   | Order has been created                                              |
| `EN_ROUTE`  | Order tracking is started the customer is on their way              |
| `NEARBY`    | The customer is close to the site                                   |
| `ARRIVED`   | The customer has arrived on premises                                |
| `WAITING`   | The customer is in a pickup area or manually said they were waiting |
| `COMPLETED` | The order is complete                                               |

## Observe the orders

If you are using Android Jetpack, you can observe orders using `LiveData` streams of orders

```kotlin
    val openOrders = FlyBuy.orders.open
    val closedOrders = FlyBuy.orders.closed
```

