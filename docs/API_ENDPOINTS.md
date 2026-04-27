# API Endpoints Documentation

## Base URL

```
http://localhost:8080/api/v1/
```

## Authentication

Currently, the API does not require authentication for public endpoints. Future versions may implement API token authentication.

## Response Format

All responses are in JSON format.

### Success Response (2xx)

```json
{
  "count": 100,
  "next": "http://localhost:8080/api/v1/stations/?page=2",
  "previous": null,
  "results": [
    {
      "id": 75056,
      "name": "Paris-Montsouris",
      "latitude": 48.8155,
      "longitude": 2.3359,
      ...
    }
  ]
}
```

### Error Response (4xx, 5xx)

```json
{
  "detail": "Not found."
}
```

## Endpoints

### Stations

#### List all stations

```
GET /stations/
```

**Parameters:**
| Name | Type | Description |
|------|------|-------------|
| `page` | integer | Page number (default: 1) |
| `limit` | integer | Results per page (default: 20) |
| `name` | string | Filter by station name |
| `latitude` | float | Filter by latitude |
| `longitude` | float | Filter by longitude |

**Example:**

```bash
curl -X GET "http://localhost:8080/api/v1/stations/?limit=10" \
  -H "Accept: application/json"
```

**Response:**

```json
{
  "count": 156,
  "next": "http://localhost:8080/api/v1/stations/?page=2&limit=10",
  "previous": null,
  "results": [
    {
      "id": 75056,
      "name": "Paris-Montsouris",
      "code": "75056",
      "latitude": 48.8155,
      "longitude": 2.3359,
      "altitude": 75
    }
  ]
}
```

#### Get station details

```
GET /stations/{id}/
```

**Parameters:**
| Name | Type | Description |
|------|------|-------------|
| `id` | integer | Station ID (required, path parameter) |

**Example:**

```bash
curl -X GET "http://localhost:8080/api/v1/stations/75056/" \
  -H "Accept: application/json"
```

**Response:**

```json
{
  "id": 75056,
  "name": "Paris-Montsouris",
  "code": "75056",
  "latitude": 48.8155,
  "longitude": 2.3359,
  "altitude": 75,
  "url": "http://localhost:8080/api/v1/stations/75056/"
}
```

---

### Daily Records (Quotidienne)

#### List daily weather records

```
GET /quotidienne/
```

**Parameters:**
| Name | Type | Description |
|------|------|-------------|
| `page` | integer | Page number |
| `limit` | integer | Results per page (max: 100) |
| `station` | integer | Filter by station ID |
| `date` | date | Filter by date (YYYY-MM-DD) |
| `date_after` | date | Records after date |
| `date_before` | date | Records before date |

**Example:**

```bash
curl -X GET "http://localhost:8080/api/v1/quotidienne/?station=75056&limit=5" \
  -H "Accept: application/json"
```

**Response:**

```json
{
  "count": 5000,
  "next": "http://localhost:8080/api/v1/quotidienne/?page=2&station=75056&limit=5",
  "previous": null,
  "results": [
    {
      "id": 1,
      "station": 75056,
      "date": "2024-01-01",
      "temperature_min": 2.5,
      "temperature_max": 8.3,
      "precipitation": 0.0,
      "wind_speed": 5.2
    }
  ]
}
```

---

### National Indicator

#### List national indicators

```
GET /national_indicator/
```

**Parameters:**
| Name | Type | Description |
|------|------|-------------|
| `page` | integer | Page number |
| `limit` | integer | Results per page |
| `date` | date | Filter by date |
| `period` | string | daily, weekly, monthly |

**Example:**

```bash
curl -X GET "http://localhost:8080/api/v1/national_indicator/?limit=10" \
  -H "Accept: application/json"
```

**Response:**

```json
{
  "count": 1000,
  "next": "http://localhost:8080/api/v1/national_indicator/?page=2",
  "previous": null,
  "results": [
    {
      "id": 1,
      "date": "2024-01-01",
      "indicator_type": "temperature_mean",
      "value": 5.8,
      "unit": "°C"
    }
  ]
}
```

---

### Temperature Deviation

#### List temperature deviations/anomalies

```
GET /temperature_deviation/
```

**Parameters:**
| Name | Type | Description |
|------|------|-------------|
| `page` | integer | Page number |
| `limit` | integer | Results per page |
| `station` | integer | Filter by station ID |
| `date` | date | Filter by date |
| `min_deviation` | float | Minimum deviation threshold |
| `max_deviation` | float | Maximum deviation threshold |

**Example:**

```bash
curl -X GET "http://localhost:8080/api/v1/temperature_deviation/?station=75056&limit=5" \
  -H "Accept: application/json"
```

**Response:**

```json
{
  "count": 300,
  "next": "http://localhost:8080/api/v1/temperature_deviation/?page=2&station=75056&limit=5",
  "previous": null,
  "results": [
    {
      "id": 1,
      "station": 75056,
      "date": "2024-01-15",
      "normal_temperature": 3.2,
      "actual_temperature": 8.5,
      "deviation": 5.3,
      "deviation_percentage": 165.6
    }
  ]
}
```

---

## Common Query Patterns

### Get latest records for a station

