FORMAT: 1A

# DigitalOcean API

The DigitalOcean API allows you to manage resources in the DigitalOcean cloud.


### Authentication

OAuth is used to authorize and revoke access to resources managed by the API. *Full
OAuth documentation will be available in a separate document.* There are two ways to use an OAuth
access token once you have one.

#### Bearer Authorization Header

```bash
$ curl -H "Authorization: Bearer $ACCESS_TOKEN" https://api.digitalocean.com
```

#### Basic Authentication

```bash
$ curl -u "$ACCESS_TOKEN:" https://api.digitalocean.com
```

For personal and development purposes, you can create a personal access token in the API control
panel and use it like a regular OAuth token.

### Schema

The API has a machine-readable JSON schema that describes what resources are available via the API,
what their URLs are, how they are represented and what operations they support. You don't need to 
authenticate to use the schema endpoint. You can access the schema using curl:

```bash
$ curl https://api.digitalocean.com/v2/schema
```

The schema format is based on [JSON Schema](http://json-schema.org/) with the draft [Validation](http://tools.ietf.org/html/draft-fge-json-schema-validation-00) and [Hypertext](http://tools.ietf.org/html/draft-luff-json-hyper-schema-00) extensions.

### Curl Examples

Curl examples used in this documentation are provided to facilitate experimentation. Variable values are represented as `$SOMETHING` so that you can manipulate them using environment variables. Examples use the `-n` option to fetch credentials from a `~/.netrc` file, which should include an entry for api.digitalocean.com similar to the following:

```
machine api.digitalocean.com
  login {your-oauth-token}
  password x
```

### Errors

Failing responses will have an appropriate [HTTP status](https://github.com/for-GET/know-your-http-well/blob/master/status-codes.md) and a JSON body containing more details about the error.

#### Example Error Response

```
HTTP/1.1 403 Forbidden
```
```javascript
{
  "id":       "forbidden",
  "message":  "Request not authorized, provided credentials do not provide access to specified resource"
}
```

### Methods

<table>
<thead><tr>
<th>Method</th>
<th>Usage</th>
</tr></thead>
<tbody>
<tr>
<td>DELETE</td>
<td>used for destroying existing objects</td>
</tr>
<tr>
<td>GET</td>
<td>used for retrieving lists and individual objects</td>
</tr>
<tr>
<td>HEAD</td>
<td>used for retrieving metadata about existing objects</td>
</tr>
<tr>
<td>PATCH</td>
<td>used for updating existing objects</td>
</tr>
<tr>
<td>POST</td>
<td>used for creating new objects</td>
</tr>
</tbody>
</table>

### Method Override

When using a client that does not support all of the [methods](#methods), you can override by using a `POST` and
setting the `Http-Method-Override` header to the desired methed. For instance, to do a `PATCH`
request, do a `POST` with header `Http-Method-Override: PATCH`.

### Parameters

Values that can be provided for an action are divided between optional and required values. The expected type for each value is specified. Parameters should be JSON encoded and passed in the request body, however, in many cases you can use regular query parameters or form parameters. For example, these two requests are equivalent:

```bash
$ curl -n -X PATCH https://api.digitalocean.com/v2/domains/$DOMAIN_ID/records/$DOMAIN_RECORD_ID \
-H "Content-Type: application/json" \
-d '{"type":"A","name":"www","data":"127.0.0.1"}'
```
```bash
$ curl -n -X PATCH https://api.digitalocean.com/v2/domains/$DOMAIN_ID/records/$DOMAIN_RECORD_ID \
-F "type=A" \
-F "name=www" \
-F "data=127.0.0.1"
```

### Ranges

List requests will return a `Content-Range` header indicating the range of values returned. Large lists may require additional requests to retrieve. If a list response has been truncated you will receive a `206 Partial Content` status and one or both of `Next-Range` and `Prev-Range` headers if there are next and previous ranges respectively. To retrieve the next or previous range, repeat the request with the `Range` header set to either the `Next-Range` or `Prev-Range` value from the previous request.

The number of values returned in a range can be controlled using a `max` key in the `Range` header. For example, to get only the first 10 values, set this header: `Range: id ..; max=10;`. `max` can also be passed when iterating over `Next-Range` and `Prev-Range`. The default page size is 200 and maximum page size is 1000.

### Versioning

Major versions of the API are backwards incompatible and are available at different path endpoints. A major version may change
in backwards compatible ways, such as adding new parameters, or if changing a parameter, the old parameter will still work.




# Group Domain Record

Domain records are the DNS records for a domain.

<table>
  <tr>
    <th>Attribute</th>
    <th>Type</th>
    <th>Description</th>
    <th>Example</th>
  </tr>
  <tr>
    <td><strong>id</strong></td>
    <td><em>integer</em></td>
    <td>unique identifier of domain record</td>
    <td><code>32</code></td>
  </tr>
  <tr>
    <td><strong>type</strong></td>
    <td><em>string</em></td>
    <td>type of DNS record (ex: A, CNAME, TXT, ...)</td>
    <td><code>"CNAME"</code></td>
  </tr>
  <tr>
    <td><strong>name</strong></td>
    <td><em>string</em></td>
    <td>name to use for the DNS record</td>
    <td><code>"subdomain"</code></td>
  </tr>
  <tr>
    <td><strong>data</strong></td>
    <td><em>string</em></td>
    <td>value to use for the DNS record</td>
    <td><code>"@"</code></td>
  </tr>
  <tr>
    <td><strong>priority</strong></td>
    <td><em>nullable integer</em></td>
    <td>priority for SRV records</td>
    <td><code>null</code></td>
  </tr>
  <tr>
    <td><strong>port</strong></td>
    <td><em>nullable integer</em></td>
    <td>port for SRV records</td>
    <td><code>null</code></td>
  </tr>
  <tr>
    <td><strong>weight</strong></td>
    <td><em>nullable integer</em></td>
    <td>weight for SRV records</td>
    <td><code>null</code></td>
  </tr>
</table>

## Domain Record Collection [/domains/{domain_id}/records]

### Domain Record Create [POST]

Create a new domain record.

+ Response 201 (application/json)

  {
  "id": 32,
  "type": "CNAME",
  "name": "subdomain",
  "data": "@",
  "priority": null,
  "port": null,
  "weight": null
}

### Domain Record List [GET]

List existing domain records.

+ Response 200 (application/json)

  [
  {
    "id": 32,
    "type": "CNAME",
    "name": "subdomain",
    "data": "@",
    "priority": null,
    "port": null,
    "weight": null
  }
]


## Domain Record [/domains/{domain_id}/records/{domain_record_id}]

### Domain Record Delete [DELETE]

Delete an existing domain record.

+ Response 200 (application/json)

  {
  "id": 32,
  "type": "CNAME",
  "name": "subdomain",
  "data": "@",
  "priority": null,
  "port": null,
  "weight": null
}

### Domain Record Info [GET]

Info for existing domain records.

+ Response 200 (application/json)

  {
  "id": 32,
  "type": "CNAME",
  "name": "subdomain",
  "data": "@",
  "priority": null,
  "port": null,
  "weight": null
}

### Domain Record Update [PATCH]

Update an existing domain records.

+ Response 200 (application/json)

  {
  "id": 32,
  "type": "CNAME",
  "name": "subdomain",
  "data": "@",
  "priority": null,
  "port": null,
  "weight": null
}


# Group Domain

Domains are managed domain names that DigitalOcean provides DNS for.

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
    <td>name of the domain</td>
    <td><code>"example.com"</code></td>
  </tr>
  <tr>
    <td><strong>id</strong></td>
    <td><em>integer</em></td>
    <td>unique identifier of domain</td>
    <td><code>32</code></td>
  </tr>
  <tr>
    <td><strong>ttl</strong></td>
    <td><em>seconds</em></td>
    <td>time to live for records on this domain</td>
    <td><code>1800</code></td>
  </tr>
  <tr>
    <td><strong>zone_file</strong></td>
    <td><em>string</em></td>
    <td>contents of the zone file used by the DNS server</td>
    <td><code>"$TTL\\t600\\n@\\t\\tIN\\tSOA ..."</code></td>
  </tr>
  <tr>
    <td><strong>bad_zone_file</strong></td>
    <td><em>nullable string</em></td>
    <td>contents of updated zone file that produced an error or null</td>
    <td><code>"$TTL\\t600\\n@\\t\\tIN\\tSOA ..."</code></td>
  </tr>
  <tr>
    <td><strong>bad_zone_error</strong></td>
    <td><em>nullable string</em></td>
    <td>error information associated with bad_zone_file or null</td>
    <td><code>"error information..."</code></td>
  </tr>
</table>

## Domain Collection [/domains]

### Domain Create [POST]

Create a new domain.

+ Response 201 (application/json)

  {
  "name": "example.com",
  "id": 32,
  "ttl": 1800,
  "zone_file": "$TTL\\t600\\n@\\t\\tIN\\tSOA ...",
  "bad_zone_file": "$TTL\\t600\\n@\\t\\tIN\\tSOA ...",
  "bad_zone_error": "error information..."
}

### Domain List [GET]

List existing domains.

+ Response 200 (application/json)

  [
  {
    "name": "example.com",
    "id": 32,
    "ttl": 1800,
    "zone_file": "$TTL\\t600\\n@\\t\\tIN\\tSOA ...",
    "bad_zone_file": "$TTL\\t600\\n@\\t\\tIN\\tSOA ...",
    "bad_zone_error": "error information..."
  }
]


## Domain [/domains/{domain_id}]

### Domain Delete [DELETE]

Delete an existing domain.

+ Response 200 (application/json)

  {
  "name": "example.com",
  "id": 32,
  "ttl": 1800,
  "zone_file": "$TTL\\t600\\n@\\t\\tIN\\tSOA ...",
  "bad_zone_file": "$TTL\\t600\\n@\\t\\tIN\\tSOA ...",
  "bad_zone_error": "error information..."
}

### Domain Info [GET]

Info for existing domain.

+ Response 200 (application/json)

  {
  "name": "example.com",
  "id": 32,
  "ttl": 1800,
  "zone_file": "$TTL\\t600\\n@\\t\\tIN\\tSOA ...",
  "bad_zone_file": "$TTL\\t600\\n@\\t\\tIN\\tSOA ...",
  "bad_zone_error": "error information..."
}


# Group Droplet Action

Droplet actions are operations on droplets that may take a while to complete.

<table>
  <tr>
    <th>Attribute</th>
    <th>Type</th>
    <th>Description</th>
    <th>Example</th>
  </tr>
  <tr>
    <td><strong>id</strong></td>
    <td><em>integer</em></td>
    <td>unique identifier of droplet action</td>
    <td><code>32</code></td>
  </tr>
  <tr>
    <td><strong>status</strong></td>
    <td><em>string</em></td>
    <td>current status of action</td>
    <td><code>"in-progress"</code></td>
  </tr>
  <tr>
    <td><strong>reboot</strong></td>
    <td><em>string</em></td>
    <td>reboot the machine, specifying a hard or soft reboot</td>
    <td><code>"hard"</code></td>
  </tr>
  <tr>
    <td><strong>shutdown</strong></td>
    <td><em>string</em></td>
    <td>shutdown the machine, specifying a hard or soft shutdown</td>
    <td><code>"soft"</code></td>
  </tr>
  <tr>
    <td><strong>boot</strong></td>
    <td><em>nullable null</em></td>
    <td>boot the machine, no value necessary</td>
    <td><code>null</code></td>
  </tr>
  <tr>
    <td><strong>resetpassword</strong></td>
    <td><em>nullable null</em></td>
    <td>reset the root password, no value necessary</td>
    <td><code>null</code></td>
  </tr>
  <tr>
    <td><strong>resize</strong></td>
    <td><em>string</em></td>
    <td>resize the machine, specifying a size id or slug</td>
    <td><code>"32mb"</code></td>
  </tr>
  <tr>
    <td><strong>snapshot</strong></td>
    <td><em>string</em></td>
    <td>snapshot the machine, specifying the name for the snapshot image</td>
    <td><code>"My snapshot"</code></td>
  </tr>
  <tr>
    <td><strong>rebuild</strong></td>
    <td><em>string or integer</em></td>
    <td>rebuild the machine, specifying an image id or slug</td>
    <td><code>32</code></td>
  </tr>
  <tr>
    <td><strong>restore</strong></td>
    <td><em>string or integer</em></td>
    <td>restore the machine, specifying an image id or slug</td>
    <td><code>32</code></td>
  </tr>
</table>

## Droplet Action Collection [/droplet/{droplet_id}/actions]

### Droplet Action Create [POST]

Create a new droplet action.

+ Response 201 (application/json)

  {
  "id": 32,
  "status": "in-progress",
  "reboot": "hard",
  "shutdown": "soft",
  "boot": null,
  "resetpassword": null,
  "resize": "32mb",
  "snapshot": "My snapshot",
  "rebuild": 32,
  "restore": 32
}


## Droplet Action [/droplet/{droplet_id}/actions/{droplet_action_id}]

### Droplet Action Info [GET]

Info for existing droplet action.

+ Response 200 (application/json)

  {
  "id": 32,
  "status": "in-progress",
  "reboot": "hard",
  "shutdown": "soft",
  "boot": null,
  "resetpassword": null,
  "resize": "32mb",
  "snapshot": "My snapshot",
  "rebuild": 32,
  "restore": 32
}


# Group Droplet

Droplets are VMs in the DigitalOcean cloud.

<table>
  <tr>
    <th>Attribute</th>
    <th>Type</th>
    <th>Description</th>
    <th>Example</th>
  </tr>
  <tr>
    <td><strong>id</strong></td>
    <td><em>integer</em></td>
    <td>unique identifier of droplet</td>
    <td><code>32</code></td>
  </tr>
  <tr>
    <td><strong>name</strong></td>
    <td><em>string</em></td>
    <td>name used to identify droplet</td>
    <td><code>"my droplet"</code></td>
  </tr>
  <tr>
    <td><strong>region</strong></td>
    <td><em>string</em></td>
    <td>slug of region for this droplet</td>
    <td><code>"nyc2"</code></td>
  </tr>
  <tr>
    <td><strong>image:id</strong></td>
    <td><em>integer</em></td>
    <td>unique identifier of image</td>
    <td><code>32</code></td>
  </tr>
  <tr>
    <td><strong>image:slug</strong></td>
    <td><em>nullable string</em></td>
    <td>url friendly name of the image</td>
    <td><code>"ubuntu-12.10-x32"</code></td>
  </tr>
  <tr>
    <td><strong>image:name</strong></td>
    <td><em>string</em></td>
    <td>display name of the image</td>
    <td><code>"My first snapshot"</code></td>
  </tr>
  <tr>
    <td><strong>image:distribution</strong></td>
    <td><em>string</em></td>
    <td>name of the Linux distribution this image is based on</td>
    <td><code>"Ubuntu"</code></td>
  </tr>
  <tr>
    <td><strong>image:public</strong></td>
    <td><em>boolean</em></td>
    <td>whether accessible by all accounts or just your account</td>
    <td><code>false</code></td>
  </tr>
  <tr>
    <td><strong>size:memory</strong></td>
    <td><em>string</em></td>
    <td>amount of RAM provided</td>
    <td><code>"512mb"</code></td>
  </tr>
  <tr>
    <td><strong>size:cpus</strong></td>
    <td><em>integer</em></td>
    <td>number of CPUs provided</td>
    <td><code>1</code></td>
  </tr>
  <tr>
    <td><strong>size:disk</strong></td>
    <td><em>string</em></td>
    <td>amount of SSD disk storage provided</td>
    <td><code>"20gb"</code></td>
  </tr>
  <tr>
    <td><strong>size:transfer</strong></td>
    <td><em>string</em></td>
    <td>amount of network transfer provided</td>
    <td><code>"1tb"</code></td>
  </tr>
  <tr>
    <td><strong>size:price_monthly</strong></td>
    <td><em>string</em></td>
    <td>cost of running for a month</td>
    <td><code>"5.00"</code></td>
  </tr>
  <tr>
    <td><strong>size:price_hourly</strong></td>
    <td><em>string</em></td>
    <td>cost of running for an hour</td>
    <td><code>"0.007"</code></td>
  </tr>
  <tr>
    <td><strong>backups</strong></td>
    <td><em>nullable array</em></td>
    <td>backup images taken, or null if disabled</td>
    <td><code>[{"id":32,"name":"Backup taken 2014-01-01 12:00:00"}]</code></td>
  </tr>
  <tr>
    <td><strong>snapshots</strong></td>
    <td><em>array</em></td>
    <td>snapshot images taken</td>
    <td><code>[{"id":32,"name":"My snapshot"}]</code></td>
  </tr>
  <tr>
    <td><strong>locked</strong></td>
    <td><em>boolean</em></td>
    <td>whether operations are currently permitted</td>
    <td><code>false</code></td>
  </tr>
  <tr>
    <td><strong>status</strong></td>
    <td><em>string</em></td>
    <td>current status of droplet</td>
    <td><code>"active"</code></td>
  </tr>
  <tr>
    <td><strong>networks:public:ip_address</strong></td>
    <td><em>string</em></td>
    <td>network IP address</td>
    <td><code>"162.243.68.122"</code></td>
  </tr>
  <tr>
    <td><strong>networks:public:netmask</strong></td>
    <td><em>string</em></td>
    <td>network subnet mask</td>
    <td><code>"255.255.255.0"</code></td>
  </tr>
  <tr>
    <td><strong>networks:public:gateway</strong></td>
    <td><em>string</em></td>
    <td>network gateway</td>
    <td><code>"162.243.68.1"</code></td>
  </tr>
  <tr>
    <td><strong>networks:private:ip_address</strong></td>
    <td><em>string</em></td>
    <td>network IP address</td>
    <td><code>"162.243.68.122"</code></td>
  </tr>
  <tr>
    <td><strong>networks:private:netmask</strong></td>
    <td><em>string</em></td>
    <td>network subnet mask</td>
    <td><code>"255.255.255.0"</code></td>
  </tr>
  <tr>
    <td><strong>networks:private:gateway</strong></td>
    <td><em>string</em></td>
    <td>network gateway</td>
    <td><code>"162.243.68.1"</code></td>
  </tr>
</table>

## Droplet Collection [/droplets]

### Droplet Create [POST]

Create a new droplet.

+ Response 201 (application/json)

  {
  "id": 32,
  "name": "my droplet",
  "region": "nyc2",
  "image": {
    "id": 32,
    "slug": "ubuntu-12.10-x32",
    "name": "Ubuntu 12.10 x32",
    "distribution": "Ubuntu",
    "public": true
  },
  "size": {
    "memory": "512mb",
    "cpus": 1,
    "disk": "20gb",
    "transfer": "1tb",
    "price_monthly": "5.00",
    "price_hourly": "0.007"
  },
  "backups": [
    {
      "id": 32,
      "name": "Backup taken 2014-01-01 12:00:00"
    }
  ],
  "snapshots": [
    {
      "id": 32,
      "name": "My snapshot"
    }
  ],
  "locked": false,
  "status": "active",
  "networks": {
    "public": {
      "ip_address": "162.243.68.122",
      "netmask": "255.255.255.0",
      "gateway": "162.243.68.1"
    }
  }
}

### Droplet List [GET]

List existing droplet.

+ Response 200 (application/json)

  [
  {
    "id": 32,
    "name": "my droplet",
    "region": "nyc2",
    "image": {
      "id": 32,
      "slug": "ubuntu-12.10-x32",
      "name": "Ubuntu 12.10 x32",
      "distribution": "Ubuntu",
      "public": true
    },
    "size": {
      "memory": "512mb",
      "cpus": 1,
      "disk": "20gb",
      "transfer": "1tb",
      "price_monthly": "5.00",
      "price_hourly": "0.007"
    },
    "backups": [
      {
        "id": 32,
        "name": "Backup taken 2014-01-01 12:00:00"
      }
    ],
    "snapshots": [
      {
        "id": 32,
        "name": "My snapshot"
      }
    ],
    "locked": false,
    "status": "active",
    "networks": {
      "public": {
        "ip_address": "162.243.68.122",
        "netmask": "255.255.255.0",
        "gateway": "162.243.68.1"
      }
    }
  }
]


## Droplet [/droplets/{droplet_id}]

### Droplet Delete [DELETE]

Delete an existing droplet.

+ Response 200 (application/json)

  {
  "id": 32,
  "name": "my droplet",
  "region": "nyc2",
  "image": {
    "id": 32,
    "slug": "ubuntu-12.10-x32",
    "name": "Ubuntu 12.10 x32",
    "distribution": "Ubuntu",
    "public": true
  },
  "size": {
    "memory": "512mb",
    "cpus": 1,
    "disk": "20gb",
    "transfer": "1tb",
    "price_monthly": "5.00",
    "price_hourly": "0.007"
  },
  "backups": [
    {
      "id": 32,
      "name": "Backup taken 2014-01-01 12:00:00"
    }
  ],
  "snapshots": [
    {
      "id": 32,
      "name": "My snapshot"
    }
  ],
  "locked": false,
  "status": "active",
  "networks": {
    "public": {
      "ip_address": "162.243.68.122",
      "netmask": "255.255.255.0",
      "gateway": "162.243.68.1"
    }
  }
}

### Droplet Info [GET]

Info for existing droplet.

+ Response 200 (application/json)

  {
  "id": 32,
  "name": "my droplet",
  "region": "nyc2",
  "image": {
    "id": 32,
    "slug": "ubuntu-12.10-x32",
    "name": "Ubuntu 12.10 x32",
    "distribution": "Ubuntu",
    "public": true
  },
  "size": {
    "memory": "512mb",
    "cpus": 1,
    "disk": "20gb",
    "transfer": "1tb",
    "price_monthly": "5.00",
    "price_hourly": "0.007"
  },
  "backups": [
    {
      "id": 32,
      "name": "Backup taken 2014-01-01 12:00:00"
    }
  ],
  "snapshots": [
    {
      "id": 32,
      "name": "My snapshot"
    }
  ],
  "locked": false,
  "status": "active",
  "networks": {
    "public": {
      "ip_address": "162.243.68.122",
      "netmask": "255.255.255.0",
      "gateway": "162.243.68.1"
    }
  }
}

### Droplet Update [PATCH]

Update an existing droplet.

+ Response 200 (application/json)

  {
  "id": 32,
  "name": "my droplet",
  "region": "nyc2",
  "image": {
    "id": 32,
    "slug": "ubuntu-12.10-x32",
    "name": "Ubuntu 12.10 x32",
    "distribution": "Ubuntu",
    "public": true
  },
  "size": {
    "memory": "512mb",
    "cpus": 1,
    "disk": "20gb",
    "transfer": "1tb",
    "price_monthly": "5.00",
    "price_hourly": "0.007"
  },
  "backups": [
    {
      "id": 32,
      "name": "Backup taken 2014-01-01 12:00:00"
    }
  ],
  "snapshots": [
    {
      "id": 32,
      "name": "My snapshot"
    }
  ],
  "locked": false,
  "status": "active",
  "networks": {
    "public": {
      "ip_address": "162.243.68.122",
      "netmask": "255.255.255.0",
      "gateway": "162.243.68.1"
    }
  }
}


# Group Image Action

Image actions are operations on images that may take a while to complete.

<table>
  <tr>
    <th>Attribute</th>
    <th>Type</th>
    <th>Description</th>
    <th>Example</th>
  </tr>
  <tr>
    <td><strong>id</strong></td>
    <td><em>integer</em></td>
    <td>unique identifier of image action</td>
    <td><code>32</code></td>
  </tr>
  <tr>
    <td><strong>transfer</strong></td>
    <td><em>string</em></td>
    <td>a region slug to transfer the image to</td>
    <td><code>"nyc2"</code></td>
  </tr>
  <tr>
    <td><strong>status</strong></td>
    <td><em>string</em></td>
    <td>current status of action</td>
    <td><code>"in-progress"</code></td>
  </tr>
</table>

## Image Action Collection [/images/{image_id_or_slug}/actions]

### Image Action Create [POST]

Create a new image action.

+ Response 201 (application/json)

  {
  "id": 32,
  "transfer": "nyc2",
  "status": "in-progress"
}


## Image Action [/images/{image_id_or_slug}/actions/{image_action_id}]

### Image Action Info [GET]

Info for existing image action.

+ Response 200 (application/json)

  {
  "id": 32,
  "transfer": "nyc2",
  "status": "in-progress"
}


# Group Image

Images are either snapshots or backups you've made, or public images of applications or base systems.

<table>
  <tr>
    <th>Attribute</th>
    <th>Type</th>
    <th>Description</th>
    <th>Example</th>
  </tr>
  <tr>
    <td><strong>id</strong></td>
    <td><em>integer</em></td>
    <td>unique identifier of image</td>
    <td><code>32</code></td>
  </tr>
  <tr>
    <td><strong>name</strong></td>
    <td><em>string</em></td>
    <td>display name of the image</td>
    <td><code>"My first snapshot"</code></td>
  </tr>
  <tr>
    <td><strong>distribution</strong></td>
    <td><em>string</em></td>
    <td>name of the Linux distribution this image is based on</td>
    <td><code>"Ubuntu"</code></td>
  </tr>
  <tr>
    <td><strong>slug</strong></td>
    <td><em>nullable string</em></td>
    <td>url friendly name of the image</td>
    <td><code>"ubuntu-12.10-x32"</code></td>
  </tr>
  <tr>
    <td><strong>size</strong></td>
    <td><em>string</em></td>
    <td>minimum droplet size image is available for</td>
    <td><code>"512mb"</code></td>
  </tr>
  <tr>
    <td><strong>public</strong></td>
    <td><em>boolean</em></td>
    <td>whether accessible by all accounts or just your account</td>
    <td><code>false</code></td>
  </tr>
  <tr>
    <td><strong>regions</strong></td>
    <td><em>array</em></td>
    <td>slugs of regions this image is currently available in</td>
    <td><code>["nyc2","nyc1"]</code></td>
  </tr>
</table>

## Image Collection [/images]

### Image List [GET]

List existing images.

+ Response 200 (application/json)

  [
  {
    "id": 32,
    "name": "My first snapshot",
    "distribution": "Ubuntu",
    "slug": "ubuntu-12.10-x32",
    "size": "512mb",
    "public": false,
    "regions": [
      "nyc2",
      "nyc1"
    ]
  }
]


## Image [/images/{image_id_or_slug}]

### Image Delete [DELETE]

Delete an existing image.

+ Response 200 (application/json)

  {
  "id": 32,
  "name": "My first snapshot",
  "distribution": "Ubuntu",
  "slug": "ubuntu-12.10-x32",
  "size": "512mb",
  "public": false,
  "regions": [
    "nyc2",
    "nyc1"
  ]
}

### Image Info [GET]

Info for existing image.

+ Response 200 (application/json)

  {
  "id": 32,
  "name": "My first snapshot",
  "distribution": "Ubuntu",
  "slug": "ubuntu-12.10-x32",
  "size": "512mb",
  "public": false,
  "regions": [
    "nyc2",
    "nyc1"
  ]
}

### Image Update [PATCH]

Update an existing image.

+ Response 200 (application/json)

  {
  "id": 32,
  "name": "My first snapshot",
  "distribution": "Ubuntu",
  "slug": "ubuntu-12.10-x32",
  "size": "512mb",
  "public": false,
  "regions": [
    "nyc2",
    "nyc1"
  ]
}


# Group Key

Keys are your public SSH keys that you can use to access Droplets.

<table>
  <tr>
    <th>Attribute</th>
    <th>Type</th>
    <th>Description</th>
    <th>Example</th>
  </tr>
  <tr>
    <td><strong>id</strong></td>
    <td><em>integer</em></td>
    <td>unique identifier of key</td>
    <td><code>18</code></td>
  </tr>
  <tr>
    <td><strong>fingerprint</strong></td>
    <td><em>string</em></td>
    <td>a unique identifying string based on contents</td>
    <td><code>"17:63:a4:ba:24:d3:7f:af:17:c8:94:82:7e:80:56:bf"</code></td>
  </tr>
  <tr>
    <td><strong>public_key</strong></td>
    <td><em>string</em></td>
    <td>full public key string</td>
    <td><code>"ssh-rsa AAAAB3NzaC1ycVc/../839Uv username@example.com"</code></td>
  </tr>
  <tr>
    <td><strong>name</strong></td>
    <td><em>string</em></td>
    <td>user specified identifier</td>
    <td><code>"primary-key"</code></td>
  </tr>
</table>

## Key Collection [/account/keys]

### Key Create [POST]

Create a new key.

+ Response 201 (application/json)

  {
  "id": 18,
  "fingerprint": "17:63:a4:ba:24:d3:7f:af:17:c8:94:82:7e:80:56:bf",
  "public_key": "ssh-rsa AAAAB3NzaC1ycVc/../839Uv username@example.com",
  "name": "primary-key"
}

### Key List [GET]

List existing keys.

+ Response 200 (application/json)

  [
  {
    "id": 18,
    "fingerprint": "17:63:a4:ba:24:d3:7f:af:17:c8:94:82:7e:80:56:bf",
    "public_key": "ssh-rsa AAAAB3NzaC1ycVc/../839Uv username@example.com",
    "name": "primary-key"
  }
]


## Key [/account/keys/{key_id_or_fingerprint}]

### Key Delete [DELETE]

Delete an existing key.

+ Response 200 (application/json)

  {
  "id": 18,
  "fingerprint": "17:63:a4:ba:24:d3:7f:af:17:c8:94:82:7e:80:56:bf",
  "public_key": "ssh-rsa AAAAB3NzaC1ycVc/../839Uv username@example.com",
  "name": "primary-key"
}

### Key Info [GET]

Info for existing key.

+ Response 200 (application/json)

  {
  "id": 18,
  "fingerprint": "17:63:a4:ba:24:d3:7f:af:17:c8:94:82:7e:80:56:bf",
  "public_key": "ssh-rsa AAAAB3NzaC1ycVc/../839Uv username@example.com",
  "name": "primary-key"
}


# Group Region

Regions are available datacenters within the DigitalOcean cloud.

<table>
  <tr>
    <th>Attribute</th>
    <th>Type</th>
    <th>Description</th>
    <th>Example</th>
  </tr>
  <tr>
    <td><strong>slug</strong></td>
    <td><em>string</em></td>
    <td>unique string identifier of region</td>
    <td><code>"nyc1"</code></td>
  </tr>
  <tr>
    <td><strong>name</strong></td>
    <td><em>string</em></td>
    <td>display name of region</td>
    <td><code>"New York 1"</code></td>
  </tr>
  <tr>
    <td><strong>sizes</strong></td>
    <td><em>array</em></td>
    <td>slugs of sizes this region currently has capacity for</td>
    <td><code>["512mb","1gb","2gb","4gb","8gb"]</code></td>
  </tr>
  <tr>
    <td><strong>available</strong></td>
    <td><em>bool</em></td>
    <td>if new droplets can currently be created</td>
    <td><code>true</code></td>
  </tr>
</table>

## Region Collection [/regions]

### Region List [GET]

List available regions.

+ Response 200 (application/json)

  [
  {
    "slug": "nyc1",
    "name": "New York 1",
    "sizes": [
      "512mb",
      "1gb",
      "2gb",
      "4gb",
      "8gb"
    ],
    "available": true
  }
]



# Group Size

Sizes represent possible Droplet resources.

<table>
  <tr>
    <th>Attribute</th>
    <th>Type</th>
    <th>Description</th>
    <th>Example</th>
  </tr>
  <tr>
    <td><strong>slug</strong></td>
    <td><em>string</em></td>
    <td>unique string identifier of size</td>
    <td><code>"512mb"</code></td>
  </tr>
  <tr>
    <td><strong>memory</strong></td>
    <td><em>string</em></td>
    <td>amount of RAM provided</td>
    <td><code>"512mb"</code></td>
  </tr>
  <tr>
    <td><strong>cpus</strong></td>
    <td><em>integer</em></td>
    <td>number of CPUs provided</td>
    <td><code>1</code></td>
  </tr>
  <tr>
    <td><strong>disk</strong></td>
    <td><em>string</em></td>
    <td>amount of SSD disk storage provided</td>
    <td><code>"20gb"</code></td>
  </tr>
  <tr>
    <td><strong>transfer</strong></td>
    <td><em>string</em></td>
    <td>amount of network transfer provided</td>
    <td><code>"1tb"</code></td>
  </tr>
  <tr>
    <td><strong>price_monthly</strong></td>
    <td><em>string</em></td>
    <td>cost of running for a month</td>
    <td><code>"5.00"</code></td>
  </tr>
  <tr>
    <td><strong>price_hourly</strong></td>
    <td><em>string</em></td>
    <td>cost of running for an hour</td>
    <td><code>"0.007"</code></td>
  </tr>
  <tr>
    <td><strong>regions</strong></td>
    <td><em>array</em></td>
    <td>slugs of regions this size is currently available in</td>
    <td><code>["nyc2","nyc1"]</code></td>
  </tr>
</table>

## Size Collection [/sizes]

### Size List [GET]

List available sizes.

+ Response 200 (application/json)

  [
  {
    "slug": "512mb",
    "memory": "512mb",
    "cpus": 1,
    "disk": "20gb",
    "transfer": "1tb",
    "price_monthly": "5.00",
    "price_hourly": "0.007",
    "regions": [
      "nyc2",
      "nyc1"
    ]
  }
]



