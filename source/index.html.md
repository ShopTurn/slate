---
title: ShopTurn API Reference

toc_footers:
  - <a target="_blank" href='https://mail.google.com/mail/u/0/?view=cm&ui=2&tf=0&fs=1&to=jake%40shopturn.com'>Request API Key</a>

search: true
---

# Introduction

## General Information

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
      -d  '{"name": "New Name", "email": "admin2@retailer.com", "timezone": "America/New_York"}'
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
      -d  '{ "name": "Jane Doe", "email": "jane@retailer.com", "phone": "(123) 456-7890"}'
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
    "phone": "+11234567890",
}
```

Get an admin with a specific `id`.

## Update an Admin

> `Example:`

```shell
curl  -X PUT  "api.shopturn.com/v1/admin/EBa52sfS733faK7Xs" \
      -H  "Authorization: YOUR_API_KEY"
      -d '{ "name": "Janette Doe", "email": "janette@retailer.com", "phone": "+10987654321" }'
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

`PUT https://api.shopturn.com/v1/admin/admin_id`

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
    "longitude": 47.512341,
}
```

Get a worker with a specific `id`.

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
    "longitude": null,
}
```

Update a worker's information. Vehicle and capacity attributes can be nulled. (null vehicle means on-foot worker)

### HTTP Request:

`PUT https://api.shopturn.com/v1/worker/worker_id`

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

`DELETE https://api.shopturn.com/v1/worker/worker_id`

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

