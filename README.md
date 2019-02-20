# socklog-overlay

The socklog-overlay is an add-on for the
[s6-overlay](https://github.com/just-containers/s6-overlay) - it provides
a small syslog replacement based on Gerrit Pape's [socklog](http://smarden.org/socklog/).

## Usage

Installation is similar to installing the `s6-overlay`:

```Dockerfile
FROM ubuntu

# Install s6-overlay
ADD https://github.com/just-containers/s6-overlay/releases/download/v1.21.8.0/s6-overlay-amd64.tar.gz /tmp/
RUN tar xzf /tmp/s6-overlay-amd64.tar.gz -C /

# Install socklog-overlay
ADD https://github.com/just-containers/socklog-overlay/releases/download/v3.0.1-1/socklog-overlay-amd64.tar.gz /tmp/
RUN tar xzf /tmp/socklog-overlay-amd64.tar.gz -C /

ENTRYPOINT ["/init"]
```

This will run a logging service with all messages in directories under `/var/log/socklog/`,
with built-in log rotation.

* `/var/log/socklog/cron`
* `/var/log/socklog/daemon`
* `/var/log/socklog/debug`
* `/var/log/socklog/errors`
* `/var/log/socklog/everything`
* `/var/log/socklog/kernel`
* `/var/log/socklog/mail`
* `/var/log/socklog/messages`
* `/var/log/socklog/secure`
* `/var/log/socklog/user`

## Customization

### Custom logging rules

`socklog-overlay` works by reading in a series of `s6-log` logging scripts from
`/etc/socklog.rules`. You can create your own rules by placing a file in
`/etc/socklog.rules`.

For example, if you wanted to save all errors for messages tagged with the
"local0" facility, you could create the file `/etc/socklog.rules/local0-error`

```
-
+^local0\.err
T
/var/log/socklog/local0-errors
```

This will match lines that begin with `local0.err`, prepend them with an ISO8601 timestamp, and save them to the `/var/log/socklog/local0-errors` folder.

Another example, if you wanted to have all syslog messages copied to stdout,
create a file at `/etc/socklog.rules/forward-stdout`:

```
+
1
```

This will match all lines (as indicated by the `+` symbol with an empty regex),
and forward them to stdout (indicated by the `1` symbol).

More details on how to write `s6-log` logging scripts are available in the
[s6-log manual](http://skarnet.org/software/s6/s6-log.html).

### Creating logging folders

The `/etc/cont-init.d/~-socklog` script should run last, and its final step
is to recursively chown `/var/log/socklog`.

Create a script in `/etc/cont-init.d` to make your needed logging folder,
if it's a subfolder of `/var/log/socklog`, you should be covered. If not,
you'll likely need to chown it as well, to the `nobody` user.

Ideas I'd like to flesh out:

* Setting an environment variable to specify number of files, size, etc
  * Right now this is just using the `s6-log` defaults - 10 files, ~100k per file

## Verifying Downloads

The `socklog-overlay` releases are signed using `gpg`, you can import our public key:

```bash
$ curl https://keybase.io/justcontainers/key.asc | gpg --import
```

Then verify the downloaded files:

```bash
$ gpg --verify socklog-overlay-amd64.tar.gz.sig socklog-overlay-amd64.tar.gz
```

## Upgrade Notes

`socklog-overlay` version 3.0.0 switched from having the hard-coded
`log/run` script with log pattern rules, to using the `/etc/socklog.rules`
folder. If you have a custom `log/run` script, it should continue to work.

## LICENSE

ISC license, see `LICENSE.md`

Binary downloads include a copy of `socklog`, which is released under
a 3-clause BSD license. Please see [COPYING](https://github.com/just-containers/socklog/blob/master/COPYING)
for details.
