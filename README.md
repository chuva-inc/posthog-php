# PostHog PHP

Official PostHog PHP library to capture and send events to any PostHog instance (including posthog.com).

This library uses an internal queue to make calls non-blocking and fast. It also batches requests and flushes asynchronously, making it perfect to use in any part of your web app or other server side application that needs performance.

## Installation 

Add the following to `composer.json`

```json
{
    "require": {
        "posthog/posthog-ruby": "1.0.*"
    }
}
```

And then install the dependencies:

```shell script
php composer.phar install
```

In your app, set your api key **before** making any calls.

```php
PostHog::init("YOUR_API_KEY");
```

You can find your key in the /setup page in PostHog.

## Making calls

### Capture

Capture allows you to capture anything a user does within your system, which you can later use in PostHog to find patterns in usage, work out which features to improve or where people are giving up.

A `capture` call requires
 - `distinct id` which uniquely identifies your user
 - `event name` to make sure 
   - We recommend using [verb] [noun], like `movie played` or `movie updated` to easily identify what your events mean later on.

Optionally you can submit
- `properties`, which can be an array with any information you'd like to add

For example:
```php
PostHog::capture(array(
  'distinctId' => 'user:123',
  'event' => 'movie played',
  'properties' => array(
    'movieId' => '123',
    'category' => 'romcom'
  )
));
```

### Identify
Identify lets you add metadata on your users so you can more easily identify who they are in PostHog, and even do things like segment users by these properties.

An `identify` call requires
- `distinct id` which uniquely identifies your user
- `properties` with a dict with any key: value pairs 

For example:

```php
PostHog::identify(array(
  'distinctId' => 'user:123',
  'properties' => array(
    'email' => 'john@doe.com',
    'proUser' => false
  )
));
```

The most obvious place to make this call is whenever a user signs up, or when they update their information.

### Alias

To marry up whatever a user does before they sign up or log in with what they do after you need to make an alias call. This will allow you to answer questions like "Which marketing channels leads to users churning after a month?" or "What do users do on our website before signing up?"

In a purely back-end implementation, this means whenever an anonymous user does something, you'll want to send a session ID with the capture call. Then, when that users signs up, you want to do an alias call with the session ID and the newly created user ID.

The same concept applies for when a user logs in.

If you're using PostHog in the front-end and back-end, doing the `identify` call in the frontend will be enough.

An `alias` call requires
- `distinct id` the current unique id
- `alias` the unique ID of the user before

For example:

```php
PostHog::alias(array(
  'distinctId' => 'user:123',
  'alias' => 'user:12345'
));
```

# Development

## How to release

1. Run `VERSION=X.Y.Z make release` (where X.Y.Z is the new version).

That's it! Composer will pick up the new tag and you can see the latest version at https://packagist.org/packages/posthog/posthog-php.

You might need to install [git-extras](https://github.com/tj/git-extras) for this to work.

## Thank you

This library is largely based on the `analytics-php` package.