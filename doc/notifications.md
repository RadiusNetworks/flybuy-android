# Notifications

FlyBuy can leverage Google's Firebase Cloud Messaging (FCM) to get updates about the status of an order. FlyBuy does not perform the push notification directly, instead it relies on the app to notify it that a new notification has been received. If you do not already handle FCM in your app, refer to the documentation at [Firebase](https://firebase.google.com) for details on how to integrate it in your app.

To pass the message to FlyBuy, override the `FirebaseMessagingService.onMessageReceived` method as follows.

```
override fun onMessageReceived(remoteMessage: RemoteMessage?) {
    // result is a LiveData<WorkStatus> object that can be observed
    val result = FlyBuy.onMessageReceived(remoteMessage)
}
```

You do not need to filter or check the body of the `remoteMessage` data, FlyBuy will inspect it and only process the notification if it is relevant to the SDK.

Since a device's push token can change, the FlyBuy SDK needs to be informed when that occurs. To do so, override the `FirebaseMessagingingService.onNewToken()` method as follows.

```
    override fun onNewToken(token: String?) {
        FlyBuy.onNewPushToken(token)
    }
```

The SDK should also be updated with the push token when the app starts. The following code snippet provides an example function that can be called from `onCreate()` in your activity.

```
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