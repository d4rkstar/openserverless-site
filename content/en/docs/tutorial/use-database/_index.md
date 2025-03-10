---
title: Use database
description: Store data into a relational database
weight: 40
draft: false
---

## Use database

### Storing the Message in the Database

We are ready to use the database that we enabled at the beginning of the
tutorial.

Since we are using a relational database, we need to create a table to
store the contact data. We can do that by creating a new action called
`create-table.js` in the `packages/contact` folder.

The directory structure have to be like this:

```shell
contact_us_app
├── packages
│   └── contact
│       ├── create-table.js
│       └── submit.js
└── web
    └── index.html
```

Put this content inside the `create-table.js` file:

```javascript
// create-table.js

//--kind nodejs:default

const { Client } = require('pg')

async function main(args) {
    console.log('Starting create-table action')
    const client = new Client({ connectionString: args.POSTGRES_URL });

    const createSchema = `CREATE SCHEMA IF NOT EXISTS demo;`

    const createTable = `
    CREATE TABLE IF NOT EXISTS demo.contacts (
        id serial PRIMARY KEY,
        name varchar(50),
        email varchar(50),
        phone varchar(50),
        message varchar(300)
    );
    `

    try {
        console.log(`Connecting to ${args.POSTGRES_URL}`);
        await client.connect();
        console.log('Connected to database');
        await client.query(createSchema);
        console.log('Schema demo created');
        await client.query(createTable);
        console.log('Contact table created');
        return { result: 'OK' };
    } catch (e) {
        if (e instanceof AggregateError) {
            for (const err of e.errors) {
                console.error('[ERROR] - ', err.message || err);
            }
        } else if (e instanceof Error) {
            console.error('[ERROR]  - ', e.message);
        } else {
            console.error('[ERROR] - ', e);
        }
        return { result: 'ERROR' };
    } finally {
        console.log('Closing connection');
        if (client) {
            await client.end();
        }
    }
}
```

We just need to run this once, therefore it doesn’t need to be a web action. Here 
we can take advantage of the `cron`  service we enabled! There are also a couple of 
console logs that we can check out.

With the cron scheduler you can annotate an action with 2 kinds of labels. One to 
make OpenServerless periodically invoke the action, the other to automatically 
execute an action once, on creation. More information about
[Cron Scheduler here](/docs/reference/references/scheduler/)

From the terminal, always opened inside the project's folder, run the
command:
```bash
ops -config -d | grep POSTGRES_URL | cut -d= -f2-
```

If you're on Windows you can use this powershell version:

```powershell
(ops -config -d | Select-String "POSTGRES_URL").ToString().Split('=')[1].Trim()
```

This should output something like:

```shell
postgresql://opstutorial:<password>@nuvolaris-postgres.nuvolaris.svc.cluster.local:5432/opstutorial
```

Take the value after the `=` sign and use it to export the POSTGRES_URL variable in the shell: 

```bash
ops action create contact/create-table packages/contact/create-table.js -a autoexec true --param POSTGRES_URL <PUT THE POSTGRES_URL HERE>
```

In OpenServerless an action invocation is called an `activation`. You
can keep track, retrieve information and check logs from an action with
`ops activation`. For example, with:

```bash
ops activation list
```

You can retrieve the list of invocations. For caching reasons the first
time you run the command the list might be empty. Just run it again and
you will see the latest invocations (probably some `hello` actions from
the deployment).

If we want to make sure `create-table` was invoked, we can do it with
this command. The cron scheduler can take up to 1 minute to run an
`autoexec` action, so let’s wait a bit and run `ops activation list`
again.

```bash
ops activation list
```
```shell
Datetime            Activation ID                    Kind      Start Duration   Status            Entity
2025-03-02 20:15:42 fe999766356749679997663567a967d2 nodejs:21 cold  86ms       success           opstutorial/create-table:0.0.1
..
```

Or we could run `ops activation poll` or `ops ide poll` to listen for new logs.

```bash
ops activation poll
```

```shell
Enter Ctrl-c to exit.
Polling for activation logs
```

When the logs from the `create-table` action appear, we can stop the
command with `Ctrl-c`.

Each activation has an `Activation ID` which can be used with other
`ops activation` subcommands or with the `ops logs` command.

We can also check out the logs with either `ops logs <activation-id>` or
`ops logs --last` to quickly grab the last activation’s logs:

```bash
ops logs --last
```

You should see logs like this:
```shell
2025-03-02T19:15:42.421229172Z stdout: Starting create-table action
2025-03-02T19:15:42.422303047Z stdout: Connecting to postgresql://opstutorial:cSJqoue7oTyO@nuvolaris-postgres.nuvolaris.svc.cluster.local:5432/opstutorial
2025-03-02T19:15:42.436288964Z stdout: Connected to database
2025-03-02T19:15:42.437224297Z stdout: Schema demo created
2025-03-02T19:15:42.437921297Z stdout: Contact table created
2025-03-02T19:15:42.437934839Z stdout: Closing connection
```

### The Action to Store the Data

We could just write the code to insert data into the table in the
`submit.js` action, but it’s better to have a separate action for that.

Let’s create a new file called `write.js` in the `packages/contact`
folder:

