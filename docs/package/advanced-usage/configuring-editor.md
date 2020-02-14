---
title: Configuring the editor
---

The Mailcoach editor is disabled by default, you can disable it by chaning the `mailcoach.editor.enabled` config to `true`.

The editor supports image uploads and uses the `laravel-medialibrary` package to automatically resize the uploaded images to a maximum width and height. The image ratio will stay untouched.

You can configure the maximum image width and height using the `mailcoach.editor.uploads.max_width` and `mailcoach.editor.uploads.max_height` config settings.

## Storing Images on S3

The editor configuration also takes a `mailcoach.editor.uploads.disk_name` config. By default this uses the public storage disk, but this can be changed to any [Laravel supported filesystem drivers](https://laravel.com/docs/master/filesystem#driver-prerequisites).
