# Notifications

FlyBuy can leverage Google's Firebase Cloud Messaging (FCM) to get updates about the status of an order. FlyBuy does not perform the push notification directly, instead it relies on the app to notify it that a new notification has been received. If you do not already handle FCM in your app, refer to the documentation at [Firebase](https://firebase.google.com) for details on how to integrate it in your app.

To pass the message to FlyBuy, override the `FirebaseMessagingService.onMessageReceived` method as follows.

```kotlin
override fun onMessageReceived(remoteMessage: RemoteMessage?) {
    FlyBuy.onMessageReceived(remoteMessage.data) { sdkError ->
        // Handle error
    }
}
```

You do not need to filter or check the data of the `remoteMessage` data, FlyBuy will inspect it and only process the notification if it is relevant to the SDK.

Since a device's push token can change, the FlyBuy SDK needs to be informed when that occurs. To do so, override the `FirebaseMessagingingService.onNewToken()` method as follows.

```kotlin
override fun onNewToken(token: String?) {
    FlyBuy.onNewPushToken(token)
}
```

Also, make sure to refresh the token when the user logs in/out. Here's examples of doing that.

```kotlin
FlyBuy.customer.login(login.value ?: LoginInfo()) { customer, sdkError ->
    handleSdkError(sdkError)
    if (null != customer) {
        FirebaseInstanceId.getInstance().deleteInstanceId()
    }
}


FlyBuy.customer.logout { sdkError ->
    handleSdkError(sdkError)
    FirebaseInstanceId.getInstance().deleteInstanceId()
}
```

The SDK should also be updated with the push token when the app starts. The following code snippet provides an example function that can be called from `onCreate()` in your activity.

```kotlin
private fun updatePushToken() {
    FirebaseInstanceId.getInstance().instanceId
        .addOnCompleteListener(OnCompleteListener { task ->
            if (!task.isSuccessful) {
                Timber.w(task.exception, "getInstanceId failed")
                return@OnCompleteListener
            }

            // Get new Instance ID token
            task.result?.token?.let {
                FlyBuy.getInstance(this).onNewPushToken(it)
            }

        })
}
```

## Setting the Service Notification Title and Content

When there is an open order, the FlyBuy SDK runs a foreground service that sends location updates to the API. Foreground services on Android require a notification while it is running. The default title and content of this notification is:

**Title**: FlyBuy Order in Progress

**Content**: We'll let the merchant know when you arrive.

To override the default values, set the following strings in the app's string resources.

```
    <string name="notif_flybuy_order_in_progress_title">FlyBuy Order in Progress</string>
    <string name="notif_flybuy_order_in_progress_content">We\'ll let the merchant know when you arrive.</string>
```

The service icon and push notification icon can also be updated from the default FlyBuy icon by overriding these two files respectively:

```
@drawable/ic_stat_location_service
@drawable/ic_stat_default
```

## Opening an Order upon Tapping a Notification

When the app is open upon tapping one of the order status notifications, an intent will be sent that contains the data from the push messages. In particular, one or more of the following data will be included in the intent extras.

| Intent Extra Key  | Description |
|---|---|
| `message_source` | Will be set to `flybuy` if the notification came from FlyBuy
| `order_id` | The `orderId` to look up the order in `FlyBuy.orders.all`. Note this is not the redemption code |
| `order_state` | The `state` of the order
| `customer_state` | The `customerState` for the order
| `eta_at` | The estimate time of arrival for the customer
