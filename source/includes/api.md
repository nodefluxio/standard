# Restful API

How To Create Nodeflux Restful API

## Terminologies

#### Resource

Is an object or representation of something, which has some associated data with it and there can be set of methods to operate on it. E.g. Animals, schools and employees are resources and delete, add, update are the operations to be performed on these resources.

#### Collections

Are set of resources, e.g Companies is the collection of Company resource.

#### URL

(Uniform Resource Locator) is a path through which a resource can be located and some actions can be performed on it.

## Request

### HTTP Methods (verbs)

#### The important HTTP methods are as follows:

1.  **_GET_** method requests data from the resource and should not produce any side effect.
    E.g /companies/3/employees returns list of all employees from company 3.
2.  **_POST_** method requests the server to create a resource in the database, mostly when a web form is submitted.
    E.g /companies/3/employees creates a new Employee of company 3.
    POST is non-idempotent which means multiple requests will have different effects.
3.  **_PUT_** method requests the server to update resource or create the resource, if it doesn’t exist.
    E.g. /companies/3/employees/john will request the server to update, or create if doesn’t exist, the john resource in employees collection under company 3.
    PUT is idempotent which means multiple requests will have the same effects.
4.  **_DELETE_** method requests that the resources, or its instance, should be removed from the database.
    E.g /companies/3/employees/john/ will request the server to delete john resource from the employees collection under the company 3.
    There are few other methods which we will discuss in another post.

#### Accept serialized JSON in request bodies

Accept serialized JSON on **_PUT_**/**_PATCH_**/**_POST_** request bodies, either instead of or in addition to form-encoded data. This creates symmetry with JSON-serialized response bodies, e.g.:

```shell
$ curl -X POST https://service.com/apps \
    -H "Content-Type: application/json" \
    -d '{"name": "demoapp"}'
```

```json
{
  "ok":true,
  "some_detail":{
    "id": "01234567-89ab-cdef-0123-456789abcdef",
    "name": "demoapp",
    "owner": {
      "email": "username@example.com",
      "id": "01234567-89ab-cdef-0123-456789abcdef"
    },
  }

  ...
}
```

note: please add "ok" attribute for adding status for API true or false

### API Endpoint

#### Resource Names

Use the plural version of a resource name unless the resource in question is a singleton within the system (for example, the overall status of the system might be `/status`). This keeps it consistent in the way you refer to particular resources.

#### Explanation Resource Names (with example)

Let’s write few APIs for Companies which has some Employees, to understand more.
Url path `/getAllEmployees` is an API which will respond with the list of employees.
Few more APIs around a Company will look like as follows:

```
/addNewEmployee
/updateEmployee
/deleteEmployee
/deleteAllEmployees
/promoteEmployee
/promoteAllEmployees
```

And there will be tons of other API endpoints like these for different operations. All of those will contain many redundant actions. Hence, all these API endpoints would be burdensome to maintain, when API count increases.

**_What is wrong?_**
The URL should only contain resources(nouns) not actions or verbs. The API path/addNewEmployee contains the action addNew along with the resource name Employee.

**_Then what is the correct way?_**
/companies endpoint is a good example, which contains no action. But the question is how do we tell the server about the actions to be performed on companies resource viz. whether to add, delete or update?

This is where the HTTP methods (**_GET_**, **_POST_**, **_DELETE_**, **_PUT_**), also called as verbs, play the role.

The resource should always be plural in the API endpoint and if we want to access one instance of the resource, we can always pass the id in the URL.

method `GET` path `/companies` should get the list of all companies
method `GET` path `/companies/34` should get the detail of company 34
method `DELETE` path `/companies/34` should delete company 34
In few other use cases, if we have resources under a resource, e.g Employees of a Company, then few of the sample API endpoints would be:

- `GET /companies/3/employees` should get the list of all employees from company 3
- `GET /companies/3/employees/45` should get the details of employee 45, which belongs to company 3
- `DELETE /companies/3/employees/45` should delete employee 45, which belongs to company 3
- `POST /companies` should create a new company and return the details of the new company created

Isn’t the APIs are now more precise and consistent? 😎

Conclusion: The paths should contain the plural form of resources and the HTTP method should define the kind of action to be performed on the resource.

#### Actions

Prefer endpoint layouts that don’t need any special actions for individual resources. In cases where special actions are needed, place them under a standard actions prefix, to clearly delineate them:

```
/resources/:resource/actions/:action
```

e.g.

```
/runs/{run_id}/actions/stop
```

#### Lowercase paths and attributes

Use Lowercase and dash-separated path names, for alignment with hostnames, e.g:

```
service-api.com/users
service-api.com/app-setups
```

