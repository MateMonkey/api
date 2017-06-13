# MateMonkey.com API v1

# Base URL

The base URL for all requests is `api/v1/`,
so for the main service `http://matemonkey.com/api/v1/`

# Test API

For testing purposes, please don't use the main service. A test API is
running on `http://playground.matemonkey.com/api/v1` for you to test your apps/scripts.

## Documents

This section will specify all documents produced by API requests.
The whole MateMonkey API is JSON, expect all documents to be JSON, even
error information!


### Error

When using a valid API URL with invalid/wrong parameters an error
document will give you valuable information what went wrong.

```json
{
  "title": "String, Reason for the error",
  "messages":
            [
              "String, Describing what went wrong",
              "String, Describing what went wrong"
            ]
}
```
The HTTP Status Code needs to be extracted from the HTTP Header.


### Product

A product has the following format:

```json
{
  "name": "String, Name of the Product",
  "id": "Integer, Internal ID of the Product",
  "type": "String, Type of Product",
  "slug": "String, Short Name for the Product",
  "image": "String, URL to an image",
  "description": "String, a short text describing the product"
}
```

`type` can be any of `['soft-drink', 'alcoholic', 'supplies', 'leaves', 'food']` and specifies the general type of the product.

*Please note:* `image` and `description` are currently not served!

### Address

Producers and Dealers both share the same format for addresses:

```json
{
  "street": "String",
  "number": "String",
  "country": "String",
  "city": "String",
  "postal": "String",
  "lat": "Float, Latitude",
  "lon": "Float, Longitude",
  "web": "String, URL",
  "email": "String",
  "phone": "String"
}
```

### Producer
```json
{
  "name": "String, Company Name",
  "id": "Integer, Internal ID",
  "slug": "String, Short Name for the Producer",
  "image": "String, URL to an image",
  "description": "String, a short text describing the product",
  "address": "embedded Address document"
}
```
*Please note:* `image` and `description` are currently not served!

### Dealer

```json
{
  "name": "String, Name of the Dealer",
  "id": "Integer, Internal ID",
  "type": "String, Type of the dealer, see below",
  "note": "String, short note about the Dealer",
  "slug": "String, Short Name for the Dealer",
  "currency": "String, ISO 4217 Code",
  "created_at": "String, Timestamp in ISO 8601 format",
  "updated_at": "String, Timestamp in ISO 8601 format",
  "address": "embedded Address document"
}
```

`currency` is determined by `lat`/`lon` information of the dealer, that
means a dealer located in Germany will automatically have `currency:
"EUR"`, a dealer in the USA `currency: "USD"`.

`type` can be one of `['bar', 'club', 'retail', 'restaurant', 'other', 'hackerspace', 'community']`

### Stock Entry

```json
{
  "status": "String, the status of that entry",
  "product": "embedded product",
  "price": "String",
  "quantity": "String",
  "special": "Boolean",
  "created_at": "String, Timestamp in ISO 8601 format",
}
```

`price` is in hundreds of the dealer's currency, for example a price of
`150` means that the item costs `1.5` of the respective currency.
If the `price` is unknown, the content will be `'?'`

`quantity` can be any of `['crate', 'piece', 'kg']`

`status` can be any of `['discontinued', 'sold-out', 'low', 'full', 'unknown']`

## Products API `products`

|                |                                                  |
|----------------|--------------------------------------------------|
| URL            | `/products`                                      |
| Request        | `GET`                                            |
| Parameter      | `None`                                           |
| URL-Parameter  | `type` (optional)                                |
| Result Type    | `application/json`                               |
| Result Status  | `200` if successful                              |
| Result Content | All products with embedded producer information  |

`type` is optional and can be one of `['soft-drink', 'alcoholic', 'supplies', 'leaves', 'food']`

### Example

Request: `/products`

```json
{
    "count": 2,
    "products": [
        {
            "name": "Club Mate 0.5l",
            "id": 1,
            "type": "soft-drink",
            "slug": "clubmate05",
            "producer": {
                "name": "Brauerei Loscher GmbH & Co. KG",
                "id": 1,
                "slug": "loscher",
                "address": {
                    "street": "Steigerwaldstrasse",
                    "number": "21-23",
                    "country": "Germany",
                    "city": "Münchsteinach",
                    "postal": "91481",
                    "lat": 49.64104,
                    "lon": 10.59215,
                    "web": "http://www.clubmate.de/",
                    "email": "info@club-mate.de",
                    "phone": "+49(0)9166607"
                }
            }
        },
        {
            "name": "Club Mate 0.33l",
            "id": 2,
            "type": "soft-drink",
            "slug": "clubmate033",
            "producer": {
                "name": "Brauerei Loscher GmbH & Co. KG",
                "id": 1,
                "slug": "loscher",
                "address": {
                    "street": "Steigerwaldstrasse",
                    "number": "21-23",
                    "country": "Germany",
                    "city": "Münchsteinach",
                    "postal": "91481",
                    "lat": 49.64104,
                    "lon": 10.59215,
                    "web": "http://www.clubmate.de/",
                    "email": "info@club-mate.de",
                    "phone": "+49(0)9166607"
                }
            }
        }
        ]
}
```

