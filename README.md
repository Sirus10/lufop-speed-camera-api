# Lufop Speed Camera API

Integrate speed camera and road hazard data into GPS devices, navigation apps, mapping tools and mobility services.

The Lufop API lets you retrieve data by country, city or GPS coordinates. Responses are available in JSON and XML. Pro and Business plans also include compact JSONC, advanced synchronization options and higher usage limits.

- **Free:** test the API and access data for France, Belgium and Switzerland.
- **Pro:** access additional countries, higher quotas, compact JSONC and the `since` parameter.
- **Business:** support higher-volume applications with priority server resources.

Data coverage and detail may vary by country. The database is regularly updated and enriched through Lufop’s data processes and community contributions. The API is not a guaranteed real-time service.

## Quick start

Create a free Lufop account and request an API key from [api.lufop.net](https://api.lufop.net/en). No credit card is required for the Free plan.

Make your first request:

```bash
# Replace YOUR_API_KEY with the key from your Lufop dashboard.
curl "https://api.lufop.net/api?key=YOUR_API_KEY&format=json&nbr=10&pays=fr"
```

The API key is passed as the `key` query parameter. Keep production keys out of public repositories. Where possible, make API requests through your server rather than exposing a key in a public client application.

## Base URL

```text
https://api.lufop.net/api
```

The read endpoint uses HTTP GET requests. Add your API key and the parameters for the data you need.

## Request parameters

| Parameter | Description | Availability |
|---|---|---|
| `key` | Your personal API key. Required for API requests. | All plans |
| `format` | Response format: `json`, `xml` or `meta`. `jsonc` is also available on Pro and Business. | `json`, `xml`, `meta`: all plans; `jsonc`: Pro and Business |
| `pays` | Country code, such as `fr`, `it` or `es`. | Country access depends on your plan |
| `pays=ALL` | Request data across all available countries. | Pro and Business |
| `nbr` | Maximum number of results returned by a request. | Free: up to 200; Pro: up to 10,000; Business: up to 30,000 |
| `since` | Return records updated since a date, in `YYYY-MM-DD` format. | Pro and Business |
| `q` | GPS coordinates in `latitude,longitude` format. | All plans |
| `m` | Approximate geographic search margin around `q`. This uses a latitude/longitude bounding box, not an exact circular distance. | All plans |
| `c` | City or municipality name, for example `narbonne`. | All plans |

### Geographic search

Use `q` to specify a location and `m` to set the approximate search margin:

```bash
# Example: search around Lyon. The margin is approximate, not an exact radius.
curl "https://api.lufop.net/api?key=YOUR_API_KEY&format=json&q=45.75,4.85&m=100"
```

The `m` value is an internal margin. As a rough guide, `m=10` corresponds to about 1 km. The search area is a bounding box, so it is not a precise distance calculation.

## Response formats

### JSON

JSON returns an array of camera records:

```json
[
  {
    "ID": "172755",
    "name": "FR Radar Fixe FR 70",
    "lat": 47.7235,
    "lng": -0.00997,
    "type": "18",
    "commune": "Clermont-Créans",
    "voie": "D323",
    "flash": "B",
    "emplacement": "R",
    "azimut": "210",
    "update": "2023-02-11 10:24:54",
    "vitesse": "80"
  }
]
```

Fields can vary according to the record and available data. Common fields include:

- `ID`: unique record identifier
- `name`: record name
- `lat`, `lng`: latitude and longitude
- `type`: internal camera type
- `commune`: city or municipality
- `voie`: road or street
- `vitesse`: speed limit, when known
- `flash`: flash direction
- `emplacement`: camera position
- `azimut`: camera orientation
- `update`: last recorded update

The `azimut` value represents the camera’s orientation or flash direction. It is not necessarily the direction of traffic flow, and may be approximate.

### XML

Set `format=xml` to receive XML:

```text
https://api.lufop.net/api?key=YOUR_API_KEY&format=xml&nbr=2
```

The response contains camera records inside a `<markers>` element.

### Compact JSONC

The compact `jsonc` format is available on Pro and Business plans. It separates field names from record values to reduce repeated data and payload size.

```text
https://api.lufop.net/api?key=YOUR_API_KEY&format=jsonc&nbr=2
```

The response contains:

- `s`: the list of field names
- `d`: the record values, arranged in the same order

Use the field-name list to map each value to its corresponding property.

### API metadata

Use `format=meta` to retrieve API metadata, including supported countries, formats, parameters, plan information and current usage:

```text
https://api.lufop.net/api?key=YOUR_API_KEY&format=meta
```

A key is required, but this metadata request does not consume the regular API quota.

## Examples

### Get speed camera records in France

```bash
# Limit the response to 100 records.
curl "https://api.lufop.net/api?key=YOUR_API_KEY&format=json&nbr=100&pays=fr"
```

### Get records for a city

```bash
curl "https://api.lufop.net/api?key=YOUR_API_KEY&format=xml&c=narbonne"
```

### Get records near a location

```bash
# Example coordinates: Lyon. Adjust q and m for your search.
curl "https://api.lufop.net/api?key=YOUR_API_KEY&q=45.75,4.85&m=100"
```

### Retrieve records updated since a date

The `since` parameter is available on Pro and Business plans:

```bash
curl "https://api.lufop.net/api?key=YOUR_API_KEY&since=2024-01-01"
```

### Request multiple countries

The `pays=ALL` option is available on Pro and Business plans:

```bash
curl "https://api.lufop.net/api?key=YOUR_API_KEY&format=json&pays=ALL&nbr=1000"
```

## Plan limits

| Feature | Free | Pro | Business |
|---|---:|---:|---:|
| Requests per day | 200 | 5,000 | No fixed daily quota |
| Requests per minute | 10 | 200 | No fixed limit |
| Maximum results per request | 200 | 10,000 | 30,000 |
| Country access | France, Belgium, Switzerland | All available countries | All available countries |
| JSON and XML | Yes | Yes | Yes |
| Compact JSONC | No | Yes | Yes |
| `since` parameter | No | Yes | Yes |
| `pays=ALL` | No | Yes | Yes |
| Priority server resources | No | No | Yes |
| Priority support | No | Email support | Yes |

For current prices and full details, visit the [Lufop API pricing page](https://api.lufop.net/pricing).

Requests beyond the Free plan quota are blocked until the quota resets. The Free plan does not automatically charge for additional requests.

## Submit a speed camera

The API also provides an endpoint for submitting a camera record for review.

```text
POST https://api.lufop.net/api_in.php?key=YOUR_API_KEY
Content-Type: application/json
```

Example JSON body:

```json
{
  "lat": 45.123456,
  "lng": 5.987654,
  "type": "fixed",
  "speed": 80,
  "user": "8f2c8a25-3b77-4924-a23b-1b2be742ee34",
  "comment": "Camera facing Grenoble direction"
}
```

Required fields:

- `lat`: latitude
- `lng`: longitude
- `type`: `fixed`, `mobile`, `construction` or `redlight`
- `user`: anonymous UUID generated by the client

Optional fields:

- `speed`: speed limit, for fixed cameras
- `comment`: additional information

Submitted records are enriched with geographic information and placed in a review queue. A successful response means the record is pending review; it does not mean the record has already been published.

## Open data and licensing

Lufop data is made available under the [Open Database License (ODbL) 1.0](https://opendatacommons.org/licenses/odbl/1-0/).

The license permits commercial use, modification and redistribution, subject to its conditions. These include attribution and, where applicable, share-alike requirements for publicly shared derivative databases.

The API plans cover the technical service—such as request quotas, advanced features, server resources and support. They do not change the rights granted by the data license. A complete database export is also available separately from the API.

Suggested attribution:

> Data: Lufop.net and OpenStreetMap contributors — ODbL 1.0

## Data use notice

Lufop data is provided for informational purposes. Records may be incomplete, approximate or out of date. Always follow road signs and applicable traffic regulations.

## Links

- [Lufop API homepage](https://api.lufop.net/en)
- [API documentation](https://api.lufop.net/en#params)
- [Plans and pricing](https://api.lufop.net/pricing)
- [Terms of use and privacy policy](https://api.lufop.net/conditions.php)
- [Lufop support forum](https://lufop.net/forum/viewtopic.php?p=23916)

---

The Lufop API is developed and maintained for the open data community. Contributions and feedback are welcome.