Lowercase attributes as well, but use underscore separators so that attribute names can be typed without quotes in JavaScript, e.g.:

```
service_class: "first"
```

#### Support non-id dereferencing for convenience

In some cases it may be inconvenient for end-users to provide IDs to identify a resource. For example, a user may think in terms of a Heroku app name, but that app may be identified by a UUID. In these cases you may want to accept both an id or name, e.g.:

```
$ curl https://service.com/apps/{app_id_or_name}
$ curl https://service.com/apps/97addcf0-c182
$ curl https://service.com/apps/www-prod
```

Do not accept only names to the exclusion of IDs.

#### Minimize path nesting

In data models with nested parent/child resource relationships, paths may become deeply nested, e.g.:

```
/orgs/{org_id}/apps/{app_id}/dynos/{dyno_id}
```

Limit nesting depth by preferring to locate resources at the root path. Use nesting to indicate scoped collections. For example, for the case above where a dyno belongs to an app belongs to an org:

```
/orgs/{org_id}
/orgs/{org_id}/apps
/apps/{app_id}
/apps/{app_id}/dynos
/dynos/{dyno_id}
```

## Response

#### Return appropriate status codes

Return appropriate HTTP status codes with each response. Successful responses should be coded according to this guide:

- `200` : Request succeeded for a **_GET_**, **_POST_**, **_DELETE_**, or **_PATCH_** call that completed synchronously, or a PUT call that synchronously updated an existing resource
- `201` : Request succeeded for a **_POST_**, or **_PUT_** call that synchronously created a new resource. It is also best practice to provide a 'Location' header pointing to the newly created resource. This is particularly useful in the POST context as the new resource will have a different URL than the original request.
- `202`: Request accepted for a **_POST_**, **_PUT_**, **_DELETE_**, or **_PATCH_** call that will be processed asynchronously
- `206`: Request succeeded on GET, but only a partial response returned: see above on ranges

Pay attention to the use of authentication and authorization error codes:

- `400` Some Error that produce that not validated by system, but cannot accepted by system
- `401` Unauthorized: Request failed because user is not authenticated
- `403` Forbidden: Request failed because user does not have authorization to access a specific resource

Return suitable codes to provide additional information when there are errors:

- `422` Unprocessable Entity: Your request was understood, but contained invalid parameters (for form validation)
- `429` Too Many Requests: You have been rate-limited, retry later
- `500` Internal Server Error: Something went wrong on the server, check status site and/or report the issue

Refer to the HTTP response code spec for guidance on status codes for user error and server error cases.

#### Provide full resources where available

Provide the full resource representation (i.e. the object with all attributes) whenever possible in the response. Always provide the full resource on 200 and 201 responses, including PUT/PATCH and DELETE requests, e.g.:

```
$ curl -X DELETE \
  https://service.com/apps/1f9b/domains/0fd4

HTTP/1.1 200 OK
Content-Type: application/json;charset=utf-8
...
{
  "created_at": "2012-01-01T12:00:00Z",
  "hostname": "subdomain.example.com",
  "id": "01234567-89ab-cdef-0123-456789abcdef",
  "updated_at": "2012-01-01T12:00:00Z"
}
```

202 responses will not include the full resource representation, e.g.:

```shell
$ curl -X DELETE \
  https://service.com/apps/1f9b/dynos/05bd

HTTP/1.1 202 Accepted
Content-Type: application/json;charset=utf-8
...
{}
```

#### Provide resource (UU)IDs

Give each resource an id attribute by default. Use UUIDs unless you have a very good reason not to. Don’t use IDs that won’t be globally unique across instances of the service or other resources in the service, especially auto-incrementing IDs.
Render UUIDs in downcased 8-4-4-4-12 format, e.g.:

```JSON
"id": "01234567-89ab-cdef-0123-456789abcdef"
```

#### Provide standard timestamps

Provide created_at and updated_at timestamps for resources by default, e.g:

```JSON
{
  // ...
  "created_at": "2012-01-01T12:00:00Z",
  "updated_at": "2012-01-01T13:00:00Z",
  // ...
}
```

These timestamps may not make sense for some resources, in which case they can be omitted.

#### Use UTC times formatted in ISO8601

Accept and return times in UTC only. Render times in ISO8601 format, e.g.:

```JSON
"finished_at": "2012-01-01T12:00:00Z"
```

#### Nest foreign key relations

Serialize foreign key references with a nested object, e.g.:

```JSON
{
  "ok":true,
  "some_detail":{
    "name": "service-production",
    "owner": {
      "id": "5d8201b0..."
    },
  // ...
  }
}
```

