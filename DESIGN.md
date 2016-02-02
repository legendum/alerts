Alerts
======

### Emails
An email has a user

* email (key)
* data:

    'legendum@gmail.com': {
      uuid: '0d599f0ec05c3bda8c3b8a68c32a1b47'
      passwordCrypt: 'blahblahblah',
      canSend: true,
      sentAt: 1454396457000
    }

### Users
A user has a username, langauge, default email address, tokens (own and given)

* uuid (key)
* data:

    '0d599f0ec05c3bda8c3b8a68c32a1b47': {
      name: 'Kevin Hutchinson',
      email: 'legendum@gmail.com',
      username: 'hutchike',
      language: 'us-gb',
      timezone: 'GMT',
      tokens: {
        '0d599f0ec05c3bda8c3b8a68c32a1b47': ['abc123'] // legendum@gmail.com
      }
    }

    'dd486f23d378d1f0b1ccb8637aeed416': {
      name: 'Kevin Okie',
      email: 'okiekevin@gmail.com',
      username: 'okiekevin',
      language: 'us-en',
      timezone: 'MST',
      tokens: {
        'dd486f23d378d1f0b1ccb8637aeed416': ['jea777'], // okiekevin@gmail.com
        '0d599f0ec05c3bda8c3b8a68c32a1b47': ['def456']  // legendum@gamil.com
      }
    }

### Tokens
A token has a particular topic with include/exclude filters and an alert policy

* accessKey (key)
* data:

    abc123: {
      owner: '0d599f0ec05c3bda8c3b8a68c32a1b47', // legendum@gmail.com
      topic: 'news',
      sinks: ['email'],
      include: [ 'Bernie' ],
      exclude: [ 'Sesame Street' ],
      lastAlertAt: 1454394718000 // where Kevin Hutchinson is up to
      alertPolicy: {frequency: 'daily', hour: 10}
    }

    def456: {
      owner: 'dd486f23d378d1f0b1ccb8637aeed416', // okiekevin@gmail.com
      sharer: '0d599f0ec05c3bda8c3b8a68c32a1b47', // legendum@gmail.com
      topic: 'news',
      sinks: ['email'],
      include: [ 'Bernie' ],
      exclude: [ 'Sesame Street' ],
      lastAlertAt: 1454398215000, // where Kevin Okie is up to
      alertPolicy: {frequency: 'daily', hour: 6}
    }

### Topics
Topics have tokens and tags

* uuid+topic (key)
* data:

    '0d599f0ec05c3bda8c3b8a68c32a1b47/news': {
      name: 'news',
      owner: '0d599f0ec05c3bda8c3b8a68c32a1b47', // user's UUID
      isPrivate: false,
      tokens: {
        shared: {
          'def123': 'dd486f23d378d1f0b1ccb8637aeed416'  // okiekevin@gmail.com
        },
        canRead: {
          'abc123': '0d599f0ec05c3bda8c3b8a68c32a1b47', // legendum@gmail.com
          'def456': 'dd486f23d378d1f0b1ccb8637aeed416'  // okiekevin@gmail.com
        },
        canWrite: {
          'abc123': '0d599f0ec05c3bda8c3b8a68c32a1b47'  // legendum@gmail.com
        }
      }
    }

### Tags

* language/tag or uuid/tag (key)
* data:

    'us-en/politics': {
      topics: ['0d599f0ec05c3bda8c3b8a68c32a1b47/news'] // public topic
    },
    '0d599f0ec05c3bda8c3b8a68c32a1b47/politics': {
      topics: ['0d599f0ec05c3bda8c3b8a68c32a1b47/news'] // private topic
    }

### Alerts

* epoch + tokenId (key)
* data (any payload, up to a certain size)

### Feeds
Feeds have items

* url (key)
* data:

    'http://www.cnet.com/rss/news/': {
      title: 'CNET News',
      description: '',
      lastBuildDate: '',
      lastReadAt: 1454397264000, // important
      image: '',
      language: 'en-us',
      topics: ['0d599f0ec05c3bda8c3b8a68c32a1b47/news']
    }

### Items

* guid (key)
* data:

    'http://www.bbc.co.uk/news/business-35460398': {
      title: '',
      description: '',
      link: '',
      pubDate: ''
    }


## Process

### User signup

1. User signs up by providing an email address and password.
2. System creates a `user` record with a newly generated UUID.
3. System creates an `Email` record with email and password and the UUID.

### User creates a new public topic ("news")

### User shares the topic with another user by creating a new token

### Topic owner subscribes an RSS feed to the topic
