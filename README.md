# heroku-buildpack-ruby-minimal

This is a [Heroku Buildpack](https://devcenter.heroku.com/articles/buildpacks) for ruby. It doesn't do nearly as much as the [official ruby buildpack](https://github.com/heroku/heroku-buildpack-ruby) but it might do enough for you.

## Using

For a new app:

```bash
$ heroku create --buildpack dpiddy/ruby
```

For an existing app:

```bash
$ heroku config:set BUILDPACK_URL=https://codon-buildpacks.s3.amazonaws.com/buildpacks/dpiddy/ruby.tgz
```

## What it does

1. determine which ruby to use based on [the `Gemfile`'s ruby directive](http://gembundler.com/v1.3/gemfile_ruby.html), defaulting to ruby-1.9.3
1. fetch ruby
1. set up `.profile.d/ruby.sh` with `GEM_PATH`, `LANG`, `PATH`, and `RACK_ENV` (defaults to `production`, can be overidden)
1. remove the cached bundle if the ruby version has changed
1. `bundle install`, using the cached bundle (inside the [`$CACHE_DIR`](https://devcenter.heroku.com/articles/buildpack-api#caching)) if available
1. `bundle clean` to save space

It supports the same ruby versions as the official ruby buildpack, except for jruby for now. You can see a list [here](https://devcenter.heroku.com/articles/ruby-support#ruby-versions).

## What it does not do

* binstubs: you must `bundle exec ...` to use the bundle
* add default addons: the official ruby buildpack will add a database if you have `pg` in your bundle
* specify default processes types: the official ruby buildpack will set defaults for `web`, `rake`, and `console`
* rails assets stuff: the official ruby buildpack will install node if necessary and try to run `rake assets:precompile`
* `database.yml`: the official ruby buildpack will set up `database.yml` for you, reading from `$DATABASE_URL`
* anything related to syck/psych
* jruby (yet): it doesn't install a JVM if you specify jruby in your `Gemfile`
