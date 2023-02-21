# Problem

Using the `build-in-app-dir` along with bootsnap should mean that cache items for boot are generated at deploy time and make running commands on a new dyno much faster https://github.com/heroku/heroku-buildpack-ruby/issues/979#issuecomment-792720228.

It's been observed in (internal link: ticket 1210780) that this works fine for common run time dynos, but for some reason does not hold with private spaces.

This application serves as a (somewhat) minimal reproduction of that behavior.

## Reproduction: Deploy to non-private-space (fast)

```
heroku create
heroku labs:enable build-in-app-dir
git push heroku main
```

Then:

```
heroku run bash
~ $ time rake -T | tail -n1
rake zeitwerk:check                       # Checks project structure for Zeitwerk compatibility

real	0m1.459s
user	0m1.160s
sys	0m0.292s
```

## Reproduction: Deploy to private-space (slow)

```
heroku spaces:create ticket-NNNNNNN
heroku spaces:create ticket-NNNNNNN --team=<TEAM NAME>
heroku spaces:wait
heroku apps:create --space=ticket-NNNNNNN
heroku labs:enable build-in-app-dir -a <name of app>
git push https://git.heroku.com/<name of app>.git main
```

Then

```
heroku run bash
~ $ time rake -T | tail -n1
rake zeitwerk:check                       # Checks project structure for Zeitwerk compatibility

real	0m4.742s
user	0m1.052s
sys	0m0.208s
```

## Reflect

Note that that the deploy to common runtime gives `time rake -T` of about 1.4 second and in a private space ~4 .7 seconds which is significantly slower.

## FYI

Here's some notes about the systems that might good to know.

- All builds for apps occur on the same infrastructure. So both common runtime and private space apps are built on the same machines. However at runtime, common runtime apps would run in our common fleet while private apps run in an isolated instance.

## Debugging

### Theory: There's something different in the env vars or CPU

**Hypothesis**: If a mistake was made in setup or if boosnap uses an environment variable for generating a cache key, then it might be generated differently betweeen deploy and runtime in private spaces (as opposed to common runtime).

**Testing**: I added debugging information to `rake assets:precompile` this will output at build and then we can use `heroku run bash` to output it at runtime.

**Results**: Here's the output run through a diffchecker https://www.diffchecker.com/IgG1E3bn/

Conclusion: There are some differences, but nothing obvious.

### Theory: Something is causing cache keys to miss

**Hypothesis**: If a mistake was made in setup or if boosnap uses an environment variable for generating a cache key, then it might be generated differently betweeen deploy and runtime in private spaces (as opposed to common runtime).

**Testing**: Set `heroku config:set BOOTSNAP_LOG=1` on both apps. If the cache misses, then we would expect to see output when running `time rake -T`

We can use `rake assets:precompile > /dev/null` to get only STDERR at runtime to see what is getting a cache miss in bootsnap.

**Results**:

After setting env vars and deploying:

```
heroku run bash
~ $ rake assets:precompile > /dev/null
```

There is no touput, so there are no cache misses. To ensure this is working as expected we can delete the cache and re-run to guarantee there is output:

```
~ $ rm -rf tmp/cache/
~ $ rake assets:precompile > /dev/null
[Bootsnap] miss /app/vendor/bundle/ruby/3.2.0/gems/railties-7.0.4.2/lib/rails/all.rb
[Bootsnap] miss /app/vendor/bundle/ruby/3.2.0/gems/railties-7.0.4.2/lib/rails.rb
[Bootsnap] miss /app/vendor/bundle/ruby/3.2.0/gems/railties-7.0.4.2/lib/rails/ruby_version_check.rb
[Bootsnap] miss /app/vendor/bundle/ruby/3.2.0/gems/activesupport-7.0.4.2/lib/active_support.rb
[Bootsnap] miss /app/vendor/ruby-3.2.1/lib/ruby/3.2.0/securerandom.rb
[Bootsnap] miss /app/vendor/ruby-3.2.1/lib/ruby/3.2.0/random/formatter.rb
...
[Bootsnap] miss /app/vendor/bundle/ruby/3.2.0/gems/railties-7.0.4.2/lib/rails/tasks/restart.rake
[Bootsnap] miss /app/vendor/bundle/ruby/3.2.0/gems/railties-7.0.4.2/lib/rails/tasks/tmp.rake
[Bootsnap] miss /app/vendor/bundle/ruby/3.2.0/gems/railties-7.0.4.2/lib/rails/tasks/yarn.rake
[Bootsnap] miss /app/vendor/bundle/ruby/3.2.0/gems/railties-7.0.4.2/lib/rails/tasks/zeitwerk.rake
[Bootsnap] miss /app/vendor/bundle/ruby/3.2.0/gems/railties-7.0.4.2/lib/rails/tasks/statistics.rake
[Bootsnap] miss /app/vendor/bundle/ruby/3.2.0/gems/activesupport-7.0.4.2/lib/active_support/fork_tracker.rb
```

**Conclusion**: The cache keys are the same. There are no misses. Strangely though, it's actually FASTER to delete the cache. For example:

Using the cache generated from build time:

```
heroku run bash
~ $ time rake -T > /dev/null

real	0m5.208s
user	0m1.056s
sys	0m0.176s
```

Removing the cache files and re-running:

```
heroku run bash
~ $ time rake -T > /dev/null

real	0m5.208s
user	0m1.056s
sys	0m0.176s
~ $ rm -rf tmp/cache/
~ $ unset BOOTSNAP_LOG && echo "reduce logging output because we only care about time for this example"
reduce logging output because we only care about time for this example
~ $ time rake -T > /dev/null

real	0m2.399s
user	0m1.960s
sys	0m0.432s
```

Notice that when we delete the cache, it's actually FASTER than running with the cache generated at build time (2.3 seconds versus 5.2 seconds).

This would give me a new theory that while the cache keys are valid, the contents of the cache are somehow unusable so that the problem is that bootsnap must do all the work of calculating cache keys and loading the data, then somehow it fails a check and it cannot use the contents. This extra work could be the cause of why `heroku run bash && time rake -T` takes LONGER than running without any cache.

## Theory: Cache keys exist, but cannot be used

**Hypothesis**: The contents of the cache are somehow unusable so that the problem is that bootsnap must do all the work of calculating cache keys and loading the data, then somehow it fails a check and it cannot use the contents. This extra work could be the cause of why `heroku run bash && time rake -T` takes LONGER than running without any cache.

**Testing**: ????

**Results**: ????

**Conclusion**: ????
