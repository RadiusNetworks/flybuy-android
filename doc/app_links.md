# App Links

These instructions will help you set up app links to your Android app from a white-label FlyBuy domain.  If you don't already have a white-label domain set up with FlyBuy go [here](https://github.com/RadiusNetworks/flybuy-documentation/blob/master/doc/white_label_domains.md) to get that set up first.

## Setting up app links in your app from a white-label FlyBuy domain

To get started we need a few things to set up your white-label FlyBuy domain to support directing users to your app via Android app links:

- The name of the existing white-label FlyBuy domain to set up for your app (e.g., `pickup.example.com`)
- The link to the app in Google Play
- The SHA-256 hash of the app certificate (used by Android to verify the app link from the white-label domain)

For more information on generating the SHA-256 hash fingerprint for your app check out [this page](https://developer.android.com/training/app-links/verify-site-associations#web-assoc) of the Android developer docs.

Please provide this info to your account rep to get started, next you'll just need to set up your app to receive app links from the white-label FlyBuy domain.

### Add an intent-filter to your app manifest

To enable verified app links to your app you need to add a web URL intent filter to your app manifest that includes `android:autoVerify="true"` and is associated with the white-label domain you have configured, for example:

```xml
<activity ...>

    <intent-filter android:autoVerify="true">
        <action android:name="android.intent.action.VIEW" />
        <category android:name="android.intent.category.DEFAULT" />
        <category android:name="android.intent.category.BROWSABLE" />
        <data android:scheme="http" android:host="pickup.example.com" />
        <data android:scheme="https" />
    </intent-filter>

</activity>
```

This tells Android to verify that the domain you have set up is associated with your app and will allow links for this domain to be opened directly in your app without user interaction.

### Respond to the incoming intent from the app link

Once the app link domain is verified and your intent filter is set up in your manifest, you just need to handle the incoming intent in the same way that you would for an ordinary deep link.  You can use the `getData()` and `getAction()` methods on the incoming `Intent` to retrieve the data about the app link.  These methods are available during the full lifecycle of the activity, but it's best to put them in the early callbacks like `onCreate()` or `onStart()`.  Here are some quick examples of this for Kotlin:

```kotlin
override fun onCreate(savedInstanceState: Bundle?) {
    super.onCreate(savedInstanceState)
    setContentView(R.layout.main)

    val action: String? = intent?.action
    val data: Uri? = intent?.data
}
```

You can also check out [this page](https://developer.android.com/training/app-links/deep-linking.html#handling-intents) in the Android docs for more details on handling the incoming intent from the app link.

The important part of the incoming URL is the order redemption code, stored in the `r` query parameter of the URL (e.g., `https://pickup.example.com/m/o?r=CODE`).  You can pull the redemption code from the URL and call the `findOrder(redemptionCode)` orders operation function to retrieve the order from the FlyBuy SDK and take appropriate action for the app link.
