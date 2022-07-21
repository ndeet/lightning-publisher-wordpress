# Bitcoin Lightning Publisher for WordPress

Bitcoin Lightning Publisher is a Bitcoin Lightning Paywall and Donation plugin for WordPress. 
It allows you to accept Bitcoin Lightning payments with your WordPress website. You can monetize any digital content with instant microtransactions and receive payments directly from your visitors - no need for expensive service providers.

The plugin is the easiest and most flexible plugin to sell your digital content and to receive donations and value 4 value payments.
Thanks to the Bitcoin Lightning Network you can create the best visitor experience with seamless one-click payments to your customers.


### Features

#### Paywall to sell content
Sell any digital content (pay-per-post, pay-per-view, pay-per-download, etc.) with a highly configurable paywall

* [WebLN enabled](https://www.webln.guide/) by default for easy on-click payments
* Add a paywall to posts and pages to easily charge for any published content
* Crowdfund option: make the content freely available after a certain amount is received
* Time-in option: keep the article freely available for a certain time and then enable the paywall after that
* Time-out option: make the article freely available after a certain time
* Configure the price in Satoshis, EUR, USD, or GBP (with real-time exchange rate)
* Configure the paywall with a shortcode (`[lnpaywall]`)
* Or configure the paywall with a Gutenberg Block
* Integrate with other tools and plugins like membership tools to control if the paywall should be enabled (see Paywall Hook section)


#### Donation/Value4Value payments
The plugin comes with various options to receive donations and Value 4 Value payments. 

* Gutenberg block for a donation widget
* Donation widget for themes
* Enable the [Lightning meta tag](https://github.com/BitcoinAndLightningLayerSpecs/rfc/issues/1) to allow users to send payments (Value 4 Value)
* Enable the `podcast:value` tag in your RSS feed to receive payments for your podcast


### Lightning Node connections
Connect to your exsisting Bitcoin Lightning node or simply create a new [Alby account](https://getalby.com/) to instantly receive lightning payments.

* [LND](https://github.com/lightningnetwork/lnd/)
* [LNDHub](https://github.com/getalby/lndhub.go) (e.g. BlueWallet)
* [LNBits](https://lnbits.com/)
* [BTCPay Server](https://btcpayserver.org/)
* [Lightning Address](https://lightningaddress.com/)


### REST-API for full advanced custom usage
For more advanced, custom Lightning integrations you can use the REST API to create and verify invoices. The API also provides a [LNURL-pay](https://github.com/fiatjaf/lnurl-rfc/blob/luds/06.md) endpoint. See the REST-API section for details.


## Requirements

* WordPress 5.6 or higher
* PHP 7.4 or higher

## Demo

Here quick Demo videos showing how to setup and use the plugin:
+ [Quck Start - setup + paywall setup](https://www.loom.com/share/095b49a87e444442ac7b297f9483dfa7)
+ [Admin Panel](https://www.loom.com/share/dbe501fe9d91445082a2c5c07a1a8ce8)

## Installation

Install from the WordPress Plugin directory or: 

Clone the repository and install the dependency

```bash
git clone https://github.com/getAlby/lightning-publisher-wordpress.git
cd lightning-publisher-wordpress
composer install # (maybe you need to add `--ignore-platform-reqs`)
```
To build a .zip file of the WordPress plugin run:
```bash
./build.sh # this builds a `wordpress-lightning-publisher.zip`
```

Then upload and activate the plugin.


## Paywall Hook to have custom logic when to enable/disable the paywall

To integrate with other plugins or to write custom conditions on when the paywall should be enabled a hook can be used. This means you can use a custom PHP function to decide if content should be behind the paywall or not.

This for example allows you to make the content available for all users/subscribers but enable the paywall for all other users.

##### Example

```php

// your function receives two arguments:
// 1. a boolean with the current check (true if the full content would be shown)
// 2. the ID of the post the user accesses
//
// return true if the full content should be shown or false to enable the paywall
function show_full_content_for_post($show_full_content, $post_id) {
  // Add your logic to check if the current user can see the post with ID $post_id

  return true; // return true to show the full content (disable the paywall)
}

// Check out the `add_filter` documentation for more information: https://developer.wordpress.org/reference/functions/add_filter/
add_filter('wp_lnp_has_paid_for_post', 'show_full_content_for_post', 10, 2);

```

Alternatively you can define a global function `wp_lnp_has_paid_for_post` which gets called. Return `true` to disable the paywall and show the full content.

```php

function wp_lnp_has_paid_for_post($show_full_content, $post_id) {
  return true; // show full content - disable the paywall
}

```

## Shortcode

If you do not use the Gutenberg editor you can use the `[lnpaywall]` shortcode. The content after the shortcode will be behind the paywall.
The following configuration options are possible: 

* amount
* currency
* description
* button_text
* total
* timeout
* timein

#### Example

```
[lnpaywall amount=2121]
```

## Plugin folder structure

Folder structure is based on https://github.com/DevinVinson/WordPress-Plugin-Boilerplate

- `wp-lightning.php` is the entrypoint of the plugin
- `includes` is where functionality shared between the admin area and the public-facing parts of the site reside
- `admin` is for all admin-specific functionality
- `public` is for all public-facing functionality
- `includes/class-wp-lightning.php` is the main plugin class which handles including all the related classes.
- `includes/class-wp-lightning-loader.php` is responsible for registering the action and filter hooks, and shortcodes.

## REST API

The plugin also provides a set of REST API Endpoints for handling payments and donations.

#### Intiate Payment for Paywall

- URL: `/lnp-alby/v1/paywall/pay`
- Method: `POST`
- Auth Required: No
- Data example

```
{
    post_id: "xxx"
}
```

#### Verify Payment for Paywall

- URL: `/lnp-alby/v1/paywall/verify`
- Method: `POST`
- Auth Required: No
- Data example

```
{
    post_id: "xxx",
    token: "xxx",
    preimage: "xxx"
}
```

#### LNURL-pay

- URL: `/lnp-alby/v1/lnurlp`
- Method: `GET`
- Auth Required: No

```
{
    "status":"OK",
    "callback":"http:\/\/wp.play.getalby.com\/wp-json\/lnp-alby\/v1\/lnurlp\/callback",
    "minSendable":10000,
    "maxSendable":1000000000,
    "tag":"payRequest",
    "metadata":"[[\"text\/identifier\", \"http:\/\/wp.play.getalby.com\"][\"text\/plain\", \"Alby\"]]"
}
```

- URL: `/lnp-alby/v1/lnurlp/callback`
- Method: `GET`
- Auth Required: No


## Get support

Do you need help? Create an issue or reach out to us: support[at]getalby.com


## About Alby

This plugin is powered by [Alby](https://getalby.com/) - We create tools to rethink content monetization on the web.

## License

GPL 3.0 (as WordPress)
