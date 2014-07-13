---
layout: post
title: "Posting to Twitter with Python"
date: 2014-07-12 20:12:50 +0200
comments: true
categories: python
tags: twitter, api, rauth, oauth
---

In my spare time I am working on a simple [Buffer][buffer] clone to post my links on various socials without having the limitation of the Free plan nor the Awesome plan.
I decided to code it in Python, because I want to leave the PHP world and add a new skill to my resume.

I begin working by studying the different methods for posting on socials, and today was **Twitter day**.

## First step: RTFM

Before searching on Google _"Post Twitter Python"_ I wanted to read [Twitter documentation][twitterdocs]. The [REST API][restapidocs] was the one that seemed to fit what I needed,
so I went on and read the [POST statuses/update][postatus] page.

To post a tweet you need to issue a `POST` request to `https://api.twitter.com/1.1/statuses/update.json` with a `status` parameter. The request must be signed
using OAuth.

Twitter offers [different methods for authentication][auth] in order to get the necessary access tokens to issue requests. As I need this application only for personal use,
I decided to use the [dev.twitter.com authentication][devauth], that enables you to post from your personal account. Simply create a new Twitter application and generate
the access tokens[^1]. There is an example in the documentation so I thought that I was almost done.

## Getting the home timeline (aka Twitter docs are outdated)

Unfortunately, Twitter documentation for Python dev.twitter.com authentication is outdated, as it uses the [Brosner's Python-OAuth2 library][python-oauth2] that was updated 5 years ago. So I searched for a more recent implementation of the OAuth protocol for Python and I found [rauth][rauth]. There is an [example in the README][rauthexample] that show how to make a request to Twitter's API. But this example (shown below) is for the complete [three-legged authentication method][threelegged] and I didn't need that.

``` python rauth basic example for 3-legged authentication
from rauth import OAuth1Service

try:
read_input = raw_input
except NameError:
read_input = input

# Get a real consumer key & secret from https://dev.twitter.com/apps/new
twitter = OAuth1Service(
        name='twitter',
        consumer_key='J8MoJG4bQ9gcmGh8H7XhMg',
        consumer_secret='7WAscbSy65GmiVOvMU5EBYn5z80fhQkcFWSLMJJu4',
        request_token_url='https://api.twitter.com/oauth/request_token',
        access_token_url='https://api.twitter.com/oauth/access_token',
        authorize_url='https://api.twitter.com/oauth/authorize',
        base_url='https://api.twitter.com/1.1/')

request_token, request_token_secret = twitter.get_request_token()

authorize_url = twitter.get_authorize_url(request_token)

print('Visit this URL in your browser: {url}'.format(url=authorize_url))
pin = read_input('Enter PIN from browser: ')

session = twitter.get_auth_session(request_token,
        request_token_secret,
        method='POST',
        data={'oauth_verifier': pin})

params = {'include_rts': 1,  # Include retweets
    'count': 10}       # 10 tweets

r = session.get('statuses/home_timeline.json', params=params, verify=True)

for i, tweet in enumerate(r.json(), 1):
    handle = tweet['user']['screen_name']
    text = tweet['text']
    print(u'{0}. @{1} - {2}'.format(i, handle, text))

```

As you can see, to get the access tokens, you'll need to get the pin from the authorization url. But with the dev.twitter.com authentication, I already have those tokens. So I just need to get the session, no need for authorization:

``` python rauth basic example for dev.twitter.com authentication
from rauth import OAuth1Service

# Get a real consumer key & secret from https://dev.twitter.com/apps/new
twitter = OAuth1Service(
        name='twitter',
        consumer_key='1uUMxRkNp9oTplhnVh1pz96cj',
        consumer_secret='aBa89p6W96aS1sMAzzWCiH18gz5UxJNkQR4Tbeyb767pmST1dC',
        request_token_url='https://api.twitter.com/oauth/request_token',
        access_token_url='https://api.twitter.com/oauth/access_token',
        authorize_url='https://api.twitter.com/oauth/authorize',
        base_url='https://api.twitter.com/1.1/')

request_token = MY_KEY
request_token_secret = MY_SECRET

authorize_url = twitter.get_authorize_url(request_token)

session = twitter.get_session((request_token, request_token_secret))

params = {'include_rts': 1,  # Include retweets
    'count': 10}       # 10 tweets

r = session.get('statuses/home_timeline.json', params=params, verify=True)

for i, tweet in enumerate(r.json(), 1):
    handle = tweet['user']['screen_name']
    text = tweet['text']
    print(u'{0}. @{1} - {2}'.format(i, handle, text))

```

The method that did the trick was [get_session][rauth.get_session] that takes a tuple containing your access tokens
and get the current session. With that, I was able to call the Twitter APIs.

## Posting to Twitter

I was almost done. To post to Twitter I needed to change the `get` method into a `post` and change the API endpoint and
parameters. So here is the final example:

``` python Posting to Twitter with Python
from rauth import OAuth1Service

if __name__ == '__main__':
    twitter = OAuth1Service(
            name='twitter',
            consumer_key=APP_CONSUMER_KEY,
            consumer_secret=APP_CONSUMER_SECRET,
            request_token_url='https://api.twitter.com/oauth/request_token',
            access_token_url='https://api.twitter.com/oauth/access_token',
            authorize_url='https://api.twitter.com/oauth/authorize',
            base_url='https://api.twitter.com/1.1/')

    request_token = MY_KEY
    request_token_secret = MY_SECRET

    session = twitter.get_session((request_token, request_token_secret))

    params = {'status': 'Tweet from Python'}
    r = session.post('statuses/update.json', params, verify=True)

    print r.json()

```
[buffer]: https://bufferapp.com/
[twitterdocs]: https://dev.twitter.com/docs/
[restapidocs]: https://dev.twitter.com/docs/api
[postatus]: https://dev.twitter.com/docs/api/1.1/post/statuses/update
[auth]: https://dev.twitter.com/docs/auth/obtaining-access-tokens
[devauth]: https://dev.twitter.com/docs/auth/tokens-devtwittercom
[python-oauth2]: https://github.com/brosner/python-oauth2
[threelegged]: https://dev.twitter.com/docs/auth/3-legged-authorization
[rauth]: https://github.com/litl/rauth
[rauthexample]: https://github.com/litl/rauth/blob/master/examples/twitter-timeline-cli.py
[rauth.get_session]: https://rauth.readthedocs.org/en/latest/api/?highlight=get_session#rauth.OAuth1Service.get_session

[^1]: Ensure that you have given your applicaton the "Read/Write" permission, before generating the tokens.