```bash
curl -X GET "http://localhost:8080/api/v1/quotidienne/?station=75056&limit=1&ordering=-date" \
  -H "Accept: application/json"
```

### Get all records for a date range

```bash
curl -X GET "http://localhost:8080/api/v1/quotidienne/?station=75056&date_after=2024-01-01&date_before=2024-01-31" \
  -H "Accept: application/json"
```

### Get high temperature deviations

```bash
curl -X GET "http://localhost:8080/api/v1/temperature_deviation/?min_deviation=3.0" \
  -H "Accept: application/json"
```

### Get all stations in a region

```bash
curl -X GET "http://localhost:8080/api/v1/stations/?latitude__range=48.5,49.5&longitude__range=2.0,3.0" \
  -H "Accept: application/json"
```

---

## HTTP Status Codes

| Code  | Meaning                                              |
| ----- | ---------------------------------------------------- |
| `200` | OK - Request successful                              |
| `201` | Created - Resource created                           |
| `204` | No Content - Request successful, no content returned |
| `400` | Bad Request - Invalid query parameters               |
| `401` | Unauthorized - Authentication required               |
| `403` | Forbidden - Access denied                            |
| `404` | Not Found - Resource not found                       |
| `500` | Internal Server Error - Server error                 |
| `503` | Service Unavailable - Server temporarily unavailable |

---

## Pagination

All list endpoints support pagination with the following parameters:

- `page`: Page number (1-indexed)
- `limit`: Number of results per page (default: 20, max: 100)

**Example:**

```bash
# Get page 2 with 50 results per page
curl "http://localhost:8080/api/v1/stations/?page=2&limit=50"
```

**Response includes:**

- `count`: Total number of items
- `next`: URL for next page (null if last page)
- `previous`: URL for previous page (null if first page)
- `results`: Array of items

---

## Filtering

Most endpoints support field filtering with query parameters:

- Exact match: `?field=value`
- Range: `?field__range=min,max`
- Greater than: `?field__gt=value`
- Less than: `?field__lt=value`
- Contains: `?field__contains=value`

**Example:**

```bash
# Filter stations by latitude range
curl "http://localhost:8080/api/v1/stations/?latitude__range=48,49"

# Filter records by temperature range
curl "http://localhost:8080/api/v1/quotidienne/?temperature_max__gt=20"
```

---

## Ordering

Results can be ordered using the `ordering` parameter:

- Ascending: `?ordering=field`
- Descending: `?ordering=-field`

**Example:**

```bash
# Order by date descending (newest first)
curl "http://localhost:8080/api/v1/quotidienne/?ordering=-date"

# Order by temperature ascending
curl "http://localhost:8080/api/v1/quotidienne/?ordering=temperature_min"
```

---

## API Documentation UI

### Swagger UI (Interactive)

- URL: `http://localhost:8080/api/v1/docs/`
- Try out endpoints directly from the browser
- See request/response examples

### ReDoc (Read-only)

- URL: `http://localhost:8080/api/v1/redoc/`
- Beautiful, user-friendly documentation

### OpenAPI Schema (Raw)

- URL: `http://localhost:8080/api/v1/schema/`
- JSON format for programmatic access

---

## Rate Limiting

Currently, no rate limiting is implemented. Future versions may include:

- Request rate limits
- Pagination limits
- IP-based throttling

---

## Error Handling

### Example Error Response

```bash
$ curl -X GET "http://localhost:8080/api/v1/stations/99999/"

HTTP/1.1 404 Not Found
Content-Type: application/json

{
  "detail": "Not found."
}
```

### Common Errors

**Invalid query parameter:**

```json
{
  "error": "Invalid value for 'limit': must be integer"
}
```

**Missing required field:**

```json
{
  "error": "Field 'date' is required"
}
```

---

## Performance Tips

1. **Use pagination**: Limit results per page (20-100)
2. **Filter data**: Use query parameters to reduce results
3. **Cache responses**: Implement client-side caching for repeated requests
4. **Use specific endpoints**: Fetch only needed data
5. **Monitor response times**: Check Prometheus metrics for slow queries

---

## Examples

### Python (requests)

```python
import requests

# Get all stations
response = requests.get('http://localhost:8080/api/v1/stations/')
stations = response.json()['results']

# Get specific station
response = requests.get('http://localhost:8080/api/v1/stations/75056/')
station = response.json()
print(station['name'])
```

### JavaScript (fetch)

```javascript
// Get all stations
const response = await fetch("http://localhost:8080/api/v1/stations/");
const data = await response.json();
console.log(data.results);

// Get specific station
const stationResponse = await fetch(
  "http://localhost:8080/api/v1/stations/75056/",
);
const station = await stationResponse.json();
console.log(station.name);
```

### cURL

```bash
# Get all stations
curl -X GET "http://localhost:8080/api/v1/stations/" \
  -H "Accept: application/json" | jq

# Get specific station with pretty formatting
curl -s "http://localhost:8080/api/v1/stations/75056/" | jq '.name'
```

---

## Support

For API issues or feature requests:

- Check the [main documentation](README_EN.md)
- Review [GitHub Issues](https://github.com/Lydia-BEDRI/InfoClimat/issues)
- Contact: See [SECURITY.md](../SECURITY.md)
