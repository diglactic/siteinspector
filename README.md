# SiteInspector

Fork of https://github.com/siteinspector/siteinspector for use on a standard Ubuntu server with some [minor
customizations](https://github.com/siteinspector/siteinspector/compare/master...diglactic:siteinspector:main).

> ⚠️ 2023-04-13: Cloudflare CDN can cause a blank screen to occur.

## Setup

```shell
# Install
bundle install
yarn install

# 2022-03-13: Issues with Postgres Gem on M1
# @see https://stackoverflow.com/a/70316977/2535504

# Lint and fix *.rb files
bundle exec rubocop -A

# Ensure assets can compile
rake assets:precompile  

# Run migrations, if needed
rake db:migrate
```

## Environment Variables

```dotenv
DATABASE_URL=postgresql://user:password@url/database
LANG=en_US.UTF-8
RACK_ENV=production
RAILS_ENV=production
RAILS_LOG_TO_STDOUT=enabled
RAILS_SERVE_STATIC_FILE=enabled
SECRET_KEY_BASE=XXX

# Default Redis database is `0`
REDIS_URL=redis://user:password@url:port/database

# May need to specify the full path for commands
PATH=/usr/share/rvm/gems/ruby-3.1.0/bin:/usr/share/rvm/gems/ruby-3.1.0@global/bin:/usr/share/rvm/rubies/ruby-3.1.0/bin:/usr/share/rvm/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin:/home/siteinspector/.rvm/bin
```

## Migrations

_Note: database role requires `CREATE ON DATABASE` permission._

```shell
rails db:migrate
```

## Local Server

```shell
DATABASE_URL="postgresql://..." LANG="en_US.UTF-8"... bundle exec puma -p 5000 -C ./config/puma.rb
DATABASE_URL="postgresql://..." LANG="en_US.UTF-8"... bundle exec sidekiq -c 10 -C ./config/sidekiq.yml
```

## Ubuntu Setup Notes

### Requirements

- Ruby (use [`rvm`](https://github.com/rvm/ubuntu_rvm))
- NGINX
- Redis
- PostgreSQL
- Supervisor

### Generating initial `supervisord` config with Foreman

```shell
gem install foreman
foreman export supervisord ./
```

### Configuring environment in `supervisord` config

```ini
[supervisord]
environment=DATABASE_URL="postgresql://...",LANG="en_US.UTF-8"...
directory=
command=
...
```

### Proxying via NGINX

```shell
location / {
    proxy_pass http://127.0.0.1:5000;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header Host $http_host;
    proxy_redirect off;
}

# Remove the following lines, if listed to avoid a 404 on both
# location = /favicon.ico { access_log off; log_not_found off; }
# location = /robots.txt  { access_log off; log_not_found off; }
```

### Deploy Script

```shell
bundle install
yarn install
rake assets:precompile

# Will require `DATABASE_URL` and possibly `PATH` environment variables
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

### Set Redis Eviction Policy

_See [https://github.com/sidekiq/sidekiq/wiki/Using-Redis#memory](https://github.com/sidekiq/sidekiq/wiki/Using-Redis#memory)._

```shell
sudo apt install redis-tools

redis-cli -h <host> -p 6379
> set maxmemory-policy noeviction
```

## License

SiteInspector is licensed under the AGPL v3 license.
