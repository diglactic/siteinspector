# SiteInspector

Fork of https://github.com/siteinspector/siteinspector.

## Setup

```shell
# Install
bundle install
yarn install

# Issues with Postgres Gem on M1
# @see https://stackoverflow.com/a/70316977/2535504

# Lint and fix *.rb files
bundle exec rubocop -A

# Ensure assets can compile
rake assets:precompile  

# Run migrations on Heroku, if needed
rake db:migrate
```

## Config

```dotenv
DATABASE_URL=postgresql://user:password@url/database
LANG=en_US.UTF-8
RACK_ENV=production
RAILS_ENV=production
RAILS_LOG_TO_STDOUT=enabled
RAILS_SERVE_STATIC_FILE=enabled
REDIS_TLS_URL=rediss://:username@url:port
REDIS_URL=redis://:username@url:port
SECRET_KEY_BASE=XXX
```

## Migrations

_Note: database role requires `CREATE ON DATABASE` permission._

```shell
rails db:migrate
```

## License

SiteInspector is licensed under the AGPL v3 license.
