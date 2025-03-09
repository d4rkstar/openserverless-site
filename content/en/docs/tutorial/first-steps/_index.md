---
title: First steps
description: Move your first steps on Apache Openserverless
draft: false
weight: 20
---

## First steps

### Starting at the Front

Right now, after a freshly installation, if we visit the `<apihost>` you
will see a very simple page with:

> Welcome to OpenServerless static content distributor landing page!!!

That’s because we’ve activated the static content, and by default it
starts with this simple `index.html` page. We will instead have our own
index page that shows the users a contact form powered by OpenServerless
actions. Let’s write it now.

Let’s create a folder that will contain all of our app code:
`contact_us_app`.

Inside that create a new folder called `web` which will store our static
frontend, and add there a `index.html`.

The directory structure should look like:

```
contact_us_app
└── web
    └── index.html
```

Paste the following markup inside the `index.html` file:
```html
<!DOCTYPE html>
<html>
<head>
    <link href="//maxcdn.bootstrapcdn.com/bootstrap/3.3.0/css/bootstrap.min.css" rel="stylesheet" id="bootstrap-css">
</head>
<body>
<div id="container">
    <div class="row">
        <div class="col-md-8 col-md-offset-2">
            <h4>Get in Touch</h4>
            <form method="POST">
                <div class="form-group">
                    <input type="text" name="name" class="form-control" placeholder="Name">
                </div>
                <div class="form-group">
                    <input type="email" name="email" class="form-control" placeholder="E-mail">
                </div>
                <div class="form-group">
                    <input type="tel" name="phone" class="form-control" placeholder="Phone">
                </div>
                <div class="form-group">
                    <textarea name="message" rows="3" class="form-control" placeholder="Message"></textarea>
                </div>
                <button class="btn btn-default" type="submit" name="button">
                    Send
                </button>
            </form>
        </div>
    </div>
</div>
</body>
</html>
```

{{< blockquote warning>}}
Before move on, be sure to have completed once the login
as [indicated here](/docs/tutorial/getting-started/#login-as-user)
{{< /blockquote >}}

Now we just have to upload it to our OpenServerless deployment. You
could upload it using something like `curl` with a `PUT` to where your
platform is deployed at, but there is an handy command that does it
automatically for all files in a folder:

```bash
ops util upload web/
```

The output will be:

```
==================| UPLOAD RESULTS |==================
| FILES      : 1
| COMPLETED  : 1
| ERRORS     : 0
| SKIPPED    : 0
| EXEC. TIME : 34.59 ms
======================================================
```

Pass to `ops util upload` the path to folder where the index.html is
stored in (the `web` folder) and visit again `<apihost>`.

Now you should see the new index page:

![Form](/docs/tutorial/images/form.png)

### The Contact Package

The contact form we just uploaded does not do anything. To make it work
let’s start by creating a new package to hold our actions. Moreover, we
can bind to this package the database url, so the actions can directly
access it!

With the `debug` command you can see what’s going on in your deployment.
This time let’s use it to grab the "postgres\_url" value:

```bash
ops -config -d | grep POSTGRES_URL | cut -d= -f2-
```

If you're on Windows you can use this powershell version:

```powershell
(ops -config -d | Select-String "POSTGRES_URL").ToString().Split('=')[1].Trim()
```

Copy the Postgres URL (something like `postgresql://...`). Now we can
create a new package for the application:

```bash
ops package create contact -p POSTGRES_URL <postgres_url>
ok: created package contact
```

The actions under this package will be able to access the "dbUri"
variable from their args!

To follow the same structure for our action files, let’s create a folder
`packages` and inside another folder `contact` to give our actions a
nice, easy to find, home.

To manage and check out your packages, you can use the `ops packages`
subcommands.

```bash
ops package list
```

the output will be:

```
packages
/opstutorial/contact 
```

And to get specific information on a package:

```bash
ops package get contact
```

output will be:

```
ok: got package contact
{
    "namespace": "opstutorial",
    "name": "contact",
    "version": "0.0.1",
    "publish": false,
    "parameters": [
        {
            "key": "POSTGRES_URL",
            "value": "postgresql://opstutorial:<password>@nuvolaris-postgres.nuvolaris.svc.cluster.local:5432/opstutorial"
        }
    ],
    "binding": {},
    "updated": 1740922160637
}
```

### Development Tools

However, OpenServerless has a set of development tools, inside the `ops ide` command, details of which are available in
[this section](/docs/reference/tasks/ide/) of the guide.

for the rest of this tutorial, we will be using `ops ide` for publishing, as they make the process quicker and easier

To start using ops ide, we need to log in, using the command `ops ide login`:

```bash
OPS_PASSWORD="SimplePassword" ops ide login opstutorial http://localhost:80
```

The output will be:

```
*** Configuring Access to OpenServerless ***
apihost=http://localhost:80 username=opstutorial
Logging in http://localhost:80 as opstutorial
Successfully logged in as opstutorial.
ok: whisk auth set. Run 'wsk property get --auth' to see the new value.
ok: whisk API host set to http://localhost:80
OpenServerless host and auth set successfully. You are now ready to use ops!
```

This login will enable the development tools.

---
