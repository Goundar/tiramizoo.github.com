---
layout: page
title: Sandbox
---

You can access [https://sandbox.tiramizoo.com](https://sandbox.tiramizoo.com) for testing purposes.
All api requests should point to [https://api-sandbox.tiramizoo.com](https://api-sandbox.tiramizoo.com)

After registering account, sandbox API token can be found on profile page.

All orders and payments will not take place, however user will be able to see flow of Tiramizoo service.

Please, take in mind, that all records on sandbox can be removed.

## Test data

#### Quotes

Known responses from quotes API, with given parameters:

* Successs response

{% highlight bash %}
curl -H 'Content-Type: application/json' -d '{"pickup":{"address_line_1":"Kadettenweg 2","postal_code":"12205","country_code":"de","intervals":true},"delivery":{"address_line_1":"Kadettenweg 29","postal_code":"12205","country_code":"de"},"items":[{"width":42,"height":59,"length":3,"weight":5}]}' https://api-sandbox.tiramizoo.com/v1/quotes
{% endhighlight %}

`201 Created`

{% highlight javascript %}
[
  {...}
]
{% endhighlight %}

* Error response when package is too big

{% highlight bash %}
curl -iH 'Content-Type: application/json' -d '{"pickup":{"address_line_1":"im dol 2","postal_code":"14195","country_code":"de","intervals":true},"delivery":{"address_line_1":"Dohnenstieg 19","postal_code":"14195","country_code":"de"},"items":[{"width":42,"height":59,"length":3,"weight":5}]}' https://api-sandbox.tiramizoo.com/v1/quotes
{% endhighlight %}

`422` Unprocessable Entity

{% highlight javascript %}
{
  "code": "items_too_big",
  "message": "items too big"
}
{% endhighlight %}

* Error response when no couriers found

{% highlight bash %}
curl -iH 'Content-Type: application/json' -d '{"pickup":{"address_line_1":"im dol 2","postal_code":"14195","country_code":"de","intervals":true},"delivery":{"address_line_1":"Berliner Allee 2","postal_code":"14196","country_code":"de"},"items":[{"width":32,"height":45,"length":3,"weight":0.5}]}' https://api-sandbox.tiramizoo.com/v1/quotes
{% endhighlight %}

`422` Unprocessable Entity

{% highlight javascript %}
{
  "code": "no_couriers_found",
  "message": "no couriers found"
}
{% endhighlight %}

* Error response when couriers are outside business hours

{% highlight bash %}
curl -iH 'Content-Type: application/json' -d '{"pickup":{"address_line_1":"Alexanderplatz 2","postal_code":"10178","country_code":"de","intervals":true},"delivery":{"address_line_1":"Fischerinsel 2","postal_code":"10178","country_code":"de"},"items":[{"width":32,"height":45,"length":3,"weight":0.5}]}' https://api-sandbox.tiramizoo.com/v1/quotes
{% endhighlight %}

`422` Unprocessable Entity

{% highlight javascript %}
{
  "code": "outside_business_hours",
  "message": "outside business hours"
}
{% endhighlight %}
