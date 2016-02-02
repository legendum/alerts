Alerts
======

### Emails
An email has a user

* email (key)
* data:

```json
    "legendum@gmail.com":{
      "uuid":"0d599f0ec05c3bda8c3b8a68c32a1b47",
      "passwordCrypt":"blahblahblah",
      "canSend":true,
      "sentAt":1454396457000,
      "confirmedAt":1454396457000
    }
```

### Users
A user has a username, language, default email address, tokens (own and given)

* uuid (key)
* data:

```json
    "0d599f0ec05c3bda8c3b8a68c32a1b47":{
      "name":"Kevin Hutchinson",
      "email":"legendum@gmail.com",
      "username":"hutchike",
      "language":"us-gb",
      "timezone":"GMT",
      "tokens":{
        "0d599f0ec05c3bda8c3b8a68c32a1b47":["abc123"]
      }
    }

    "dd486f23d378d1f0b1ccb8637aeed416":{
      "name":"Kevin Okie",
      "email":"okiekevin@gmail.com",
      "username":"okiekevin",
      "language":"us-en",
      "timezone":"MST",
      "tokens":{
        "dd486f23d378d1f0b1ccb8637aeed416":["jea777"],
        "0d599f0ec05c3bda8c3b8a68c32a1b47":["def456"]
      }
    }
```

### Tokens
A token has a particular topic with include/exclude filters and an alert policy

* accessKey (key)
* data:

```json
    "abc123":{
      "owner":"0d599f0ec05c3bda8c3b8a68c32a1b47",
      "topic":"news",
      "sinks":["email"],
      "include":["Bernie"],
      "exclude":["Sesame Street"],
      "lastAlertAt":1454394718000,
      "alertPolicy":{"frequency":"daily","hour":10}
    }

    "def456":{
      "owner":"dd486f23d378d1f0b1ccb8637aeed416", 
      "sharer":"0d599f0ec05c3bda8c3b8a68c32a1b47",
      "topic":"news",
      "sinks":["email"],
      "include":["Bernie"],
      "exclude":["Sesame Street"],
      "lastAlertAt":1454398215000,
      "alertPolicy":{"frequency":"daily","hour":6}
    }
```

### Topics
Topics have tokens and tags

* uuid+topic (key)
* data:

```json
    "0d599f0ec05c3bda8c3b8a68c32a1b47/news":{
      "name":"news",
      "owner":"0d599f0ec05c3bda8c3b8a68c32a1b47",
      "isPrivate":false,
      "tokens":{
        "shared":{
          "def123":"dd486f23d378d1f0b1ccb8637aeed416"
        },
        "canRead":{
          "abc123":"0d599f0ec05c3bda8c3b8a68c32a1b47",
          "def456":"dd486f23d378d1f0b1ccb8637aeed416"
        },
        "canWrite":{
          "abc123":"0d599f0ec05c3bda8c3b8a68c32a1b47"
        }
      }
    }
```

### Tags

* language/tag or uuid/tag (key)
* data:

```json
    "us-en/politics":{
      "topics":["0d599f0ec05c3bda8c3b8a68c32a1b47/news"]
    },
    "0d599f0ec05c3bda8c3b8a68c32a1b47/politics":{
      "topics":["0d599f0ec05c3bda8c3b8a68c32a1b47/news"]
    }
```

### Alerts (each alerts database is named with a topic, like "alerts_news")
Alerts may come from user tokens _or_ from feeds

* epoch+token or epoch+feedUUID (key)
* data (any payload, up to a certain size)

### Feeds
Feeds have items

* url (key)
* data:

```json
    "http://www.cnet.com/rss/news/":{
      "title":"CNET News",
      "description":"",
      "lastBuildDate":"",
      "lastReadAt":1454397264000,
      "image":"",
      "language":"en-us",
      "topics":{
        "0d599f0ec05c3bda8c3b8a68c32a1b47/news":{"subscribedAt":1454401979000}
      }
    }
```

### Items

* guid (key)
* data:

```json
    "http://www.bbc.co.uk/news/business-35460398":{
      "uuid":"c0e901dab656aef0716655cec8e7eee7",
      "title":"",
      "description":"",
      "link":"",
      "pubDate":""
    }
```

## Process

### User signup

1. User signs up by providing an email address and password.
2. System creates a `user` record with a newly generated UUID.
3. System creates an `Email` record with email and password and the UUID.

### User creates a new public topic ("news")

1. A new token is created for the user
2. A new topic is created and the token is associated with the topic

### User shares the topic with another user by creating a new token

1. A new token is created for the other user as shared by this user
2. The topic is updated to include details about the shared token

### Topic owner subscribes an RSS feed to the topic

1. A new RSS feed is created with the topic included in the topic hash
2. Periodically, new items are read, and the topics in the hash are updated
3. For each new item, an alert is created for each subscribed topic
