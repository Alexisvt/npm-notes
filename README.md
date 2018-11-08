# Notes about `npm`

## How to list globally installed `npm` packages and versions

```cmd
npm list -g --depth=0
```

## How to initialize a node package with `npm`

```cmd
npm init
```

Then we need to answer all the questions. There is another way to do it and that is to use the shorthand version of init where will default all the question.

```cmd
npm init -y
```

That command will basically say **yes** to all the questions

## How to run locally installed binarie tools

Fist we need to install [npx](https://www.npmjs.com/package/npx) globally, then we need to go inside our node project folder and run the next command:

```cmd
npx eslint
```

Here we assuming the we have `eslint` package installed, when we run this command we are executing the `eslint` executable without passing the path of it, just easy as that.

More reference: [Introducing npx: an npm package runner](https://medium.com/@maybekatz/introducing-npx-an-npm-package-runner-55f7d4bd282b)

**Note**: According to the above article this executable comes with with npm version 5.2.0 by default.

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

Reference: [npm bin documentation](https://docs.npmjs.com/cli/bin)

## How to see the all the environment values available

```cmd
npm run env
```

The env script is a special built-in command that can be used to list environment variables that will be available to the script at runtime. If an "env" command is defined in your package it will take precedence over the built-in.

If we are using bash, we can run the next command to filtering the result:

```cmd
npm run env | grep npm
```

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

**Note**: It's important to end with `& wait` to allow to cancel all the process with `Control + C`. Also we can install packages to allow this for example [npm-run-all](https://www.npmjs.com/package/npm-run-all)

### Using `npm-run-all` tool

To run scripts parallel we can use `npm-run-all --parallel` or `run-p` shorthand:

```cmd
npm-run-all --parallel script1 script2
```

Parallel shorthand way:

```cmd
run-p script1 script2
```

The next one is how to run the scripts in series using `npm-run-all` or `run-s` shorthand:

```cmd
npm-run-all script1 script2
```

Serie shorthand way:

```cmd
run-s script1 script2
```

**Note**: If we want more info about the usage of `npm-run-all` please go the [npm-rull-all documentation](https://www.npmjs.com/package/npm-run-all#-usage)

## How to run a set of similar npm scripts with a wildcard

First lets see an abstraction of a `package.json` file

```json
{
  "scripts": {
    "test": "npm-run-all eslint stylelint",
    "eslint": "eslint --cache --fix ./",
    "stylelint": "stylelint \"**/*.scss\" --syntax scss"
  }
}
```

We can change the above with:

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

## How to run scripts before or after another

We prefix the names of the scripts with `pre` or `post`

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
    "script1": "npm run a | npm run b-with-passing-data-from-a | npm run so-on"
  }
}
```

Note: More information in [How to Use npm as a Build Tool](https://www.keithcirkel.co.uk/how-to-use-npm-as-a-build-tool) search for *Streaming to multiple tasks*.

## How to run npm scripts when files change with `onchange`

Not all executables support `watch` option, in that cases we can use [onchange](https://www.npmjs.com/package/onchange)


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

Reference: Sample from [variables in npm scripts](http://ianmcnally.me/blog/2016/2/4/variables-in-npm-scripts)

**Note**: If we want to use the `$` symbol in Windows that will fail we need to use a cross environment way to do it. There is a package [cross-var](https://www.npmjs.com/package/cross-var), so the final result of the last sample will be:

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

Reference: Sample from [variables in npm scripts](http://ianmcnally.me/blog/2016/2/4/variables-in-npm-scripts)


We can list all configuration variables with:

```cmd
npm config list
```

You can override a config value with:

```cmd
npm config set <key> <new-value>
```

If we want to delete an override we just run:

```cmd
npm config delete <key>
```

## How to run npm scripts with git hooks

Note: More information in [Prevent Bad Commits with husky](https://davidwalsh.name/prevent-bad-commits-husky)


## How to change the level of console output when running npm scripts

We can accomplish this using `-s` silent flag to minimize the console output.

```cmd
npm run script-command -s
```

There are other flags that we can use, please see [this reference](https://docs.npmjs.com/misc/config#loglevel) and [this one](https://docs.npmjs.com/misc/config#shorthands-and-other-cli-niceties) to see the list.

## How to make npm scripts cross-environment friendly

If want to use environment variable in a cross environment way we can use [cross-env](https://www.npmjs.com/package/cross-env) package to define environment variables in our scripts.

A sample to how to use it

```json
{
  "scripts": {
    "build": "cross-env \"NODE_ENV=production webpack --config build/webpack.config.js\""
  }
}
```

and if we want to delete using a similar approach we can use [rimraf](https://www.npmjs.com/package/rimraf) instead of `rm -rf`.

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

## How to list the local installed packages

```cmd
npm ls name-of-package
```

The ouput will let us know if the package exists or not. If we want to search for a package globally, the only thing that we need to do is to run the same command but with `-g` at the end.

## How to check the version of a Node Module

```cmd
npm v create-react-app version
```

The above command will check the current version of `create-react-app`, if we want to see all the versions available, we can run:

```cmd
npm v create-react-app version
```

Or if we want to see the different versions but seeing also the tags, run:

```cmd
npm v create-react-app
```

## How to install node packages using shortcuts

To install a package to the `dependencies` property, we use `-S`

```cmd
npm i eslint -S

```

If we want to install a node package but this time to `devDependencies` property use `-D` instead

```cmd
npm i eslint -D
```

If we are in `bash` we can install multiple packages using bash brace expansion:

```cmd
npm i babel{-cli,-preset-env,-plugin-trasform-object-rest-spread} -Ds
```

This time we are using `Ds` to keep the log in silence mode.