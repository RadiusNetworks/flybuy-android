# Customers

Customers are managed via a `CustomerOperation`

A customer operation can be retrieved using the following.

```kotlin
    val customerOperation = FlyBuy.getInstance(context).customer.getCustomerOperation()
```

## Observe the customer

Returns a `LiveData` stream of the current customer to observe. The customer received will either be the logged in user or `null`

```kotlin
    val customer: Customer = customerOperation.customers
```

## Create a Customer

Create a customer account using information from the user. Consent should be collected from the user (e.g. checkboxes)

```kotlin
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
    
    fun createProfile(): LiveData<WorkStatus> {
        return customerOperation.create(customerInfo.value!!, customerConsent)
    }
```

## Sign In

Sign the user in using existing credentials

```kotlin
    val loginInfo = LoginInfo(
                    email = "test@example.com",
                    password = "password"
                    )

    fun login(loginInfo: LoginInfo): LiveData<WorkStatus> {
        return customerOperation.login(loginInfo)
    }
```

## Sign out the current Customer

Signs out the current customer.

```kotlin
    fun signOut(): LiveData<WorkStatus> {
        return customerOperation.logout()
    }
```

## Update a Customer

Update customer info for the logged in user

```kotlin
    val customerInfo = CustomerInfo (
                           name = "Marty McFly",
                           carType = "DeLorean",
                           carColor = "Silver",
                           licensePlate = "OUTATIME"
                           )
    
    fun update(customerInfo: CustomerInfo): LiveData<WorkStatus> {
        return customerOperation.updateCustomer(customerInfo)
    }
```


