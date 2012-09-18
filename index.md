---
layout: page
title: Overview
---

![tiramizoo API Documentation](/assets/images/tiramizoo-logo.png "tiramizoo API Documentation")

You can easily offer the Tiramizoo delivery service to your customers, within your current
e-commerce platform. Either download and install one of our plugins or do a custom integration by
programming against the public REST/JSON API. The API is "eat your own
dogfood" style - tiramizoo uses it internally for the "book a courier"
website, administration and plugins, so you can count that it is well maintained.

For a detailed overview on how to use the API and how the workflow with tiramizoo.com looks like, take a look at the
PDF Guide and instructions at http://www.tiramizoo.com/retailers.

For testing purposes use [Tiramizoo sandbox](/sandbox.html)

The tiramizoo.com API uses the **HTTP** protocol for communication and **JSON**
for serialization of request and response bodies.  All URLs start with
`https://api.tiramizoo.com/`. **SSL** is mandatory.

Any programming language used in a web development context will usually
feature libraries for conveniently accessing a [restful](http://en.wikipedia.org/wiki/Representational_state_transfer) [JSON](http://en.wikipedia.org/wiki/JSON) web service - so do
PHP, Ruby, Java, .Net and Python to name only a few. Integrating restful
JSON services usually is also much faster and easier than working with
[SOAP](http://en.wikipedia.org/wiki/SOAP) and other XML based or stateful services.


If you encounter a problem when using the API, please
[let us know](https://github.com/tiramizoo/tiramizoo.github.com/issues/new).

Plugins
-------

Currently we are providing plugins for [osCommerce](http://www.oscommerce.com/)
and [xt:Commerce](http://www.xt-commerce.com/), OXID and magento are pending.
Take a look at our [plugins
page](/plugins.html).

Request an API Key
------------------

For accessing the API - either via a plugin or via the API directly - you
will need an API key. Please register a free account at [tiramizoo.com](https://www.tiramizoo.com)
with username and password or via facebook in order to get one. The API key
can be found via Profile / Edit profile when logged in.

Versioning
----------

All API urls have to be prefixed with API version, e.g. `/v1/quotes`.

Authentication
--------------

Some API calls require authentication with an API token. We accept API
tokens as a URL param named `api_token`.  You must keep the API token
secret. You can always request a new API token.

Making Requests
---------------

The following examples use the `curl` shell command and are rendered as a
single copy-pastable line with a multiline representation for easy
reading.

A minimal `POST` request to the **quotes** resource which doesn't require
authentication:

{% highlight bash %}
curl -H 'Content-Type: application/json'
  -d '{ "pickup": { "address_line_1": "Im Dol 1, Berlin", "postal_code": "14195", "country_code": "de" },
        "delivery": { "address_line_1": "Thujaweg 1, Berlin", "postal_code": "14195", "country_code": "de" },
        "items": [{
          "width": 1,
          "height": 1,
          "length": 1,
          "weight: 1
        }]
      }'
  "https://api.tiramizoo.com/v1/quotes"
{% endhighlight %}

{% highlight bash %}
curl -H 'Content-Type: application/json' -d '{"pickup":{"address_line_1":"Im Dol 1, Berlin","postal_code":"14195","country_code":"de"},"delivery":{"address_line_1":"Thujaweg 1, Berlin","postal_code":"14195","country_code":"de"},"items":[{"width":1,"height":1,"length":1,"weight":1}]}' "https://api.tiramizoo.com/v1/quotes"
{% endhighlight %}

Detailed Documentation
----------------------

* [Sandbox](/sandbox.html)
* [Quotes](/quotes.html)
* [Orders](/orders.html)
* [Web Hooks](/web_hooks.html)
