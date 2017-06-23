
JSON standards on property names
======

At my job, we are debating JSON API Naming standards for JSON property names.  Some in our company believe **property names must always be camelCase strings.**

I agree for simplistic cases, however, in many situations it is incredibly beneficial to have dynamic property names that are NOT camelCase and not even valid Java/javascript variable names.

The difference here is NOT just academic debate but has **significant consequences on developer productivity and processing performance!**   

Let's take a deeper look and understand why rather than blindly dictating camelCase for all property names.   Understanding why will help us create better, more efficient and natural APIs while not burdening developers with make-work standards that add no value yet increase work.

Why?
------

Anytime you see curly braces in JSON it represents a map with keys and values.  This is true whether the JSON is parsed in javascript or Java.   A Map in both javascript and Java has the following characteristics:
* Keys cannot be duplicated.
* A value can be looked up efficiently by key.

So, if we have a bunch of car records in a data structure, blindly following the naming standard would yield something like this:

```
{
    "carsAsList": [
        {
            "vin" : "2HGFG12809H518926",
            "year": "2017.06.19",
            "cyl" : 6
        },
        {
            "vin" : "ZFF68NHA2D0192834",
            "year": "2017.06.18",
            "cyl" : 8
        }
    ]   
}
```

Suppose there are 1,000 records instead of just two in the list of cars above.   And suppose
that within the javascript or Java code processing this data we know we want the record for a particular car with
vin = ZFF68NHA2D0192834.  How do we find the record on that particular car?   There are several approaches.   We could
do a linear search thru the list looking for vin = ZFF68NHA2D0192834.   Or, we could transform the list into a map by vin and then
directly retrieve the desired record.   The point is, with this structure we have to do some thinking and some programming.  It's **not efficient in computer resources and not efficient in developer productivity.**

On the other hand, if we recognize we have a list of vehicles with vin as the unique primary key, we can structure the JSON like this:

```
{
    "carsMapByVin" : {
        "2HGFG12809H518926" : {
            "year": "2017.06.19",
            "cyl" : 6
        },
        "ZFF68NHA2D0192834" : {
            "year": "2017.06.18",
            "cyl" : 8
        }
    }
}
```

Now, with this improved structure, if we want the record for vin = ZFF68NHA2D0192834 we simply do this:
```
carsMapByVin["ZFF68NHA2D0192834"];         --javascript--
carsMapByVin.get("ZFF68NHA2D0192834");     --Java--
```
With this approach there is no thinking, searching or extra code required by the developer, it just works naturally and efficiently.

In real-world scenarios we often deal with nested hierarchies of maps within maps within maps.  The benefits and drawbacks of these two approaches are 
greatly magnified when working with nested maps; still just as easy when the keys are natural primary unique keys but even more troublesome when 
the keys are simple assigned names.

Who agrees with me?
------

Google and Facebook agree with me.


### Google JSON Style Guide
[https://google.github.io/styleguide/jsoncstyleguide.xml#Key_Names_in_JSON_Maps](https://google.github.io/styleguide/jsoncstyleguide.xml#Key_Names_in_JSON_Maps)

> The **property name naming rules do not apply when a JSON object is used as a map.** A map (also referred to as an associative array) is a data type with arbitrary key/value pairs that use the keys to access the corresponding values. JSON objects and JSON maps look the same at runtime; this distinction is relevant to the design of the API. The API documentation should indicate when JSON objects are used as maps.

> The keys of a map do not have to obey the naming guidelines for property names. Map keys may contain any Unicode characters. Clients can access these properties using the square bracket notation familiar for maps (for example, result.thumbnails["72"]).

```javascript
{
  // The "address" property is a sub-object
  // holding the parts of an address.
  "address": {
    "addressLine1": "123 Anystreet",
    "city": "Anytown",
    "state": "XX",
    "zip": "00000"
  },
  // The "thumbnails" property is a map that maps
  // a pixel size to the thumbnail url of that size.
  "thumbnails": {
    "72": "http://url.to.72px.thumbnail",
    "144": "http://url.to.144px.thumbnail"
  }
}
```


### Facebook Graph API
[https://developers.facebook.com/docs/graph-api/using-graph-api](https://developers.facebook.com/docs/graph-api/using-graph-api)

In the following API documentation from Facebook, notice how userids are used as property names for efficient and convenient lookup.

```
{
  "{user-id-a}": {
    "data": [
      {
        "id": "12345",
        "picture": "{photo-url}",
        "created_time": "2014-07-15T15:11:25+0000"
      }
      ... // More photos
    ]
  },
  "{user-id-b}": {
    "data": [
      {
        "id": "56789",
        "picture": "{photo-url}",
        "created_time": "2014-01-15T12:24:47+0000"
      }
      ... // More photos
    ]
  },
}
```

**_-- David Hickman, 2017.06.18_**

---
