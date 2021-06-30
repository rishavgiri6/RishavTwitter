# RishavTwitter_tweepy!



A modular clone modelled on the Twitter social networking platform built in Flask, Postgres and Python supporting all CRUD functionalities

### Stack

- Python
- Flask
- postgres



## Features
- [x] User can register/signin/signout
- [x] User can post tweets
- [x] Added a simple all users profile management page
- [x] User can follow users and get their tweets
- [x] Add more tests
- [ ] Refactoring and like functionality
- [ ] UI updation

##Future Integrations:
Quote a Tweet: One of the most used aspects of Twitter is retweets with comments. To do that, users can click the retweet icon in the app then select the person you want to retweet. A retweet is used to appreciate one’s tweet and agrees with them.

Create lists: Users can create a list with people they often chat or view. These lists appear on a separate tab so that users can check it as and when they require it. These lists can be changed to the public or private view.

Tagging users: If a particular tweet is related to the users, they can be tagged in the tweet. So once the tweet goes live, the tagged members are notified.

Pining tweet: Users can pin tweets in the Twitter clone app, and then it will appear on top of the profile. People usually use this feature to gain more attention to a particular link or tweet.

Website cards: Users can post links as images through these web card features. Other users are navigated to the connected website when they click on it.

## Data layout

### Users

User IDs are generated sequencially by [Redis's INCR](https://redis.io/commands/incr):

```js
INCR user:ids => 123
```

Each User is stored in [Redis Hashes](https://redis.io/topics/data-types). The Redis key of user is something like `user:123` and fields are:

```
name (User name)
pass (Encripted password)
fullname (User's full name)
```

Every time someone registered, this app execute the following steps.

```
INCR user:ids => 123
HMSET user:123 name "bob" pass "asdfjkl;" fullname "Bob Marley"
```

### Followers and followings

For followers and followings, [Redis's Sorted Set](https://redis.io/topics/data-types) data structure is a good fit because whenever someone start following you, or you start following someone else, that history is always sorted by insertion order (the timestamp) using the score of Sorted Set.

```
user:123:followers => Sorted Set of user IDs of all the followers users
user:123:followings => Sorted Set of user IDs of all the follwings users
```

When adding new follower:

```
ZADD user:123:followers 1401267618 456 =>> Add user 456 with time 1401267618
```

### Tweets

Tweets also have sequencial IDs, generated by [Redis's INCR](https://redis.io/commands/incr) which is same as User ID.

```js
INCR tweet:ids => 789
```

Every time someone tweets, that tweet is stored in a hash, where each key is something like `tweet:789`, with the following fields:

```
text (Body of the tweet)
created_at (Timestamp created)
user_id (User ID)
```

### Timeline

Another interesting part of this application is the Timeline. where users see all the latest updates in user's home page. Since we show those updates in chronological order, from the most recent update to the oldest, again, the Sorted Set data structure is the good fit for this use.

The app needs to create two Timelines, one is [home timeline](https://dev.twitter.com/rest/reference/get/statuses/home_timeline) where all new users will see, and the other is [user timeline](https://dev.twitter.com/rest/reference/get/statuses/user_timeline) where all updates are customized per user.

```
user:123:user_timeline => Sorted Set of tweet IDs posted by the user
user:123:home_timeline => Sorted Set of tweet IDs posted by the user and the users they follow
```

For example, retrieving latest 5 tweet IDs from the user timeline is:

```
ZREVRANGE user:123:home_timeline 0 4
```

## Development

### Download project

Clone this repository and `cd` to the project's home directory, run:

```
$ npm install
```

This will install all required dependencies.

### Install Redis

For Mac OSX, the easiest way would be to use [Homebrew][homebrew].

```
$ brew install redis
```

Otherwise, please look for other options in [Redis's official documentaiton][redis].

### Start application

This application is based on the web application framework called [Express][express]. To start the application, run the following command:

```
$ npm start
```

It will serve the application at `http://localhost:3000/`.

## Deployment

For deployment, `Procfile` is packaged as a default configuration to integrate with Heroku. If you are not familiar with Heroku I would recommend you to first read through the documentation of [how to get started with Node.js on Heroku][heroku-getting-started-with-node].

The whole deployment process should be as simple as the follwing 3 steps.

Login to Heroku:

```
$ heroku login
````

Create an app on Heroku:

```
$ heroku create
```

Deploy an app:

```
$ git push heroku master
```

## References

- [antirez/retwis][retwis] - A Twitter-toy clone written in PHP and Redis
- [twissandra/twissandra][twissandra] - Twissandra is an example project, created to learn and demonstrate how to use Cassandra.

-[node]: https://nodejs.org/
-[redis]: http://redis.io/
-[homebrew]: http://brew.sh/
-[express]: http://expressjs.com/
-[heroku-getting-started-with-node]: https://devcenter.heroku.com/articles/getting-started-with-nodejs#introduction
-[retwis]: https://github.com/antirez/retwis
-[twissandra]: https://github.com/twissandra/twissandra


## License

MIT License

Copyright (c) 2020 Rishav Giri

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.


