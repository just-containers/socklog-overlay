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
ADD https://github.com/just-containers/socklog-overlay/releases/download/v2.2.1-4/socklog-overlay-amd64.tar.gz /tmp/
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

None yet, if you have any ideas we'll gladly accept pull requests!

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

## LICENSE

ISC license, see `LICENSE.md`

Binary downloads include a copy of `socklog`, which is released under
a 3-clause BSD license. Please see [COPYING](https://github.com/just-containers/socklog/blob/master/COPYING)
for details.
