# Overview
This repository will serve as the source to all information about the API of https://jordan-developers.com, please use the issues tracker to report issues.

## License
I'm not a lawyer, I don't know the words, but basically do whatever you want; just do not sue me, and use the API as is and on your own responsibility. 

## A Word Before You Start
That said, please be respectful of the service, this is a non-profit project and its only purpose is to help others. So, use it for good; help people find jobs, learn how to integrate an API with your app, and possibly monetize your app with ads.

Oh one more thing, while not mandatory, I'd appreciate when your app (web, or mobile) make it to production to put a credit somewhere and link back to https://jordan-developers.com. You can use some utm tags with your name and email so that I can thank you x))

Thanks! You're a nice person and I hope you'll enjoy using the API.

## Current Version
Currently I only have one version of the API and is hosted at `https://jordan-developers.com/api/v1`,  any breaking changes to the request/response will be implemented on a new `v2` endpoint.

_Note_: shit happens, if I someday push a breaking change, please submit an issue of how/where you're getting the error and prefix the issue title with `[bc]`.

## Schema
All API access is over HTTPS, and accessed from `https://jordan-developers.com/api`.  All data is sent and received as JSON. All responses will have their "meat" wrapped in a root-level `data` key, additional keys are sometimes provided based on context, like when pagination is available. All timetamps are returned in the format `YYYY-MM-DD HH:MM:SS` and will be in the `Asia\Amman` timezone (+2/+3 based on daylight saving).

### Summary Representations
When you fetch a list of resources, the response includes a *subset* of the attributes for that resource. This is the "summary" representation of the resource. (Some attributes are computationally expensive for the API to provide. For performance reasons, the summary representation excludes those attributes. To obtain those attributes, fetch the "detailed" representation.)

**Example**: When you get a list of job listings, you get the summary representation of each job. Here, we fetch the list of jobs

```
GET /jobs
```

### Detailed Representations
When you fetch an individual resource, the response typically includes *all* attributes for that resource. This is the "detailed" representation of the resource.

**Example**: When you get an individual job, you get the detailed representation of the job, including the job description. Here, we fetch the job of id 7:

```
GET /jobs/7
```

Below I'll provide an example response for each API method. The example response illustrates all attributes that are returned by that method.

## Pagination
Requests that return multiple items will be paginated to 15 items by default.  You can specify further pages with the `?page` parameter.

```
GET /api/v1/jobs?page=2
```

Note that page numbering is 1-based and that omitting the `?page` parameter will return the first page.

### Links
In most cases, try to avoid constructing pagination links yourself since it will make it harder for future upgrades, for this reason, the API will return the links already constructed for the **first**, **next**, **previous** and **last** pages of the current resource in the root-level `links` key in the response. 

```
"links": {
    "first": "https://jordan-developers.com/api/v1/jobs?page=1",
    "last": "https://jordan-developers.com/api/v1/jobs?page=3",
	"prev": null,
    "next": "https://jordan-developers.com/api/v1/jobs?page=2"
},
```

### Meta
In addition to the links, the API returns some meta information to enrich the context of the response, giving you an idea of where you stand in the resource, this will be returned in the root-level `meta` key in the response.

```
"meta": {
    "current_page": 1,
	"from": 1,
    "last_page": 3,
    "path": "https://jordan-developers.com/api/v1/jobs",
    "per_page": 15,
    "to": 15,
    "total": 33
}
```

## Rate Limiting
The API has rate limiting in-place, it allows for up to 60 requests per minute, requests are associated with the originating IP address.

The returned HTTP headers of any API request show your current rate limit status:

```
$ curl -I -H "User-Agent: docs@example.com" https://jordan-developers.com/api/v1/jobs
HTTP/1.1 200 OK
....
X-RateLimit-Limit: 60
X-RateLimit-Remaining: 59
```

`X-RateLimit-Limit` will show the maximum number of requests you're permitted to make per minute, while `X-RateLimit-Remaining` will show the number of requests remaining in the current rate limit window.

Exceeding the rate-limit will result in a `HTTP 429 Too Many Requests` error

```
$ curl -I -H "User-Agent: docs@example.com" https://jordan-developers.com/api/v1/jobs
HTTP/1.1 429 Too Many Requests
```

_Note_: If you exceed your rate limit, you can likely fix the issue by caching the API responses.

## User-Agent Required
All requests MUST include a valid `User-Agent` header containing a valid, working email address. Requests with no `User-Agent` header, or with a one not containing an email address will be rejected with a `HTTP 400 Bad Request` error. This allows me to contact you, the app developer, if there are problems, or pat you on the back if you're doing something awesome.

## Endpoints
For easier integration, a [Postman](https://www.postman.com) collection is provided [here](postman-collection.json), you'll find a list of all the API endpoints, in addition to a sample request, sample response for each one.
