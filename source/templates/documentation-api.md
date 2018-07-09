# 1 sentences about resources

## resourcename

---

overall about resource name

```http
GET {HOST}/api/v1/streamers
Content-Type: application/json
Authorization: JWT {token}
```

### Response

Response data are sent as `application/json`.

#### Success Response

```json
Response 200 OK
{
    "ok":true,
    "streamers":[
        {
            "analytic_stream_url":"http://100.10.10.1/123-res.mjpeg",
            "stream_name":"Gelora Bung Karno",
            "location":{"lat":"1231","long":"-101.1321"}
        },
        {
            "analytic_stream_url":"http://100.10.10.1/121-res.mjpeg",
            "stream_name":"Gelora Bung Karno",
            "location":{"lat":"1231","long":"-101.1321"}
        },
        ...
    ]
}
```

#### Error Response

Response 401 Not Authorized
token already expired or token is false

```json
{
  "message": "Not Authorized"
}
```

## Recent Recognition

---

get Recent Recognition

```http
GET {HOST}/api/v1/recent-recognitions/:type-recognition/:type-data
Content-Type: application/json
Authorization: JWT {token}
```

### Response

Response data are sent as `application/json`.

#### Success Response

**_GET_** {HOST}/api/v1/recent-recognitions/face/known

```json
Response 200 OK
{
    "ok":true,
    "cctvs":[
        {
            "thumbnail":"http://xxx.xxx.xxx/thumbnail.jpg",
            "name":"user1",
            "location_name":"Location 1",
            "location_stream":{"lat":"1231","long":"-101.1321"},
            "timestamp":"2018-01-11 14:44:55"
        },
        {
            "thumbnail":"http://xxx.xxx.xxx/thumbnail.jpg",
            "name":"user2",
            "location_name":"Location 2",
            "location_stream":{"lat":"1231","long":"-101.1321"},
            "timestamp":"2018-01-11 14:44:55"
        },
        ...
    ]
}
```

**_GET_** {HOST}/api/v1/recent-recognitions/face/unknown

```json
Response 200 OK
{
    "ok":true,
    "cctvs":[
        {
            "thumbnail":"http://xxx.xxx.xxx/thumbnail.jpg",
            "name":"user1",
            "location_name":"Location 1",
            "location_stream":{"lat":"1231","long":"-101.1321"},
            "timestamp":"2018-01-11 14:44:55"
        },
        {
            "thumbnail":"http://xxx.xxx.xxx/thumbnail.jpg",
            "name":"user2",
            "location_name":"Location 2",
            "location_stream":{"lat":"1231","long":"-101.1321"},
            "timestamp":"2018-01-11 14:44:55"
        },
        ...
    ]
}
```

**_GET_** {HOST}/api/v1/recent-recognitions/plate/known

```json
Response 200 OK
{
    "ok":true,
    "cctvs":[
        {
            "thumbnail":"http://xxx.xxx.xxx/thumbnail.jpg",
            "name":"user1",
            "location_name":"Location 1",
            "location_stream":{"lat":"1231","long":"-101.1321"},
            "timestamp":"2018-01-11 14:44:55"
        },
        {
            "thumbnail":"http://xxx.xxx.xxx/thumbnail.jpg",
            "name":"user2",
            "location_name":"Location 2",
            "location_stream":{"lat":"1231","long":"-101.1321"},
            "timestamp":"2018-01-11 14:44:55"
        },
        ...
    ]
}
```

#### Error Response

Response 400 Bad Request
usually :type-recognition or :type-data same with predefined

```json
{
  "ok": false,
  "error": {
    "code": "not-valid",
    "message": "something wrong"
  }
}
```

Response 401 Not Authorized
token already expired or token is false

```json
{
  "error": {
    "code": "not-authorized",
    "message": "user not authorized"
  }
}
```

# Errors

| Code | Description                                |
| ---- | ------------------------------------------ |
| 401  | Requested by user not Authorized           |
| 400  | Request parameter not same with predefined |
