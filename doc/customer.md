# Customers

## Create a Customer

Create a customer account using information from the user. Consent should be collected from the user (e.g. checkboxes).

```kotlin
fun createCustomer() {
    val customerInfo = CustomerInfo (
                           name = "Marty McFly",
                           carType = "DeLorean",
                           carColor = "Silver",
                           licensePlate = "OUTATIME",
                           phone = "555-555-5555"
                           )
    
    FlyBuy.customer.create(customerInfo, termOfService = userAcceptedTerms,
            ageVerification = userVerifiedAge) { customer, sdkError ->
        // Handle customer or deal with error
    }
}
```

_Note: The `phone` parameter is optional_

**IMPORTANT:** If the `termsOfService` and `ageVerification` parameters are not `true`, an error will be returned and the customer will not be created.

## Create a Customer with Login

Create a customer account with email and password using information from the user. Consent should be collected from the user (e.g. checkboxes).

```kotlin
fun createCustomerWithLogin() {
    val customerInfo = CustomerInfo (
                            name = "Marty McFly",
                            carType = "DeLorean",
                            carColor = "Silver",
                            licensePlate = "OUTATIME",
                            phone = "555-555-5555"
                            )
    
    FlyBuy.customer.createWithLogin(customerInfo, email = "test@example.com", password = "passwordk",
            termsOfService = termsAccepted, ageVerification = ageVerified) { customer , sdkError ->
        // Handle customer or deal with error
    }
}
```

## Sign Up a Customer

Link an email and password with the current anonymous logged in user. 

```kotlin
fun signUp() {
    FlyBuy.customer.signUp("test@example.com", "password") { customer, sdkError ->
        // Handle customer or deal with error
    }
}
```

## Login

Login the user in using existing credentials

```kotlin
fun login() {
    FlyBuy.customer.login("test@example.com", "password") { customer, sdkError ->
        // Handle customer or deal with error
    }
}
```

## Login with Token

Login the user with a previously obtained customer API token

```kotlin
fun loginWithToken() {
    FlyBuy.customer.loginWithToken("token") { customer, sdkError ->
        // Handle customer or deal with error
    }
}
```

## Log out the current Customer

Logs out the current customer.

```kotlin
fun signOut() {
    FlyBuy.customer.logout { sdkError ->
        // Handle error
    }
}
```

## Update a Customer

Update customer info for the logged in user

```kotlin
fun updateCustomer() {
    val customerInfo = CustomerInfo (
                           name = "Marty McFly",
                           carType = "DeLorean",
                           carColor = "Silver",
                           licensePlate = "OUTATIME"
                           )
    
    FlyBuy.customer.update(customerInfo) { customer, sdkError ->
        // Handle customer or deal with error
    }
}
```

## Get the current Customer

Returns an instance of the current customer. This may not be accessed directly from the main thread.

```kotlin
FlyBuy.customer.current
```


