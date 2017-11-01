---
title: ShopTurn API Reference

language_tabs: # must be one of https://git.io/vQNgJ
  - curl

toc_footers:
  - <a target="_blank" href='https://mail.google.com/mail/u/0/?view=cm&ui=2&tf=0&fs=1&to=jake%40shopturn.com'>Sign Up for a Developer Key</a>

includes:
  - errors

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

> Example Request:

```shell
curl  -X GET  "api.shopturn.com/v1/auth/test" \
      -H  "Authorization: YOUR_API_KEY"
```

> Example Response:

```json
"Hello ORG_NAME! Your request is valid."
```

`GET https://api.shopturn.com/v1/auth/test`

Check that you are formatting your request correctly and that your API key is valid.


# Organizations

Organizations are the top-most entity within ShopTurn. [Administrators](/#reference/Administrators), [Workers](/#reference/workers), [Stores](/#reference/stores) and [Tasks](/#reference/tasks) all belong to an organization. 

## Get Organization

> Example Request:

```shell
curl  -X GET  "api.shopturn.com/v1/organization" \
      -H  "Authorization: YOUR_API_KEY"
```

> Example Response:

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

> Example Request:

```shell
curl  -X PUT  "api.shopturn.com/v1/organization" \
      -H  "Authorization: YOUR_API_KEY"
      -d  '{"name": "New Name", "email": "admin2@retailer.com", "timezone": "America/New_York"}'
```

> Example Response:

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







































































# Introduction

Welcome to the Kittn API! You can use our API to access Kittn API endpoints, which can get information on various cats, kittens, and breeds in our database.

We have language bindings in Shell, Ruby, and Python! You can view code examples in the dark area to the right, and you can switch the programming language of the examples with the tabs in the top right.

This example API documentation page was created with [Slate](https://github.com/lord/slate). Feel free to edit it and use it as a base for your own API's documentation.

# Authentication

> To authorize, use this code:

```ruby
require 'kittn'

api = Kittn::APIClient.authorize!('meowmeowmeow')
```

```python
import kittn

api = kittn.authorize('meowmeowmeow')
```

```shell
# With shell, you can just pass the correct header with each request
curl "api_endpoint_here"
  -H "Authorization: meowmeowmeow"
```

```javascript
const kittn = require('kittn');

let api = kittn.authorize('meowmeowmeow');
```

> Make sure to replace `meowmeowmeow` with your API key.

Kittn uses API keys to allow access to the API. You can register a new Kittn API key at our [developer portal](http://example.com/developers).

Kittn expects for the API key to be included in all API requests to the server in a header that looks like the following:

`Authorization: meowmeowmeow`

<aside class="notice">
You must replace <code>meowmeowmeow</code> with your personal API key.
</aside>

# Kittens

## Get All Kittens

```ruby
require 'kittn'

api = Kittn::APIClient.authorize!('meowmeowmeow')
api.kittens.get
```

```python
import kittn

api = kittn.authorize('meowmeowmeow')
api.kittens.get()
```

```shell
curl "http://example.com/api/kittens"
  -H "Authorization: meowmeowmeow"
```

```javascript
const kittn = require('kittn');

let api = kittn.authorize('meowmeowmeow');
let kittens = api.kittens.get();
```

> The above command returns JSON structured like this:

```json
[
  {
    "id": 1,
    "name": "Fluffums",
    "breed": "calico",
    "fluffiness": 6,
    "cuteness": 7
  },
  {
    "id": 2,
    "name": "Max",
    "breed": "unknown",
    "fluffiness": 5,
    "cuteness": 10
  }
]
```

This endpoint retrieves all kittens.

### HTTP Request

`GET http://example.com/api/kittens`

### Query Parameters

Parameter | Default | Description
--------- | ------- | -----------
include_cats | false | If set to true, the result will also include cats.
available | true | If set to false, the result will include kittens that have already been adopted.

<aside class="success">
Remember — a happy kitten is an authenticated kitten!
</aside>

## Get a Specific Kitten

```ruby
require 'kittn'

api = Kittn::APIClient.authorize!('meowmeowmeow')
api.kittens.get(2)
```

```python
import kittn

api = kittn.authorize('meowmeowmeow')
api.kittens.get(2)
```

```shell
curl "http://example.com/api/kittens/2"
  -H "Authorization: meowmeowmeow"
```

```javascript
const kittn = require('kittn');

let api = kittn.authorize('meowmeowmeow');
let max = api.kittens.get(2);
```

> The above command returns JSON structured like this:

```json
{
  "id": 2,
  "name": "Max",
  "breed": "unknown",
  "fluffiness": 5,
  "cuteness": 10
}
```

This endpoint retrieves a specific kitten.

<aside class="warning">Inside HTML code blocks like this one, you can't use Markdown, so use <code>&lt;code&gt;</code> blocks to denote code.</aside>

### HTTP Request

`GET http://example.com/kittens/<ID>`

### URL Parameters

Parameter | Description
--------- | -----------
ID | The ID of the kitten to retrieve

## Delete a Specific Kitten

```ruby
require 'kittn'

api = Kittn::APIClient.authorize!('meowmeowmeow')
api.kittens.delete(2)
```

```python
import kittn

api = kittn.authorize('meowmeowmeow')
api.kittens.delete(2)
```

```shell
curl "http://example.com/api/kittens/2"
  -X DELETE
  -H "Authorization: meowmeowmeow"
```

```javascript
const kittn = require('kittn');

let api = kittn.authorize('meowmeowmeow');
let max = api.kittens.delete(2);
```

> The above command returns JSON structured like this:

```json
{
  "id": 2,
  "deleted" : ":("
}
```

This endpoint deletes a specific kitten.

### HTTP Request

`DELETE http://example.com/kittens/<ID>`

### URL Parameters

Parameter | Description
--------- | -----------
ID | The ID of the kitten to delete

