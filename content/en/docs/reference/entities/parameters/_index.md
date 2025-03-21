---
title: Parameters
description: Supply data to actions adding parameters
weight: 30
---

## Introduction to parameters

When working with serverless actions, data is supplied by adding
parameters to the actions; these are in the parameter declared as an
argument to the main serverless function. All data arrives this way and
the values can be set in a few different ways. The first option is to
supply parameters when an action or package is created (or updated).
This approach is useful for data that stays the same on every execution,
equivalent to environment variables on other platforms, or for default
values that might be overridden at invocation time. The second option is
to supply parameters when the action is invoked - and this approach will
override any parameters already set.

This page outlines how to configure parameters when deploying packages
and actions, and how to supply parameters when invoking an action. There
is also information on how to use a file to store the parameters and
pass the filename, rather than supplying each parameter individually on
the command-line.

## Passing parameters to an action at invoke time

Parameters can be passed to the action when it is invoked. These
examples use JavaScript but all [the other languages](/docs/reference/runtimes/) work the same way.

1. Use parameters in the action. For example, create ‘hello.js’ file
    with the following content:

<!-- -->

    function main(params) {
        return {payload:  'Hello, ' + params.name + ' from ' + params.place};
    }

The input parameters are passed as a JSON object parameter to the `main`
function. Notice how the `name` and `place` parameters are retrieved
from the `params` object in this example.

1. Update the action so it is ready to use:

<!-- -->

    ops action update hello hello.js

1. Parameters can be provided explicitly on the command-line, or by
    supplying a file containing the desired parameters

To pass parameters directly through the command-line, supply a key/value
pair to the `--param` flag:
`ops action invoke --result hello --param name Dorothy --param place Kansas`

This produces the result:

    {
        "payload": "Hello, Dorothy from Kansas"
    }

Notice the use of the `--result` option: it implies a blocking
invocation where the CLI waits for the activation to complete and then
displays only the result. For convenience, this option may be used
without `--blocking` which is automatically inferred.

Additionally, if parameter values specified on the command-line are
valid JSON, then they will be parsed and sent to your action as a
structured object. For example, if we update our hello action to:

    function main(params) {
        return {payload:  'Hello, ' + params.person.name + ' from ' + params.person.place};
    }

Now the action expects a single `person` parameter to have fields `name`
and `place`. If we invoke the action with a single `person` parameter
that is valid JSON:

    ops action invoke --result hello -p person '{"name": "Dorothy", "place": "Kansas"}'

The result is the same because the CLI automatically parses the `person`
parameter value into the structured object that the action now expects:
`json { "payload": "Hello, Dorothy from Kansas" }`

## Setting default parameters on an action

Actions can be invoked with multiple named parameters. Recall that the
`hello` action from the previous example expects two parameters: the
*name* of a person, and the *place* where they’re from.

Rather than pass all the parameters to an action every time, you can
bind certain parameters. The following example binds the *place*
parameter so that the action defaults to the place “Kansas”:

1. Update the action by using the `--param` option to bind parameter
    values, or by passing a file that contains the parameters to
    `--param-file` (for examples of using files, see the section on
    [working with parameter files](#working-with-parameter-files).

To specify default parameters explicitly on the command-line, provide a
key/value pair to the `param` flag:

    ops action update hello --param place Kansas

1. Invoke the action, passing only the `name` parameter this time.

<!-- -->

    ops action invoke --result hello --param name Dorothy

    {
        "payload": "Hello, Dorothy from Kansas"
    }

Notice that you did not need to specify the `place` parameter when you
invoked the action. Bound parameters can still be overwritten by
specifying the parameter value at invocation time.

1. Invoke the action, passing both `name` and `place` values, and
    observe the output:

<!-- -->

    ops action invoke --result hello --param name Dorothy --param place "Washington, DC"

    {
        "payload": "Hello, Dorothy from Washington, DC"
    }

Despite a parameter set on the action when it was created/updated, this
is overridden by a parameter that was supplied when invoking the action.

## Setting default parameters on a package

Parameters can be set at the package level, and these will serve as
default parameters for actions unless:

- The action itself has a default parameter.

- The action has a parameter supplied at invoke time, which will
    always be the “winner” where more than one parameter is available.

The following example sets a default parameter of `name` on the `MyApp`
package and shows an action making use of it.

1. Create a package with a parameter set:

<!-- -->

    ops package update MyApp --param name World

1. Create an action in this package:

<!-- -->

       function main(params) {
           return {payload: "Hello, " + params.name};
       }

    ops action update MyApp/hello hello.js

1. Invoke the action, and observe the default package parameter in use:

<!-- -->

    ops action invoke --result MyApp/hello

       {
           "payload": "Hello, World"
       }

## Working with parameter files

It’s also possible to put parameters into a file in JSON format, and
then pass the parameters in by supplying the filename with the
`param-file` flag. This works for both packages and actions when
creating/updating them, and when invoking actions.

1. As an example, consider the very simple `hello` example from
    earlier. Using `hello.js` with this content:

<!-- -->

    function main(params) {
        return {payload:  'Hello, ' + params.name + ' from ' + params.place};
    }

1. Update the action with the updated contents of `hello.js`:

<!-- -->

    ops action update hello hello.js

1. Create a parameter file called `parameters.json` containing
    JSON-formatted parameters:

<!-- -->

    {
        "name": "Dorothy",
        "place": "Kansas"
    }

1. Use the `parameters.json` filename when invoking the action, and
    observe the output

<!-- -->

    ops action invoke --result hello --param-file parameters.json

    {
        "payload": "Hello, Dorothy from Kansas"
    }
