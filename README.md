# ed-php-serverless
Serverless functions with PHP

Serverless is a cloud computing model that lets developers build and run applications without provisioning, managing, or scaling server infrastructure. Serverless applications are hostless, stateless, elastic, distributed, and event-driven. A cloud provider handles the routine work of allocating and scaling compute resources, and charges only for the resources used. Serverless is also known as function as a service (FaaS) or compute as a service (CaaS).

Serverless does not mean that there are no servers involved, but rather that the developers do not have to worry about them.

> Serverless functions provide a great way to build API end-points since they only consume resources when they are called and we do not have to pay for the infrastructure when they are sitting idle.

In DigitalOcean you can write functions in PHP (among other popular languages like Javascript or GO) and manage them through a web based user interface or using a CLI.

Writing your own function is easy. You have to define a main method that takes the request parameters through an $arg array.

You can use environment variables and get them through the standard PHP calls. You can use external PHP libraries as well through composer.

In the example below we use a 3rd party service to collect geo information based on a given IP address.

```
<?php
function main(array $args) : array
{
    $apiKey = getenv('API_KEY');

    if (!isset($args['ip_address'])) {
        return [
            'statusCode' => 400,
            'body' => [
                'status' => 'error',
                'message' => 'No IP address provided',
            ],
        ];
    }

    $ip_address = $args['ip_address'];

    $queryString = http_build_query(['access_key' => $apiKey]); 
    $apiUrl = "http://api.ipstack.com/${ip_address}?" . $queryString;

    $response = file_get_contents($apiUrl);

    return ($response !== false)
        ? [
              'statusCode' => 200,
              'body' => [
                  'status' => 'success',
                  'message' => 'ipstack query successfull',
                  'data' => json_decode($response),
              ],
        ]
        : [ 
              'statusCode' => 500, 
              'body' => [ 
                  'status' => 'error', 
                  'message' => 'An error occured during the request!', 
              ], 
        ];
}
```

In this example the results will be returned as json encoded data together with a HTTP status code.

You can access your web function using the standard HTTP methods using GET, POST, PUT, DELETE etc. or using the REST API.

For more information you can take a look on other examples on the following [web-site:](https://docs.digitalocean.com/products/functions/quickstart/sample-functions/)
