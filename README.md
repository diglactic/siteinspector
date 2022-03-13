# SiteInspector

Fork of https://github.com/siteinspector/siteinspector.

```shell
# Setup
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

## License

SiteInspector is licensed under the AGPL v3 license.
