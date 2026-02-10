## API

Controller Types (CQRS Pattern)
The eQual framework uses three types of controllers, each located in specific directories:

* [Data providers](../../core-development-guide/controllers-routing/#data-providers)
* [Action handlers](../../core-development-guide/controllers-routing/#action-handlers)
* [Application providers](../../core-development-guide/controllers-routing/#application-providers)


### Remote-apis

Example of remote-api :

```php
<?php
$body = [
    'access_token'  => MAPBOX_KEY
];

// Send query to Mapbox geocoding API
$request = new HttpRequest('/geocoding/v5/mapbox.places/'.urlencode
($params['address']).'.json', ['Host' => 'api.mapbox.com:443']);

$response = $request
            ->setBody($body)
            ->send();

/* expected response match a JSON array of geo+json Features 
(https://tools.ietf.org/html/rfc7946) */
$response->getBody();
```

Another example :

```php
<?php
use equal\http\HttpRequest;

/**
 * HTTP native support
 *
 */
list($params, $providers) = eQual::announce([
    'description'   => 'Get picture data from imgur.com using imgur API.',
    'params'        => [
        'id' => [
            'description'   => 'Hash of the image to retrieve',
            'type'          => 'string',
            'default'       => 'a5NE1kW'
        ]
    ],
    'response'      => [
        'content-type'  => 'application/json',
        'charset'       => 'utf-8'
    ],
    'providers'     => ['context']
]);

$request = new HttpRequest("GET https://api.imgur.com/3/image/{$params['id']}");

$response = $request
            ->header('Authorization', "Client-ID 34030ab1f5ef12d")
            ->send();

$providers['context']
    ->httpResponse()
    ->body(['result' => $response->body()])
    ->send();
```

**Use cases of remote-API'S in eQual** :

- eQual is used to relay indirectly (use of a remote-API) informations to the User.

  Example :

  ```php
  $request = new HttpRequest('/geocoding/v5/mapbox.places/'.urlencode
  ($params['address']).'.json', ['Host' => 'api.mapbox.com:443']);
  ```

  In this example, eQual sends 'address information' and expects to get 'gps informations' from an API.

* eQual is used as proxy

  Some API's expect identifiers, and to keep those secret, the API-call can be done in eQual.

* eQual is used as an adapter

  Example :

  If an API-response is in XML, an eQual controller could transform/read the file to the expected value, needed for the frontend.  

#### Authentication to external social networks

The `oauth controller` attempts to authenticate a user from an external social network.

The controller expects two parameters :

```php
'params' 		=>	[
        'network_name'  =>  [
            'description'   => 'name of the social network to address 
            oauth request.',
            'type'          => 'string', 
            'required'      => true
        ],
        'network_token' =>  [
            'description'   => 'valid acess token for oauth.',
            'type'          => 'string',
            'required'      => true
        ]
    ],
```

And gives an `access token` as a response, that will be used to encrypt the messages sent to the server.

Those tokens have a limited life expectancy and must be renewed.

## CLI References

CLI usage:

```bash
./equal.run [--flag=value] [arguments]
```

## Global Flags

| **FLAG** | **DEFINITON** |
|----------|---------------|
| `--get=` | Execute a data provider|
| `--do=` | Execute an action handler |
| `--show=` | Execute an application provider controller |
| `--package=` | Target a package name (e.g. `core`, `demo`) |
| `--entity=` | Target entity class (format: `package_ClassName`) |
| `--right=` | <!--TODO to complete--> |
| `--level=` | |

### DB rights

Available rights:

* `create`
* `read`
* `update`
* `delete`
* `manage`

You can grant one right for one entity at a time:

```bash
./equal.run --do=group_grant --group=default --right=update --entity="core\User"
```

### Data providers (`--get`)

Every data provider is located in `packages/{package}/data`. They are used to retrieve and query data.

Example:
```bash
./equal.run --get=model_collect --entity='core_User'
```

### Action Handlers (`--do`)

Every action handler is located in `packages/{package}/actions. They are used to perform our operations and modifications.

Example:
```bash
./equal.run --do=init_db
```
<!--TODO add link-->
Example (see anonymisation):
```bash
./equal.run --do_anonymize --package=core
```

### Application Providers (`--show`)

Every application provider is located in `packages/{package}/show`. They are used to perform server-side rendering.

Example:
```bash
./equal.run --show=core_welcome --announce=1
```

#### Dot notation

```bash
$ ./equal.run \
--get=model_collect \
--entity=core\\User \
--fields=[id,name,groups_ids.name,groups_ids.description]
```

### Array notation

```bash
$ ./equal.run \
--get=model_collect \
--entity=core\\User \
--fields="{id,name,groups_ids:{name,description}}" 
```
