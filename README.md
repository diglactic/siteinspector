# SiteInspector

Fork of https://github.com/siteinspector/siteinspector.

## Setup

[![Screenshot](https://www.getsiteinspector.com/packs/media/landing/images/si8-e5152df8eadeeabe91ef6f1d63170f9d.png)](https://www.getsiteinspector.com)

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

# Run migrations, if needed
rake db:migrate
```

## Migrations

_Note: database role requires `CREATE ON DATABASE` permission._

```shell
rails db:migrate
```

## Server

Note: all environment variables must be passed manually. `.env` is not automatically loaded anywhere.

```shell
DATABASE_URL="postgresql://..." LANG="en_US.UTF-8"... bundle exec puma -p 5000 -C ./config/puma.rb
DATABASE_URL="postgresql://..." LANG="en_US.UTF-8"... bundle exec sidekiq -c 10 -C ./config/sidekiq.yml
```

## Ubuntu Setup Notes

### Configuring Ruby

[Install `rvm`](https://github.com/rvm/ubuntu_rvm).

### Generating `supervisord` config with Foreman

```shell
gem install foreman
foreman export supervisord ./
```

### Configuring environment in `supervisord` config

```ini
[supervisord]
environment = PATH="/usr/share/rvm/gems/ruby-3.1.0/bin:/usr/share/rvm/gems/ruby-3.1.0@global/bin:/usr/share/rvm/rubies/ruby-3.1.0/bin:/usr/share/rvm/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin:/home/siteinspector/.rvm/bin",DATABASE_URL="postgresql://...",LANG="en_US.UTF-8"...
```

### Proxying via NGINX

```shell
location / {
    proxy_pass http://127.0.0.1:5000;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header Host $http_host;
    proxy_redirect off;
}
```

### Deploy Script

```shell
bundle install
yarn install
rake assets:precompile
rake db:migrate
supervisorctl restart all
```

### Create PostgreSQL User

```postgresql
CREATE DATABASE siteinspector;

CREATE USER siteinspector;
ALTER USER siteinspector PASSWORD '<new-password>';

GRANT CONNECT ON DATABASE siteinspector TO siteinspector;
GRANT USAGE ON SCHEMA public TO siteinspector;
GRANT ALL PRIVILEGES ON ALL TABLES IN SCHEMA public TO siteinspector;
GRANT CREATE ON SCHEMA public TO siteinspector;

-- Avoid needing to assign superuser perms to user
CREATE EXTENSION citext;
```

## Set Redis Eviction Policy

```shell
sudo apt install redis-tools

redis-cli -h host -p 6379
> set maxmemory-policy noeviction
```

## License

SiteInspector is licensed under the AGPL v3 license.
