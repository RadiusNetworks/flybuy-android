# Sites

Examples are in Kotlin.

- [Fetch Sites](#fetch-sites)
- [Fetch All Sites](#fetch-all-sites)

## <span id="fetch-sites">Fetch Sites</span>

Fetch sites for the app. The `query` parameter will return results that
match the `partnerIdentifier` or `name` of the site.

Note that this method uses pagination to retrieve sites. The 
`Pagination` object contains the current page and total pages. Use the 
`page` parameter of fetch along with the `Pagination` object in the 
callback to send subsequent requests to retrieve more sites.

```kotlin
FlyBuy.sites.fetch("1234", 1) { sites, pagination, sdkError ->
    // Handle orders or deal with error
}
```

Get the cached list of sites.

```
FlyBuy.sites.all
```

If you are using Android Jetpack, you can observe sites using `LiveData`.

```kotlin
val sites = FlyBuy.sites.allLiveData
```

## <span id="fetch-all-sites">Fetch All Sites</span>

Fetch all sites for the app.

**IMPORTANT**: This method could result in long running operation with 
multiple API calls behind the scenes. It is recommended to only use this 
method with a `query` parameter to reduce the number of sites in the 
response.

```kotlin
FlyBuy.sites.fetchAll("1234") { sites, sdkError ->
    // Handle orders or deal with error
}
```
