---
title: ShopTurn API Reference

toc_footers:
  - <a target="_blank" href='https://mail.google.com/mail/u/0/?view=cm&ui=2&tf=0&fs=1&to=jake%40shopturn.com'>Request API Key</a>

search: true
---

# General Info

## Introduction

The ShopTurn API is a RESTful web service for developers to programmatically interact with ShopTurn’s real-time delivery and return logistics platform.

All data exchanged between clients and the API is JSON over HTTPS. Note that we will only accept `application/json` as content type and that the request body, if relevant, must be JSON-encoded.

The base URL for the ShopTurn API is `https://shopturn.com/api/v1`.

All sample requests in this documentation can be viewed in multiple programming languages for your convenience.

All body parameters are required unless noted otherwise.
Task
If you have any questions, feedback, or a bug you'd like to report, drop us a line at [jake@shopturn.com](mailto://jake@shopturn.com).

## API Standards

The ShopTurn API has been architected with standards at its core.

- numbers will never be strings
- `null` is always returned for optional properties that have not been set
- geographic coordinates are always returned as per the [GeoJSON Standard](http://geojson.org/)
- geographic coordinates are always requested via a latitude and a longitude property 
- timestamps are always formatted as [Unix timestamps](https://en.wikipedia.org/wiki/Unix_time)
- most endpoints return a body, but there are some exceptions. For example deleting an object would repond with a `200` status code but without a body.
- the ShopTurn API uses URL-safe resource IDs
- phone number are always returned and requested in the format +19991234567, following [E.164](https://en.wikipedia.org/wiki/E.164) standards
- to remove an optional property from an entity, set the value to `null`.
- include an array of multiple entities to add them in bulk.


## ShopTurn Entities

TBA

Include an array of multiple entities to add them in bulk.

## Rate Limiting

There is a 25 requests per second limitation set accross all of your organizations API keys. If you exceed 25 requests per second, you will receive a 429 error. If you require a higher request rate, please don't hesitate to contact us.

## Errors & Status Codes

The API responds with a non-200 status code and an `Error` object when something has gone awry. An `Error` object is always provided so your application has visibility into what has gone wrong. 

An `Error` object always has the following properties:

Name | Type | Info
:--- | :--- | :---
code | *integer* | The status code of the response
type | *string* | The error type 
message | *string* | A message for debugging purposes

`Error` types:

Code | Error Type | Message
:--- | :--- | :---
400 | BadRequest | The request is invalid, missing information, or out of context. The status text will contain more insight.
401 | Unauthorized | The authorization header (your API key) is either missing, invalid, or expired.
403 | Forbidden | The request is kosher, but has been refused. 
404 | NotFound | The requested resource could not be found.
429 | ClientError | The request was refused per our rate limiting policy.
50X | InternalError | Generic error. See the message for more information.

## Status

To check the status of the API head over to our [status page](www.shopturn.com) (under development). We will also notify you via email when the status changes. 

# Authentication

Requests must be authenticated by setting the `Authorization` header to one of your organization’s API keys.

Admins can request API keys by emailing [jake@shopturn.com](mailto://jake@shopturn.com) while the dashboard is under development. You can request as many API keys as required to suit your different applications and environments. 

If you requite a separate organization for testing purposes, please contact us.

<aside class="notice">
You must include <code>Authorization: API Key</code> as a header in every request.
</aside>

## Test API Key

> `Example:`

```shell
curl  -X GET  "api.shopturn.com/v1/auth/test" \
      -H  "Authorization: YOUR_API_KEY"
```

> `Response: 200 (application/json)`:

```json
"Hello ORG_NAME! Your request is valid."
```

`GET https://api.shopturn.com/v1/auth/test`

Check that you are formatting your request correctly and that your API key is valid.


# Organizations

Organizations are the top-most entity within ShopTurn. [Administrators](/#reference/Administrators), [Workers](/#reference/workers), [Stores](/#reference/stores) and [Tasks](/#reference/tasks) all belong to an organization. 

## Get Organization

> `Example:`

```shell
curl  -X GET  "api.shopturn.com/v1/organization" \
      -H  "Authorization: YOUR_API_KEY"
```

> `Response: 200 (application/json)`:

```json
{
    "id": "62a848a8ead94f879b4c3a0e533beca7",
    "name": "ST Retailer",
    "time_created": 1509310476,
    "email": "admin@retailer.com",
    "image": "img.google.com/your_logo_url",
    "time_last_modified": 1509310701,
    "country": "US",
    "timezone": "America/Los_Angeles"
}
```

Retrieve your organization's information.

### HTTP Request:

`GET https://api.shopturn.com/v1/organization`

## Update Organization

> `Example:`

```shell
curl  -X PUT  "api.shopturn.com/v1/organization" \
      -H  "Authorization: YOUR_API_KEY"
      -d  '{
              "name": "New Name", 
              "email": "admin2@retailer.com", 
              "timezone": "America/New_York"
           }'
```

> `Response: 200 (application/json)`:

```json
{
    "id": "62a848a8ead94f879b4c3a0e533beca7",
    "name": "New Name",
    "time_created": 1509310476,
    "email": "admin2@retailer.com",
    "image": "img.google.com/your_logo_url",
    "time_last_modified": 1509310701,
    "country": "US",
    "timezone": "America/New_York"
}
```

Update the attributes of your organization.

### HTTP Request:

`PUT https://api.shopturn.com/v1/organization`

### Body Parameters:

Name | Type | Info
:--- | :--- | :---
name | *string* | **optional** Matches entities with names containing or equal to this value
email | *string* | **optional** Matches entities with emails containing or equal to this value
image | *string* | **optional** URL to new image
timezone | *string* | **optional** timezone the organization operates in


# Admins

Administrators are users who can access the ShopTurn dashboard. An organization has one administrator of type `super` and zero or more of type `standard` (also known as dispatchers). Dispatchers can manage workers, tasks, and more via the dashboard.

## Create an Admin

> `Example:`

```shell
curl  -X POST  "api.shopturn.com/v1/admin" \
      -H  "Authorization: YOUR_API_KEY"
      -d  '{ 
              "name": "Jane Doe", 
              "email": "jane@retailer.com", 
              "phone": "(123) 456-7890"
           }'
```

> `Response: 200 (application/json)`:

```json
{
    "id": "EBa52sfS733faK7Xs",
    "time_created": 1508438064,
    "time_last_modified": 1508438064,
    "organization": "3gUcz9mNDgNOL",
    "email": "jane@retailer.com",
    "is_super": "standard",
    "name": "Jane Doe",
    "is_active": false, 
    "phone": "+11234567890",
    "metadata": []
}
```

Create a new Admin. An email will be sent to the provided email address to further onboard them. 

### HTTP Request:

`POST https://api.shopturn.com/v1/admin`

### Body Parameters:

Name | Type | Info
:--- | :--- | :---
name | *string* | Full name of the admin 
email | *string* | Email of the admin
phone | *string* | Phone of the admin

## Get an Admin

> `Example:`

```shell
curl  -X GET  "api.shopturn.com/v1/admin/QSa52sfS733faK7Xs" \
      -H  "Authorization: YOUR_API_KEY"
```

> `Response: 200 (application/json)`:

```json
{
    "id": "QSa52sfS733faK7Xs",
    "time_created": 1508438064,
    "time_last_modified": 1508438064,
    "organization": "3gUcz9mNDgNOL",
    "email": "zach@retailer.com",
    "is_super": "standard",
    "name": "Zach Doe",
    "is_active": true,
    "metadata": []
    "phone": "+11234567890"
}
```

Get an admin with a specific `id`.

### HTTP Request:

`GET https://api.shopturn.com/v1/admin/<admin_id>`

## Update an Admin

> `Example:`

```shell
curl  -X PUT  "api.shopturn.com/v1/admin/EBa52sfS733faK7Xs" \
      -H  "Authorization: YOUR_API_KEY"
      -d '{ 
            "name": "Janette Doe", 
            "email": "janette@retailer.com", 
            "phone": "+10987654321" 
          }'
```

> `Response: 200 (application/json)`:

```json
{
    "id": "EBa52sfS733faK7Xs",
    "time_created": 1508438064,
    "time_last_modified": 1508438064,
    "organization": "3gUcz9mNDgNOL",
    "email": "Janette@retailer.com",
    "is_super": "standard",
    "name": "Janette Doe",
    "is_active": false, 
    "phone": "+10987654321",
    "metadata": []
}
```

Update an admin's information.

### HTTP Request:

`PUT https://api.shopturn.com/v1/admin/<admin_id>`

### Body Parameters:

Name | Type | Info
:--- | :--- | :---
name | *string* | **optional** New name of the admin
email | *string* | **optional** New email of the admin
phone | *string* | **optional** New phone of the admin


## Delete an Admin

> `Example:`

```shell
curl  -X DELETE   "api.shopturn.com/v1/admin/EBa52sfS733faK7Xs" \
      -H  "Authorization: YOUR_API_KEY"
```

> `Response: 200 (application/json)`:


Delete an Admin

### HTTP Request:

`DELETE https://api.shopturn.com/v1/admin`

## Search for Admins

> `Example:`

```shell
curl  -X POST  "api.shopturn.com/v1/admin/search" \
      -H  "Authorization: YOUR_API_KEY"
      -d '{"name":"%zach%"}'
```

> `Response: 200 (application/json)`:

```json
[
  {
      "id": "QSa52sfS733faK7Xs",
      "time_created": 1508438064,
      "time_last_modified": 1508438064,
      "organization": "3gUcz9mNDgNOL",
      "email": "zach@retailer.com",
      "is_super": "standard",
      "name": "Zach Doe",
      "is_active": true,
      "metadata": []
      "phone": "+11234567890",
  }, 
  {...},
]
```

Search for admins matching *all* of any combination of the following search parameters: 

### HTTP Request:

`POST https://api.shopturn.com/v1/admin/search`

### Body Parameters: 

Name | Type | Info
:--- | :--- | :---
name | *string* | **optional** Matches entities with names containing or equal to this value
phone | *string* | **optional** Matches entities with phone numbers containing or equal to this value
email | *string* | **optional** Matches entities with emails containing or equal to this value
order_by | *string* | **optional** name (default), email, phone, on_duty, time_created, or time_last_modified
descending | *boolean* | **optional** true to list workers in descending order. Defaults to `false`
limit | *number* | **optional** value to limit to results to
offset | *number* | **optional** value to offset results by

# Workers

Workers are the members of an organization who perform returns and deliveries. Workers have a vehicle with which tasks are completed.

## Create a Worker

> Example:

```shell
curl  -X POST  "api.shopturn.com/v1/worker" \
      -H  "Authorization: YOUR_API_KEY"
      -d  ' {
                "name": "Janette Doe",
                "email": "janette@retailer.com",
                "phone": "+10987654321"
                "vehicle_type": "CAR", 
                "vehicle_license_plate": "FYC-7992", // (optional for cyclist/foot) license plate or relevant visible identifier
                "vehicle_color": "#000000", // (only required for VAN/CAR)
                "vehicle_description": "Toyata Camry", (only required for VAN/CAR)
                "vehicle_year": 2014, (only required for VAN/CAR)
                "capacity_value": 10000.00, // max total value the worker can carry
                "capacity_lbs": 200, // max lbs the worker can carry
                "capacity_items": 10 // max items the worker can carrt
            } '
```

> `200 (application/json)`:

```json
{
    "id": "sFtimptJJdC2qmptJ",
    "time_created": 1508438064,
    "time_last_modified": 1508438064,
    "time_last_seen": null,
    "organization": "mDdfg3mNz9agUcNDrTsNOL",
    "name": "Janette Doe",
    "phone": "+10987654321",
    "current_task": null,
    "tasks": [], // tasks assigned to the driver for the current time block (10-12pm, 12-2pm, ... 8-10pm)
    "on_duty": false, 
    "delay_time": 0, // (minutes behind schedule)
    "metadata": [],
    "vehicle_id": "5F4RR11235JAx3wL8",
    "vehicle_type": "CAR",
    "vehicle_description": "Toyota Camry",
    "vehicle_license_plate": "FYC-7992",
    "vehicle_color": "#0000",
    "vehicle_year": 2014
    "latitude": null, // off duty
    "longitude": null, // off duty
}
```

Create a new worker. An SMS will be sent to the worker's phone with a temporary password and iOS/Android download details.

After logging in for the first time, the worker will be required to create a permanent password.

### HTTP Request:

`POST https://api.shopturn.com/v1/worker`

### Body Parameters:

Name | Type | Info
:--- | :--- | :---
name | *string* | Name of the worker 
email | *string* | Email of the worker
phone | *string* | Phone of the worker
vehicle_type | *string* | Type of vehicle (VAN, CAR, MOTORBIKE, or BIKE)
vehicle_license_plate | *string* | Email of the worker
vehicle_phone | *string* | Phone of the worker
vehicle_color | *string* | A web safe hex color (ex: #ffffff or #96DD93)
vehicle_description | *string* | The make and model of the vehicle
vehicle_year | *integer* | The year the vehicle was made
capacity_value | *number* | **optional** Maximum value of merchandise the worker can carry
capacity_lbs | *number* | **optional**  Maximum weight in lbs the worker can carry
capacity_items | *integer* | **optional**  Maximum number of items the worker can carry


## Get a Worker

> Example:

```shell
curl  -X GET  "api.shopturn.com/v1/worker/sFtimptJJdC2qmptJ" \
      -H  "Authorization: YOUR_API_KEY"
```

> `200 (application/json)`:

```json
{
    "id": "sFtimptJJdC2qmptJ",
    "time_created": 1508438064,
    "time_last_modified": 1508438064,
    "time_last_seen": null,
    "organization": "mDdfg3mNz9agUcNDrTsNOL",
    "name": "Janette Doe",
    "phone": "+10987654321",
    "current_task": null,
    "tasks": [tJJtJJdC2qdC2q, tJC2imptJdqmptJ], // tasks assigned to the driver for the current time block (10-12pm, 12-2pm, ... 8-10pm)
    "on_duty": true, 
    "delay_time": 0, // (minutes behind schedule)
    "metadata": [],
    "vehicle_id": "5F4RR11235JAx3wL8",
    "vehicle_type": "CAR",
    "vehicle_description": "Toyota Camry",
    "vehicle_license_plate": "FYC-7992",
    "vehicle_color": "#0000",
    "vehicle_year": 2014,
    "latitude": -102.123412,
    "longitude": 47.512341
}
```

Get a worker with a specific `id`.

### HTTP Request:

`GET https://api.shopturn.com/v1/worker/<worker_id>`

## Update a Worker

> Example:

```shell
curl  -X PUT  "api.shopturn.com/v1/worker/sFtimptJJdC2qmptJ" \
      -H  "Authorization: YOUR_API_KEY"
      -d '{"vehicle_description":"Tesla Model X"}'
```

> `200 (application/json)`:

```json
{
    "id": "sFtimptJJdC2qmptJ",
    "time_created": 1508438064,
    "time_last_modified": 1508438064,
    "time_last_seen": null,
    "organization": "mDdfg3mNz9agUcNDrTsNOL",
    "name": "Janette Doe",
    "phone": "+10987654321",
    "current_task": null,
    "tasks": [], // tasks assigned to the driver for the current time block (10-12pm, 12-2pm, ... 8-10pm)
    "on_duty": false, 
    "delay_time": 0, // (minutes behind schedule)
    "metadata": [],
    "vehicle_id": "5F4RR11235JAx3wL8",
    "vehicle_type": "CAR",
    "vehicle_description": "Tesla Model X",
    "vehicle_license_plate": "FYC-7992",
    "vehicle_color": "#0000",
    "vehicle_year": 2014,
    "latitude": null,
    "longitude": null
}
```

Update a worker's information. Vehicle and capacity attributes can be nulled. (null vehicle means on-foot worker)

### HTTP Request:

`PUT https://api.shopturn.com/v1/worker/<worker_id>`

### Body Parameters:

Name | Type | Info
:--- | :--- | :---
name | *string* |  **optional** Name of the worker 
email | *string* |  **optional** Email of the worker
phone | *string* |  **optional** Phone of the worker
vehicle_type | *string* |  **optional** Type of vehicle (VAN, CAR, MOTORBIKE, or BIKE)
vehicle_license_plate | *string* |  **optional** Email of the worker
vehicle_phone | *string* |  **optional** Phone of the worker
vehicle_color | *string* |  **optional** A web safe hex color (ex: #ffffff or #96DD93)
vehicle_description | *string* |  **optional** The make and model of the vehicle
vehicle_year | *integer* |  **optional** The year the vehicle was made
capacity_value | *number* | **optional** Maximum value of merchandise the worker can carry
capacity_lbs | *number* | **optional**  Maximum weight in lbs the worker can carry
capacity_items | *integer* | **optional**  Maximum number of items the worker can carry


## Delete a Worker

> Example:

```shell
curl  -X DELETE   "api.shopturn.com/v1/worker/sFtimptJJdC2qmptJ" \
      -H  "Authorization: YOUR_API_KEY"
```

> `200 (application/json)`:


Delete a Worker

### HTTP Request:

`DELETE https://api.shopturn.com/v1/worker/<worker_id>`

## Search for Workers

> `Example:`

```shell
curl  -X POST  "api.shopturn.com/v1/worker/search" \
      -H  "Authorization: YOUR_API_KEY"
      -d '{"name":"Janette%"}'
```

> `Response: 200 (application/json)`:

```json
[ 
    {
      "id": "sFtimptJJdC2qmptJ",
      "time_created": 1508438064,
      "time_last_modified": 1508438064,
      "time_last_seen": null,
      "organization": "mDdfg3mNz9agUcNDrTsNOL",
      "name": "Janette Doe",
      "phone": "+10987654321",
      "current_task": null,
      "tasks": [], // tasks assigned to the driver for the current time block (10-12pm, 12-2pm, ... 8-10pm)
      "on_duty": false, 
      "delay_time": 0, // (minutes behind schedule)
      "metadata": [],
      "vehicle_id": "5F4RR11235JAx3wL8",
      "vehicle_type": "CAR",
      "vehicle_description": "Tesla Model X",
      "vehicle_license_plate": "FYC-7992",
      "vehicle_color": "#0000",
      "vehicle_year": 2014,
      "latitude": null,
      "longitude": null,
    },
    {...},
]
```

Search for workers matching *all* of any combination of the following search parameters: 

### HTTP Request:

`POST https://api.shopturn.com/v1/worker/search`

### Body Parameters: 

Search for workers matching *all* of any combination of the following search parameters:

### URL parameters: 

Name | Type | Info
:--- | :--- | :---
latitude | *number* | **optional** The latitude value of the search coordinates.
longitude | *number* | **optional** The longitude value of the search coordinates.
radius | *number* | **optional** Length in meters of desired radius. Defaults to 1000. Max value is 10000. 
phone | *string* | **optional** Matches entities with phone numbers containing or equal to this value
name | *string* | **optional** Matches entities with names containing or equal to this value
email | *string* | **optional** Matches entities with emails containing or equal to this value
on_duty | *boolean* | **optional** on_duty value to search for
order_by | *string* | **optional** name (default), email, phone, on_duty, delay_time, tasks, time_created, time_last_modified, or time_last_seen
descending | *boolean* | **optional** true to list workers in descending order. Defaults to `false`
limit | *number* | **optional** value to limit to results to
offset | *number* | **optional** value to offset results by

Latitude and longitude must both be included or both be excluded.

# Stores

A store is a location where deliveries start and returns are completed by workers.

## Create a Store

> Example:

```shell
curl  -X POST  "api.shopturn.com/v1/store" \
      -H  "Authorization: YOUR_API_KEY"
      -d  '{
              "name": "NY Lower East Side"
              "entry_notes": "Loop arround to the loading dock."
              "address": {
                  "number": "176",
                  "street": "E 3rd St",
                  "city": "New York",
                  "state": "NY",
                  "country": "United States",
                  "postal_code": "10009"
              }
          }'
```

> `200 (application/json)`:

```json
{
  "id": "SgHz2n4pOZ36b6pFspEUwGA",
  "name": "NY Lower East Side",
  "location": [
    -102.400942,
    47.018286
  ],
  "address": {
    "number": "176",
    "street": "E 3rd St",
    "city": "New York",
    "state": "NY",
    "country": "United States",
    "postal_code": "10009"
  },
  "time_created": 1509513811,
  "time_last_updated": 1509513811
}
```

Create a new store to start accepting returns and scheduling deliveries.

### HTTP Request:

`POST https://api.shopturn.com/v1/store`

### Body Parameters:

Name | Type | Info
:--- | :--- | :---
name | *string* | Name of the store 
entry_notes | *string* | Notes for getting into the store
address | *object* | Details below
phone | *string* | **optional** Phone of the store for workers to call in case of a problem
latitude | *number* | **optional** The latitude value of the store.
longitude | *number* | **optional** The longitude value of the store.

If the latitude and longitude are ommited, the API will automatically set the coordinates by reverse geocoding the address. 

An `address` object has the following properties:

Name | Type | Info
:--- | :--- | :---
name | *string* | **optional** A name for the address (i.e. Empire State Building)
number | *string* | The street number
street | *string* | The street name
apartment | *string* | **optional** Apartment number / address line 2
city | *string* | The city name
state | *string* | The state name or 2 letter code
country | *string* | The country name
postal_code | *string* | The postal code
unparsed | *string* | **optional** Unparsed human-readable address on one line. If provided, all other properties except for `name` and `apartment` are ignored.


## Get a Store

> Example:

```shell
curl  -X GET  "api.shopturn.com/v1/store/SgHz2n4pOZ36b6pFspEUwGA" \
      -H  "Authorization: YOUR_API_KEY"
```

> `Response: 200 (application/json)`:

```json
{
  "id": "SgHz2n4pOZ36b6pFspEUwGA",
  "name": "NY Lower East Side",
  "location": [
    -102.400942,
    47.018286
  ],
  "address": {
    "number": "176",
    "street": "E 3rd St",
    "city": "New York",
    "state": "NY",
    "country": "United States",
    "postal_code": "10009"
  },
  "time_created": 1509513811,
  "time_last_updated": 1509513931
}
```

Get a store with a specific `id`.

### HTTP Request:

`GET https://api.shopturn.com/v1/store/<store_id>`

## Update a Store

> Example:

```shell
curl  -X PUT  "api.shopturn.com/v1/store/SgHz2n4pOZ36b6pFspEUwGA" \
      -H  "Authorization: YOUR_API_KEY"
      -d '{"address":{"unparsed":"4 S circle dr, great neck ny"}, "name": "NY Long Island"}'
```

> `200 (application/json)`:

```json
{
  "id": "SgHz2n4pOZ36b6pFspEUwGA",
  "name": "NY Long Island",
  "location": [
    -104.940042,
    49.021886
  ],
  "address": {
    "number": "4",
    "street": "South Circle Drive",
    "city": "Great Neck",
    "state": "NY",
    "country": "United States",
    "postal_code": "11021"
  },
  "time_created": 1509513811,
  "time_last_updated": 1509514531
}
```

Update a store's information.

### HTTP Request:

`PUT https://api.shopturn.com/v1/store/<store_id>`

### Body parameters:

Name | Type | Info
:--- | :--- | :---
name | *string* | **optional** New name of the store 
entry_notes | *string* | **optional** New notes for getting into the store
address | *object* | **optional** Details below
phone | *string* | **optional** Phone of the store for workers to call in case of a problem
latitude | *number* | **optional** The latitude value of the store
longitude | *number* | **optional** The longitude value of the store

If the latitude and longitude are ommited, the API will automatically set the coordinates by reverse geocoding the address. 

An `address` object has the following properties:

Name | Type | Info
:--- | :--- | :---
name | *string* | **optional** A name for the address (i.e. Empire State Building)
number | *string* | The street number
street | *string* | The street name
apartment | *string* | **optional** Apartment number / address line 2
city | *string* | The city name
state | *string* | The state name or 2 letter code
country | *string* | The country name
postal_code | *string* | The postal code
unparsed | *string* | **optional** Unparsed human-readable address on one line. If provided, all other properties except for `name` and `apartment` are ignored.


## Delete a Store

> Example:

```shell
curl  -X DELETE   "api.shopturn.com/v1/store/SgHz2n4pOZ36b6pFspEUwGA" \
      -H  "Authorization: YOUR_API_KEY"
```

> `200 (application/json)`:


Delete a store.

### HTTP Request:

`DELETE https://api.shopturn.com/v1/store/<store_id>`


## Search for Stores

> `Example:`

```shell
curl  -X POST  "api.shopturn.com/v1/store/search" \
      -H  "Authorization: YOUR_API_KEY"
      -d '{"name":"NY Long Island"}'
```

> `Response: 200 (application/json)`:

```json
[
  
    {
      "id": "SgHz2n4pOZ36b6pFspEUwGA",
      "name": "NY Long Island",
      "location": [
        -104.940042,
        49.021886
      ],
      "address": {
        "number": "4",
        "street": "South Circle Drive",
        "city": "Great Neck",
        "state": "NY",
        "country": "United States",
        "postal_code": "11021"
      },
      "time_created": 1509513811,
      "time_last_updated": 1509514531,
    },
    {...},
]
```

Search for stores matching *all* of any combination of the following search parameters: 

### HTTP Request:

`POST https://api.shopturn.com/v1/store/search`

### Body Parameters: 

Name | Type | Info
:--- | :--- | :---
latitude | *number* | **optional** The latitude value of the search coordinates.
longitude | *number* | **optional** The longitude value of the search coordinates.
radius | *number* | **optional** Length in meters of desired radius. Defaults to 1000. Max value is 10000. 
phone | *string* | **optional** Matches entities with phone numbers containing or equal to this value
name | *string* | **optional** Matches entities with names containing or equal to this value
street_number | *string* | **optional** Matches entities with addresses with street numbers equal to this value
street_name | *string* | **optional** Matches entities with addresses with street names equal to this value
city | *string* | **optional**  Matches entities with addresses with city names equal to this value
state | *string* | **optional** Matches entities with addresses with state names equal to this value
country | *string* | **optional** Matches entities with addresses with country names equal to this value
postal_code | *string* | **optional** Matches entities with addresses with postal codes equal to this value
order_by | *string* | **optional** name (default), email, phone, on_duty, delay_time, tasks, time_created, time_last_modified, or time_last_seen
descending | *boolean* | **optional** true to list workers in descending order. Defaults to `false`
limit | *number* | **optional** value to limit to results to
offset | *number* | **optional** value to offset results by

# Customers

A customer is a target for a task. (i.e. the person ordering a delivery or scheduling a return).

## Create a Customer

> Example:

```shell
curl  -X POST  "api.shopturn.com/v1/customer" \
      -H  "Authorization: YOUR_API_KEY"
      -d  '{
            "name": "John Snow",
            "external_id": "4SGd53hSF6s27"
            "phone": "+15163535572",
            "email": "jsnow@got.com",
            "notes": "Very stubborn. Sticks to his morals.",
            "skip_phone_number_verification": true
          }'
```

> `200 (application/json)`:

```json
{
    "id": "0jdRwOVvKVS2rTLxcOd6Y5G",
    "external_id": "4SGd53hSF6s27"
    "time_created": 1508438064,
    "time_last_modified": 1508438064,
    "name": "John Snow",
    "phone": "+15163535572",
    "email": "jsnow@got.com",
    "notes": "Very stubborn. Sticks to his morals.",
    "skip_notifications": false,
    "skip_phone_number_verification": true,
    "skip_email_verification" : false,
    "email_verified": true,
    "phone_verified": null
}
```

Create a new customer to start requesting returns and deliveries.

### HTTP Request:

`POST https://api.shopturn.com/v1/customer`

### Body Parameters:

Name | Type | Info
:--- | :--- | :---
external_id | *string* | The customer's external ID
name | *string* | The customer's full name
phone | *string* | A unique phone number with the recipients country code (in the format +11234567890)
email | *string* | A unique email belonging to the customer
notes | *string* | **optional** Global notes for the customer, only visible to the organization. Should not be task specific. (e.g. Has a hot temper. Be nice!)
skip_notifications | *boolean* | **optional** Whether the customer should not receive mobile notifications relating to a task. If set to `false`, notifications will be sent in the following order pending availability: Push Notifications, SMS, Email. Defaults to `false`
skip_phone_number_verification | *boolean* | **optional** Whether ShopTurn should not verify the customer's phone number. Defaults to `false`
skip_email_verification | *boolean* | **optional** Whether ShopTurn should not verify the customer's email address. Defaults to `false`


## Get a Customer

> Example:

```shell
curl  -X GET  "api.shopturn.com/v1/customer/0jdRwOVvKVS2rTLxcOd6Y5G" \
      -H  "Authorization: YOUR_API_KEY"
```

> `Response: 200 (application/json)`:

```json
{
    "id": "0jdRwOVvKVS2rTLxcOd6Y5G",
    "external_id": "4SGd53hSF6s27"
    "time_created": 1508438064,
    "time_last_modified": 1508438064,
    "name": "John Snow",
    "phone": "+15163535572",
    "email": "jsnow@got.com",
    "notes": "Very stubborn. Sticks to his morals.",
    "skip_notifications": false,
    "skip_phone_number_verification": true,
    "skip_email_verification" : false,
    "email_verified": true,
    "phone_verified": null
}
```

Get a Customer with a specific `id`.

### HTTP Request:

`GET https://api.shopturn.com/v1/customer/<customer_id>`

## Update a Customer

> Example:

```shell
curl  -X PUT  "api.shopturn.com/v1/customer/0jdRwOVvKVS2rTLxcOd6Y5G" \
      -H  "Authorization: YOUR_API_KEY"
      -d '{"name": "John Targarian", "email": "jtargarian@got.com"}'
```

> `200 (application/json)`:

```json
{
    "id": "0jdRwOVvKVS2rTLxcOd6Y5G",
    "external_id": "4SGd53hSF6s27"
    "time_created": 1508438064,
    "time_last_modified": 1508438064,
    "name": "John Targarian",
    "phone": "+15163535572",
    "email": "jtargarian@got.com",
    "notes": "Very stubborn. Sticks to his morals.",
    "skip_notifications": false,
    "skip_phone_number_verification": true,
    "skip_email_verification" : false,
    "email_verified": true,
    "phone_verified": null
}
```

Update a Customer's information. Changing a customer's `email` or `phone` will cause the values of `email_verified` or `phone_verified` to reset. It will also attempt to verify them again if set. Setting a new `email` and setting `skip_email_verification` to true in the same request will prevent the API from verifying the new email. Same goes for phone. 


### HTTP Request:

`PUT https://api.shopturn.com/v1/customer/0jdRwOVvKVS2rTLxcOd6Y5G`

### Body parameters:

Name | Type | Info
:--- | :--- | :---
name | *string* | **optional** The customer's new full name
phone | *string* | **optional** A unique phone number with the recipients country code (in the format +11234567890)
email | *string* | **optional** A unique email belonging to the customer
notes | *string* | **optional** Global notes for the customer, only visible to the organization. Should not be task specific. (e.g. Has a hot temper. Be nice!)
skip_notifications | *boolean* | **optional** Whether the customer should not receive mobile notifications relating to a task. If set to `false`, notifications will be sent in the following order pending availability: Push Notifications, SMS, Email. Defaults to `false`
skip_phone_number_verification | *boolean* | **optional** Whether ShopTurn should not verify the customer's phone number. Defaults to `false`
skip_email_verification | *boolean* | **optional** Whether ShopTurn should not verify the customer's email address. Defaults to `false`

## Delete a Customer

> Example:

```shell
curl  -X DELETE   "api.shopturn.com/v1/customer/0jdRwOVvKVS2rTLxcOd6Y5G" \
      -H  "Authorization: YOUR_API_KEY"
```

> `200 (application/json)`:


Delete a customer.

### HTTP Request:

`DELETE https://api.shopturn.com/v1/customer/0jdRwOVvKVS2rTLxcOd6Y5G`


## Search for Customers

> `Example:`

```shell
curl  -X POST  "api.shopturn.com/v1/customer/search" \
      -H  "Authorization: YOUR_API_KEY"
      -d '{
            "name":"%snow%"
          }'
```

> `Response: 200 (application/json)`:

```json
[
  
    {
        "id": "0jdRwOVvKVS2rTLxcOd6Y5G",
        "time_created": 1508438064,
        "time_last_modified": 1508438064,
        "name": "John Snow",
        "phone": "+15163535572",
        "email": "jsnow@got.com",
        "notes": "Very stubborn. Sticks to his morals.",
        "skip_notifications": false,
        "skip_phone_number_verification": true,
        "skip_email_verification" : false,
        "email_verified": true,
        "phone_verified": null, 
    },
    {
        "id": "2rTLxcOd6Y5G0jdRwOVvKVS",
        "time_created": 1508438064,
        "time_last_modified": 1508438064,
        "name": "Ramsey Snow",
        "phone": "+15165635572",
        "email": "rsnow@got.com",
        "notes": "Scary, unpredictable and sporatic. Don't get on his bad side!",
        "skip_notifications": false,
        "skip_phone_number_verification": false,
        "skip_email_verification" : false,
        "email_verified": true,
        "phone_verified": true,
    },
    {...},
]
```

Search for customers matching *all* of any combination of the following search parameters:

### HTTP Request:

`POST https://api.shopturn.com/v1/customers/search`

### Body Parameters: 

Name | Type | Info
:--- | :--- | :---
name | *string* | **optional** Matches entities with names containing or equal to this value
phone | *string* | **optional** Matches entities with phone numbers containing or equal to this value
email | *string* | **optional** Matches entities with emails containing or equal to this value
order_by | *string* | **optional** name (default), email, phone, on_duty, time_created, or time_last_modified
descending | *boolean* | **optional** true to list workers in descending order. Defaults to `false`
limit | *number* | **optional** value to limit to results to
offset | *number* | **optional** value to offset results by

# Task Estimates

Task estimates represent the intent to create a task for a worker to complete on behalf of a customer. They also give you available `Time Window`s, validate address info, and validate metro area servicability. First request a Task Estimate for a given customer and destination, then use one of the returned `Time Window` objects to turn the estimate into a task.

A `Time Window` is an object specifying a window that a task can be competed in. It has the following properties:

Name | Type | Info
:--- | :--- | :---
id | *string* | A unique identifier for the `Time Window` (required to create a Task)
start_time | *number* | Unix timestamp for when the `Time Window` starts
end_time | *number* | Unix timestamp for when the `Time Window` ends
expires_at | *number* | Unix timestamp for when the window must be reserved by. 

**Note**: If you wait untill after the expiration time, a new Task Estimate will have to be created to receive valid `Time Window`s.

## Create an Estimate

> Example:

```shell
curl  -X POST  "api.shopturn.com/v1/estimate" \
      -H  "Authorization: YOUR_API_KEY"
      -d  '{
            "address":{
                "unparsed": "176 East 3rd st nyc 10009",
                "apartment": "4c"
            }, 
            "customer_id":"2rTLxcOd6Y5G0jdRwOVvKVS",
            "store_id":"d6Y5G0jd2rTLxcORwOVvKVS", // leave out to auto-assign
            "barcodes": ["3647847698354","245736732","3647847698354"]
          }'
```

> `200 (application/json)`:

```json
{
    "id": "FsSgHz2n4pOZ36b6ppEUwGA",
    "address" : {
        "number": "176",
        "street": "E 3rd St.",
        "apartment": "4C",
        "city": "New York", 
        "state": "New York",
        "country": "United States",
        "postal_code": "10009"

    },
    "customer_id": "2rTLxcOd6Y5G0jdRwOVvKVS",
    "store_id": "d6Y5G0jd2rTLxcORwOVvKVS",
    "barcodes": ["724272463834", "638372427244"]
    "time_windows": {
        {
            "id": "Hz2npEUwG4pOZ36b6pFsSgA",
            "start_time": 1508535337,
            "end_time": 1508595337,
            "expires_at": 1508525337,
        },
        {
            "id": "Hz2npEUwG4pOZ36b6pFsSgA",
            "start_time": 1508535337,
            "end_time": 1508595337,
            "expires_at": 1508525337
        },
        {
            "id": "Hz2npEUwG4pOZ36b6pFsSgA",
            "start_time": 1508535337,
            "end_time": 1508595337,
            "expires_at": 1508525337
        },
    },
    "pricing": {
        "miles": 1.5, 
        "base": 2.25,
        "rate": 2.00,
        "total": 5.25 
    }
}
```

Creating a `Task Estimate` is the first step to creating a `Task`. 

### HTTP Request:

`POST https://api.shopturn.com/v1/estimate`

### Body Parameters:

Name | Type | Info
:--- | :--- | :---
address | *object* | Details below
customer_id | *string* | **optional** The id of the customer the worker is going to
customer_external_id | *string* | **optional** The external unique id of the customer the worker is going to
store_id | *string* | **optional**  The id of the store the task is going to. leave out to auto-assign 
is_delivery | *boolean* | **optional** Set to `true` if the task is a delivery. Default is `false`
barcodes | *array* | An array of barcodes (strings) that must be scanned by the worker at pickup, and at transfer.

Either `customer_id` or `customer_external_id` must be included.

An `address` object has the following properties:

Name | Type | Info
:--- | :--- | :---
name | *string* | **optional** A name for the address (i.e. Empire State Building)
number | *string* | The street number
street | *string* | The street name
apartment | *string* | **optional** Apartment number / address line 2
city | *string* | The city name
state | *string* | The state name or 2 letter code
country | *string* | The country name
postal_code | *string* | The postal code
unparsed | *string* | **optional** Unparsed human-readable address on one line. If provided, all other properties except for `name` and `apartment` are ignored.


## Get a Task Estimate

> Example:

```shell
curl  -X GET  "api.shopturn.com/v1/estimate/FsSgHz2n4pOZ36b6ppEUwGA" \
      -H  "Authorization: YOUR_API_KEY"
```

> `Response: 200 (application/json)`:

```json
{
    "id": "FsSgHz2n4pOZ36b6ppEUwGA",
    "address" : {
        "number": "176",
        "street": "E 3rd St.",
        "apartment": "4C",
        "city": "New York", 
        "state": "New York",
        "country": "United States",
        "postal_code": "10009"

    },
    "customer_id": "2rTLxcOd6Y5G0jdRwOVvKVS",
    "store_id": "d6Y5G0jd2rTLxcORwOVvKVS",
    "barcodes": ["724272463834", "638372427244"]
    "time_windows": {
        {
            "id": "Hz2npEUwG4pOZ36b6pFsSgA",
            "start_time": 1508535337,
            "end_time": 1508595337,
            "expires_at": 1508525337,
        },
        {
            "id": "Hz2npEUwG4pOZ36b6pFsSgA",
            "start_time": 1508535337,
            "end_time": 1508595337,
            "expires_at": 1508525337
        },
        {
            "id": "Hz2npEUwG4pOZ36b6pFsSgA",
            "start_time": 1508535337,
            "end_time": 1508595337,
            "expires_at": 1508525337
        },
    },
    "pricing": {
        "miles": 1.5, 
        "base": 2.25,
        "rate": 2.00,
        "total": 5.25 
    }
}
```

Get a Task Estimate with a specific `id`. This re-generates the `Time Window` objects with later `expires_at` attributes, so use this if any of them expire before the user selects a time slot. They usually expire within 60 seconds depending on demand. 

### HTTP Request:

`GET https://api.shopturn.com/v1/estimate/<estimate_id>`


# Tasks

Tasks represent units of work, defined by one destination and one recipient, and are automatically assigned to workers for completion. Note that both return pick-ups and deliveries are considered tasks. 

The first step to creating a `Task` is to create a `Task Estimate`. Once created, save the `id` of the estimate, and present the available `Time Window`s to the customer (returned as part of the `Task Estimate` object). When they select a `Time Window`, send the `id` of the `Task Estimate` along with the `id` of the `Time Window` to the `Tasks` endpoint as described in more detail below. 


## Create a Task

> Example:

```shell
curl  -X POST  "api.shopturn.com/v1/task" \
      -H  "Authorization: YOUR_API_KEY"
      -d  '{
                "estimate_id":"FsSgHz2n4pOZ36b6ppEUwGA",
                "windowId":"Hz2npEUwG4pOZ36b6pFsSgA",
                "charge_customer":true,
                "return_store_credit":false,
                "notes": "I left my bag and receipt with the doorman. Thx!"
           }'
```

> `200 (application/json)`:

```json
{
  "id": "WQSb6pFz4pOEUwZ36pSgsHGA",
  "charge_customer": true, 
  "return_store_credit": false,
  "status": "assigned" // created, assigned, inTransit, arrived, completed, canceled, or aborted
  "worker": { // null if status is 'created'
      "id": "sFtimptJJdC2qmptJ",
      "time_created": 1508438064,
      "time_last_modified": 1508438064,
      "time_last_seen": null,
      "organization": "mDdfg3mNz9agUcNDrTsNOL",
      "name": "Janette Doe",
      "phone": "+10987654321",
      "current_task": null,
      "tasks": [], // tasks assigned to the driver for the current time block (10-12pm, 12-2pm, ... 8-10pm)
      "on_duty": false, 
      "delay_time": 0, // (minutes behind schedule)
      "metadata": [],
      "vehicle": {
          "id": "5F4RR11235JAx3wL8",
          "type": "CAR",
          "description": "Toyota Camry",
          "license_plate": "FYC-7992",
          "color": "#0000",
          "year": 2014
      },
  },
  "customer_id": "0jdRw*OVvKVS2rTLxcOd6Y5G",
  "store": {
    "id": "SgHz*2n4pOZ36b6pFspEUwGA",
    "name": "NY Lower East Side",
    "location": [
      -102.400942,
      47.018286
    ],
    "address": {
      "number": "176",
      "street": "E 3rd St",
      "city": "New York",
      "state": "NY",
      "country": "United States",
      "postal_code": "10009"
    }
  },
  "time_window": {
      "id": "Hz2npEUwG4pOZ36b6pFsSgA",
      "start_time": 1508535337,
      "end_time": 1508595337,
  },
  "completed_at": null, // null if not completed
  "notes": "I left my bag and receipt with the doorman. Thx!",
  "pricing": {
      "miles": 1.5, 
      "base": 2.25,
      "rate": 2.00,
      "total": 5.25 
  },
  "barcodes": ["724272463834", "638372427244"]
  "time_created": 1509050462,
  "time_last_modified": 1509310701,
}
```

Create a new `Task` from a `Task Estimate` and a `Time Window`.

### HTTP Request:

`POST https://api.shopturn.com/v1/task`

### Body Parameters:

Name | Type | Info
:--- | :--- | :---
estimate_id | String | The id of the task estimate you are converting to a task
time_window_id | String | The id of the desired `Time Window` to schedule the task for
charge_customer | Boolean | Set to `true` to charge the customer for the task
return_store_credit | Boolean | Set to `true` to return store credit to the consumer (only effects returns)
notes | String | **optional** Entry or completion instructions from the customer to the worker

## Get a Task

> Example:

```shell
curl  -X GET  "api.shopturn.com/v1/task/WQSb6pFz4pOEUwZ36pSgsHGA" \
      -H  "Authorization: YOUR_API_KEY"
```

> `Response: 200 (application/json)`:

```json
{
  "id": "WQSb6pFz4pOEUwZ36pSgsHGA",
  "charge_customer": true, 
  "return_store_credit": false,
  "status": "assigned" // created, assigned, inTransit, arrived, completed, canceled, or aborted
  "worker": { // null if status is 'created'
      "id": "sFtimptJJdC2qmptJ",
      "time_created": 1508438064,
      "time_last_modified": 1508438064,
      "time_last_seen": null,
      "organization": "mDdfg3mNz9agUcNDrTsNOL",
      "name": "Janette Doe",
      "phone": "+10987654321",
      "current_task": null,
      "tasks": [], // tasks assigned to the driver for the current time block (10-12pm, 12-2pm, ... 8-10pm)
      "on_duty": false, 
      "delay_time": 0, // (minutes behind schedule)
      "metadata": [],
      "vehicle": {
          "id": "5F4RR11235JAx3wL8",
          "type": "CAR",
          "description": "Toyota Camry",
          "license_plate": "FYC-7992",
          "color": "#0000",
          "year": 2014
      },
  },
  "customer_id": "0jdRwOVvKVS2rTLxcOd6Y5G",
  "store": {
    "id": "SgHz2n4pOZ36b6pFspEUwGA",
    "name": "NY Lower East Side",
    "location": [
      -102.400942,
      47.018286
    ],
    "address": {
      "number": "176",
      "street": "E 3rd St",
      "city": "New York",
      "state": "NY",
      "country": "United States",
      "postal_code": "10009"
    }
  },
  "time_window": {
      "id": "Hz2npEUwG4pOZ36b6pFsSgA",
      "start_time": 1508535337,
      "end_time": 1508595337,
  },
  "completed_at": null, // null if not completed
  "notes": "I left my bag and receipt with the doorman. Thx!",
  "pricing": {
      "miles": 1.5, 
      "base": 2.25,
      "rate": 2.00,
      "total": 5.25 
  },
  "barcodes": ["724272463834", "638372427244"]
  "time_created": 1509050462,
  "time_last_modified": 1509050462,
}
```

Get a Task with a specific `id`.

### HTTP Request:

`GET https://api.shopturn.com/v1/task/<task_id>`

## Update a Task

> Example:

```shell
curl  -X PUT  "api.shopturn.com/v1/task/WQSb6pFz4pOEUwZ36pSgsHGA" \
      -H  "Authorization: YOUR_API_KEY"
      -d '{
            "notes": "It's with my neighbor actually in apartment 4B! Thank you!", 
            "barcodes": ["724272463834"]
          }'
```

> `200 (application/json)`:

```json
{
  "id": "WQSb6pFz4pOEUwZ36pSgsHGA",
  "charge_customer": true, 
  "return_store_credit": false,
  "status": "assigned" // created, assigned, inTransit, arrived, completed, canceled, or aborted
  "worker": { // null if status is 'created'
      "id": "sFtimptJJdC2qmptJ",
      "time_created": 1508438064,
      "time_last_modified": 1508438064,
      "time_last_seen": null,
      "organization": "mDdfg3mNz9agUcNDrTsNOL",
      "name": "Janette Doe",
      "phone": "+10987654321",
      "current_task": null,
      "tasks": [], // tasks assigned to the driver for the current time block (10-12pm, 12-2pm, ... 8-10pm)
      "on_duty": false, 
      "delay_time": 0, // (minutes behind schedule)
      "metadata": [],
      "vehicle": {
          "id": "5F4RR11235JAx3wL8",
          "type": "CAR",
          "description": "Toyota Camry",
          "license_plate": "FYC-7992",
          "color": "#0000",
          "year": 2014
      },
  },
  "customer_id": "0jdRwOVvKVS2rTLxcOd6Y5G",
  "customer_external_id": "S2rTLxc0jdRwOVvKVOd6Y5G",
  "store": {
    "id": "SgHz2n4pOZ36b6pFspEUwGA",
    "name": "NY Lower East Side",
    "location": [
      -102.400942,
      47.018286
    ],
    "address": {
      "number": "176",
      "street": "E 3rd St",
      "city": "New York",
      "state": "NY",
      "country": "United States",
      "postal_code": "10009"
    }
  },
  "time_window": {
      "id": "Hz2npEUwG4pOZ36b6pFsSgA",
      "start_time": 1508535337,
      "end_time": 1508595337,
  },
  "completed_at": null, // null if not completed
  "notes": "I left my bag and receipt with the doorman. Thx!",
  "pricing": {
      "miles": 1.5, 
      "base": 2.25,
      "rate": 2.00,
      "total": 5.25 
  },
  "barcodes": ["724272463834"]
  "time_created": 1509050462,
  "time_last_modified": 1509050462,
}
```

____

### HTTP Request:

`PUT https://api.shopturn.com/v1/task/WQSb6pFz4pOEUwZ36pSgsHGA`

### Body parameters:

Name | Type | Info
:--- | :--- | :---
charge_customer | Boolean | **optional** Set to `true` to charge the customer for the task
return_store_credit | Boolean | **optional** Set to `true` to return store credit to the consumer (only effects returns)
notes | String | **optional** Entry or completion instructions from the customer to the worker
barcodes | *array* | An array of barcodes (strings) that must be scanned by the worker at pickup, and at transfer. Replaces the existing items. Sending an empty list throws an error. To delete / cancel a task see the delete enpoint.
time_window_id | String | The id of the desired `Time Window` to schedule the task for


**Note**: To change the time_window of a task you need to send the id of an unexpired `Time Window`. To do so, create another `Task Estimate` with the same customer and address, present the times to the consumer, and pass back the time_window_id of the newly selected `Time Window`.  

## Delete a Task

> Example:

```shell
curl  -X DELETE   "api.shopturn.com/v1/task/WQSb6pFz4pOEUwZ36pSgsHGA" \
      -H  "Authorization: YOUR_API_KEY"
```

> `200 (application/json)`:


Delete a `Task` to cancel it. There may be a small fee if the time of cancelation is within a few hours of the pickup. 

### HTTP Request:

`DELETE https://api.shopturn.com/v1/task/<task_id>`

## Search for Tasks

> `Example:`

```shell
curl  -X POST  "api.shopturn.com/v1/tasks/search" \
      -H  "Authorization: YOUR_API_KEY"
      -d '{"customer_external_id": "S2rTLxc0jdRwOVvKVOd6Y5G"}'
```

> `Response: 200 (application/json)`:

```json
[
  
{
  "id": "WQSb6pFz4pOEUwZ36pSgsHGA",
  "charge_customer": true, 
  "return_store_credit": false,
  "status": "assigned" // created, assigned, inTransit, arrived, completed, canceled, or aborted
  "worker": { // null if status is 'created'
      "id": "sFtimptJJdC2qmptJ",
      "time_created": 1508438064,
      "time_last_modified": 1508438064,
      "time_last_seen": null,
      "organization": "mDdfg3mNz9agUcNDrTsNOL",
      "name": "Janette Doe",
      "phone": "+10987654321",
      "current_task": null,
      "tasks": [], // tasks assigned to the driver for the current time block (10-12pm, 12-2pm, ... 8-10pm)
      "on_duty": false, 
      "delay_time": 0, // (minutes behind schedule)
      "metadata": [],
      "vehicle": {
          "id": "5F4RR11235JAx3wL8",
          "type": "CAR",
          "description": "Toyota Camry",
          "license_plate": "FYC-7992",
          "color": "#0000",
          "year": 2014
      },
  },
  "customer_id": "0jdRwOVvKVS2rTLxcOd6Y5G",
  "customer_external_id": "S2rTLxc0jdRwOVvKVOd6Y5G",
  "store": {
    "id": "SgHz2n4pOZ36b6pFspEUwGA",
    "name": "NY Lower East Side",
    "location": [
      -102.400942,
      47.018286
    ],
    "address": {
      "number": "176",
      "street": "E 3rd St",
      "city": "New York",
      "state": "NY",
      "country": "United States",
      "postal_code": "10009"
    }
  },
  "time_window": {
      "id": "Hz2npEUwG4pOZ36b6pFsSgA",
      "start_time": 1508535337,
      "end_time": 1508595337,
  },
  "completed_at": null, // null if not completed
  "notes": "I left my bag and receipt with the doorman. Thx!",
  "pricing": {
      "miles": 1.5, 
      "base": 2.25,
      "rate": 2.00,
      "total": 5.25 
  },
  "barcodes": ["724272463834"]
  "time_created": 1509050462,
  "time_last_modified": 1509050462,
},
    {...},
]
```

Search for upcoming, in progress, completed or canceled tasks matching *all* of any combination of the following search parameters: 

### HTTP Request:

`POST https://api.shopturn.com/v1/task/search`

### Body Parameters: 

Name | Type | Info
:--- | :--- | :---
worker_id | *string* | **optional** Matches entities with workers who have this id
customer_id | *string* | **optional** Matches entities with customer who have this id
customer_external_id | *string* | **optional** Matches entities with customer who have this external id
status | *string* | **optional** Matches entities with tasks that have this status
order_by | *string* | **optional** name (default), email, phone, on_duty, time_created, or time_last_modified
descending | *boolean* | **optional** true to list workers in descending order. Defaults to `false`
limit | *number* | **optional** value to limit to results to
offset | *number* | **optional** value to offset results by

# Webhooks

ShopTurn makes it possible for your applications to be notified exactly when important system events occur thanks to [Webhooks](https://en.wikipedia.org/wiki/Webhook).

You're free to create as many webhooks as you like but keep in mind that a webhook always targets a single trigger and external url. 

We're also more than willing to add any webhooks you request within a few hours so shoot us an email if you need something we don't have yet! 

## Types of Webhooks

Trigger Id | Trigger Name | Info | Payload 
:--- | :--- | :--- | :--- 
0 | task_en_route | Worker began a task | `Task` 
1 | task_eta | Every time the worker's eta changes by 1 minute (in either direction) | `Task`
2 | task_arrived | Worker arrived at task address | `Task`
3 | task_completed | Worker completed a task | `Task`
4 | task_failed | Task could not be completed. Reason included in `Task.failed_reason` | `Task`
5 | task_created | Task was created | `Task`
6 | task_updated | Task was changed | `Task`
7 | task_deleted | Task was canceled. Check pricing to see if the customer was charged | `Task`
8 | task_assigned | Task was assigned to a worker | `Task`
9 | task_unassigned | Task was unassigned from a worker | `Task`
10 | task_delayed | Task was delayed | `Task`
11 | worker_duty | Worker's on-duty status changed | `Worker`

Webhooks can be maintained through the developer dashboard (under development) or via the API. We include the webhook id and name in the payload, so you can overload the same URL to handle all the webhookds. 

The JSON payload we will POST to your url will include the following:

Name | Type | Info 
:--- | :--- | :--- 
webhook_id | *integer* | The id of the webhook as listed above
webhook_name | *string* | The name of the webhook as listed above
secret | *string* | A secret you can provide to us when you create the webhook for verification purposes
payload | *object* | The payload of the webhook as listed above 
time | *integer* | Unix epoch time the event took place

## Failure & Retry

Any request that fails (non-200 status code) will be retried every 15 minutes for up to 24 hrs. You will receive an email after the 10th attempt, and after 24 hrs. If the webhook fails 288 times in a row (3 days) the webhook will be disabled and you will receive an email. 

## Create a Webhook

> Example:

```shell
curl  -X POST  "api.shopturn.com/v1/webhook" \
      -H  "Authorization: YOUR_API_KEY"
      -d  '{
            "url": "www.your.url/task",
            "secret": "your_secret",
            "trigger_name": "task_en_route"
          }'
```

> `200 (application/json)`:

```json
{
    "id": "nIE19LipXcrzH1Pn1",
    "count": 0,
    "url": "www.your.url/task",
    "trigger_name": task_en_route,
    "trigger_id": 0,
    "time_created": 1509521194
    "time_last_modified": 1509521194
}
```

Create a webhook.

### HTTP Request:

`POST https://api.shopturn.com/v1/webhooks`

### Body Parameters:

Name | Type | Info
:--- | :--- | :---
url | *string* | The URL where we will make a post request when the webhook triggers
secret | *string* | **optional** Secret we will send with each post request for you verification purposes
trigger_name | *string* | **optional** Name of the trigger
trigger_id | *string* | **optional** Id of the trigger

**Note:** You must include either the trigger_name or the trigger_id

## Get all Webhooks

> `Example:`

```shell
curl  -X GET  "api.shopturn.com/v1/webhooks" \
      -H  "Authorization: YOUR_API_KEY"
```

> `Response: 200 (application/json)`:

```json
[
  
    {
      "id": "nIE19LipXcrzH1Pn1",
      "count": 0,
      "url": "www.your.url/task",
      "trigger_name": task_en_route,
      "trigger_id": 0,
      "time_created": 1509521194
      "time_last_modified": 1509521194
    },
    {...},
]
```

List all webhooks belonging to your organization. 

### HTTP Request:

`GET https://api.shopturn.com/v1/webhooks`

## Delete a Webhook

> Example:

```shell
curl  -X DELETE   "api.shopturn.com/v1/webhooks/nIE19LipXcrzH1Pn1" \
      -H  "Authorization: YOUR_API_KEY"
```

> `200 (application/json)`:


Delete a webhook.

### HTTP Request:

`DELETE https://api.shopturn.com/v1/webhook/<webhook_id>`


