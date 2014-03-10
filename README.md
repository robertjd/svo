# Subject - Verb - Object

This is an exploration of a data structure for flexible collection of user behaviour within an application,
inspired by [Subject–verb–object][svo-wikipedia] sentence structure from linguistics.

Motivated by my integration experience with services such as Mixpanel, Kiss Metrics, et. al, hereto known as "services"

Some common issues were:

* Service independence, i.e.
  * Without your own copy of that data you couldn't backfill new services that you wanted to try out.  Hereto known as the "Data Sourcing Problem"

  * The method by which A) a unique user is identified and B) their events are aggregated is not in your control, and is often poorly documented.  Combining streams from multiple sessions (e.g. different devices) required a lot of research, usually with some support requests involved.  In the end it still felt like a black box.

* Discrepancies between the words used in event data and the words used within your team to describe your product can be disastrous, hereto the "Product Language Problem"

This SVO structure is an attempt to address the Data Sourcing Problem by developing an abstract structure which can capture event data in an origin form that can be mapped or reduced to the format required by a service.  In my experiments I would store these SVO objects in a MongoDB collection and then send them to Keen.IO for on-demand mapping and reducing, the Keen.IO web interface was used to prove that I could achieve the same level of insight from this origin data that we were getting from the other services.

The "Product Language Problem" is not directly addressed by this experiment, and in my opinion this problem is best solved by discipline.  However this SVO structure could provide a framework for such discipline.

Initial experiments were positive and I wish to develop this further.

### Subject

The state of the person at the time they perform the behaviour.  I encourage this field to be as heavy as you need it.  In my experiments I would store ~10 string properties in here.

```
{
    subject: {
        user:{
            name: "Robert",
            id: 1234"
        }
    }
}
```

### Verb

What is the subject doing?  This one I encourage to keep tight and simple.

```
{
    subject: {
        user:{
            name: "Robert",
            id: "1234"
        }
    },
    verb: "saw"
}
```

### Object

What is the thing that the subject is working with?

```
{
    subject: {
        user:{
            name: "Robert",
            id: "1234"
        }
    },
    verb: "saw",
    object: {
        page: "Homepage"
    }
}
```

### Context

Use this to add contextual information that can be used for segmentation and click-stream analysis.  These properties give context to the behaviour but do not describe it.

Aa page view, as seen by a request handler on a server:

```
{
    subject: {
        user{
            name: "Robert",
            id: "1234"
        }
    },
    verb: "saw",
    object: {
        page: "Homepage"
    },
    context:{
        ts: "2014-03-10T18:34:18.383Z",
        request: {
            uri: "/",
            ip: "183.23.45.123",
            user-agent: "Mozilla",
            accept-language: "en",
            cookie: "iMdAazqJ5Ez/d6r0WeDJVawwJxZ3YNAfZHBcrWjR"
        }
    }
}
```

### Things that might be useful

Add a random string to every SVO object, before writing it to origin storage.  I did this in my experiments so that I could scan for any obvious duplication:

```
{
    subject: { ... },
    verb: { ... },
    object: { ... },
    context: { ... },
    entropy: "ffd821e0-a888-11e3-a5e2-0800200c9a66"
}
```

### Caveats

* Massive footprint for the source data, a good data store (other than mongo db) should be chosen.
* You'll spend some time writing the data transformations for your services


[svo-wikipedia]: http://en.wikipedia.org/wiki/Subject%E2%80%93verb%E2%80%93object