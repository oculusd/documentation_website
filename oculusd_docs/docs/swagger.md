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

```bash
$ curl -X GET "https://data-us1.oculusd.com/v2/accept-new-root-accounts" -H  "accept: application/json"
```

##### Expected output

The JSON output to expect will look something like the message below

```json
{
    "Data": {
        "CreateNewRootAccount": true
    },
    "ErrorMessage": null,
    "IsError": false,
    "Message": "We are open for new business",
    "TraceId": "0Kdl-kAUM-WChl-gtiv"
}
```

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

```bash
$ curl -X POST "https://data-us1.oculusd.com/v2/register/root-account/your-email-address@example.tld" \
-H  "accept: application/json" \
-H  "Content-Type: application/json" \
-d "{\"AccountName\":\"Temp account for documentation\",\"PassPhrase\":\"A very strong passphrase is easy to remember\"}"
```

##### Expected output

A successful registration will result in a message like the one below. Take note of the `RootAccountId`:

```json
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
```

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

```bash
$ curl -X POST "https://data-us1.oculusd.com/v2/activate/root-account/raber6mk7/xxxx...." -H \
"accept: application/json" \
-H  "Content-Type: application/json" \
-d "{\"Passphrase\":\"A very strong passphrase is easy to remember\"}"
```

Note: The `xxxx....` represents the activation token you received via e-mail.

##### Expected output

On successful activation you will receive the following messages:

```json
{
    "Data": null,
    "ErrorMessage": null,
    "IsError": false,
    "Message": "RootAccount activated successfully",
    "TraceId": "eLm7-Olww-TWf1-zmet"
}
```

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

```bash
$ curl -X POST "https://data-us1.oculusd.com/v2/account/root-account/raber6mk7/authenticate" \
-H  "accept: application/json" \
-H  "Content-Type: application/json" \
-d "{\"Passphrase\":\"A very strong passphrase is easy to remember\",\"TokenTTL\":300}"
```

##### Expected output

After successfully authenticating, you will receive the following output:

```json
{
    "Data": "eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJEYXRhIjoic1grTlVxL2c0QldCM1MvT3Mvb053YU1wMHZ3L29KM28zUVNzTVQ0Rkd1K0ZQQkxDcWdkYkxrNllTYnZ3OFdaSmtxSEp2V0hrcGlnazVHcGpHUkdHdlpIVEdHYmVaUnNXcnkxRTRFZFB5Ky9MNFVvb1NSbnc0MmcxSFZqd1NOVE55MTljMHpvMlFWWlFkSVVIT3A5aHk4K2M1K3FiYWg4OE5ScVEvVWt1dGtQTWxINWRhQWtkMHljZXFrRXVzMTFqOEdBTTRmVjJteFV4ZDdtYWVWWWpOZi83VkdaVE5MbXYwWmMvYnZJem1MNnRML005a1ludzA1cWRDQTlGL3Rld3ZiclFQQjcrbUJUT1hyenc2MXRHdytPVXRacE4xWWlpTTgrT3hsdURBbHA2UE9DRSIsInN1YiI6Im9jdWx1c2QifQ.WlVCPzfJFFKBoV9DFyvbu2oFIdOnPpOZvl5Hd6Voh0A",
    "ErrorMessage": null,
    "IsError": false,
    "Message": "RootAccount authenticated successfully. Warnings: []",
    "TraceId": "1ABW-qfWf-7sFj-cq8U"
}
```

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

```bash
$ curl -X GET "https://data-us1.oculusd.com/v2/thinggroup/root-account-context/raber6mk7/list-groups" \
-H  "accept: application/json" \
-H  "Authorization: Bearer aaa.bbb.ccc"
```

##### Expected output

The following output can be expected:

```json
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
```

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

```json
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
```

##### [Command Line] Command to Run

The curl(1) command:

```bash
$ curl -X POST "https://data-us1.oculusd.com/v2/thinggroup/root-account-context/raber6mk7/new-thing/tgberbkrib" \
-H  "accept: application/json" \
-H  "Authorization: Bearer aaa.bbb.ccc" \
-H  "Content-Type: application/json" \
-d "{...}"
```

**Note**: The command has been trimmed. Replace the `Authorization` and JSON definition with the correct values.

