# Quick Start

## Getting Started

This guide will walk through the basic setup for integrating the FlyBuy SDK into an Android app. There are a few things that need to be set up properly, we recommend following this guide carefully when first integrating with your app.

## Requirements

- Android API 17 or higher

## SDK Installation

Currently FlyBuy can be installed via gradle or manually.

### Gradle Install

Include the following in your `build.gradle` dependencies

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

    // Other dependencies ...
}
```

** If you want to leverage the `LiveData` that is returned from the SDK methods, refer to [Android Lifecycle Dependencies](https://developer.android.com/jetpack/androidx/releases/lifecycle) for the necessary dependencies based on your usage and needs.

Finally, resync your project with Gradle Files

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

Alternatively, you may add the following to your activity lifecycle methods>

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

FlyBuy uses mobile sensor data to identify the location of a customer.  The FlyBuy SDK requires FINE_LOCATION permission to properly function. 

Add the following to `AndroidManifest.xml`:

```xml
<uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION"/>
<uses-permission android:name="android.permission.ACCESS_FINE_LOCATION"/>
```

If you are targeting API 23 or higher, the app must request the permission at runtime.

Refer to [https://developer.android.com/training/permissions/requesting] on the Android Developer site for details on requesting permissions and best practices. The suggested rationale for the permission is "To accurately locate you for order delivery"


**IMPORTANT:** Whenever, the location permission changes (accepted or declined), make sure to call:

```
FlyBuy.onLocationPermissionChanged()
```

This ensures that the FlyBuy SDK is aware of the permission change so the user location is updated appropriately.


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

