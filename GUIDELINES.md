# Cosmo API Design Guidelines

## JSON
All data structures are provided as json.

If we must support XML as well - then let's discuss it. Otherwise we assume json only.

## POST, PATCH and PUT should use JSON bodies
This includes the payload of a POST command sent to the server. If the POST needs to contain data, this data is JSON.

The POST, PUT and PATH should require a `Content-Type` header be set to `application/json` or throw a `415 Unsupported Media Type` HTTP status code.

## camelCase
As per JSON convension, all attribute names are camelCased and not snake_cased.

## Pretty Print JSON
All output should be pretty printed (i.e. json indented) for readability

## UTF8
The encoding is UTF8

## Security
All API endpoints should be secured with SSL.
So all APIs should begin with `https://`.
You should not redirect non secure clients to secure ports, e.g. do not redirect `http://` to `https://...`, simply throw an error.

More? TBD

## Authentication
TBD

## API Versioning
TBD

* Option 1: In the URL path e.g. `/api/v1/xxx`.
* Option 2: In the URL as optional paramter e.g. `/api/xxx?version=1`
* Option 2: As an optional header e.g `cloudify-api-version: 1`


## Including and excluding fields
By default an endpoint such as /blueprint/1 will return all fields of the blueprint.
However, if you'd like to modify thats, provide a parameter `&fields=permalink,createdAt` to request only the listed fields.
Nested fields should be specified with a dot, so for example: `&fields=x.y`

## Filtering
In cases where a collection of resutls is returned (such as with `GET /blueprints`) we want to provide filtering options, for example return all blueprints with modification date after Nov 1.
Filtering should be defined by URL parameters so for example `GET /blueprints?filter="updatedAt>2013-11-01"`. The exact DSL for filtering (search) is TBD, but assume it's something like what chef provides or other search tools such as ElasticSearch

## Sorting
Similar to filtering, in cases where a collection of results is returned, an optional `&sort` parameter may be provided. For example: `GET /blueprints?sort="createdAt,-updatedAt"` which means first order by ascending creation date and then by descending modification date.

## Search
TBD
We might want to provide full text search. Or partial search. Have to think more about this.

## Lists should return partial objects
When querying for potentially a large number of objects, such as in `GET /blueprints` we should return partial object information so that not to overflow the network. The exact fields are TBD per API call, but at a minimum we want to have an `id` and a `permalink` attribute so it's easy to proceed from this result.

## Pagination.
TBD how to provide paging for large result sets.
Take a look at this [http://www.vinaysahni.com/best-practices-for-a-pragmatic-restful-api#pagination](http://www.vinaysahni.com/best-practices-for-a-pragmatic-restful-api#pagination)

## Headers
In circumstances where special (non standard) HTTP headers should be returned, they should all start with the prefix `cloudify-`. So for example in the `HEAD /executions/5` response we will see a header `cloudify-progress` and possibly others.

## Updates and Creation should return a resource representation
A `PUT`, `POST` or `PATCH` may make modifications to the underlying resource.
Upun success, they should return the resulting resource, to prevent the client from making another roundtrip.

## A POST should return 201
In case of a POST that resulted in a creation, use a HTTP 201 status code and include a Location header that points to the URL of the new resource.

## Support for Embedded documents
TBD if we want to support embedded documents. See http://www.vinaysahni.com/best-practices-for-a-pragmatic-restful-api#autoloading

## Rate Limiting
Rate limiting is useful even in "internal" APIs such as cloudify, mainly in order to protect the system from unintended, perhaps bogus usage.
We have to decide on the actaul implementtion. [http://www.vinaysahni.com/best-practices-for-a-pragmatic-restful-api#rate-limiting](http://www.vinaysahni.com/best-practices-for-a-pragmatic-restful-api#rate-limiting)

## Errors
All errors should return the appropriate HTTP error code as well as a JSON encoded body.

## Browserable API
The API should be browsable. That means that by using a browser it's easy to click and navigate from one object to other, related objects. That's why we have in many places a `permalink` attribute.

Nice to have: On top of that we can also make the API easy to consume in a browser such that any .html resource will output an HTML representation of this resoruce with nice formatting and href links etc. (the default formatting will be json with the extension of .json or no extension at all). Example frameworks that help building this feature are [Django Rest](http://django-rest-framework.org/) and [Swagger](https://developers.helloreverb.com/swagger/)

## CORS
Nice to have, but probably very low hanging fruit is Cross Origin Resource Sharing support. http://en.wikipedia.org/wiki/Cross-origin_resource_sharing

