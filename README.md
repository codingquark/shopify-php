# shopify-php

A simple [Shopify API](http://api.shopify.com/) client in PHP.

The canoncial repository for this stream of development is
[https://github.com/TeamOffshoot/shopify-php](https://github.com/TeamOffshoot/shopify-php)

## Requirements

* PHP 5.3 (or higher)
* ext-curl, ext-json
* kriswallsmith/buzz 0.10

## Development Requirements

* phpunit/phpunit 3.7

## Getting Started

Install shopify-php via [Composer](http://getcomposer.org/)

Create a `composer.json` file if you don't already have one in your projects
root directory and require shopify-php:

    {
      "require": {
        "offshoot/shopify-php": "1.0.x"
      }
    }

To learn more about Composer, including the complete installation process,
visit http://getcomposer.org/

### Using cURL

If you're using a cURL based HttpClient like the `BuzzAdapter`, you will want
to include the cacert.pem file that can be found at
[http://curl.haxx.se/docs/caextract.html](http://curl.haxx.se/docs/caextract.html)

You can add this as a dependency in your composer file. Your `composer.json`
might look something like this:

    {
      "require": {
        "offshoot/shopify-php": "1.0.x",
        "haxx-se/curl": "1.0.0"
      },
      "repositories": [
        {
          "type": "package",
          "package": {
            "name": "haxx-se/curl",
            "version": "1.0.0",
            "dist": {
              "url": "http://curl.haxx.se/ca/cacert.pem",
              "type": "file"
            }
          }
        }
      ]
    }

You will be able to find the cacert.pem file in `vendor/haxx-se/curl/cacert.pem`

## Usage

### Authentication

If you do not already have a Shopify API Permanent Access Token, you will need
you authenticate with the Shopify API first

    $pathToCertificateFile = "vendor/haxx-se/curl/cacert.pem";
    $httpClient = new \Shopify\HttpClient\BuzzAdapter($pathToCertificateFile);

    $redirector = new \Shopify\Redirector\HeaderRedirector();

    $authenticate = new \Shopify\Api\AuthenticationGateway(
        $httpClient, $redirector
    );

    $authenticate->forShopName('mycoolshop')
        ->usingClientId('XXX1234567890') // get this from your Shopify Account
        ->withScope(array('write_products', 'read_orders'))
        ->andReturningTo("http://wherever.you/like")
        ->initiateLogin();

This will redirect your user to a Shopify login screen where they will need
to authenticate with their Shopify credentials. After doing that, Shopify will
perform a GET request to your redirect URI, that will look like:

    GET http://wherever.you/like?code=TEMP_TOKEN

Your application will need to capture the `code` query param from the request
and use that to get the permanent access token from Shopify

    $temporaryToken = $_GET['code'];
    // You should do some input sanitization to $temporaryToken here

    $permanentAccessToken = $authenticate->exchange($temporaryToken);

### Interacting with the Shopify API

TODO: coming soon...

## Contributing

Contributions are welcome. Just fork the repository and send a pull request.
Please be sure to include test coverage with your pull request. You can learn
more about Pull Requests [here](https://help.github.com/articles/creating-a-pull-request)

In order to run the test suite, ensure that the development dependencies have
been installed via composer. Then from your command line, simple run:

    vendor/bin/phpunit --bootstrap tests/bootstrap.php tests/

## License

This library is released under the [MIT License](https://github.com/TeamOffshoot/shopify-php/blob/master/LICENSE.txt)

## Acknowledgements

Thanks to [Sandeep Shetty](https://github.com/sandeepshetty/shopify_api) for
his development of the initial code base.
