# Direct API Integration

Since our own library updates tend to lag slightly behind API updates, we also publish a Swagger UI as well as the [OpenAPI 3.0.0](https://swagger.io/specification/).

You have the following options to explore the Swagger UI:

1. The Swagger UI as deployed with our service [available here](https://data-us1.oculusd.com/v2/ui/)
2. The YAML definition is also [available on GitHub](https://github.com/oculusd/openapi-definitions)

## Basic Flow

### New Accounts (aka Root Account)

Currently, the service offers only the concept of a `RootAccount`. In the near future each root account will have the ability to create normal users with a flexible permissions system.

To use the service, you would need at least one active root account.

#### Check if new root accounts can be registered.

Before going through the effort of creating a new root account, you could a quick simple check to see if root account registration is open.

Since we are still in Alpha testing, we may occasionally suspend new root account registrations. 

##### Preparations

You will need the following:

* A system with Internet connectivity
* [curl(1)](https://curl.haxx.se/) installed (optional, if you are not using the Swagger UI)

![Step 1](img/quick_start_step_001.png)

[Swagger Link](https://data-us1.oculusd.com/v2/ui/#/default/iotappv2.iotappv2.pf_accept_new_root_accounts)

##### [Command Line] Command to Run

If you are not using the Swagger UI, run the following command in a terminal/shell

    $ curl -X GET "https://data-us1.oculusd.com/v2/accept-new-root-accounts" -H  "accept: application/json"

##### Expected output

The JSON output to expect will look something like the message below

    {
        "Data": {
            "CreateNewRootAccount": true
        },
        "ErrorMessage": null,
        "IsError": false,
        "Message": "We are open for new business",
        "TraceId": "0Kdl-kAUM-WChl-gtiv"
    }

If the `CreateNewRootAccount` field value is `true`, a new root account can be created.

#### Create a new root account

Next you can create your account. 

**WARNING**: The root account is similar to an administrator account and you should take care to protect your account credentials.

##### Preparations

You will need the following information in order to proceed:

* A valid email address
* An account name - anything you wish, really
* A passphrase

The requirements for the passphrase:

1. At least 20 characters long
2. Must contain at least one space

![Step 1](img/quick_start_step_002.png)

[Swagger Link](https://data-us1.oculusd.com/v2/ui/#/default/iotappv2.iotappv2.pf_register_root_account)

##### [Command Line] Command to Run



##### Expected output

#### Activate root account

##### Preparations

##### [Command Line] Command to Run

##### Expected output

#### Authenticate a new root account

##### Preparations

##### [Command Line] Command to Run

##### Expected output

### New Things

#### Defining a new Thing

##### Preparations

##### [Command Line] Command to Run

##### Expected output

### Thing Data

#### Log thing data

##### Preparations

##### [Command Line] Command to Run

##### Expected output

#### Basic query of thing data

##### Preparations

##### [Command Line] Command to Run

##### Expected output


