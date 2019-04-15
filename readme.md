# composer build-env

Build .env files from .env.json

## What is .env.json?

The .env.json file replaces a project's .env.example file, storing the sample .env values in a json file format.
This json file allows for more advanced configuration management for different project environments like local, testing, staging and production.

## Installation
Using composer:
```
composer global require "charlretief/build-env":"^1.1"
```

## Creating a .env.json file for a project

When you first run `composer build-env` on a project, if there is an .env.example file it will be converted to a .env.json file.


In the .env.json file a key can have either a single value, then this value is used in all builds:

		"APP_NAME": "Hello World",
			
Or a key can have an array of values, specifying different values for each environment:
			
		"APP_URL": {
			"local": "http:\/\/app.local"
			"staging": "http:\/\/staging.example.com"
			"production": "http:\/\/app.example.com"
		},

New and updated .env values should be added to .env.json and committed into your vcs.
After updating .env.json run "composer build-env" to re-build your .env file.

## Usage

		composer build-env [<environment>] [<defaults>] [--set-defaults]


### Basic Usage

To build a basic .env file for local development just run:

		composer build-env		
or to specify a different build environment:

		composer build-env staging

### Advanced Usage #1 - Pinned values

Pinned values allows for local overrides to values that should not be replaced by running build-env.
One possible use for this is secrets management like database credentials that you do not want to commit into vcs.

To create local pinned values simply add them to the bottom of any generated .env file below the placeholder.

	###############################################################
	# Local pinned values                                         #
	###############################################################

	DB_USERNAME="root"
	DB_PASSWORD="password123"

After adding pinned values run `composer build-env` again. It will then comment out the same variables in the generated portion of the file to prevent duplicate keys in the file.
				
### Advanced Usage #2 - Defaults file

A defaults file is a json formatted file, that allows for overriding keys in the .env.json with default values.

This is useful for CI/CD processes that need to always set the same hostnames in all .env files.
It can also function as an alternative to pinned values for secrets management for things like database credentials that you do not want to commit into vcs.

For example to always override your DB_HOST create a file `.env.defaults` with content:

	{
		"DB_HOST": "127.0.0.1"
	}

Then the generated .env file will always have this value for DB_HOST.

		composer build-env local .env.defaults

To remember your defaults file for next time you run without the defaults option add the --set-defaults argument:

		composer build-env local .env.defaults --set-defaults
		
### Advanced Usage #3 - Placeholder variable substitution

In the .env.json file there can be placeholder variables in double curly braces, like `{{myvar}}`, that will be replaced with values from a defaults file if they are present in the defaults file.

Example:

		"CONTACT_EMAIL": "{{EMAIL}}",
		"APP_URL": {
			"local": "http:\/\/{{myvar}}.local",
			"staging": "http:\/\/staging.example.com",
			"production": "http:\/\/app.example.com"
		},