##### Expected output

If the new thing was successfully created, you will receive the following:

```json
{
    "Data": {
        "ThingId": "thingberdom381"
    },
    "ErrorMessage": null,
    "IsError": false,
    "Message": "A new thing group was created successfully.",
    "TraceId": "83WE-lTB1-gr6b-wvHL"
}
```

The `Data` field contain a field called `ThingId` which has the new `Thing` ID you will need when logging sensor data from this thing.

### Thing Data

#### Creating a Thing Session Token

In order for things to log data, a thing token is required. This section explain the steps to create a thing token.

Typically thing tokens do not have to expire and the default token time-to-live (TTL) is set to the maximum integer value of a 64-bit positive integer.

When a token TTL needs to be set, supply the number of seconds the session should be valid for from the current timestamp.

##### Preparations

The following information is required for the request:

* Root account bearer token
* Root account ID
* Thing group ID
* Thing ID
* (Optional) Token TTL

![Step 7](img/quick_start_step_007.png)

[Swagger Link](https://data-us1.oculusd.com/v2/ui/#/default/iotappv2.iotappv2.pf_thing_root_account_create_thing_session)

##### [Command Line] Command to Run

The following curl(1) command can be run:

```bash
$ curl -X GET "https://data-us1.oculusd.com/v2/thing/root-account-context/raber6mk7/create-thing-session/tgberbkrib/thingberdom381" \
-H  "accept: application/json" \
-H  "Authorization: Bearer aaa.bbb.ccc"
```

##### Expected output

The following JSON data will be returned if the command completed successfully.

```json
{
  "Data": {
    "ThingToken": "xxxxxx.yyyyyy.zzzzzz",
    "ThingTokenExpiry": 9223372036854776000
  },
  "ErrorMessage": null,
  "IsError": false,
  "Message": null,
  "TraceId": "wQeG-KojF-8aJ6-aAC9"
}
```

**Note**: The `Data` field contains the following two fields:

* `ThingToken` - The bearer token the `Thing` will require to authorize the submitted sensor data.
* `ThingTokenExpiry` - The Unix timestamp at which the token will expire

#### Log thing data

AT some point a `Thing` will start to collect sensor data. This data can be submitted to the OculusD API service.

**Important**: The current service allow for a periodic submission of sensor data, determined by the account limits (rate, volume etc.). The exact numbers is subject to change in the ALPHA and BETA testing phases before being finalized for the final commercial packages. There is not yet a streaming (MQTT-type) service.

##### Preparations

In order to collect sensor data, the `Thing` must prepare the following information:

* A valid `Thing` token
* The `Thing` ID
* The actual sensor data
* (Optional) If you need to record the exact local system timestamp with the data, you must also record the timestamp when collecting sensor data.

**Note**: WHen a timestamp is supplied, that timestamp represents the timestamp for ALL sensor readings. There is not yet a facility to record fine-grained per-sensor timestamp values.

![Step 8](img/quick_start_step_008.png)

[Swagger Link](https://data-us1.oculusd.com/v2/ui/#/default/iotappv2.iotappv2.pf_thing_root_account_create_thing_session)

For this example, the following JSON data will be used:

```JSON
{
    "ReadingTimestamp": 1555568797,
    "Sensors": [
        {
            "SensorName": "Cpu Utilization",
            "AxisReadings": [
                {
                    "AxisName": "System Usage",
                    "Data": "1.6"
                },
                {
                    "AxisName": "User Usage",
                    "Data": "12.9"
                }
            ]
        },
        {
            "SensorName": "Memory Utilization",
            "AxisReadings": [
                {
                    "AxisName": "Ram Used",
                    "Data": "3791920"
                },
                {
                    "AxisName": "Swap Used",
                    "Data": "0"
                }
            ]
        }
    ]
}
```

##### [Command Line] Command to Run

The following curl(1) command is an example of how the sample data can be submitted:

```bash
$ curl -X POST "https://data-us1.oculusd.com/v2/data/log/thingberdom381" \
-H  "accept: application/json" \
-H  "Authorization: Bearer aaa.bbb.ccc" \
-H  "Content-Type: application/json" \
-d "{\"ReadingTimestamp\":1555568797,\"Sensors\":[{\"SensorName\":\"Cpu Utilization\",\"AxisReadings\":[{\"AxisName\":\"System Usage\",\"Data\":\"1.6\"},{\"AxisName\":\"User Usage\",\"Data\":\"12.9\"}]},{\"SensorName\":\"Memory Utilization\",\"AxisReadings\":[{\"AxisName\":\"Ram Used\",\"Data\":\"3791920\"},{\"AxisName\":\"Swap Used\",\"Data\":\"0\"}]}]}"
```

##### Expected output

Once the data is capture, the following JSON message will be returned:

```json
{
  "Data": {
    "AxisIgnored": 0,
    "SensorsIgnored": 0,
    "SensorsProcessed": 0,
    "TotalRecordQty": 0
  },
  "ErrorMessage": null,
  "IsError": false,
  "Message": null,
  "TraceId": "nrwU-Qt3I-SdB3-VzQt"
}
```

#### Basic query of thing data

At the moment only a basic query interface is provided, which is explained below.

##### Preparations

To execute a basic query, you need the following information:

* Root account bearer token (requires root account authentication)
* Root account ID
* Thing group ID
* Thing ID
* (Optional) Limit parameter (limits the number of rows per sensor-axis)
* (Optional) `notBefore` parameter, which is a unix timestamp
* (Optional) `notAfter` parameter, which is a unix timestamp

![Step 9](img/quick_start_step_009.png)

[Swagger Link](https://data-us1.oculusd.com/v2/ui/#/default/iotappv2.iotappv2.pf_data_root_account_query_simple)

##### [Command Line] Command to Run

Use the following curl(1) command to execute a basic query:

```bash
$ curl -X GET "https://data-us1.oculusd.com/v2/data/query/root-account-context/raber6mk7/tgberbkrib/thingberdom381/simple" \
-H  "accept: application/json" \
-H  "Authorization: Bearer aaa.bbb.ccc"
```

##### Expected output

The following example output is from data collected from the example `Thing` over a couple of minutes:

```json
{
  "Data": {
    "CsvData": [
      "\"Cpu Utilization\",\"System Usage\",\"1555568797\",\"1555569281\",\"1.6\"",
      "\"Cpu Utilization\",\"User Usage\",\"1555568797\",\"1555569281\",\"12.9\"",
      "\"Memory Utilization\",\"Ram Used\",\"1555568797\",\"1555569281\",\"3187\"",
      "\"Memory Utilization\",\"Swap Used\",\"1555568797\",\"1555569281\",\"0\"",
      "\"Cpu Utilization\",\"System Usage\",\"1555576865\",\"1555576929\",\"2.2\"",
      "\"Cpu Utilization\",\"User Usage\",\"1555576865\",\"1555576929\",\"14.2\"",
      "\"Memory Utilization\",\"Ram Used\",\"1555576865\",\"1555576929\",\"3941272\"",
      "\"Memory Utilization\",\"Swap Used\",\"1555576865\",\"1555576929\",\"0\"",
      "\"Cpu Utilization\",\"System Usage\",\"1555577385\",\"1555577385\",\"1.3\"",
      "\"Cpu Utilization\",\"User Usage\",\"1555577385\",\"1555577385\",\"8.9\"",
      "\"Memory Utilization\",\"Ram Used\",\"1555577385\",\"1555577385\",\"3881236\"",
      "\"Memory Utilization\",\"Swap Used\",\"1555577385\",\"1555577385\",\"0\"",
      "\"Cpu Utilization\",\"System Usage\",\"1555577417\",\"1555577418\",\"0.9\"",
      "\"Cpu Utilization\",\"User Usage\",\"1555577417\",\"1555577418\",\"7.8\"",
      "\"Memory Utilization\",\"Ram Used\",\"1555577417\",\"1555577418\",\"3909008\"",
      "\"Memory Utilization\",\"Swap Used\",\"1555577417\",\"1555577418\",\"0\"",
      "\"Cpu Utilization\",\"System Usage\",\"1555577827\",\"1555577827\",\"3.5\"",
      "\"Cpu Utilization\",\"User Usage\",\"1555577827\",\"1555577827\",\"17.1\"",
      "\"Memory Utilization\",\"Ram Used\",\"1555577827\",\"1555577827\",\"3883636\"",
      "\"Memory Utilization\",\"Swap Used\",\"1555577827\",\"1555577827\",\"0\"",
      "\"Cpu Utilization\",\"System Usage\",\"1555577887\",\"1555577888\",\"1.6\"",
      "\"Cpu Utilization\",\"User Usage\",\"1555577887\",\"1555577888\",\"10.5\"",
      "\"Memory Utilization\",\"Ram Used\",\"1555577887\",\"1555577888\",\"3985688\"",
      "\"Memory Utilization\",\"Swap Used\",\"1555577887\",\"1555577888\",\"0\"",
      "\"Cpu Utilization\",\"System Usage\",\"1555577947\",\"1555577947\",\"1.4\"",
      "\"Cpu Utilization\",\"User Usage\",\"1555577947\",\"1555577947\",\"10.6\"",
      "\"Memory Utilization\",\"Ram Used\",\"1555577947\",\"1555577947\",\"3927100\"",
      "\"Memory Utilization\",\"Swap Used\",\"1555577947\",\"1555577947\",\"0\"",
      "\"Cpu Utilization\",\"System Usage\",\"1555578007\",\"1555578008\",\"2.0\"",
      "\"Cpu Utilization\",\"User Usage\",\"1555578007\",\"1555578008\",\"11.9\"",
      "\"Memory Utilization\",\"Ram Used\",\"1555578007\",\"1555578008\",\"3932172\"",
      "\"Memory Utilization\",\"Swap Used\",\"1555578007\",\"1555578008\",\"0\"",
      "\"Cpu Utilization\",\"System Usage\",\"1555578067\",\"1555578068\",\"1.2\"",
      "\"Cpu Utilization\",\"User Usage\",\"1555578067\",\"1555578068\",\"11.7\"",
      "\"Memory Utilization\",\"Ram Used\",\"1555578067\",\"1555578068\",\"4065728\"",
      "\"Memory Utilization\",\"Swap Used\",\"1555578067\",\"1555578068\",\"0\"",
      "\"Cpu Utilization\",\"System Usage\",\"1555578128\",\"1555578128\",\"3.6\"",
      "\"Cpu Utilization\",\"User Usage\",\"1555578128\",\"1555578128\",\"17.7\"",
      "\"Memory Utilization\",\"Ram Used\",\"1555578128\",\"1555578128\",\"4043776\"",
      "\"Memory Utilization\",\"Swap Used\",\"1555578128\",\"1555578128\",\"0\"",
      "\"Cpu Utilization\",\"System Usage\",\"1555578187\",\"1555578188\",\"4.5\"",
      "\"Cpu Utilization\",\"User Usage\",\"1555578187\",\"1555578188\",\"14.5\"",
      "\"Memory Utilization\",\"Ram Used\",\"1555578187\",\"1555578188\",\"3969872\"",
      "\"Memory Utilization\",\"Swap Used\",\"1555578187\",\"1555578188\",\"0\"",
      "\"Cpu Utilization\",\"System Usage\",\"1555578247\",\"1555578248\",\"1.3\"",
      "\"Cpu Utilization\",\"User Usage\",\"1555578247\",\"1555578248\",\"9.5\"",
      "\"Memory Utilization\",\"Ram Used\",\"1555578247\",\"1555578248\",\"4008100\"",
      "\"Memory Utilization\",\"Swap Used\",\"1555578247\",\"1555578248\",\"0\"",
      "\"Cpu Utilization\",\"System Usage\",\"1555578307\",\"1555578308\",\"1.4\"",
      "\"Cpu Utilization\",\"User Usage\",\"1555578307\",\"1555578308\",\"10.3\"",
      "\"Memory Utilization\",\"Ram Used\",\"1555578307\",\"1555578308\",\"4022364\"",
      "\"Memory Utilization\",\"Swap Used\",\"1555578307\",\"1555578308\",\"0\""
    ],
    "CsvHeaderLine": "sensor-name,axis-name,reading-timestamp,service-timestamp,data"
  },
  "ErrorMessage": null,
  "IsError": false,
  "Message": null,
  "TraceId": "Di0x-hBmj-MeuT-g3uU"
}
```