```javascript
// write.js

//--kind nodejs:default
//--param POSTGRES_URL $POSTGRES_URL
const {Client} = require('pg')

async function main(args) {
    const client = new Client({connectionString: args.dbUri});

    // Connect to database server
    await client.connect();

    const {name, email, phone, message} = args;

    try {
        let res = await client.query(
            'INSERT INTO demo.contacts(name,email,phone,message) VALUES($1,$2,$3,$4)',
            [name, email, phone, message]
        );
        console.log(res);
    } catch (e) {
        console.log(e);
        throw e;
    } finally {
        client.end();
    }

    return {
        body: args.body,
        name,
        email,
        phone,
        message
    };
}
```

{{< blockquote info>}}
You may have noticed here again the comments on top of the file. As said before,
these comments are used by `ops ide` to automatically handle the publishing of files
by calling `ops package` or `ops action` as needed.
In particular:
<ul>
<li><code>--kind nodejs:default</code> will ask OpenServerless to run this code on the nodejs default runtime.</li>
<li>the <code>--param POSTGRES_URL $POSTGRES_URL</code> will automatically fill in the parameters required by the action,
taking it's value from <code>ops</code>'s configuration file.</li>
</ul>
{{< /blockquote >}}


Very similar to the create table action, but this time we are inserting
data into the table by passing the values as parameters. There is also a
`console.log` on the response in case we want to check some logs again.

Let’s deploy it:

```bash
ops ide deploy
```
```
/homr/openserverless/.ops/tmp/deploy.pid
PID 7931
> Scan:
>> Action: packages/contact/write.js
>> Action: packages/contact/create-table.js
>> Action: packages/contact/submit.js
> Deploying:
>> Package: contact
$ $OPS package update contact 
ok: updated package contact
>>> Action: packages/contact/write.js
$ $OPS action update contact/write packages/contact/write.js --web true --kind nodejs:default --param POSTGRES_URL $POSTGRES_URL
ok: updated action contact/write
>>> Action: packages/contact/create-table.js
$ $OPS action update contact/create-table packages/contact/create-table.js --kind nodejs:default --param POSTGRES_URL $POSTGRES_URL
ok: updated action contact/create-table
>>> Action: packages/contact/submit.js
$ $OPS action update contact/submit packages/contact/submit.js --web true --kind nodejs:default
ok: updated action contact/submit
build process exited with code 0
UPLOAD ASSETS FROM web
==================| UPLOAD RESULTS |==================
| FILES      : 1
| COMPLETED  : 1
| ERRORS     : 0
| SKIPPED    : 0
| EXEC. TIME : 43.04 ms
======================================================
URL: http://opstutorial.localhost:80
```

### Finalizing the Submit

Alright, we are almost done. We just need to create a pipeline of
`submit` → `write` actions. The `submit` action returns the 4 form
fields together with the HTML body. The `write` action expects those 4
fields to store them. Let’s put them together into a `sequence`:

```bash
ops action create contact/submit-write  --sequence contact/submit,contact/write --web true
```
```shell
ok: created action contact/submit-write
```

With this command we created a new action called `submit-write` that is
a sequence of `submit` and `write`. This means that OpenServerless will
call in a sequence `submit` first, then get its output and use it as
input to call `write`.

Now the pipeline is complete, and we can test it by submitting the form
again. This time the data will be stored in the database.

Note that `write` passes on the HTML body so we can still see the thank
you message. If we want to hide it, we can just remove the `body`
property from the return value of `write`. We are still returning the
other 4 fields, so another action can use them (spoiler: it will happen
next chapter).

Let’s check out again the action list:

```bash
ops action list
```

```shell
actions
/opstutorial/contact/submit-write                                      private sequence
/opstutorial/contact/submit                                            private nodejs:21
/opstutorial/contact/create-table                                      private nodejs:21
/opstutorial/contact/write                                             private nodejs:21
```

You probably have something similar. Note the submit-write is managed as
an action, but it’s actually a sequence of 2 actions. This is a very
powerful feature of OpenServerless, as it allows you to create complex
pipelines of actions that can be managed as a single unit.

### Trying the Sequence

As before, we have to update our `index.html` to use the new action.
First let’s get the URL of the `submit-write` action:

```bash
ops url contact/submit-write
```
```shell
<apihost>/api/v1/web/openserverless/contact/submit-write
```

Then we can update the `index.html` file. Change the form submit 
action with the url from the previous command:

```html
<form method="POST" action="/api/v1/web/opstutorial/contact/submit-write"
          enctype="application/x-www-form-urlencoded">
```

We just need to add `-write` to the action name.

Now give a `ops ide deploy` to publish all the modifications.

Try again to fill the contact form (with correct data) and submit it.
This time the data will be stored in the database.

If you want to retrieve info from you database, ops provides several
utilities under the `ops devel` command. They are useful to interact
with the integrated services, such as the database we are using.

For instance, let’s run:

```bash
ops devel psql sql "SELECT * FROM demo.CONTACTS"
```

You should see an output like this:
```shell
[{'id': 1, 'name': 'OpenServerless', 'email': 'info@nuvolaris.io', 'phone': '5551233210', 'message': 'This is awesome!'}]
```

---
