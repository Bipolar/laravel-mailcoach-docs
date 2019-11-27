---
title: Mailgun
---

`laravel-mailcoach-mailgun-feedback` can handle bounce feedback coming from Mailgun. All e-mail address that permanently bounce will be unsubscribed from all lists.

You can install the add-on package via composer:

```bash
composer require spatie/laravel-mailcoach-mailgun-feedback
```

## Migrating the database

Under the hood this package uses [spatie/laravel-webhook-client](https://github.com/spatie/laravel-mailcoach) to process handle webhooks. You are required to publish its migration to create the `webhook_calls` table. You can skip this step if your project already uses the `laravel-webhook-client` package directly.

```php
php artisan vendor:publish --provider="Spatie\WebhookClient\WebhookClientServiceProvider" --tag="migrations"
```

After the migration has been published, you can create the `webhook_calls` table by running the migrations:

```php
php artisan migrate
```

## Adding the webhooks route

You must use this route macro in your route service provider. We recommend to not apply the `web` group middleware to this route as that would cause an unnecessary session to be started for each webhook call.

You can replace `mailgun-feedback` with any url you'd like.


```php
Route::mailgunFeedback('mailgun-feedback');
```

## Configuring webhooks

At Mailgun you must [configure a new webhook](https://www.mailgun.com/blog/a-guide-to-using-mailguns-webhooks/).

In the `mailcoach` config file you must add this section.

```php
// in config/mailcoach.php

    'mailgun_feedback' => [
        'signing_secret' => env('MAILGUN_SIGNING_SECRET'),
   ],
```

In your `.env` you must add a key `MAILGUN_SIGNING_SECRET` with the Mailgun signing secret you'll find at the Mailgun dashboard as its value. 

You must use this route macro somewhere in your routes file. Replace `'mailgun-feeback'` with the url you specified at Mailgun when setting up the webhook there.

## Using the correct mail driver

If you haven't done so already, you must configure Laravel to use the Mailgun driver. Follow the instruction in [the mail section of the Laravel docs](https://laravel.com/docs/master/mail#driver-prerequisites).
