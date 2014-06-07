### What is Karver

Inspired by ActiveRecord database migrations, Karver allows you to follow the same schema evolution pattern but against the filesystem.

### How does it work?
Give Karver a list of migrations and a target path. Each migration will receive the target as its first parameter.

After each migration, if the execution ends successfully, a flag will be written in the target path. The flag file will be used to determine which migrations need to be run, and which ones have already been run.

If the flag file does not exist, all migrations will be executed in order (cronological ascending).

### What is it trying to solve?
Karver tries to solve exactly this situation:

You have 3 instances of a filesystem, each in a different state (imagine a VM with 3 different versions of your software):
i.e.:

* v1 creates /foo
* v2 moves /foo to /bar/foo
* v3 creates a symbolic link: /bar/foo -> /new/path/foo

Karver allows you to create an executable for each step (a simple bash script?), and run them only when needed based on the starting point.

If executed against v1, it would run migrations from v1 -> v2 and v2 -> v3. If executed against v2, it would only run migrations from v2 -> v3.

```
$ karver --migrations /opt/karver/migrations create 'first migration'
New migration: /opt/karver/migrations/20140604205700_first_migration.sh

$ karver --migrations /opt/karver/migrations/ --target /opt/karver/target/ run
2014/06/04 21:06:45 Karving /opt/karver/target/...
2014/06/04 21:06:45 Running migration 20140604205700_first_migration.sh...
2014/06/04 21:06:45 /opt/karver/target/ has been karved. :D
```

###  Use it with Docker
If you need to maintain and upgrade data containers, you can easily use https://github.com/karver/docker-karver for the purpose.

```
$ docker run \
  --volumes-from target-container \
  custom_karver_migrations \
  --target /target-path \
  run
```
### Authors
Salvador Gironès | salvador@redbooth.com | [Twitter](https://twitter.com/salvadorgirones) | [GitHub](https://github.com/sgirones)

Ferran Basora | ferran@redbooth.com | [Twitter](https://twitter.com/openfcsonline) | [GitHub](https://github.com/fcsonline)
