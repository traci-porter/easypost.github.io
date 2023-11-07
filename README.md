# Shipment Rate Optimizer API
The Shipment Rate Optimizer API enables you to create labels by automatically choosing the cheapest option, completing a purchase, and returning a label object.

## Introduction
This guide walks you through using the Shipment Rate Optimizer API. The example describes how to [purchase a shipping label](https://traci-porter.github.io/easypost.github.io/#step-1-purchase-shipping-label), [create a parcel](https://traci-porter.github.io/easypost.github.io/#step-2-create-a-parcel), [create a shipment](https://traci-porter.github.io/easypost.github.io/#step-3-create-a-shipment-and-get-rates), [get rates](https://traci-porter.github.io/easypost.github.io/#step-3-create-a-shipment-and-get-rates), and [buy and generate a shipping label](https://traci-porter.github.io/easypost.github.io/#step-4-buy-and-generate-a-shipping-label).

## Before you start
Before you can make requests, ensure you have completed the following:

* Create an [EasyPost account](https://www.easypost.com/signup) to obtain a Test Key and a Production Key. Switch to the Production Key when buying real postage.
* Enter your carrier-specific credentials on the [Carrier Account Dashboard](https://www.easypost.com/account/settings?tab=carriers).
* [Download an EasyPost Client Library](https://www.easypost.com/docs/libraries) in Python, Ruby, PHP, Java, Node.js, or C#(.NET).
* Become familiar with [EasyPost Objects](https://www.easypost.com/docs/api#objects) to help understand code samples and optimize your application.

> **_NOTES:_** 
> * Additional [client libraries](https://www.easypost.com/docs/libraries) (e.g., Perl, iOS) are available. 
> * You can interact directly with the API with cURL.

## Step 1: Purchase shipping label
In order to purchase a shipping label, you must have an item to ship. In this example, we will ship an EasyPost T-shirt from EasyPost headquarters to a customer. To start, create the To and From Addresses for the package. Once you create an Address object for both the To and From Addresses, the API returns a unique ID for each address. You can reuse the ID for other packages which is helpful when sending many packages from a location.

For more information on the Address object, see [Addresses](https://www.easypost.com/docs/api/curl#addresses).

> **_NOTE:_**
> For every object type created on EasyPost, you will receive a reusable unique ID.
 
### Example: Create a from address
The following example shows how to create a from address.

#### Request
```
EASYPOST_API_KEY  
Content type: application/json  
POST: HTTP://api.easypost.com/v2/addresses  
{
    "address": {
        "street1": "417 MONTGOMERY ST",
        "street2": "FLOOR 5",
        "city": "SAN FRANCISCO",
        "state": "CA",
        "zip": "94104",
        "country": "US",
        "company": "EasyPost",
        "phone": "415-123-4567"
    }
}
```
#### Response
```
{
    "id": "adr_f3075f0079dc11ee95d3ac1f6bc53342",
    "object": "Address",
    "created_at": "2023-11-03T00:07:17+00:00",
    "updated_at": "2023-11-03T00:07:17+00:00",
    "name": null,
    "company": "EasyPost",
    "street1": "417 MONTGOMERY ST",
    "street2": "FLOOR 5",
    "city": "SAN FRANCISCO",
    "state": "CA",
    "zip": "94104",
    "country": "US",
    "phone": "4151234567",
    "email": null,
    "mode": "test",
    "carrier_facility": null,
    "residential": null,
    "federal_tax_id": null,
    "state_tax_id": null,
    "verifications": {}
}
```
### Example: Create a to address 
The following example shows how to create a to address.

#### Request

```
EASYPOST_API_KEY  
Content type: application/json  
POST: HTTP://api.easypost.com/v2/addresses  
{
"address": {
"company": "EasyPost",
"street1": "417 Montgomery Street",
"street2": "5th Floor",
"city": "San Francisco",
"state": "CA",
"zip": "94104",
"phone": "415-528-7555"
}
}
```
#### Response
```
{
    "id": "adr_9f06d49b79dd11eebf81ac1f6bc53342",
    "object": "Address",
    "created_at": "2023-11-03T00:12:05+00:00",
    "updated_at": "2023-11-03T00:12:05+00:00",
    "name": null,
    "company": "EasyPost",
    "street1": "417 Montgomery Street",
    "street2": "5th Floor",
    "city": "San Francisco",
    "state": "CA",
    "zip": "94104",
    "country": "US",
    "phone": "4155287555",
    "email": null,
    "mode": "test",
    "carrier_facility": null,
    "residential": null,
    "federal_tax_id": null,
    "state_tax_id": null,
    "verifications": {}
}
```

## Step 2: Create a Parcel
A parcel object contains information about the weight and dimensions (length, width, and height) of an object. Once you create a parcel, the API returns a unique parcel ID in the response object under the ID Key. You can reference this ID in future calls. In this example, the parcel is a T-shirt, which means the package size is small and light.

For more information on the Parcel object, see [Parcels](https://www.easypost.com/docs/api/curl#parcels).

> **_NOTE:_**
> Enter weight in ounces and dimensions in inches.

### Example: Create a parcel 
The following example shows how to create a parcel.

#### Request
 ```
EASYPOST_API_KEY  
Content type: application/json  
POST: HTTP://api.easypost.com/v2/parcels  
{
    "parcel": {
        "length": "20.2",
        "width": "10.9",
        "height": "5",
        "weight": "2"
    }
}
```
#### Response
```
{
    "id": "prcl_e89f00a6b8a44dfdb06843e028b4fcb8",
    "object": "Parcel",
    "created_at": "2023-11-03T00:14:58Z",
    "updated_at": "2023-11-03T00:14:58Z",
    "length": 20.2,
    "width": 10.9,
    "height": 5.0,
    "predefined_package": null,
    "weight": 2.0,
    "mode": "test"
}
```


## Step 3: Create a Shipment and Get Rates
After creating To and From Addresses and a parcel, you can create a shipment. The API returns shipment rates for all the carriers you enable. The rates are for shipping the parcel between the specified To and From Addresses. In this example, the request body includes the to and from addresses for sending the T-shirt and the parcel information. A successful request returns a rate ID.

An individual rate contains information about the carrier, the service level (e.g., one day, two days, Ground, etc), cost, and the estimated number of days for delivery (when available).

For more information, see [Shipments](https://www.easypost.com/docs/api/curl#shipments) and [Rates](https://www.easypost.com/docs/api/curl#rates).

> **_NOTE:_**
> The simple example below uses USPS as the carrier. Feel free to add additional carriers. See [Adding Carrier Accounts](https://support.easypost.com/hc/en-us/articles/360037347912-Adding-Carrier-Accounts) for details. 

### Example: Create a shipment and get rates
The following example shows how to create a shipment and get rates.

#### Request
 ```
 EASYPOST_API_KEY  
 Content type: application/json  
 POST: https://api.easypost.com/v2/shipments  
{
    "shipment": {
        "to_address": {
            "id": "adr_9f06d49b79dd11eebf81ac1f6bc53342",
            "name": "Dr. Steve Brule",
            "street1": "179 N Harbor Dr",
            "city": "Redondo Beach",
            "state": "CA",
            "zip": "90277",
            "country": "US",
            "phone": "8573875756",
            "email": "dr_steve_brule@gmail.com"
        },
        "from_address": {
            "id": "adr_f3075f0079dc11ee95d3ac1f6bc53342",
            "name": "EasyPost",
            "street1": "417 Montgomery Street",
            "street2": "5th Floor",
            "city": "San Francisco",
            "state": "CA",
            "zip": "94104",
            "country": "US",
            "phone": "4153334445",
            "email": "support@easypost.com"
        },
        "parcel": {
            "id": "prcl_e89f00a6b8a44dfdb06843e028b4fcb8",
            "length": "20.2",
            "width": "10.9",
            "height": "5",
            "weight": "2"
        }
    }
}
```
#### Response

```
{
    "created_at": "2023-11-03T00:22:03Z",
    "is_return": false,
    "messages": [
        {
            "carrier": "UPSDAP",
            "carrier_account_id": "ca_db267ba68fd04ee5a11e0adc92f9db78",
            "type": "rate_error",
            "message": "Your UPS account is being set up. Please try again in a few minutes."
        }
    ],
    "mode": "test",
    "options": {
        "currency": "USD",
        "payment": {
            "type": "SENDER"
        },
        "date_advance": 0
    },
    "reference": null,
    "status": "unknown",
    "tracking_code": null,
    "updated_at": "2023-11-03T00:22:03Z",
    "batch_id": null,
    "batch_status": null,
    "batch_message": null,
    "customs_info": null,
    "from_address": {
        "id": "adr_f3075f0079dc11ee95d3ac1f6bc53342",
        "object": "Address",
        "created_at": "2023-11-03T00:07:17+00:00",
        "updated_at": "2023-11-03T00:07:17+00:00",
        "name": null,
        "company": "EasyPost",
        "street1": "417 MONTGOMERY ST",
        "street2": "FLOOR 5",
        "city": "SAN FRANCISCO",
        "state": "CA",
        "zip": "94104",
        "country": "US",
        "phone": "4151234567",
        "email": null,
        "mode": "test",
        "carrier_facility": null,
        "residential": null,
        "federal_tax_id": null,
        "state_tax_id": null,
        "verifications": {}
    },
    "insurance": null,
    "order_id": null,
    "parcel": {
        "id": "prcl_e89f00a6b8a44dfdb06843e028b4fcb8",
        "object": "Parcel",
        "created_at": "2023-11-03T00:14:58Z",
        "updated_at": "2023-11-03T00:14:58Z",
        "length": 20.2,
        "width": 10.9,
        "height": 5.0,
        "predefined_package": null,
        "weight": 5.0,
        "mode": "test"
    },
    "postage_label": null,
    "rates": [
        {
            "id": "rate_c59553bff89f42039f3c9b40083df177",
            "object": "Rate",
            "created_at": "2023-11-03T00:22:03Z",
            "updated_at": "2023-11-03T00:22:03Z",
            "mode": "test",
            "service": "Priority",
            "carrier": "USPS",
            "rate": "6.73",
            "currency": "USD",
            "retail_rate": "9.35",
            "retail_currency": "USD",
            "list_rate": "7.64",
            "list_currency": "USD",
            "billing_type": "easypost",
            "delivery_days": 1,
            "delivery_date": null,
            "delivery_date_guaranteed": false,
            "est_delivery_days": 1,
            "shipment_id": "shp_9f9439c33c7b48429aa12cb5742f0797",
            "carrier_account_id": "ca_39fec5d59faf493fac56c8010fc9c2d3"
        },
        {
            "id": "rate_efc993289cce40fa886dfde6298cc658",
            "object": "Rate",
            "created_at": "2023-11-03T00:22:03Z",
            "updated_at": "2023-11-03T00:22:03Z",
            "mode": "test",
            "service": "GroundAdvantage",
            "carrier": "USPS",
            "rate": "3.99",
            "currency": "USD",
            "retail_rate": "5.40",
            "retail_currency": "USD",
            "list_rate": "3.99",
            "list_currency": "USD",
            "billing_type": "easypost",
            "delivery_days": 2,
            "delivery_date": null,
            "delivery_date_guaranteed": false,
            "est_delivery_days": 2,
            "shipment_id": "shp_9f9439c33c7b48429aa12cb5742f0797",
            "carrier_account_id": "ca_39fec5d59faf493fac56c8010fc9c2d3"
        },
        {
            "id": "rate_1c27e5e93c60440db38b7b4e6d5fa33a",
            "object": "Rate",
            "created_at": "2023-11-03T00:22:03Z",
            "updated_at": "2023-11-03T00:22:03Z",
            "mode": "test",
            "service": "First",
            "carrier": "USPS",
            "rate": "3.99",
            "currency": "USD",
            "retail_rate": "5.40",
            "retail_currency": "USD",
            "list_rate": "3.99",
            "list_currency": "USD",
            "billing_type": "easypost",
            "delivery_days": 2,
            "delivery_date": null,
            "delivery_date_guaranteed": false,
            "est_delivery_days": 2,
            "shipment_id": "shp_9f9439c33c7b48429aa12cb5742f0797",
            "carrier_account_id": "ca_39fec5d59faf493fac56c8010fc9c2d3"
        },
        {
            "id": "rate_89d8849599494d3288ecb1559eb5321c",
            "object": "Rate",
            "created_at": "2023-11-03T00:22:03Z",
            "updated_at": "2023-11-03T00:22:03Z",
            "mode": "test",
            "service": "ParcelSelect",
            "carrier": "USPS",
            "rate": "3.99",
            "currency": "USD",
            "retail_rate": "5.40",
            "retail_currency": "USD",
            "list_rate": "3.99",
            "list_currency": "USD",
            "billing_type": "easypost",
            "delivery_days": 2,
            "delivery_date": null,
            "delivery_date_guaranteed": false,
            "est_delivery_days": 2,
            "shipment_id": "shp_9f9439c33c7b48429aa12cb5742f0797",
            "carrier_account_id": "ca_39fec5d59faf493fac56c8010fc9c2d3"
        },
        {
            "id": "rate_8ee803496372403fab4834b58ab6045b",
            "object": "Rate",
            "created_at": "2023-11-03T00:22:03Z",
            "updated_at": "2023-11-03T00:22:03Z",
            "mode": "test",
            "service": "Express",
            "carrier": "USPS",
            "rate": "24.90",
            "currency": "USD",
            "retail_rate": "28.75",
            "retail_currency": "USD",
            "list_rate": "24.90",
            "list_currency": "USD",
            "billing_type": "easypost",
            "delivery_days": null,
            "delivery_date": null,
            "delivery_date_guaranteed": false,
            "est_delivery_days": null,
            "shipment_id": "shp_9f9439c33c7b48429aa12cb5742f0797",
            "carrier_account_id": "ca_39fec5d59faf493fac56c8010fc9c2d3"
        }
    ],
    "refund_status": null,
    "scan_form": null,
    "selected_rate": null,
    "tracker": null,
    "to_address": {
        "id": "adr_9f06d49b79dd11eebf81ac1f6bc53342",
        "object": "Address",
        "created_at": "2023-11-03T00:12:05+00:00",
        "updated_at": "2023-11-03T00:12:05+00:00",
        "name": null,
        "company": "EasyPost",
        "street1": "417 Montgomery Street",
        "street2": "5th Floor",
        "city": "San Francisco",
        "state": "CA",
        "zip": "94104",
        "country": "US",
        "phone": "4155287555",
        "email": null,
        "mode": "test",
        "carrier_facility": null,
        "residential": null,
        "federal_tax_id": null,
        "state_tax_id": null,
        "verifications": {}
    },
    "usps_zone": 1,
    "return_address": {
        "id": "adr_f3075f0079dc11ee95d3ac1f6bc53342",
        "object": "Address",
        "created_at": "2023-11-03T00:07:17+00:00",
        "updated_at": "2023-11-03T00:07:17+00:00",
        "name": null,
        "company": "EasyPost",
        "street1": "417 MONTGOMERY ST",
        "street2": "FLOOR 5",
        "city": "SAN FRANCISCO",
        "state": "CA",
        "zip": "94104",
        "country": "US",
        "phone": "4151234567",
        "email": null,
        "mode": "test",
        "carrier_facility": null,
        "residential": null,
        "federal_tax_id": null,
        "state_tax_id": null,
        "verifications": {}
    },
    "buyer_address": {
        "id": "adr_9f06d49b79dd11eebf81ac1f6bc53342",
        "object": "Address",
        "created_at": "2023-11-03T00:12:05+00:00",
        "updated_at": "2023-11-03T00:12:05+00:00",
        "name": null,
        "company": "EasyPost",
        "street1": "417 Montgomery Street",
        "street2": "5th Floor",
        "city": "San Francisco",
        "state": "CA",
        "zip": "94104",
        "country": "US",
        "phone": "4155287555",
        "email": null,
        "mode": "test",
        "carrier_facility": null,
        "residential": null,
        "federal_tax_id": null,
        "state_tax_id": null,
        "verifications": {}
    },
    "forms": [],
    "fees": [],
    "id": "shp_9f9439c33c7b48429aa12cb5742f0797",
    "object": "Shipment"
}
```

## Step 4: Buy and generate a shipping label
This last step walks you through buying and generating the shipping label for your parcel. There are two options for generating the shipping label.
 1. **Client Libraries** Use the convenience functions built into the [client libraries](https://www.easypost.com/docs/libraries) to retrieve the rate ID with the lowest rate. 

 2. **Shipment Rate Optimizer API** This API allows you to buy the lowest shipping rate for your shipment. Enter the shipment ID in the URL. Then, enter the rate ID with the lowest rate obtained in the [Create shipments](#step-3-create-a-shipment-and-get-rates) response body and POST it to the shipment resource. After successfully submitting the request, the response contains a URL (e.g., `label_url`) to the image of your label. Download and print the label. In addition, the response includes a tracking ID for your package. You can store this information and pass it on to your customers.

For more information on the Shipment Rate Optimizer API, see the [SmartRate Guide](https://www.easypost.com/smartrate-guide).

> **_NOTE:_**
> Labels are returned in .PNG format, but you can request other formats.

### Example: Create a shipping label
The following example shows how to create a shipping label.

#### Request
```
 EASYPOST_API_KEY  
 Content type: application/json  
 POST: https://api.easypost.com/v2/shipments/:id/optimize_buy
{
    "rate": {
        "id": "rate_1c27e5e93c60440db38b7b4e6d5fa33a"
    }
}
```

#### Response

```
{
    "created_at": "2023-11-03T00:22:03Z",
    "is_return": false,
    "messages": [
        {
            "carrier": "UPSDAP",
            "carrier_account_id": "ca_db267ba68fd04ee5a11e0adc92f9db78",
            "type": "rate_error",
            "message": "Your UPS account is being set up. Please try again in a few minutes."
        }
    ],
    "mode": "test",
    "options": {
        "currency": "USD",
        "payment": {
            "type": "SENDER"
        },
        "date_advance": 0
    },
    "reference": null,
    "status": "unknown",
    "tracking_code": "9400100105440274280133",
    "updated_at": "2023-11-03T01:39:16Z",
    "batch_id": null,
    "batch_status": null,
    "batch_message": null,
    "customs_info": null,
    "from_address": {
        "id": "adr_f3075f0079dc11ee95d3ac1f6bc53342",
        "object": "Address",
        "created_at": "2023-11-03T00:07:17+00:00",
        "updated_at": "2023-11-03T00:07:17+00:00",
        "name": null,
        "company": "EasyPost",
        "street1": "417 MONTGOMERY ST",
        "street2": "FLOOR 5",
        "city": "SAN FRANCISCO",
        "state": "CA",
        "zip": "94104",
        "country": "US",
        "phone": "4151234567",
        "email": null,
        "mode": "test",
        "carrier_facility": null,
        "residential": null,
        "federal_tax_id": null,
        "state_tax_id": null,
        "verifications": {}
    },
    "insurance": "50.00",
    "order_id": null,
    "parcel": {
        "id": "prcl_e89f00a6b8a44dfdb06843e028b4fcb8",
        "object": "Parcel",
        "created_at": "2023-11-03T00:14:58Z",
        "updated_at": "2023-11-03T00:14:58Z",
        "length": 20.2,
        "width": 10.9,
        "height": 5.0,
        "predefined_package": null,
        "weight": 2.0,
        "mode": "test"
    },
    "postage_label": {
        "object": "PostageLabel",
        "id": "pl_64e869831cd64880a764df86d399fee1",
        "created_at": "2023-11-03T01:39:16Z",
        "updated_at": "2023-11-03T01:39:16Z",
        "date_advance": 0,
        "integrated_form": "none",
        "label_date": "2023-11-03T01:39:16Z",
        "label_resolution": 300,
        "label_size": "4x6",
        "label_type": "default",
        "label_file_type": "image/png",
        "label_url": "https://easypost-files.s3.us-west-2.amazonaws.com/files/postage_label/20231103/e841b19441a04b4d608efa111f331ca216.png",
        "label_pdf_url": null,
        "label_zpl_url": null,
        "label_epl2_url": null,
        "label_file": null
    },
    "rates": [
        {
            "id": "rate_c59553bff89f42039f3c9b40083df177",
            "object": "Rate",
            "created_at": "2023-11-03T00:22:03Z",
            "updated_at": "2023-11-03T00:22:03Z",
            "mode": "test",
            "service": "Priority",
            "carrier": "USPS",
            "rate": "6.73",
            "currency": "USD",
            "retail_rate": "9.35",
            "retail_currency": "USD",
            "list_rate": "7.64",
            "list_currency": "USD",
            "billing_type": "easypost",
            "delivery_days": 1,
            "delivery_date": null,
            "delivery_date_guaranteed": false,
            "est_delivery_days": 1,
            "shipment_id": "shp_9f9439c33c7b48429aa12cb5742f0797",
            "carrier_account_id": "ca_39fec5d59faf493fac56c8010fc9c2d3"
        },
        {
            "id": "rate_efc993289cce40fa886dfde6298cc658",
            "object": "Rate",
            "created_at": "2023-11-03T00:22:03Z",
            "updated_at": "2023-11-03T00:22:03Z",
            "mode": "test",
            "service": "GroundAdvantage",
            "carrier": "USPS",
            "rate": "3.99",
            "currency": "USD",
            "retail_rate": "5.40",
            "retail_currency": "USD",
            "list_rate": "3.99",
            "list_currency": "USD",
            "billing_type": "easypost",
            "delivery_days": 2,
            "delivery_date": null,
            "delivery_date_guaranteed": false,
            "est_delivery_days": 2,
            "shipment_id": "shp_9f9439c33c7b48429aa12cb5742f0797",
            "carrier_account_id": "ca_39fec5d59faf493fac56c8010fc9c2d3"
        },
        {
            "id": "rate_1c27e5e93c60440db38b7b4e6d5fa33a",
            "object": "Rate",
            "created_at": "2023-11-03T00:22:03Z",
            "updated_at": "2023-11-03T00:22:03Z",
            "mode": "test",
            "service": "First",
            "carrier": "USPS",
            "rate": "3.99",
            "currency": "USD",
            "retail_rate": "5.40",
            "retail_currency": "USD",
            "list_rate": "3.99",
            "list_currency": "USD",
            "billing_type": "easypost",
            "delivery_days": 2,
            "delivery_date": null,
            "delivery_date_guaranteed": false,
            "est_delivery_days": 2,
            "shipment_id": "shp_9f9439c33c7b48429aa12cb5742f0797",
            "carrier_account_id": "ca_39fec5d59faf493fac56c8010fc9c2d3"
        },
        {
            "id": "rate_89d8849599494d3288ecb1559eb5321c",
            "object": "Rate",
            "created_at": "2023-11-03T00:22:03Z",
            "updated_at": "2023-11-03T00:22:03Z",
            "mode": "test",
            "service": "ParcelSelect",
            "carrier": "USPS",
            "rate": "3.99",
            "currency": "USD",
            "retail_rate": "5.40",
            "retail_currency": "USD",
            "list_rate": "3.99",
            "list_currency": "USD",
            "billing_type": "easypost",
            "delivery_days": 2,
            "delivery_date": null,
            "delivery_date_guaranteed": false,
            "est_delivery_days": 2,
            "shipment_id": "shp_9f9439c33c7b48429aa12cb5742f0797",
            "carrier_account_id": "ca_39fec5d59faf493fac56c8010fc9c2d3"
        },
        {
            "id": "rate_8ee803496372403fab4834b58ab6045b",
            "object": "Rate",
            "created_at": "2023-11-03T00:22:03Z",
            "updated_at": "2023-11-03T00:22:03Z",
            "mode": "test",
            "service": "Express",
            "carrier": "USPS",
            "rate": "24.90",
            "currency": "USD",
            "retail_rate": "28.75",
            "retail_currency": "USD",
            "list_rate": "24.90",
            "list_currency": "USD",
            "billing_type": "easypost",
            "delivery_days": null,
            "delivery_date": null,
            "delivery_date_guaranteed": false,
            "est_delivery_days": null,
            "shipment_id": "shp_9f9439c33c7b48429aa12cb5742f0797",
            "carrier_account_id": "ca_39fec5d59faf493fac56c8010fc9c2d3"
        }
    ],
    "refund_status": null,
    "scan_form": null,
    "selected_rate": {
        "id": "rate_89d8849599494d3288ecb1559eb5321c",
        "object": "Rate",
        "created_at": "2023-11-03T01:39:16Z",
        "updated_at": "2023-11-03T01:39:16Z",
        "mode": "test",
        "service": "ParcelSelect",
        "carrier": "USPS",
        "rate": "3.99",
        "currency": "USD",
        "retail_rate": "5.40",
        "retail_currency": "USD",
        "list_rate": "3.99",
        "list_currency": "USD",
        "billing_type": "easypost",
        "delivery_days": 2,
        "delivery_date": null,
        "delivery_date_guaranteed": false,
        "est_delivery_days": 2,
        "shipment_id": "shp_9f9439c33c7b48429aa12cb5742f0797",
        "carrier_account_id": "ca_39fec5d59faf493fac56c8010fc9c2d3"
    },
    "tracker": {
        "id": "trk_b931ab7c2d034d4497a92782e6032303",
        "object": "Tracker",
        "mode": "test",
        "tracking_code": "9400100105440274280133",
        "status": "unknown",
        "status_detail": "unknown",
        "created_at": "2023-11-03T01:39:16Z",
        "updated_at": "2023-11-03T01:39:16Z",
        "signed_by": null,
        "weight": null,
        "est_delivery_date": null,
        "shipment_id": "shp_9f9439c33c7b48429aa12cb5742f0797",
        "carrier": "USPS",
        "tracking_details": [],
        "fees": [],
        "carrier_detail": null,
        "public_url": "https://track.easypost.com/djE6dHJrX2I5MzFhYjdjMmQwMzRkNDQ5N2E5Mjc4MmU2MDMyMzAz"
    },
    "to_address": {
        "id": "adr_9f06d49b79dd11eebf81ac1f6bc53342",
        "object": "Address",
        "created_at": "2023-11-03T00:12:05+00:00",
        "updated_at": "2023-11-03T01:39:15+00:00",
        "name": null,
        "company": "EASYPOST",
        "street1": "417 MONTGOMERY ST # 5",
        "street2": "",
        "city": "SAN FRANCISCO",
        "state": "CA",
        "zip": "94104-1129",
        "country": "US",
        "phone": "4155287555",
        "email": null,
        "mode": "test",
        "carrier_facility": null,
        "residential": false,
        "federal_tax_id": null,
        "state_tax_id": null,
        "verifications": {
            "zip4": {
                "success": true,
                "errors": [],
                "details": null
            },
            "delivery": {
                "success": true,
                "errors": [],
                "details": {
                    "latitude": 37.79342,
                    "longitude": -122.40288,
                    "time_zone": "America/Los_Angeles"
                }
            }
        }
    },
    "usps_zone": 1,
    "return_address": {
        "id": "adr_f3075f0079dc11ee95d3ac1f6bc53342",
        "object": "Address",
        "created_at": "2023-11-03T00:07:17+00:00",
        "updated_at": "2023-11-03T00:07:17+00:00",
        "name": null,
        "company": "EasyPost",
        "street1": "417 MONTGOMERY ST",
        "street2": "FLOOR 5",
        "city": "SAN FRANCISCO",
        "state": "CA",
        "zip": "94104",
        "country": "US",
        "phone": "4151234567",
        "email": null,
        "mode": "test",
        "carrier_facility": null,
        "residential": null,
        "federal_tax_id": null,
        "state_tax_id": null,
        "verifications": {}
    },
    "buyer_address": {
        "id": "adr_9f06d49b79dd11eebf81ac1f6bc53342",
        "object": "Address",
        "created_at": "2023-11-03T00:12:05+00:00",
        "updated_at": "2023-11-03T01:39:15+00:00",
        "name": null,
        "company": "EASYPOST",
        "street1": "417 MONTGOMERY ST # 5",
        "street2": "",
        "city": "SAN FRANCISCO",
        "state": "CA",
        "zip": "94104-1129",
        "country": "US",
        "phone": "4155287555",
        "email": null,
        "mode": "test",
        "carrier_facility": null,
        "residential": false,
        "federal_tax_id": null,
        "state_tax_id": null,
        "verifications": {
            "zip4": {
                "success": true,
                "errors": [],
                "details": null
            },
            "delivery": {
                "success": true,
                "errors": [],
                "details": {
                    "latitude": 37.79342,
                    "longitude": -122.40288,
                    "time_zone": "America/Los_Angeles"
                }
            }
        }
    },
    "forms": [],
    "fees": [
        {
            "object": "Fee",
            "type": "LabelFee",
            "amount": "0.00000",
            "charged": true,
            "refunded": false
        },
        {
            "object": "Fee",
            "type": "PostageFee",
            "amount": "3.99000",
            "charged": true,
            "refunded": false
        },
        {
            "object": "Fee",
            "type": "InsuranceFee",
            "amount": "0.25000",
            "charged": true,
            "refunded": false
        }
    ],
    "id": "shp_9f9439c33c7b48429aa12cb5742f0797",
    "object": "Shipment"
}
```
## FAQs
**How can I generate the shipping label?**

The response body for the `v2/shipments/:id/optimize_buy` endpoint contains a `label_url` field. You can go to the URL to download and print your shipping label.

**How can I buy the lowest rate?**

Use the functions built into the client libraries to buy the lowest rate or use the rate ID of the cheapest rate in `v2/shipments/:id/optimize_buy` endpoint.


## References
* [Address](https://www.easypost.com/docs/api/curl#addresses)
* [Parcels](https://www.easypost.com/docs/api/curl#parcels)
* [Shipments](https://www.easypost.com/docs/api/curl#shipments)  
* [Rates](https://www.easypost.com/docs/api/curl#rates)
* [Client libraries](https://www.easypost.com/docs/libraries)
* [SmartRate Guide](https://www.easypost.com/smartrate-guide)
