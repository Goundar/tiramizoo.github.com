---
layout: page
title: Overview
---

You can easily offer the Tiramizoo delivery service to your customers, within your current
e-commerce platform. Either download and install our [OXID plugin](https://github.com/tiramizoo/oxid-plugin) or do a custom integration by
programming against the public REST/JSON API. The API is "eat your own
dogfood" style - tiramizoo uses it internally for the "book a courier"
website, administration and plugins, so you can count that it is well maintained.

For a detailed overview on how the workflow with tiramizoo.com looks like, please go to [our website](https://www.tiramizoo.com).

For testing purposes use the [tiramizoo.com sandbox](/sandbox.html). It's a full copy of the production environment
except no delivery you trigger there is dispatched to our couriers.

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

### API Tokens

For accessing the API - either via a plugin or via the API directly - you
will need an API token. Please register an test account at [sandbox.tiramizoo.com](https://sandbox.tiramizoo.com)
with username and password or via facebook in order to get one. The API token
can be found via Profile / Edit profile when logged in.

To simplify integration in some examples test user's API token is used

When ready with integration please register an account on our production system at [tiramizoo.com](https://tiramizoo.com)


### Versioning

All API urls have to be prefixed with API version, e.g. `/v1/orders`.


### Authentication

Some API calls require authentication with an API token. We accept API
tokens as a URL param named `api_token`. You must keep the API token
secret. You can always request a new API token.
In case of providing wrong token, `401 Unauthorized` response will be returned.


### Making Requests

The following examples use the `curl` shell command and are rendered as a
single copy-pastable line with a multiline representation for easy
reading.

A minimal `POST` request to the **quotes** resource which doesn't require
authentication:

{% highlight bash %}
curl -v -H 'Content-Type: application/json' -d '{
  "pickup_postal_code": "14195",
  "delivery_postal_code": "12437"
}' "https://api-sandbox.tiramizoo.com/v1/quotes"
{% endhighlight %}

##### Try it yourself #####
{% highlight bash %}
curl -v -H 'Content-Type: application/json' -d '{"pickup_postal_code":"14195","delivery_postal_code":"12437"}' "https://api-sandbox.tiramizoo.com/v1/quotes"
{% endhighlight %}


### Airbrake support

Additionaly you can provide your AIRBRAKE_API_KEY ([More about Airbrake](https://airbrake.io/pages/home) ) on [www.tiramizoo.com/dashboard/development_settings/edit](https://sandbox.tiramizoo.com/dashboard/development_settings/edit) and in a case web hook notification fails we will send you error notification to your airbrake account
