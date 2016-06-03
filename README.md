## Voucherify Python SDK

[Voucherify](http://voucherify.io?utm_source=inbound&utm_medium=github&utm_campaign=voucherify-python-sdk) has a new platform that will help your team automate voucher campaigns. It does this by providing composable API and the marketer-friendly interface that increases teams' productivity:

- **roll-out thousands** of vouchers **in minutes** instead of weeks,
- **check status** or disable **every single** promo code in real time,
- **track redemption** history and build reports on the fly.

Here you can find a library that makes it easier to integrate Voucherify with your Python based application.

Full documentation is located at [voucherify.readme.io](https://voucherify.readme.io).

### Usage

#### Authentication

[Log-in](http://app.voucherify.io/#/login) to Voucherify web interface and obtain your Application Keys from [Configuration](https://app.voucherify.io/#/app/configuration):

![](https://www.filepicker.io/api/file/WKYkl2bSAWKHccEN9tEG)



#### Configure through a non-global API object

```python
from voucherify import Client as voucherifyClient

voucherify = voucherifyClient(
    application_id="c70a6f00-cf91-4756-9df5-47628850002b",
    client_secret_key="3266b9f8-e246-4f79-bdf0-833929b1380c"
)
```

#### Listing vouchers

`voucherify.list(filter)`

Filter parameters:

- code_query
- limit (default 10)
- skip (default 0)
- category
- campaign
- customer

Example:
```python
filter_params = {
  "limit": 10,
  "skip": 20,
  "category": "API Test"
}

vouchers_list = voucherify.list(filter_params)
```

Result:
```json
[{
     "code": "9mYBpIk",
     "campaign": null,
     "category": "API Test",
     "discount": {
       "type": "AMOUNT",
       "amount_off": 400
     },
     "start_date": "2016-03-01T12:00:00Z",
     "expiration_date": null,
     "redemption": {
       "quantity": 1,
       "redeemed_quantity": 0,
       "redemption_entries": []
     },
     "active": true,
     "additional_info": null,
     "metadata": null
   },
   {
       "code": "AzTsIH",
       "campaign": null,
       "category": "API Test",
       "discount": {
        "type": "AMOUNT",
        "amount_off": 400
       },
       "start_date": "2016-03-01T10:00:00Z",
       "expiration_date": null,
       "redemption": {
        "quantity": 1,
        "redeemed_quantity": 0,
        "redemption_entries": []
       },
       "active": true,
       "additional_info": null,
       "metadata": null
   },
   ...
]
```

#### Getting voucher details

`voucherify.get(voucher_code)`

Example:
```python
voucher = voucherify.get("Testing7fjWdr")
```

Result:
```json
{
    "code": "v1GiJYuuS",
    "campaign": "vip",
    "discount": {
        "percent_off": 10.0,
        "type": "PERCENT"
    },
    "expiration_date": "2016-12-31T23:59:59Z",
    "redemption": {
        "quantity": 3,
        "redeemed_quantity": 1,
        "redemption_entries": [
            {
                "id": "r_gQzOnTwmhn2nTLwW4sZslNKY",
                "object": "redemption",
                "date": "2016-04-24T06:03:35Z",
                "tracking_id": "GENERATED-OR-PROVIDED-TRACKING-ID"
            }
        ]
    },
    "additional_info": ""
}
```
#### Creating a voucher

`voucherify.create(voucher)`

Example:

```python
payload = {
    "discount": {
        "type": "AMOUNT",
        "amount_off": 1000  # 10.00
    },
    "category": "API Test",
    "start_date": "2016-01-01T00:00:00Z",
    "expiration_date": "2016-12-31T23:59:59Z"
}

new_voucher = voucherify.create(payload)
```

Result:

```json
{
    "code": "JxiJaV",
    "campaign": null,
    "category": "API Test",
    "discount": {
        "type": "AMOUNT",
        "amount_off": 1000
    },
    "start_date": "2016-01-01T00:00:00Z",
    "expiration_date": "2016-12-31T23:59:59Z",
    "redemption": {
        "quantity": 1,
        "redeemed_quantity": 0,
        "redemption_entries": []
    },
    "active": true,
    "additional_info": null,
    "metadata": null
}
```

#### Disabling a voucher

`voucherify.disable(voucher_code)`

Example:

```python
result = voucherify.disable("JxiJaV")
```

Result:

```text
True|None
```

#### Enabling a voucher

`voucherify.enable(voucher_code)`

Example:

```python
result = voucherify.enable("JxiJaV")
```

Result:
```text
True|None
```

#### Getting voucher redemption

`voucherify.redemption(voucher_code)`

Example:
```python
result = voucherify.redemption("Testing7fjWdr")
```

Result:
```json
{
    "quantity": 3,
    "redeemed_quantity": 1,
    "redemption_entries": [
        {
            "id": "r_gQzOnTwmhn2nTLwW4sZslNKY",
            "object": "redemption",
            "date": "2016-04-24T06:03:35Z",
            "tracking_id": "GENERATED-OR-PROVIDED-TRACKING-ID"
        }
    ]
}
```

#### Publishing voucher

This method selects a voucher that is suitable for publication, adds a publish entry and returns the voucher.
A voucher is suitable for publication when it's active and has not been published more times than the redemption limit.

##### Publish using campaign name

`voucherify.publish(campaign_name)`

Example:

```python
result = voucherify.publish("First Ride")
```

##### Publish using params

`voucherify.publish(params)`

Example:

```python
payload = {
    "campaign": "First Ride",
    "channel": "Email",
    "customer": "donny.roll@mail.com"
}
result = voucherify.publish(payload)
```

Result *(for both)*:

```json
{
   "code": "FR-zT-u9I7zG",
   "campaign": "First Ride",
   "category": null,
   "discount": {
      "type": "PERCENT",
      "amount_off": 50
   },
   "start_date": "2015-01-01T00:00:00Z",
   "expiration_date": "2016-12-31T23:59:59Z",
   "publish": {
        "count": 1,
        "entries": [{
            "channel": "Email",
            "customer": "donny.roll@mail.com",
            "published_at": "2016-01-22T09:25:07Z"
        }]
   },
   "redemption": {
      "quantity": 1,
      "redeemed_quantity": 0,
      "redemption_entries": []
   },
   "active": true,
   "additional_info": null
}
```

Error:

```json
{
  "code": 400,
  "message": "Couldn't find any voucher suitable for publication."
}
```

#### Redeeming voucher

`voucherify.redeem(voucher_code, tracking_id|customer_profile*)`

##### 1. Just by code

Example:
```python
result = voucherify.redeem("Testing7fjWdr")
```

Result (voucher details after redemption):

```json
{
    "id": "r_yRmanaA6EgSE9uDYvMQ5Evfp",
    "object": "redemption",
    "date": "2016-04-25T10:34:57Z",
    "tracking_id": "(tracking_id not set)",
    "voucher": {
        "code": "v1GiJYuuS",
        "campaign": "vip",
        "discount": {
            "percent_off": 10.0,
            "type": "PERCENT"
        },
        "expiration_date": "2016-12-31T23:59:59Z",
        "redemption": {
            "quantity": 3,
            "redeemed_quantity": 2,
            "redemption_entries": [
                {
                    "id": "r_gQzOnTwmhn2nTLwW4sZslNKY",
                    "object": "redemption",
                    "date": "2016-04-24T06:03:35Z",
                    "tracking_id": "(tracking_id not set)"
                },
                {
                    "id": "r_yRmanaA6EgSE9uDYvMQ5Evfp",
                    "object": "redemption",
                    "date": "2016-04-25T10:34:57Z",
                    "tracking_id": "(tracking_id not set)"
                }
            ]
        },
        "active": true,
        "additional_info": ""
    }
}
```

Error:

```json
{
  "code": 400,
  "message": "voucher expired or quantity exceeded",
  "details": "v1GiJYuuS"
}
```

##### 2. With tracking id

You can provide a tracking id (e.g. your customer's login or a generated id) to the voucher redemption request.

Example:

```python
result = voucherify.redeem("Testing7fjWdr", "alice.morgan")
```

Result:

```json
{
    "id": "r_yRmanaA6EgSE9uDYvMQ5Evfp",
    "object": "redemption",
    "date": "2016-04-25T10:34:57Z",
    "tracking_id": "(tracking_id not set)",
    "voucher": {
        "code": "v1GiJYuuS",
        "campaign": "vip",
        "discount": {
            "percent_off": 10.0,
            "type": "PERCENT"
        },
        "expiration_date": "2016-12-31T23:59:59Z",
        "redemption": {
            "quantity": 3,
            "redeemed_quantity": 3,
            "redemption_entries": [
                {
                    "id": "r_gQzOnTwmhn2nTLwW4sZslNKY",
                    "object": "redemption",
                    "date": "2016-04-24T06:03:35Z",
                    "tracking_id": "(tracking_id not set)"
                },
                {
                    "id": "r_yRmanaA6EgSE9uDYvMQ5Evfp",
                    "object": "redemption",
                    "date": "2016-04-25T10:34:57Z",
                    "tracking_id": "(tracking_id not set)"
                },
                {
                    "id": "r_irOQWUTAjthQwnkn5JQM1V6N",
                    "object": "redemption",
                    "date": "2016-04-25T12:04:08Z",
                    "tracking_id": "alice.morgan"
                }
            ]
        },
        "active": true,
        "additional_info": ""
    }
}
```

##### 3. With customer profile

You can record a detailed customer profile consisting of an `id` (obligatory), `name`, `email`, `description` and a `metadata` section that can include any data you wish.

Example:

```python
customer = {
    "id": "alice.morgan",
    "name": "Alice Morgan",
    "email": "alice@morgan.com",
    "description": "",
    "metadata": {
        "locale": "en-GB",
        "shoeSize": 5,
        "favourite_brands": ["Armani", "L'Autre Chose", "Vicini"]
    }
}
payload = {
    "voucher": "v1GiJYuuS",
    "customer": customer
}
voucherify.redeem(payload)
```

### Listing redemptions

UseUse `redemptions` to get a filtered list of redemptions.

Filter parameters:

- limit (default: 100)
- page (default: 0)
- start_date (default: beginning of current month)
- end_date (default: end of current month)
- result - Success | Failure-NotExist | Failure-Inactive
- customer

`voucherify.redemptions(filter)`

Example:

```python
filter_params = {
    "limit": 1000,
    "page": 0,
    "start_date": "2016-04-01T00:00:00",
    "end_date": "2016-04-30T23:59:59",
    "result": "Success"
}

redemptions = voucherify.redemptions(filter_params)
```

#### Rollback a redemption

Use `rollback` to revert a redemption.
It will create a rollback entry in `redemption.redemption_entries` and give 1 redemption back to the pool
(decrease `redeemed_quantity` by 1).

`voucherify.rollback(redemption_id, reason*)`

Example:

```python
result = voucherify.rollback("r_irOQWUTAjthQwnkn5JQM1V6N", "alice.morgan")
```

Result:
```json
{
    "id": "rr_1634wLkb8glgRXrTmsxRzDBd",
    "object": "redemption_rollback",
    "date": "2016-04-25T10:35:02Z",
    "tracking_id": "alice.morgan",
    "redemption": "r_irOQWUTAjthQwnkn5JQM1V6N",
    "voucher": {
        "code": "v1GiJYuuS",
        "campaign": "vip",
        "discount": {
            "percent_off": 10.0,
            "type": "PERCENT"
        },
        "expiration_date": "2016-12-31T23:59:59Z",
        "redemption": {
            "quantity": 3,
            "redeemed_quantity": 2,
            "redemption_entries": [
                {
                    "id": "r_gQzOnTwmhn2nTLwW4sZslNKY",
                    "object": "redemption",
                    "date": "2016-04-24T06:03:35Z",
                    "tracking_id": "(tracking_id not set)"
                },
                {
                    "id": "r_yRmanaA6EgSE9uDYvMQ5Evfp",
                    "object": "redemption",
                    "date": "2016-04-25T10:34:57Z",
                    "tracking_id": "(tracking_id not set)"
                },
                {
                    "id": "r_irOQWUTAjthQwnkn5JQM1V6N",
                    "object": "redemption",
                    "date": "2016-04-25T12:04:08Z",
                    "tracking_id": "alice.morgan"
                },
                {
                    "id": "rr_1634wLkb8glgRXrTmsxRzDBd",
                    "object": "redemption_rollback",
                    "date": "2016-04-25T10:35:02Z",
                    "tracking_id": "alice.morgan",
                    "redemption": "r_irOQWUTAjthQwnkn5JQM1V6N"
                }
            ]
        },
        "active": true,
        "additional_info": ""
    }
}
```

Possible errors are:
- 404 - Resource not found - if voucher with given `redemption_id` doesn't exist
- 400 - Already rolled back - if redemption with given `redemption_id` has been rolled back already
- 400 - Invalid redemption id - when trying to rollback a rollback.

### Utils

Use our set of utils to calculate a price after discount or discount amount.

#### Available methods

- `utils.calculatePrice(basePrice, voucher, unit_price)` - Calculate discount amount
- `utils.calculateDiscount(basePrice, voucher, unit_price)` - Calculate new price after discount

#### Example

```Python
from voucherify import utils

# Example voucher object.
# You can use `get` method from API to get correct voucher dictionary shape.
voucher = {
  "code": "Testing7fjWdr",
  "discount": {
    "type": "AMOUNT",
    "amount_off": 1080  # 10.80
  },
  "category": "API Test",
  "start_date": "2016-01-01T00:00:00Z",
  "expiration_date": "2016-12-31T23:59:59Z"
}

# Price of one item
unit_price = 83.45

# Number of items
items_count = 13

# Total price
base_price = unit_price * items_count

# Calculate discount amount
discount = utils.calculate_discount(base_price, voucher, unit_price)

# Calculate new price after discount
new_price = utils.calculate_price(base_price, voucher, unit_price)
```

### Changelog

- **2016-05-31** - `0.1.0` - First version:
  - Authentication
  - Voucher informations: *get*, *usage*
  - Voucher operations: *use*