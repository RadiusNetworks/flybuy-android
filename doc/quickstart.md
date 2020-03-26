# Quick Start

## Getting Started

This guide will walk through the basic setup for integrating the FlyBuy SDK into an Android app. There are a few things that need to be set up properly, we recommend following this guide carefully when first integrating with your app.

## Requirements

- Android API 17 or higher

## SDK Installation

Currently FlyBuy can be installed via gradle or manually.

### Gradle Install

Add the following to your root `build.gradle` 

```groovy
allprojects {
    repositories {
        maven { url 'https://dl.bintray.com/radiusnetworks/flybuy-sdk'}
    }
}
```

Include the following in your app `build.gradle` dependencies

```groovy
    implementation "com.radiusnetworks.flybuy:sdk:<version>"
    implementation "com.radiusnetworks.flybuy:api:<version>"
```

### Manual Install

Download the [latest SDK release](https://github.com/RadiusNetworks/flybuy-android/releases/latest).

Copy the `sdk-release.aar` and `api-release.aar` files into the `libs` folder of your app module

Make sure your `build.gradle` has the following:

```groovy
dependencies {
    implementation fileTree(include: ['*.jar', '*.aar'], dir: 'libs')

    // Android Architecture Components
    implementation "androidx.room:room-common:2.1.0"
    implementation "androidx.room:room-runtime:2.1.0"
    implementation "androidx.room:room-rxjava2:2.1.0"
    implementation "androidx.room:room-ktx:2.1.0"
    kapt "androidx.room:room-compiler:2.1.0"
    androidTestImplementation "androidx.room:room-testing:2.1.0"
    // Networking
    implementation "com.squareup.retrofit2:retrofit:2.5.0"
    implementation "com.squareup.retrofit2:converter-gson:2.5.0"
    implementation "com.squareup.retrofit2:converter-scalars:2.5.0"
    implementation "com.squareup.okhttp3:logging-interceptor:3.12.3"
    // Threeten BP
    api "org.threeten:threetenbp:1.3.8"
    // Timber logging util
    implementation "com.jakewharton.timber:timber:4.7.1"
    // Google Play Location Services
    implementation "com.google.android.gms:play-services-location:16.0.0"
    // Test dependencies
}
```

** If you want to leverage the `LiveData` that is returned from the SDK methods, refer to [Android Lifecycle Dependencies](https://developer.android.com/jetpack/androidx/releases/lifecycle) for the necessary dependencies based on your usage and needs.

Finally, resync your project with Gradle Files

### Google API Keys

The SDK needs a Google API key for access to location APIs. If you have not already, go to your [Google API Console](https://console.developers.google.com/apis/credentials) and create an Android API key ([link for instructions](https://support.google.com/googleapi/answer/6158862?hl=en)). Then, enable the `Maps SDK for Android`.

Add the following to your `AndroidManifest.xml` with the API key you generated above.

```xml
<application>
    <meta-data
        android:name="com.google.android.geo.API_KEY"
        android:value="<INSERT_API_KEY>"/>
</application>
```

## Configure FlyBuy

FlyBuy needs to be setup and configured at application launch. However, it does not run in the background or use device resources until there is an active order.

The easiest way to configure FlyBuy in your app is to extend the `FlyBuyApplication` and call the `FlyBuy.configure(...)` method from `onCreate()` to setup the API token as follows:

```
class MyApplication : FlyBuyApplication() {

    override fun onCreate() {
        super.onCreate()
        FlyBuy.configure(this, appToken)
    }
    
}
```

Alternatively, you may call `FlyBuy.configure()` in your application `onCreate()` method, and add the following to your activity lifecycle methods:

```
class MyApplication : Application() {

    override fun onCreate() {
        super.onCreate()
        FlyBuy.configure(this, appToken)
    }
    
}
```

```
class MyActivity : Activity() {

    override fun onStart() {
        super.onStart()
        FlyBuy.onActivityStarted()
    }
    
    override fun onStop() {
        super.onStop()
        FlyBuy.onActivityStopped()
    }

}
```

If you don't already have an API token please contact your Account Executive or drop an email to [support@radiusnetworks.com](mailto:support@radiusnetworks.com) with the Project URL you want to enable, and we will set you up.

## Setting Permissions

In order to use the SDK your app will need to request the proper permissions.


### Ask for Location Services permissions

FlyBuy uses mobile sensor data to identify the location of a customer.  The FlyBuy SDK requires FINE_LOCATION permission to properly function which will be merged with your app's Android Manifest. 

If you are targeting API 23 or higher, the app must request the permission at runtime.

Refer to [https://developer.android.com/training/permissions/requesting] on the Android Developer site for details on requesting permissions and best practices. The suggested rationale for the permission is "To accurately locate you for order delivery"


**IMPORTANT:** Whenever, the location permission changes (accepted or declined), make sure to call:

```
FlyBuy.onLocationPermissionChanged()
```

This ensures that the FlyBuy SDK is aware of the permission change so the user location is updated appropriately.

#### Android 10 (API 29 and higher)

As of Android 10 (API 29), location permissions have changed to allow
users to select "when in use" as an option. If your app targets and API 
before API 29, then some additional work is needed for your app to
compile properly. Once you target API 29 or higher, this code needs to
be removed.

In your `AndroidManifest.xml`, add the following service:

```xml
    <service
        android:name="com.radiusnetworks.flybuy.sdk.service.LocationService"
        android:enabled="true"
        android:exported="false"
        tools:node="replace">
        <meta-data
            android:name="com.google.android.gms.version"
            android:value="@integer/google_play_services_version" />
    </service>
```

Note: If this code is not removed when targeting API 29 or higher, your
app will throw the following exception:

```
java.lang.RuntimeException: Unable to start service com.radiusnetworks.flybuy.sdk.service.LocationService ...
java.lang.IllegalArgumentException: foregroundServiceType 0x00000008 is not a subset of foregroundServiceType attribute 0x00000000 in service element of manifest file
```

## Typical Usage Pattern

The FlyBuy SDK leverages several architecture components from [Android Jetpack](https://developer.android.com/jetpack/androidx). `LiveData` objects are returned from SDK methods for data that may need to be observed. Here's an example pattern for using the `LiveData` within a `Fragment` and corresponding `ViewModel`

### Fragment Example

```
class OrdersListFragment : Fragment() {

    // ViewModel for Orders List
    private lateinit var viewModel: OrdersListViewModel

    // Data binding layout for orders list
    private lateinit var binding: FragmentOrdersListBinding

    // UI callback for handling user interactions
    private val uiCallback = object: OrdersListUiCallback {
        override fun onItemClicked(item: OrderItem?) {
            item?.order?.let {
                Toast.makeText(context, it.site.name + " clicked!", Toast.LENGTH_SHORT)
                    .show()
            }        
        }
    }


    private lateinit var skeleton: Skeleton

    override fun onCreateView(inflater: LayoutInflater, container: ViewGroup?, 
                              savedInstanceState: Bundle?): View? {
        // Inflate layout and create view model
        binding = FragmentOrdersListBinding.inflate(inflater).apply {
            lifecycleOwner = this@OrdersListFragment
        }
        val context = context ?: return binding.root
        val factory = InjectorUtils.provideOrdersListViewModelFactory(context)
        viewModel = ViewModelProviders.of(this, factory).get(OrdersListViewModel::class.java)

        return binding.root
    }

    override fun onViewCreated(view: View, savedInstanceState: Bundle?) {
        super.onViewCreated(view, savedInstanceState)
        // Bind UI callback and view model to layout
        viewModel.uiCallback = uiCallback
        binding.data = viewModel
        // Subscribe to orders live data and initiate a sync to retrieve orders
        subscribeUi(viewModel)
        sync(viewModel)

    }

    private fun subscribeUi(viewModel: OrdersListViewModel) {
        // Update the order list when the data changes
        viewModel.orders.observe(this, Observer { posts ->
            posts.let {
                viewModel.updateOrders(posts)
            }
        })
    }

    private fun sync(viewModel: OrdersListViewModel) {
        // Observe status changes in the worker syncing orders
        viewModel.sync().observe(this, Observer { status ->
            viewModel.handleWorkStatus(status)
        })
    }

}
```

### ViewModel Example

```
class OrdersListViewModel(val ordersOperation: OrdersOperation) : BaseIrisViewModel() {

    // Callback to notify the Fragment when user interactions occur
    internal var uiCallback: OrdersListUiCallback? = null
    
    // Callback from data binding layout when user clicks an order
    private val actionCallback = OrderActionCallback {
        uiCallback?.onItemClicked(it)
    }

    // Adapter for RecyclerView
    val dataBoundAdapter: MultiTypeDataBoundAdapter = MultiTypeDataBoundAdapter(actionCallback)

    // Expose the Orders LiveData so the UI can observe it.
    val orders: LiveData<List<Order>> = ordersOperation.orders

    // Start the orders operation to sync orders with the server
    fun sync(): LiveData<WorkStatus> {
        return ordersOperation.sync()
    }

    // Update the items in the adapter with the orders that are received
    internal fun updateOrders(orderModels: List<Order>?) {
        val items = ArrayList<OrderItem>()
        if (null != orderModels && !orderModels.isEmpty()) {
            for (model in orderModels) {
                items.add(OrderItem(model))
            }
        }
        dataBoundAdapter.setItems(*items.toTypedArray())
    }

}

```

# Next Steps

Details on specific SDK functionality that can be used with your app.

- [Handling Notifications](notifications.md)
- [Managing Customers](customer.md)
- [Managing Orders](orders.md)
- [Getting Sites](sites.md)
- [Data Models](data_models.md)

