# Notes about `npm`

## How to initialize an node package with `npm`

```cmd
npm init
```

Then we need to answer all the questions. There is another way to do it and that is to use the shorthand version of init where will default all the question.

```cmd
npm init -y
```

That command will basically say **yes** to all the questions

## How to see all available script commands in the `package.json` file

```cmd
npm run
```

The other way to see all the script commands is to install [ntl](https://www.npmjs.com/package/ntl) globally then run `ntl` command inside the project, the result will be a nice menu where you can choose which script task run.

## How to know where `npm` will install the executables

```cmd
npm bin
```

If we want to know where `npm` install the executables globally we use instead:

```cmd
npm bin -g
```

More reference: [npm bin documentation](https://docs.npmjs.com/cli/bin)

## How to see the all the environment values availables

```cmd
npm run env
```

The env script is a special built-in command that can be used to list environment variables that will be available to the script at runtime. If an "env" command is defined in your package it will take precedence over the built-in.

## How to run scripts in series in `npm`

We use `&&` to concat the scripts like this:

```cmd
npm run eslint && npm run somethingelse
```

If `eslint` fail the process stop right there and it will not continue to `somethingelse`

## How to run scripts in parallel

We use a single `&`:

```cmd
npm run script1 & npm run script2 & wait
```

**Note**: It's important to end with `& wait` to allow to cancel all the process with `control + c`. Also we can install packages to allow this for example [npm-run-all](https://www.npmjs.com/package/npm-run-all)

```cmd
npm-run-all --parallel script1 script2
```

The above is how the final script will looks like and the next one is how to run the scripts in series using `npm-run-all` tool

```cmd
npm-run-all script1 script2
```

## How to run a set of similar npm scripts with a wildcard

First lets see an abstraction of a `package.json` file

```json
{
  "scripts": {
    "test": "npm-run-all eslint stylelint",
    "test": "npm-run-all eslint stylelint",
    "eslint": "eslint --cache --fix ./",
    "stylelint": "stylelint \"**/*.scss\" --syntax scss"
  }
}
```

We can right above

```json
{
  "scripts": {
    "test": "npm-run-all lint:*",
    "lint:js": "eslint --cache --fix ./",
    "lint:css": "stylelint \"**/*.scss\" --syntax scss",
    "lint:css:fix": "stylefmt -R src/"
  }
}
```

The `*` indicates `npm` will run any `script` that matches the pattern. In this case will run `lint:js` and `lint:css` but not the `lint:css:fix`. If we want to match all we use instead:

```json
{
  "scripts": {
    "test": "npm-run-all lint:**",
    "lint:js": "eslint --cache --fix ./",
    "lint:css": "stylelint \"**/*.scss\" --syntax scss",
    "lint:css:fix": "stylefmt -R src/"
  }
}
```

## How to run pre and post scripts

We just create scripts and we need to prefix the names of the scripts with `pre` or `post`

```json
{
  "scripts": {
    "pretest": "npm run run-this-before-test",
    "test": "npm-run-all eslint stylelint",
    "eslint": "eslint --cache --fix ./",
    "posteslint": "npm run run-this-after-eslint"
  }
}
```

## How to pass arguments to npm scripts

```json
{
  "scripts": {
    "script1": "npm run script2 -- --watch",
    "script2": "npm run someCommand"
  }
}
```

`npm` will pass all the arguments after the `--` directly to `script2`

**Note**: more information in [npm-run-script docs](https://docs.npmjs.com/cli/run-script)

## How to pipe data from one npm script to another

We use the `|` character

```json
{
  "scripts": {
    "script1": "npm run a | npm run b-with-the-passing-data-of-a | npm run so-on"
  }
}
```

Note: More information in [How to Use npm as a Build Tool](https://www.keithcirkel.co.uk/how-to-use-npm-as-a-build-tool/) search for *Streaming to multiple tasks*.

## How to run npm scripts when files change with `onchange`

Not all executables support `watch` option in that cases we can use [onchange](https://www.npmjs.com/package/onchange)


```json
{
  "scripts": {
    "watch:lint": "onchange \"**/*.js\" -- npm run lint",
    "lint": "eslint --cache --fix ./"
  }
}
```

In this case we are watching all the `js` files and when something change in any of those files it will run the `lint` script

## How to use package.json variables in npm scripts

We reference a variable just using the `$` symbol.

```json
{
  "main": "index.js",
  "scripts": {
    "start": "node $npm_package_main"
  }
}
```

In this sample when we run the `start` script it will run the code in `index.js` file. Also we can use any environment variable.

Reference: The sample was taken from [variables in npm scripts](http://ianmcnally.me/blog/2016/2/4/variables-in-npm-scripts)

**Note**: If we want to use the `$` symbol in windows that will fail we need to use a cross environment way to do it. There is a package [cross-var](https://www.npmjs.com/package/cross-var), so the final result of the last sample will be:

```json
{
  "main": "index.js",
  "scripts": {
    "start": "cross-var \"node $npm_package_main\""
  }
}
```

## How to use custom config settings in your npm scripts

```json
{
  "main": "index.js",
  "config": {
    "domain": "localhost:8000"
  },
  "scripts": {
    "start": "cross-var \"node $npm_package_main --domain $npm_package_config_domain\""
  }
}
```

Note: The sample was taken from [variables in npm scripts](http://ianmcnally.me/blog/2016/2/4/variables-in-npm-scripts)

you can override a config value:

```cmd
npm config set the-name-of-the-package:variable-to-override new-value
```

we can list all the overrides configuration variables with:

```cmd
npm config list
```

If we want to delete an override we just run:

```cmd
npm config delete the-name-of-the-package:override-variable-name-to-delete
```

## How to run npm scripts with git hooks

Note: More information in [Prevent Bad Commits with husky](https://davidwalsh.name/prevent-bad-commits-husky)


## How to change the level of console output when running npm scripts

We can accomplish that using `-s` flag. This flag is use when you want to run a script silently.

```cmd
npm run script-command -s
```

There other flags that we can use, please see [this reference](https://docs.npmjs.com/misc/config#loglevel) and [this one](https://docs.npmjs.com/misc/config#shorthands-and-other-cli-niceties) to see the list.

## How to make npm scripts cross-environment friendly

if want to use environment variable in a cross environment way we can use [cross-env](https://www.npmjs.com/package/cross-env) package to define environment variables in our scripts.

A sample to how to use it

```json
{
  "scripts": {
    "build": "cross-env NODE_ENV=production webpack --config build/webpack.config.js"
  }
}
```

and If we want to delete using a similar approach we can use [rimraf](https://www.npmjs.com/package/rimraf) instead of `rm -rf`.

```json
{
  "scripts": {
    "clean": "rimraf folder-to-clean"
  }
}
```

If we use the `open` command we can use [opn-cli](https://www.npmjs.com/package/opn-cli) package that provide a similar approach in a cross environment way

```json
{
  "scripts": {
    "opensite": "opn some-folder/index.html"
  }
}
```