Instead of e.g.:

```JSON
{
  "name": "service-production",
  "owner_id": "5d8201b0...",
  // ...
}
```

This approach makes it possible to inline more information about the related resource without having to change the structure of the response or introduce more top-level response fields, e.g.:

```JSON
{
  "ok":true,
  "some_detail":{
    "name": "service-production",
    "owner": {
      "id": "5d8201b0...",
      "name": "Alice",
      "email": "alice@heroku.com"
    },
  }
  // ...
}
```

#### Generate structured result json

Generate consistent,structured response bodies for send result

#### Naming convention

```JSON
  {
    "ok":true,
    "streamers":{
      "url_streamer":"http://xxx.xxx.xxx",
      "additional_config":{ // using snake_case for attribute
        "threshold":3,
        "max":1
      }
    }
  }
```

##### For Data List format

```shell
$ curl -X GET \
  https://localhost/streamers

HTTP/1.1 200 OK
Content-Type: application/json;charset=utf-8
...
{}
```

```JSON
{
  "ok":true,
  "streamers":[
    {
    ... // for detail
    },
    {
    ... // for detail
    },
    {
    ... // for detail
    }
  ],
}
```

##### For Create or Update format

```shell
$ curl -X POST / PUT  \
  https://localhost/streamers

HTTP/1.1 201 OK
Content-Type: application/json;charset=utf-8
...
{}
```

```JSON
{
  "ok":true,
  "stream":{
    ... // for detail
  },
}
```

##### For Get Detail format

```shell
$ curl -X GET  \
  https://localhost/streamers/{id}

HTTP/1.1 201 OK
Content-Type: application/json;charset=utf-8
...
{}
```

```JSON
{
  "ok":true,
  "stream":{
    ... // for detail
  },
}
```

#### Generate structured errors

Generate consistent, structured response bodies on errors. Include a machine-readable error id, a human-readable error message, and optionally a url pointing the client to further information about the error and how to resolve it, e.g.:

```
HTTP/1.1 429 Too Many Requests
```

```JSON
{
  "ok":false,
  "error":{
    "code":"rate_limit",
    "message": "Account reached its API rate limit.",
    "url":     "https://docs.service.com/rate-limits" // for documentation
  },
}
```

Document your error format and the possible error ids that clients may encounter.

#### Show rate limit status

Rate limit requests from clients to protect the health of the service and maintain high service quality for other clients. You can use a token bucket algorithm to quantify request limits.
Return the remaining number of request tokens with each request in the RateLimit-Remaining response header.

### Keep JSON minified in all responses

Extra whitespace adds needless response size to requests, and many clients for human consumption will automatically "prettify" JSON output. It is best to keep JSON responses minified e.g.:

```JSON
{"beta":false,"email":"alice@heroku.com","id":"01234567-89ab-cdef-0123-456789abcdef","last_login":"2012-01-01T12:00:00Z","created_at":"2012-01-01T12:00:00Z","updated_at":"2012-01-01T12:00:00Z"}
```

Instead of e.g.:

```JSON
{
  "beta": false,
  "email": "alice@heroku.com",
  "id": "01234567-89ab-cdef-0123-456789abcdef",
  "last_login": "2012-01-01T12:00:00Z",
  "created_at": "2012-01-01T12:00:00Z",
  "updated_at": "2012-01-01T12:00:00Z"
}
```

You may consider optionally providing a way for clients to retrieve more verbose response, either via a query parameter (e.g. `?pretty=true`) or via an `Accept` header param (e.g. `Accept: application/vnd.heroku+json; version=3; indent=4;`).

### Structure Folder for Restful API

```shell
  projectroot
    config
    bin
    src
      v{versioning}
        controllers
        models
        routes
        services
      v{versioning}
        controllers
        models
        routes
        services
```

Note:

- config for the called environment called as module
- Must using version so url called must be /v1 or /v2
- controllers for application logic
- controllers and services if too long must devide to small chunk function per file insider folder
- models for data structure and ORM for binding to database
- routes for validation and routing logic
- services for all general function

### Naming Function for controllers

Must use format:

```
{get|create|{delete/remove}|update}{List|Single}{name_data_will_get}
ex:
  getListVenue
  getListStreamer
  getDetailCctv
  createVenue
  createCctvConfig
  deleteConfig
  updateEnrollmentData
```

##### Example Format Documentation API

[`API Documentation`](../templates/documentation-api.md)

##### Reference

https://github.com/WhiteHouse/api-standards
https://geemus.gitbooks.io/http-api-design/content/en/
https://hackernoon.com/restful-api-designing-guidelines-the-best-practices-60e1d954e7c9
