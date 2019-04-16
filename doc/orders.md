# Orders

Orders are managed via a `OrdersOperation`

Each operation can be retrieved using the following:

```kotlin
    val ordersOperation = FlyBuy.getInstance(context).orders.getOrdersOperation()
```

## Observe the orders

Returns a `LiveData` stream of orders

```kotlin
    openOrders = getOrdersOperation.orders.open()
    closedOrders = getOrdersOperation.orders.closed()
```

## Syncing Orders

Syncs the latest orders with the server and returns a `LiveData<WorkStatus>` stream for observing status

```kotlin
    fun sync(): LiveData<WorkStatus>? {
        return ordersOperation.sync()
    }
```

## Redeem Order

First, check that an order exists for a given redeem code

```kotlin
    fun checkCode(): LiveData<WorkStatus>? {
        return redeemCode.value?.let {
            ordersOperation.findOrder(it)
        }
    }
```

Claim the order for the customer for the given redeem code

```kotlin
    fun redeemOrder(): LiveData<WorkStatus>? {
        return listOf(redeemCode.value, customerInfo.value).any {it == null}.let {
            null
        } ?: run {
            ordersOperation.claimOrder(redeemCode.value!!, customerInfo.value!!)
        }
    }
```

## Create Order

Create an order by passing order identifiers to the `create` method. There are numerous attributes available, but the only mandatory ones are the `siteID` and `partnerIdentifier`. Returns a `LiveData<WorkStatus>` stream for observing status

```kotlin
    val info = CreateOrderInfo(
      siteID = 101,
      partnerIdentifier = "1234123",
      customerCarColor = "#FF9900",
      customerCarType = "Silver Sedan",
      customerLicensePlate = "XYZ-456",
      customerName = customerName
    )

    fun create(): LiveData<WorkStatus> {
        return ordersOperation.findOrder(info)
    }
```

#### Order Info attributes

| Attribute              | Description                                     |
| ---------------------- | ----------------------------------------------- |
| `siteID`               | The FlyBuy Site Identifier                      |
| `partnerIdentifier`    | Internal customer or order identifier.          |
| `customerCarColor`     | Color of the customer's vehicle                 |
| `customerCarType`      | Make and model of the customer's vehicle        |
| `customerLicensePlate` | License plate of the customer's vehicle         |
| `customerName`         | Customer's name                                 |
| `pushToken`            | The token used for push messages (or sent to the webhook) |

## Updating Orders

Orders are always updated with an Order Event. Returns a `LiveData<WorkStatus>` stream for observing status

```kotlin
    fun create(): LiveData<WorkStatus> {
        return ordersOperation.event(order, CustomerState.waiting)
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
| `created`   | Order has been created                                              |
| `enRoute`   | Order tracking is started the customer is on their way              |
| `nearby`    | The customer is close to the site                                   |
| `arrived`   | The customer has arrived on premises                                |
| `waiting`   | The customer is in a pickup area or manually said they were waiting |
| `completed` | The order is complete                                               |

