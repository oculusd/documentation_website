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

![Step 2](img/quick_start_step_002.png)

[Swagger Link](https://data-us1.oculusd.com/v2/ui/#/default/iotappv2.iotappv2.pf_register_root_account)

##### [Command Line] Command to Run

The following example shows how to use curl(1) to create a new root account:

    $ curl -X POST "https://data-us1.oculusd.com/v2/register/root-account/your-email-address@example.tld" \
    -H  "accept: application/json" \
    -H  "Content-Type: application/json" \
    -d "{\"AccountName\":\"Temp account for documentation\",\"PassPhrase\":\"A very strong passphrase is easy to remember\"}"

##### Expected output

A successful registration will result in a message like the one below. Take note of the `RootAccountId`:

    {
        "Data": {
            "RootAccountId": "raber6mk7",
            "RootAccountStatus": "PENDING"
        },
        "ErrorMessage": null,
        "IsError": false,
        "Message": "RootAccount created successfully",
        "TraceId": "pD7K-axnw-JUos-CLxz"
    }

#### Activate root account

Once the account has been registered, you will need to activate the account. An e-mail will be send to the e-mail address you used during registration, containing the activation token.

##### Preparations

You will need the following:

* The `RootAccountId` (output from the registration command)
* The activation token (in your e-mail)
* The passphrase you used during registration

![Step 3](img/quick_start_step_003.png)

[Swagger Link](https://data-us1.oculusd.com/v2/ui/#/default/iotappv2.iotappv2.pf_activate_root_account)

##### [Command Line] Command to Run

Use the following example curl(1) command to activate your account:

    $ curl -X POST "https://data-us1.oculusd.com/v2/activate/root-account/raber6mk7/xxxx...." -H \
    "accept: application/json" \
    -H  "Content-Type: application/json" \
    -d "{\"Passphrase\":\"A very strong passphrase is easy to remember\"}"

Note: The `xxxx....` represents the activation token you received via e-mail.

##### Expected output

On successful activation you will receive the following messages:

    {
        "Data": null,
        "ErrorMessage": null,
        "IsError": false,
        "Message": "RootAccount activated successfully",
        "TraceId": "eLm7-Olww-TWf1-zmet"
    }

You will also receive a final confirmation e-mail stating that your account was activated.

#### Authenticate a new root account

In order to use any of the administration functions, you will need to authenticate in order to receive a token to be used in subsequent requests.

##### Preparations

You will need the following information to authenticate:

* The `RootAccountId` (output from the registration command)
* The passphrase you used during registration
* (Optional) a session time-to-live (TTL). Maximum value can only be 600 seconds. This is the maximum time the returned authentication token will be valid for. After this time, you will need to re-authenticate and obtain another token.

![Step 4](img/quick_start_step_004.png)

[Swagger Link](https://data-us1.oculusd.com/v2/ui/#/default/iotappv2.iotappv2.pf_account_root_account_authenticate)

##### [Command Line] Command to Run

Run the following command using curl(1):

    $ curl -X POST "https://data-us1.oculusd.com/v2/account/root-account/raber6mk7/authenticate" \
    -H  "accept: application/json" \
    -H  "Content-Type: application/json" \
    -d "{\"Passphrase\":\"A very strong passphrase is easy to remember\",\"TokenTTL\":300}"

##### Expected output

After successfully authenticating, you will receive the following output:

    {
        "Data": "eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJEYXRhIjoic1grTlVxL2c0QldCM1MvT3Mvb053YU1wMHZ3L29KM28zUVNzTVQ0Rkd1K0ZQQkxDcWdkYkxrNllTYnZ3OFdaSmtxSEp2V0hrcGlnazVHcGpHUkdHdlpIVEdHYmVaUnNXcnkxRTRFZFB5Ky9MNFVvb1NSbnc0MmcxSFZqd1NOVE55MTljMHpvMlFWWlFkSVVIT3A5aHk4K2M1K3FiYWg4OE5ScVEvVWt1dGtQTWxINWRhQWtkMHljZXFrRXVzMTFqOEdBTTRmVjJteFV4ZDdtYWVWWWpOZi83VkdaVE5MbXYwWmMvYnZJem1MNnRML005a1ludzA1cWRDQTlGL3Rld3ZiclFQQjcrbUJUT1hyenc2MXRHdytPVXRacE4xWWlpTTgrT3hsdURBbHA2UE9DRSIsInN1YiI6Im9jdWx1c2QifQ.WlVCPzfJFFKBoV9DFyvbu2oFIdOnPpOZvl5Hd6Voh0A",
        "ErrorMessage": null,
        "IsError": false,
        "Message": "RootAccount authenticated successfully. Warnings: []",
        "TraceId": "1ABW-qfWf-7sFj-cq8U"
    }

The `Data` field contain your token to be used in subsequent commands.

**Note**: Each time a root account authenticates, an e-mail of the event will also be sent to the registered e-mail address of the root account.

### New Things

#### Listing of thing groups

Every `Thing` created on the system has to belong to a group, also known as a thing group.

When a new root account is created, a default thing group is created. You will need the thing group ID in order to register new things and therefore you will need this function to obtain the default thing group ID.

##### Preparations

* You will need your root account ID
* You need a valid authentication token

![Step 5](img/quick_start_step_005.png)

[Swagger Link](https://data-us1.oculusd.com/v2/ui/#/default/iotappv2.iotappv2.pf_thinggroup_root_account_list_groups)

##### [Command Line] Command to Run

Run the following curl(1) command to obtain a list of thing groups:

    $ curl -X GET "https://data-us1.oculusd.com/v2/thinggroup/root-account-context/raber6mk7/list-groups" \
    -H  "accept: application/json" \
    -H  "Authorization: Bearer aaa.bbb.ccc"

##### Expected output

The following output can be expected:

    {
        "Data": {
            "ThingGroups": [
            {
                "ThingGroupId": "tgberbkrib",
                "ThingGroupName": "Default Thing Group"
            }
            ]
        },
        "ErrorMessage": null,
        "IsError": false,
        "Message": null,
        "TraceId": "iGGb-0E4m-1AuG-dKzp"
    }

Note the `ThingGroups` field that will contain a list of groups. For new accounts, there is only one group.

#### Defining a new Thing

A `Thing` in the context of the service could be anything with network connectivity capable of integrating with the service end-point to submit sensor data. This includes all of the following examples:

* A computer
* A mobile phone
* A Raspberry Pi Single Board Computer (SBC)
* A project based on the Arduino platform with some kind of network capability, like a GSM shield

Typically, these things will have many sensors. Sensors could be physical, or operate in the physical world, for example a temperature sensor. There is also logical sensors, or software based sensors, that act internally to the system, for example to measure the CPU utilization or free RAM on a computer.

When a sensors take a measurement, several values can be taken. In terms of the service, these various values are measured across axis. For example, a logical CPU sensor may have two axis: the user space CPU utilization value and the system CPU utilization value. Another example is a GPS sensor that may easily have three axis: longitude, latitude and altitude.

Each root account account has limits imposed in terms of how many things can be registered. Furthermore, each thing is limited to a certain number of sensors and each sensor is limited to the amount of axis. These limits can be increased depending on the upfront commercial commitment a user is prepared to make.

##### Preparations

For this example, we are going to create a simple `computer` thing to record some basic system utilization data, including CPU usage and memory usage. The information we require is the following:

* A thing group ID that this thing will belong to
* A root account authentication token
* Details about our thing and sensors (see example below)

![Step 6](img/quick_start_step_006.png)

[Swagger Link](https://data-us1.oculusd.com/v2/ui/#/default/iotappv2.iotappv2.pf_thinggroup_root_account_new_group)

**TIP**: Since the definition of the `Thing` can take some time, first prepare the definition in a text editor and only authenticate using the root account credentials when you are happy with the definition. 

The following JSON definition represents our example thing of a hypothetical LAB PC:

    { 
        "Thing": {
            "ThingName": "Home Lab PC", 
            "ThingDescription": "A PC used in the maker lab", 
            "ThingMetaData": {
                "ComputerHostname": "ABCDEFG",
                "Make": "Dell",
                "RAMInstalled": 17179869184
            },
            "Sensors": [ 
                {
                    "SensorName": "Cpu Utilization", 
                    "SensorDescription": "System and User CPU Utilization", 
                    "SensorAxis": [ 
                        {
                            "AxisName": "System Usage", 
                            "DataType": "NUMBER", 
                            "UserDefinedType": "PERCENT"
                        },
                        {
                            "AxisName": "User Usage",
                            "DataType": "NUMBER",
                            "UserDefinedType": "PERCENT"
                        }
                    ]
                },
                {
                    "SensorName": "Memory Utilization", 
                    "SensorDescription": "RAM and Swap space used on the system", 
                    "SensorAxis": [
                        {
                            "AxisName": "Ram Used", 
                            "DataType": "NUMBER",
                            "UserDefinedType": "BYTES"
                        },
                        {
                            "AxisName": "Swap Used", 
                            "DataType": "NUMBER", 
                            "UserDefinedType": "BYTES"
                        }
                    ]
                }
            ]
        }
    }

##### [Command Line] Command to Run

The curl(1) command:

    $ curl -X POST "https://data-us1.oculusd.com/v2/thinggroup/root-account-context/raber6mk7/new-thing/tgberbkrib" \
    -H  "accept: application/json" \
    -H  "Authorization: Bearer aaa.bbb.ccc" \
    -H  "Content-Type: application/json" \
    -d "{...}"

**Note**: The command has been trimmed. Replace the `Authorization` and JSON definition with the correct values.

##### Expected output

If the new thing was successfully created, you will receive the following:

    {
        "Data": {
            "ThingId": "thingberdom381"
        },
        "ErrorMessage": null,
        "IsError": false,
        "Message": "A new thing group was created successfully.",
        "TraceId": "83WE-lTB1-gr6b-wvHL"
    }

The `Data` field contain a field called `ThingId` which has the new `Thing` ID you will need when logging sensor data from this thing.

### Thing Data

#### Log thing data

##### Preparations

##### [Command Line] Command to Run

##### Expected output

#### Basic query of thing data

##### Preparations

##### [Command Line] Command to Run

##### Expected output


