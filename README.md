OpenLog - Track client side Javascript errors in real time. [![Build Status](https://travis-ci.org/MarshallOfSound/OpenLog.svg?branch=master)](https://travis-ci.org/MarshallOfSound/OpenLog)
==========================================================

OpenLog is a lightweight Javascript plugin (< 1kb gzipped!) that streams browser console errors & warnings in real time to a dynamic dashboard built with Node.


Installation
------------

1. Add [capture.min.js](dist/capture.min.js) to your app. We recommend placing this above any other `<script>`'s:

````html
<!-- Sends Logs To Dashboard -->
<script type="text/javascript" src="./path/to/capture.min.js"></script>
````

2. Install OpenLog server: `npm install -g openlog`
3. Start the dashboard: `openlog`

...and that's it! Now you can access the dashboard at [http://localhost:4783](http://localhost:4783).

**Username:** openlog
**Password:** password


Configuration
------------

- Frontend configuration for `capture.min.js` can be defined in the script tag (easy), or with JavaScript if you're using a build pipeline.
- OpenLog Server configuration is defined in your global NPM install directory.

### Error Type Limiting [Frontend]

Define which types of logs should be sent and recorded by OpenLog.

**Easy:**

```html
<script type="text/javascript" src="capture.min.js" data-capture="error info"></script>
```

**w/ JavaScript:**

```javascript
Log.config.capture = ['error', 'warn', 'info'];
```

### Domain Restriction [Frontend]

This setting allows you to specify origin domain name(s) permitted to send logs to the OpenLog server, and is particularly useful for reporting console noise only in a staging/production environment (as opposed to developers working locally). By default, OpenLog will send logs from all sources.

**Easy:**

```html
<!-- HOSTNAMES, separated with spaces -->
<script type="text/javascript" src="capture.min.js" data-restrict-to="mydomain.com staging.mydomain.com"></script>
```

**w/ JavaScript:**

```javascript
// HOSTNAMES array
Log.config.restrictTo = ['mydomain.com', 'staging.mydomain.com'];
```

### Custom Logging URL [Frontend / Server]

Run the OpenLog dashboard independently from your app server (or change ports).

**Easy:**

```html
<script type="text/javascript" src="capture.min.js" data-log-url="http://logs.mydomain.com"></script>
```

**w/ JavaScript:**

```javascript
// After capture.min.js included
Log.config.logUrl = 'http://logs.mydomain.com';
```

[Click here](wiki/) for more information about running OpenLog server on port 80/your own domain.

### Changing Dashboard Login [Server]

To change the username and password for the OpenLog dashboard, you'll need to enter the installation directory in your global NPM modules.

1. Enter OpenLog directory:

```bash
> cd "$(npm root -g)/openlog"
```

2. Edit `config.inc.json`:

```javascript
{
  "auth": [{
    "user": "openlog",
    "password": "password"
  }]
}
```

3. Rebuild the server:

```bash
> npm install
> npm install -g grunt-cli
> grunt build
```

Done. Your username and password will now be updated the next time you start the OpenLog server.


Using Silent Logging
------------
If you are already logging using `console.info()`, `console.warn()` and `console.error()` then OpenLog requires no extra work at all. This includes any third party vendor scripts, such as jQuery, Angular, React etc.

On top of this, OpenLog also gives you the ability to log information to the dashboard without sending it to the clients web console. Simply use OpenLog's in-built methods:

- `Log.error('Oops! This error is pretty bad.');`
- `Log.warn('You know that thing we thought wouldn't happen? It happened.');`
- `Log.info('Someone just arrived from our MySpace Page!');`

`Log` is a globally defined object by the `capture.min.js` file, and can be used in any of your frontend JavaScript.


Updating OpenLog Server
-----------------

Fancy, *smooth*, no-data-loss updating method coming soon :)


Contributing
---------

All PR's and issues are welcomed with open arms.

1. Fork this repository.
2. Create a new branch: `git checkout -b my-new-feature`.
3. Make your changes and add some commits.
4. Push your changes to GitHub and send in a pull request to this repository.


Requirements
------------

- `node.js` > 0.10
- `npm`


Author
-------

Samuel Attard - <me@samuelattard.com> - <https://twitter.com/marshallofsound> - <https://www.samuelattard.com>


Maintainers
-------

Adam McArthur - <adam@adammcarthur.net> - <https://twitter.com/adammcarth> - <https://adammcarthur.net>


License
-------

OpenLog is licensed under the MIT License - see the [LICENSE.txt](LICENSE.txt) file for details.