## Producer API `producers`

|                |                                                  |
|----------------|--------------------------------------------------|
| URL            | `/producers`                                     |
| Request        | `GET`                                            |
| Parameter      | `None`                                           |
| URL-Parameter  | `None`                                           |
| Result Type    | `application/json`                               |
| Result Status  | `200` if successful                              |
| Result Content | All producers with embedded address information  |

### Example

```json
{
    "count": 1,
    "producers": [
        {
            "name": "Brauerei Loscher GmbH & Co. KG",
            "id": 1,
            "slug": "loscher",
            "address": {
                "street": "Steigerwaldstrasse",
                "number": "21-23",
                "country": "Germany",
                "city": "Münchsteinach",
                "postal": "91481",
                "lat": 49.64104,
                "lon": 10.59215,
                "web": "http://www.clubmate.de/",
                "email": "info@club-mate.de",
                "phone": "+49(0)9166607"
            }
        }
    ]
}
```

## Producer API `producers/:id/products`

|                |                                                  |
|----------------|--------------------------------------------------|
| URL            | `/producers`                                     |
| Request        | `GET`                                            |
| Parameter      | `id` ID of the Producer                          |
| URL-Parameter  | `None`                                           |
| Result Type    | `application/json`                               |
| Result Status  | `200` if successful                              |
| Result Content | All products produced by that producer           |

### Example

```json
{
    "count": 2,
    "products": [
        {
            "name": "Club Mate 0.5l",
            "id": 1,
            "type": "soft-drink",
            "slug": "clubmate05",
            "producer": {
                "name": "Brauerei Loscher GmbH & Co. KG",
                "id": 1,
                "slug": "loscher",
                "address": {
                    "street": "Steigerwaldstrasse",
                    "number": "21-23",
                    "country": "Germany",
                    "city": "Münchsteinach",
                    "postal": "91481",
                    "lat": 49.64104,
                    "lon": 10.59215,
                    "web": "http://www.clubmate.de/",
                    "email": "info@club-mate.de",
                    "phone": "+49(0)9166607"
                }
            }
        },
        {
            "name": "Club Mate 0.33l",
            "id": 2,
            "type": "soft-drink",
            "slug": "clubmate033",
            "producer": {
                "name": "Brauerei Loscher GmbH & Co. KG",
                "id": 1,
                "slug": "loscher",
                "address": {
                    "street": "Steigerwaldstrasse",
                    "number": "21-23",
                    "country": "Germany",
                    "city": "Münchsteinach",
                    "postal": "91481",
                    "lat": 49.64104,
                    "lon": 10.59215,
                    "web": "http://www.clubmate.de/",
                    "email": "info@club-mate.de",
                    "phone": "+49(0)9166607"
                }
            }
        }
    ]
}
```

## Search API `search`

This is a simple search API. Currently only searches for Geo Locations
such as cities.

|                |                                                  |
|----------------|--------------------------------------------------|
| URL            | `/search`                                        |
| Request        | `GET`                                            |
| Parameter      | `None`                                           |
| URL-Parameter  | `query`                                          |
| Result Type    | `application/json`                               |
| Result Status  | `200` if found or `404` for no results           |
| Result Content | Latitude and Longitude for the query term        |

`query` is a search term like "Berlin" or "Stuttgart"

### Example

Request: `search?query=München`

```json
{
    "type": "GeoLocation",
    "lat": 48.1372719,
    "lon": 11.5754815
}

```

## Dealer API `dealers/:id`

|                |                                                  |
|----------------|--------------------------------------------------|
| URL            | `/dealers/:id`                                   |
| Request        | `GET`                                            |
| Parameter      | `id`, internal ID                                |
| URL-Parameter  | `None`                                           |
| Result Type    | `application/json`                               |
| Result Status  | `200` if found or `404` for no results           |
| Result Content | Dealer document with embedded address document   |

### Example

Request: `dealers/3416`

