This guide is also available on the wiki at: ()[].

# OpenLog Server

As you guessed, OpenLog is split up into two components - a script that gets added to your existing app, and a standalone server to store/report logs. This guide will focus on the latter - a Node.js app that runs independently from your application.

## Installation

To install the OpenLog server, run the following command. This will install OpenLog globally in your system (allowing you to run `openlog` commands from any directory):

```bash
> npm install -g openlog
```

Once installed, you can start the server using the default configuration:

```bash
> openlog
```

Now you can access the dashboard at [http://localhost:4783](http://localhost:4783).

## Change Login Username/Password & Server Port Number

1. Enter OpenLog directory:

```bash
> cd "$(npm root -g)/openlog"
```

2. Edit `config.inc.json`:

```javascript
{
  "port": "4783",
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

Restart the OpenLog server for your changes to take effect.

## Quick Guide: Running OpenLog On A Subdomain

This guide shows you how to run OpenLog an a separate subdomain from your app.

**Breakdown:**

1. Point Subdomain To Server IP
2. Install + Configure OpenLog
3. Run OpenLog With [pm2](https://github.com/Unitech/pm2).
4. Setup Reverse Proxy With NGINX

**Foreword:** You are free to run OpenLog on the same server as your existing app, or spin up a new instance specifically for OpenLog. Choose the best option for your project size and budget.

#### 1. Point Subdomain To Server IP

In your domain registrar/web hosting dashboard, create a new A record on your domain to setup a new subdomain. The record should point to the IP address of the server where you're going to run the OpenLog.

| Type | Name | IP Address   | TTL  |
|------|------|--------------|------|
| A    | logs | 123.45.67.89 | 3600 |

The above would create a subdomain for (http://logs.mydomain.com)[http://logs.mydomain.com] pointing at 123.45.67.89.

#### 2. Install + Configure OpenLog

SSH into your server.

```bash
> ssh root@123.45.67.89
```

Install OpenLog:

```bash
> npm install openlog -g
```

Change the login username/password:

```bash
> vim "$(npm root -g)/openlog/config.inc.json"
```

```javascript
{
  "port": "4783",
  "auth": [{
    "user": "myProduct", // <- change
    "password": "Th1sIS@BetterP!ssword" // <- change
  }]
}
```

For this guide, we're going to leave the port number at `4783`.

Finally, rebuild the server:

```bash
> npm install
> npm install -g grunt-cli
> grunt build
```

#### 3. Run OpenLog With pm2

We're going to use the [pm2](https://github.com/Unitech/pm2) process manager to keep the OpenLog server running.

```bash
> npm install pm2 -g
> pm2 start openlog --name "OpenLog Server"
> pm2 startup # Generate/update server boot script
> pm2 status
```

The pm2 status table should show that OpenLog is *'Running'* in a background process, and should now be available on your server at [http://localhost:4783](http://localhost:4783). If a different status is given, run `openlog` to inspect the error message.

#### 4. Setup Reverse Proxy With NGINX

The last step is to make the OpenLog server available on our subdomain. In order to do this, we're going to use a basic reverse proxy. If you're using Apache you might want to look for a separate guide for this &mdash; NGINX and Apache won't work together very easily.

```bash
> sudo apt-get update
> sudo apt-get install nginx
```

```bash
> touch /etc/nginx/sites-available/openlog.conf
```

Add the following to the new file, after filling in the correct `server_name`...

```nginx
# /etc/nginx/sites-available/openlog.conf
#
# Basic reverse proxy configuration for OpenLog server

server {
        listen 80;
        server_name logs.mydomain.com; # name of your subdomain
        location / {
                proxy_pass http://127.0.0.1:4783;
        }
}
```

Now tell NGINX that you're ready to use the new config file, and restart...

```bash
> sudo ln -s /etc/nginx/sites-available/openlog.conf /etc/nginx/sites-enabled
> sudo service nginx restart
```

All done! You can now visit (http://logs.mydomain.com)[http://logs.mydomain.com] and access the OpenLog dashboard with your username and password.

Don't forget to update your frontend configuration to point to your new OpenLog server URL...

**Easy:**

```html
<script type="text/javascript" src="capture.min.js" data-log-url="http://logs.mydomain.com"></script>
```

**w/ JavaScript:**

```javascript
// After capture.min.js included
Log.config.logUrl = 'http://logs.mydomain.com';
```
