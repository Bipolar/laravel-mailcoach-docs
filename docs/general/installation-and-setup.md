---
title: Installation & setup
---

This package can be installed using Composer. In your `composer.json`  you must for add the `satis.mailcoach.app` repository.

```php
"repositories": [
    {
        "type": "composer",
        "url": "https://satis.mailcoach.app"
    }
],
```

Next, you need to create a file called `auth.json` and place it either next to the `composer.json` file in your project, or in the composer home directory. You can determine the composer home directory on *nix machines by using this command.

```bash
composer config --list --global | grep home
```

This is the content you should put in `auth.json`:

```php
{
    "http-basic": {
        "satis.mailcoach.app": {
            "username": "<YOUR mailcoach.app USERNAME HERE>",
            "password": "<YOUR-LICENSE-KEY-HERE>"
        }
    }
}
```

With the configuration above in place you'll be able to install the package into your project using this command:

```bash
composer require "spatie/laravel-mailcoach:^1.0.0"
```

## Configure an email sending service

Mailcoach can send out mail via various email sending services and can handle the feedback (opens, clicks, bounces, complaints) those services give.

Follow the instruction on the dedicated docs page of each supported service.

- [Amazon SES](TODO: add link)
- [Mailgun](TODO: add link)
- [Sendgrid](TODO: add link)

If want to use another email sending service you should manually configure it. Here are instructions on how you can [manually handle feedback](TODO: add link).


## Prepare the database

You need to publish and run the migration:

```bash
php artisan vendor:publish --provider="Spatie\Mailcoach\MailcoachServiceProvider" --tag="migrations"
php artisan migrate
```

## Add the route macro

You must register the routes needed to handle subscription confirmations, open, and click tracking. We recommend that you don't put this in your routes file, but in the `map` method of `RouteServiceProvider`

```php
Route::mailcoach('mailcoach');
```

## Schedule the calculate statistics command

In the console kernel, you should schedule these commands.

```php
// in app/Console/Kernel.php
protected function schedule(Schedule $schedule)
{
    // ...
    $schedule->command('mailcoach:calculate-statistics')->everyMinute();
    $schedule->command('mailcoach:send-campaign-summary-mails')->hourly();
}
```

## Publish the config file

You must publish the config file with this command.

```bash
php artisan vendor:publish --provider="Spatie\Mailcoach\MailcoachServiceProvider" --tag="config"
```

Below is the default content of the config file:

```php
return [

    /*
     * Here you can specify which jobs should run on which queues.
     * Use an empty string to use the default queue.
     */
    'perform_on_queue' => [
        'calculate_statistics_job' => '',
        'register_click_job' => '',
        'register_open_job' => '',
        'send_campaign_job' => '',
        'send_mail_job' => '',
        'send_test_mail_job' => '',
    ],

    /*
     * By default only 5 mails per second will be sent to avoid overwhelming your
     * e-mail sending service. To use this feature you must have Redis installed.
     */
    'throttling' => [
        'enabled' => false,
        'redis_connection_name' => 'default',
        'redis_key' => 'laravel-mailcoach',
        'allowed_number_of_jobs_in_timespan' => 5,
        'timespan_in_seconds' => 1,
        'release_in_seconds' => 5,
    ],

    /*
       * You can customize some of the behavior of this package by using our own custom action.
       * Your custom action should always extend the one of the default ones.
       */
    'actions' => [
        'personalize_html_action' => \Spatie\Mailcoach\Actions\PersonalizeHtmlAction::class,
        'prepare_email_html_action' => \Spatie\Mailcoach\Actions\PrepareEmailHtmlAction::class,
        'prepare_webview_html_action' => \Spatie\Mailcoach\Actions\PrepareWebviewHtmlAction::class,
        'subscribe_action' => \Spatie\Mailcoach\Actions\SubscribeAction::class,
        'confirm_subscription_action' => \Spatie\Mailcoach\Actions\ConfirmSubscriptionAction::class,
    ],
];
```

## Install and configure redis

It's common for e-mail providers to limit the number of e-mails you can send within a given amount of time. The package uses Redis to throttle e-mails, so make sure it's available on your system. You must specify a valid Redis connection name in the `throttling.redis_connection_name` key.

By default, we set this value to the default Laravel connection name, `default`.

## Add authorization to Mailcoach UI

By default you can only use the Mailcoach on a local environment. To use it in other environment you need to register an gate check. Head over to [the section on authorizing users](TODO: add link) to learn more.
