# Customers

## Create a Customer

Create a customer account using information from the user. Consent should be collected from the user (e.g. checkboxes).

```kotlin
fun createCustomer() {
    val customerInfo = CustomerInfo (
                           name = "Marty McFly",
                           carType = "DeLorean",
                           carColor = "Silver",
                           licensePlate = "OUTATIME"
                           )
    val customerConsent = CustomerConsent(
                            termsOfService = true,
                            ageVerification = true
                            )
    
    FlyBuy.customer.create(customerInfo, customerConsent) { customer, sdkError ->
        // Handle customer or deal with error
    }
}
```

## Create a Customer with Login

Create a customer account with email and password using information from the user. Consent should be collected from the user (e.g. checkboxes).

```kotlin
fun createCustomerWithLogin() {
    val loginInfo = LoginInfo (
                            email = "test@example.com",
                            password = "password"
                            )
    val customerInfo = CustomerInfo (
                            name = "Marty McFly",
                            carType = "DeLorean",
                            carColor = "Silver",
                            licensePlate = "OUTATIME"
                            )
    val customerConsent = CustomerConsent(
                            termsOfService = true,
                            ageVerification = true
                            )
    
    FlyBuy.customer.createWithLogin(customerInfo, loginInfo, customerConsent) { customer , sdkError ->
        // Handle customer or deal with error
    }
}
```

## Sign Up a Customer

Link an email and password with the current anonymous logged in user. 

```kotlin
fun signUp() {
    val loginInfo = LoginInfo (
                            email = "test@example.com",
                            password = "password"
                            )
    
    FlyBuy.customer.signUp(loginInfo) { customer, sdkError ->
        // Handle customer or deal with error
    }
}
```

## Sign In

Sign in the user in using existing credentials

```kotlin
fun login() {
    val loginInfo = LoginInfo(
                    email = "test@example.com",
                    password = "password"
                    )

    FlyBuy.customer.login(loginInfo) { customer, sdkError ->
        // Handle customer or deal with error
    }
}
```

## Sign In with Token

Sign in the user with a previously obtained customer API token

```kotlin
fun loginWithToken() {
    FlyBuy.customer.loginWithToken("token") { customer, sdkError ->
        // Handle customer or deal with error
    }
}
```

## Sign out the current Customer

Signs out the current customer.

```kotlin
fun signOut() {
    FlyBuy.customer.signOut { sdkError ->
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