```json
{
    "name": "Exampledealer",
    "id": 3416,
    "type": "retail",
    "slug": "exampledealer",
    "currency": "EUR",
    "created_at": "2015-05-25T15:47:59+00:00",
    "address": {
        "street": "Schleißheimerstraße",
        "number": "42",
        "country": "Germany",
        "city": "München",
        "postal": "80797",
        "lat": 48.15075035,
        "lon": 11.5601324434141
    }
}
```

## Dealer API `dealers`

|                |                                                  |
|----------------|--------------------------------------------------|
| URL            | `/dealers`                                       |
| Request        | `POST`                                           |
| Parameter      | `None`                                           |
| URL-Parameter  | `None`                                           |
| Result Type    | `application/json`                               |
| Result Status  | `201` if successful                              |
| Result Content | Dealer document with embedded address document   |


Provide a JSON document of dealer (see [Dealer](#dealer)) and
make sure that you provide an embedded [Address](#address) document.
These attributes can not be entered directly using the API: `id`, `currency`, `created_at`, `updated_at`, `slug`.

The Dealer document needs to have at least: `name`, `type`, `address`

The Address document needs to have at least:

(`street`, `city`, `postal`, `country`) OR (`lat`, `lon`)

*Please note:* If all values (address + `lat`/`lon`) are supplied,
the `lat`/`lon` values take precedence.

Supplying only `lat`/`lon` can for example used in apps with a
"Create a dealer at my location" button.

### Example using a full address

Request: `dealers`

JSON Body:

```json
{
    "name": "Exampledealer",
    "type": "retail",
    "address": {
        "street": "Schleißheimerstraße",
        "number": "42",
        "country": "Germany",
        "city": "München",
        "postal": "80797"
    }
}
```

Response:

Result Status: `201 Created`

```json
{
    "name": "Exampledealer",
    "id": 3416,
    "type": "retail",
    "slug": "exampledealer",
    "currency": "EUR",
    "created_at": "2015-05-25T15:47:59+00:00",
    "address": {
        "street": "Schleißheimerstraße",
        "number": "42",
        "country": "Germany",
        "city": "München",
        "postal": "80797",
        "lat": 48.15075035,
        "lon": 11.5601324434141
    }
}
```

### Example using only lat & lon

Request: `dealers`

JSON Body:

```json
{
    "name": "Example Club",
    "note": "Created only using lat/lon",
    "type": "club",
    "address": {
        "lat": 48.1402569,
        "lon": 11.5609371
    }
}
```

Response:

Result Status: `201 Created`

```json
{
  "name": "Example Club",
  "id": 3423,
  "type": "club",
  "note": "Created only using lat/lon",
  "slug": "example-club",
  "currency": "EUR",
  "created_at": "2015-11-10T21:56:42+00:00",
  "address": {
    "street": "Bahnhofplatz",
    "country": "Germany",
    "city": "Munich",
    "postal": "80335",
    "lat": 48.1402569,
    "lon": 11.5609371
  }
}
```

|                |                                                  |
|----------------|--------------------------------------------------|
| URL            | `/dealers`                                       |
| Request        | `GET`                                            |
| Parameter      | `None`                                           |
| URL-Parameter  | `bbox` (optional)                                |
|                | `type` (optional)                                |
|                | `product` (optional)                             |
| Result Type    | `application/json`                               |
| Result Status  | `200` if successful                              |
| Result Content | Dealers document with embedded address document  |

`bbox` is String describing a bounding box in the format
`Latitude_Bottom_Left,Longitude_Bottom_Left,Latitude_Top_Right,Longitude_Top_Right`

Latitudes must be `>= -90` and `<= 90`, Longitudes `>= -180` and `<= 180`

`type` can be any combination of `['bar', 'club', 'retail', 'restaurant', 'other', 'hackerspace', 'community']` separated by commas,
e.g. `type=retail,club`

`product` can be a combination of product IDs separated by commas,
e.g. `product=1,3,5`

*Please note:* Requesting *all* dealers currently amounts to 1.3 MB,
please try to use the `bbox` parameter to keep the requests as small as
possible!

### Example

Search all clubs in Munich that sell product 2 (Club Mate 0.33)

Request: `dealers?bbox=47.858960625924794,11.21480941772461,48.38699007140056,11.880855560302734&product=2&type=club`

```json
{
    "count": 2,
    "dealers": [
        {
            "name": "Harry Klein",
            "id": 3415,
            "type": "club",
            "note": null,
            "slug": "harry-klein",
            "created_at": "2015-05-20T11:18:50+00:00",
            "updated_at": "2015-05-20T23:49:10+00:00",
            "address": {
                "street": "Sonnenstraße",
                "number": "8",
                "country": "Germany",
                "city": "München",
                "postal": "80331",
                "lat": 48.1375536,
                "lon": 11.5643507,
                "web": "http://harrykleinclub.de/",
                "email": "",
                "phone": ""
            },
            "currency": "EUR"
        },
        {
            "name": "Rote Sonne",
            "id": 1767,
            "type": "club",
            "note": "",
            "slug": "rote-sonne",
            "created_at": "2015-05-20T09:32:26+00:00",
            "updated_at": "2015-05-20T23:49:10+00:00",
            "address": {
                "street": "Maximiliansplatz",
                "number": "5",
                "country": "Germany",
                "city": "München",
                "postal": "80333",
                "lat": 48.142124,
                "lon": 11.5697566,
                "web": "http://www.rote-sonne.com/",
                "email": null,
                "phone": ""
            },
            "currency": "EUR"
        }
    ]
}
```

## Dealer API `dealers/:id`

|                |                                                  |
|----------------|--------------------------------------------------|
| URL            | `/dealers/:id`                                   |
| Request        | `PUT`                                            |
| Parameter      | `id`, internal ID                                |
| URL-Parameter  | `None`                                           |
| Result Type    | `application/json`                               |
| Result Status  | `200` if successful                              |
| Result Content | Dealer document with embedded address document   |

Simply provide a JSON document of dealer (see [Dealer](#dealer)) and
overwrite any attribute.

These attributes can not be changed directly using the API: `id`, `currency`, `slug`, `created_at`, `updated_at`.

When `lat` and `lon` are supplied and the values differ from the current
values, the position will be updated in the database.
This update will mark this address as updated using latitude and
longitude values. This means that any change to `street`, `number` etc.
will not change `lat` and `lon`.
This feature can be used to change the position of a dealer when the
position returned by the geocoding service is not correct.

*Please note:* The `slug` attribute can not be changed by changing the
dealer's name.

### Example changing address details

Request: `dealers/3416`

JSON Body:

```json
{
    "address": {
        "number": "23"
    }
}
```

Response:

Result Status: `200 OK`

```json
{
    "name": "Exampledealer",
    "id": 3416,
    "type": "retail",
    "slug": "exampledealer",
    "currency": "EUR",
    "created_at": "2015-05-25T15:47:59+00:00",
    "updated_at": "2015-05-26T14:45:45+00:00",
    "address": {
        "street": "Schleißheimerstraße",
        "number": "23",
        "country": "Germany",
        "city": "München",
        "postal": "80797",
        "lat": 48.15090955,
        "lon": 11.5598256792681
    }
}
```

*Please note:* This request changed `lat` and `lon` automatically since
the address has been updated.

### Example changing the position

Request: `dealers/3416`

JSON Body:

```json
{
    "address": {
        "lat": "48.15090955",
        "lon": "11.55982567"
    }
}
```

Response:

Result Status: `200 OK`

```json
{
    "name": "Exampledealer",
    "id": 3416,
    "type": "retail",
    "slug": "exampledealer",
    "currency": "EUR",
    "created_at": "2015-05-25T15:47:59+00:00",
    "updated_at": "2015-05-26T14:45:45+00:00",
    "address": {
        "street": "Schleißheimerstraße",
        "number": "42",
        "country": "Germany",
        "city": "München",
        "postal": "80797",
        "lat": 48.15090955,
        "lon": 11.55982567
    }
}
```

*Please note:* After this request any change to the address will not
update `lat`, `lon`. Only updates with `lat`/`lon` will change the
position!


## Dealer API `dealers/slug/:slug`


|                |                                                  |
|----------------|--------------------------------------------------|
| URL            | `/dealers/slug/:slug`                            |
| Request        | `GET`                                            |
| Parameter      | `slug`, short name of the dealer                 |
| URL-Parameter  | `None`                                           |
| Result Type    | `application/json`                               |
| Result Status  | `200` if found or `404` for no results           |
| Result Content | Dealer document with embedded address document   |

This request can be used to find a dealer that was
requested using a deep link (such as
http://matemonkey.com/map/dealer/exampledealer)

### Example

Request: `dealers/slug/exampledealer`

```json
{
    "name": "Exampledealer",
    "id": 3416,
    "type": "retail",
    "slug": "exampledealer",
    "currency": "EUR",
    "created_at": "2015-05-25T15:47:59+00:00",
    "address": {
        "street": "Schleißheimerstraße",
        "number": "42",
        "country": "Germany",
        "city": "München",
        "postal": "80797",
        "lat": 48.15075035,
        "lon": 11.5601324434141
    }
}
```

## Stock API `dealers/:id/stock`

|                |                                                  |
|----------------|--------------------------------------------------|
| URL            | `/dealers/:id/stock`                             |
| Request        | `GET`                                            |
| Parameter      | `id` ID of the Dealer                            |
| URL-Parameter  | `current`, Boolean                               |
| Result Type    | `application/json`                               |
| Result Status  | `200` if successful           |
| Result Content | Stock Entry Documents with embedded Product Documents  |

`current=true` means that only the latest stock entries will be
returned, omitting it will return *all* stock entries. This can be used
to e.g. draw a graph of the price/availability over time for that dealer.

### Example

Request: `api/v1/dealers/3416/stock?current=true`

```json
{
    "count": 2,
    "entries": [
        {
            "status": "full",
            "product": {
                "name": "Club Mate 0.5l",
                "id": 1,
                "type": "soft-drink",
                "slug": "clubmate05",
                "producer": {
                    "name": "Brauerei Loscher GmbH & Co. KG",
                    "id": 1,
                    "slug": "loscher",
                    "address": {
                        "street": "Steigerwaldstrasse",
                        "number": "21-23",
                        "country": "Germany",
                        "city": "Münchsteinach",
                        "postal": "91481",
                        "lat": 49.64104,
                        "lon": 10.59215,
                        "web": "http://www.clubmate.de/",
                        "email": "info@club-mate.de",
                        "phone": "+49(0)9166607"
                    }
                }
            },
            "price": 1337,
            "quantity": "crate",
            "special": false,
            "created_at": "2015-05-26T14:48:26+00:00"
        },
        {
            "status": "low",
            "product": {
                "name": "1337mate",
                "id": 25,
                "type": "soft-drink",
                "slug": "1337mate",
                "producer": {
                    "name": "1337 und so GmbH",
                    "id": 11,
                    "slug": "1337undsogmbh",
                    "address": {
                        "street": "Papendamm",
                        "number": "22",
                        "country": "Germany",
                        "city": "Hamburg",
                        "postal": "20146",
                        "lat": 53.56612,
                        "lon": 9.9755,
                        "web": "http://www.1337mate.com/",
                        "email": "info@leetmate.de",
                        "phone": "+49(0)4030720081"
                    }
                }
            },
            "price": 150,
            "quantity": "piece",
            "special": false,
            "created_at": "2015-05-26T14:48:38+00:00"
        }
    ]
}
```

|                |                                                  |
|----------------|--------------------------------------------------|
| URL            | `/dealers/:id/stock`                             |
| Request        | `POST`                                           |
| Parameter      | `id` ID of the Dealer                            |
| URL-Parameter  | `None`                                           |
| Result Type    | `application/json`                               |
| Result Status  | `201` if successful                              |
| Result Content | `None`                                           |

### JSON Body

```json
{
  "product": "Integer, ID of the product",
  "status": "String, Status of the product",
  "quantity": "String",
  "price": "Integer, Price of the product/quantity in hundreds",
  "special": "Boolean, True if the price is a special",
}

```

All values except `product` are optional to make the API as stateless as
possible.

* If only `product` is provided and this is the first entry for that
  dealer, `status` will default to `unknown`, `price` to '?' and `quantity`
  to `piece`.
* If only `product` and `status` are provided, the information of the
  last stock entry (sorted by the creation date) will be used to fill
  in `price` and `special`. This will automatically update all quantities
  of that product unless `quantity` is provided.
* If `price`  is provided, an independent stock entry will be created
  with `status` defaulting to `unknown`, `special` to `false`,
  `quantity` to `piece`.

For the possible values of `quantity`, `price` and `status`, see [the Stock Entry document specification](#stock-entry)

*Exception*: A price of `-1` means that the price is unknown. When the
entry is rendered, the price of that entry will be `'?'`.

### Example 1

Request: `api/v1/dealers/3416/stock`

JSON Body:

```json
{
  "product": 1,
  "status": "low"
}
```
This will set all entries for "Club Mate 0.5l" to low.
The response status is `201 Created` with an empty body.

### Example 2

Request: `api/v1/dealers/3416/stock`

JSON Body:

```json
{
  "product": 2
}
```

This will create a stock entry for "Club Mate 0.33l" with a price of
`-1`/`'?'` for one `piece` (`quantity`) and a `status` of `unknown`.

### Example 3

```json
{
  "status":"full",
  "product":2,
  "price":150,
  "quantity":"piece"
}
```

This sets the price to `1.50 EUR` (currency can be retrieved from
the dealer) for one `piece` and the status to `full` for "Club Mate 0.33l".
