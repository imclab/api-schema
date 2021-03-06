FORMAT: 1A

# DigitalOcean API
This is the new DigitalOcean API allowing you to manage resources in the DigitalOcean cloud.

## Authentication
DigitalOcean uses OAuth Authorization. You can use Basic auth using your API key for development and debugging.

## Error States
The common [HTTP Response Status Codes](https://github.com/for-GET/know-your-http-well/blob/master/status-codes.md) are used.

# Group API Reference

## Regions [/regions]
Regions are available datacenters within the DigitalOcean cloud.

<table>
  <tr>
    <th>Attribute</th>
    <th>Type</th>
    <th>Description</th>
    <th>Example</th>
  </tr>
  <tr>
    <td><strong>name</strong></td>
    <td><em>string</em></td>
    <td>display name of region</td>
    <td><code>"New York 1"</code></td>
  </tr>
  <tr>
    <td><strong>id</strong></td>
    <td><em>integer</em></td>
    <td>unique identifier of region</td>
    <td><code>3</code></td>
  </tr>
  <tr>
    <td><strong>slug</strong></td>
    <td><em>string</em></td>
    <td>url friendly name</td>
    <td><code>"nyc1"</code></td>
  </tr>
</table>

+ Model (application/json)

    + Body

            [
              {
                "name": "New York 1",
                "id": 3,
                "slug": "nyc1"
              }
            ]

### List All Regions [GET]

#### Curl Example

    $ curl https://api.digitalocean.com/v1/regions

+ Response 200

    [Regions][]

## Gist [/gists/{id}{?access_token}]
A single Gist object. The Gist resource is the central resource in the Gist Fox API. It represents one paste - a single text note.

The Gist resource has the following attributes: 

- id
- created_at
- description
- content

The states *id* and *created_at* are assigned by the Gist Fox API at the moment of creation. 

+ Parameters
    + id (string) ... ID of the Gist in the form of a hash.
    + access_token (string, optional) ... Gist Fox API access token.

+ Model (application/hal+json)

    HAL+JSON representation of Gist Resource. In addition to representing its state in the JSON form it offers affordances in the form of the HTTP Link header and HAL links.

    + Headers

            Link: <http:/api.gistfox.com/gists/42>;rel="self", <http:/api.gistfox.com/gists/42/star>;rel="star"

    + Body

            {
                "_links": {
                    "self": { "href": "/gists/42" },
                    "star": { "href": "/gists/42/star" },
                },
                "id": "42",
                "created_at": "2014-04-14T02:15:15Z",
                "description": "Description of Gist",
                "content": "String contents"
            }

### Retrieve a Single Gist [GET]
+ Response 200
    
    [Gist][]

### Edit a Gist [PATCH]
To update a Gist send a JSON with updated value for one or more of the Gist resource attributes. All attributes values (states) from the previous version of this Gist are carried over by default if not included in the hash.

+ Request (application/json)

        {
            "content": "Updated file contents"
        }

+ Response 200
    
    [Gist][]

### Delete a Gist [DELETE]
+ Response 204

## Star [/gists/{id}/star{?access_token}]
Star resource represents a Gist starred status. 

The Star resource has the following attribute:

- starred

+ Parameters
    + id (string) ... ID of the gist in the form of a hash
    + access_token (string, optional) ... Gist Fox API access token.    

+ Model (application/hal+json)

    HAL+JSON representation of Star Resource.

    + Headers

            Link: <http:/api.gistfox.com/gists/42/star>;rel="self"

    + Body

            {
                "_links": {
                    "self": { "href": "/gists/42/star" },
                },
                "starred": true
            }

### Star a Gist [PUT]
This action requries an `access_token` with `gist_write` scope. 

+ Response 204

### Unstar a Gist [DELETE]
This action requries an `access_token` with `gist_write` scope. 

+ Response 204

### Check if a Gist is Starred [GET]
+ Response 200

    [Star][]

# Group Access Authorization and Control
Access and Control of *Gist Fox API* OAuth token.

## Authorization [/authorization]
Authorization Resource represents an authorization granted to the user. You can **only** access your own authorization, and only through **Basic Authentication**.

The Authorization Resource has the following attribute:

- token
- scopes

Where *token* represents an OAuth token and *scopes* is an array of scopes granted for the given authorization. At this moment the only available scope is `gist_write`.

+ Model (application/hal+json)

    + Headers

            Link: <http:/api.gistfox.com/authorizations/1>;rel="self"

    + Body

            {
                "_links": {
                    "self": { "href": "/authorizations" },
                },
                "scopes": [
                    "gist_write"
                ],
                "token": "abc123"
            }

### Retrieve Authorization [GET]
+ Request
    + Headers

            Authorization: Basic QWxhZGRpbjpvcGVuIHNlc2FtZQ==

+ Response 200

    [Authorization][]

### Create Authorization [POST]
+ Request (application/json)
    + Headers

            Authorization: Basic QWxhZGRpbjpvcGVuIHNlc2FtZQ==

    + Body

            {
                "scopes": [
                    "gist_write"
                ]
            }

+ Response 201

    [Authorization][]

### Remove an Authorization [DELETE]
+ Request
    + Headers

            Authorization: Basic QWxhZGRpbjpvcGVuIHNlc2FtZQ==

+ Response 204