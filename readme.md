# composer build-env

Build .env files from .env.example with advanced configuration management for different project environments like local, testing, staging, production.
Also supports dynamic variables and environment defaults in ENV values.

## Installation
Using composer:

    composer global require "charlretief/build-env":"^2"


## Usage

    composer build-env [<environment>] [<defaults>] [--set-defaults] [--setup]

### Multi-environment Build Setup

To setup a project for multi-environment builds run:

    composer build-env --setup

After running the setup new sections will be added to the .env.example like:

    ################################################################
    # APP_ENV=production
    ################################################################
    #APP_DEBUG=false
    #APP_ENV=production


Variables put under these sections will be used when the target build environment matches the name in APP_ENV comment section.
Values in this section should be commented, they will get uncommented when the target build environment matches. 

### Basic Usage

To build a basic .env file for local development just run:

    composer build-env		
or to specify a different build environment:

    composer build-env staging

### Advanced Usage #1 - Pinned values

Pinned values allows for local overrides to values that should not be replaced by running build-env.
One possible use for this is secrets management like database credentials that you do not want to commit into vcs.

To create local pinned values simply add them to the bottom of any generated .env file below the placeholder.

    ################################################################
    # Local pinned values                                          #
    ################################################################
	DB_USERNAME="root"
	DB_PASSWORD="password123"

After adding pinned values run `composer build-env` again. It will then comment out the same variables in the generated portion of the file to prevent duplicate keys in the file.
				
### Advanced Usage #2 - Defaults file

A defaults file is another "env" file, that allows for overwriting keys in the .env.example with default values.

This is useful for CI/CD processes that need to always set the same hostnames in all .env files.
It can also function as an alternative to pinned values for secrets management for things like database credentials that you do not want to commit into vcs.

For example to always overwriting your DB_HOST create a file `.env.defaults` (can be any filename) with content:

	DB_HOST="127.0.0.1"

Then the generated .env file will always have this value for DB_HOST.

    composer build-env local .env.defaults

To remember your defaults file for next time you run without the defaults option add the --set-defaults argument:

    composer build-env local .env.defaults --set-defaults
		
### Advanced Usage #3 - Placeholder variable substitution

In the .env.example file there can be placeholder variables in double curly braces, like `{{myvar}}`, that will be replaced with values from a defaults file if they are present in the defaults file.

Example:

    CONTACT_EMAIL={{EMAIL}}
    APP_URL="http://{{myvar}}.local.dev"


## Version 2

[build-env version 1](readme_v1.md) is now deprecated, and with it the experimental .env.json file format.
However version 2 will remain fully backwards compatible with reading the .env.json file format.
