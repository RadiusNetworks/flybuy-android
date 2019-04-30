# Customers

Customers are managed via a `CustomerOperation`

A customer operation can be retrieved using the following.

```
    val customerOperation = FlyBuy.customer.getCustomerOperation()
```

## Observe the customer

Returns a `LiveData` stream of the current customer to observe. The customer received will either be the logged in user or `null`

```
    val customer: Customer = customerOperation.customers
```

## Create a Customer

Create a customer account using information from the user. Consent should be collected from the user (e.g. checkboxes)

```
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

```
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

```
    fun signOut(): LiveData<WorkStatus> {
        return customerOperation.logout()
    }
```

## Update a Customer

Update customer info for the logged in user

```
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


