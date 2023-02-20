## Deploy to non-private-space

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

## Deploy to private-space

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
