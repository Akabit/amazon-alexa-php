# Amazon alexa php library
This library is a helper for amazon alexa skills with php.

[![Build Status](https://travis-ci.org/maxbeckers/amazon-alexa-php.svg?branch=master)](https://travis-ci.org/maxbeckers/amazon-alexa-php)
[![Coverage Status](https://coveralls.io/repos/github/maxbeckers/amazon-alexa-php/badge.svg?branch=master)](https://coveralls.io/github/maxbeckers/amazon-alexa-php?branch=master)
[![MIT Licence](https://badges.frapsoft.com/os/mit/mit.svg?v=103)](https://opensource.org/licenses/mit-license.php)
[![contributions welcome](https://img.shields.io/badge/contributions-welcome-brightgreen.svg?style=flat)](https://github.com/maxbeckers/amazon-alexa-php/issues)

## Install via composer
Require the package with composer:
```
composer require maxbeckers/amazon-alexa-php
```

## Usage
Handle the request: 
- map request data to request object
- validate request
- handle request data
- create response
- send response

### Map request data to request object
Map needed request headers and request body to `Request`.
```php
use MaxBeckers\AmazonAlexa\Request\Request;
...
$requestBody  = file_get_contents('php://input');
$alexaRequest = Request::fromAmazonRequest($requestBody, $_SERVER['HTTP_SIGNATURECERTCHAINURL'], $_SERVER['HTTP_SIGNATURE']);
```
### Validate request
The `RequestValidator` will handle the amazon request validation.
```php
use MaxBeckers\AmazonAlexa\Validation\RequestValidator;
...
$validator = new RequestValidator();
$validator->validate($alexaRequest);
```
### Register request handlers
For different requests it's helpful to create different RequestHandlers.
```php
use MaxBeckers\AmazonAlexa\RequestHandler\RequestHandlerRegistry;
...
$requestHandlerRegistry = new RequestHandlerRegistry();
$requestHandlerRegistry->addHandler($myRequestHandler);
```
### Use registry to handle request
```php
use MaxBeckers\AmazonAlexa\RequestHandler\RequestHandlerRegistry;
...
$requestHandler = $requestHandlerRegistry->getSupportingHandler($alexaRequest);
$response       = $requestHandler->handleRequest($alexaRequest);
```
### Render response
```php
header('Content-Type: application/json');
echo json_encode($response);
exit();
```
### Create a new request handler
The new request handler must extend `AbstractRequestHandler`.
In constructor set the `supportedApplicationIds` to your skill Ids.
```php
public function __construct()
{
    $this->supportedApplicationIds = ['my_amazon_skill_id'];
}
```
Then implement the abstract `supportsRequest`-method.
```php
public function supportsRequest(Request $request): bool
{
    return $request->request instanceOf MaxBeckers\AmazonAlexa\Request\Request\Standard\IntentRequest &&
        'MyTestIntent' === $request->request->intent->name;
}
```
Then implement the `handleRequest`-method. For simple responses there is a `ResponseHelper`.
```php
use MaxBeckers\AmazonAlexa\Helper\ResponseHelper;
...
public function handleRequest(Request $request): Response
{
    return $this->responseHelper->respond('Success :)');
}
```
## Symfony Integration
There is also a symfony bundle on [maxbeckers/amazon-alexa-bundle](https://github.com/maxbeckers/amazon-alexa-bundle).
