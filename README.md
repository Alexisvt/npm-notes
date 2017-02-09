# Notes aboit `npm`

## How to initialize an node package with `npm`

```cmd
npm init
```

Then we need to answer all the questions. There is another way to do it and that is to use the shorthand version of init where will default all the question.

```cmd
npm init -y
```

That command will basically say **yes** to all the questions

## How to see all the available script commands in the `package.json` file

```cmd
npm run
```

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

We use a single `$`:

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
    "stylelint": "stylelint '**/*.scss' --syntax scss"
  }
}
```

We can right above

```json
{
  "scripts": {
    "test": "npm-run-all lint:*",
    "lint:js": "eslint --cache --fix ./",
    "lint:css": "stylelint '**/*.scss' --syntax scss",
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
    "lint:css": "stylelint '**/*.scss' --syntax scss",
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

