# maubot-webhook
A [maubot](https://github.com/maubot/maubot) plugin to send messages using webhooks.


## Features
- Jinja2 Templating
- JSON support
- HTTP Basic and Token Bearer Authorization


## Installation
Either download an `.mbp` file from the [release assets](https://github.com/jkhsjdhjs/maubot-webhook/releases) or [build one yourself](#building).
Then, [upload](https://docs.mau.fi/maubot/usage/basic.html#uploading-plugins) it to your maubot instance.

Furthermore this plugin requires Jinja2 for template rendering. But since maubot already depends on Jinja2, you shouldn't have to install it manually.


## Usage
Create a new instance in the maubot management interface and select `me.jkhsjdhjs.maubot.webhook` as `Type`.
The client selected as `Primary user` will be used to send the messages.

Each instance of this plugin provides a single webhook.
To create multiple webhooks, just instantiate this plugin multiple times.


## Configuration
This plugin has the following settings you can configure:


### `path`
The path the webhook will be available at.
It must start with a `/` or be empty.
It is relative to the webapp base URL of the instance:

```
http://your.maubot.instance/_matrix/maubot/plugin/<instance ID>/<path>
```

The URL under which the webhook is made available is logged on instance startup, so if you're unsure, you can check the logs.

The path supports variable resources, which can be used to extract information from the request URL to format the [`room`](#room) and the [`message`](#message).
Further information on this can be found in [the formatting section](#formatting).

*The instance has to be restarted for changes to this setting to take effect:
Click the `Running` switch to stop the instance, then save. Click the `Running` switch again to start it, then save again.*


### `method`
Specifies the HTTP method that can be used on the given path.
Should be one of `GET`, `POST`, `PUT`, `DELETE`, `PATCH`, `HEAD`, `OPTIONS` or `*` for any method.
This setting is case-insensitive.

See also: https://docs.aiohttp.org/en/stable/web_reference.html?highlight=add_route#aiohttp.web.UrlDispatcher.add_route

*The instance has to be restarted for changes to this setting to take effect:
Click the `Running` switch to stop the instance, then save. Click the `Running` switch again to start it, then save again.*


### `room`
The room the message shall be sent to when the webhook is triggered.
Supports formatting [as defined below](#formatting).


### `message`
The message that is sent when the webhook is triggered.
Supports formatting [as defined below](#formatting).


### `auth_type`
This can be used to protect a webhook against unauthorized access.
Can be one of `Basic` for HTTP basic auth with username and password or `Bearer` for bearer token auth.
Leave empty to disable authorization.
The username/password or token is specified via the [`auth_token`](#auth_token) option.


### `auth_token`
This specifies the username/password or token for authorization, depending on [`auth_type`](#auth_type).
If `auth_type` is `Basic`, this must be the username and password, separated by a colon (\<username\>:\<password\>).
If `auth_type` is `Bearer`, this is the token used for token bearer authorization, so requests must carry an `Authorization: Bearer <token>` header.


### `markdown`
This setting takes a boolean and specifies if the message should be sent as Markdown or as plaintext.
If `false` (the default), the message will be sent as plaintext.
Otherwise it will be sent as Markdown.


### `force_json`
This setting takes a boolean and specifies whether the request body should be interpreted and parsed as json, even if the content type says otherwise.



## Formatting
The `room` and `message` options can be formatted with Jinja2 using values from the path, the query string and the request body.
Values extracted from the path are available via the `path` variable. A path-variable named `foo` can be accessed via `{{ path.foo }}`.
Similarly, query parameters are available via the `query` variable.
The request body in plain text is available as `$body`.

If a request with content-type `application/json` is received (or if [`force_json`](#force_json) is enabled), the request body will be parsed as such and made available via the `json` variable.

For more information on Jinja2 templates please refer to https://jinja.palletsprojects.com/en/3.1.x/templates/.  
For more information on URL path templates in aiohttp, see https://docs.aiohttp.org/en/stable/web_quickstart.html#variable-resources.



## Building
Use the `mbc` tool to build this plugin:
```
mbc build
```
Optionally use the `-u` switch to upload it to your maubot instance, if configured:
```
mbc build -u
```

Since `.mbp` files are just zip archives with a different name, you can also just zip the files of this repository:
```
zip -9r plugin.mbp *
```


## License
<img align="right" src="https://www.gnu.org/graphics/agplv3-155x51.png"/>

This project is licensed under the GNU Affero General Public License v3.0, see [LICENSE](LICENSE).
