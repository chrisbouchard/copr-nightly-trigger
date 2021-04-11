# Copr Nightly Trigger

[![Copr build status][copr-status-image]][copr-nightly-trigger-package]

Systemd units to trigger nightly builds of one or more Copr projects.


## Installation

This package is available from the
[chrisbouchard/upliftinglemma][upliftinglemma-project] Copr. Assuming
you're using a recent version of Fedora or CentOS, you can install using `dnf`.

```console
$ dnf copr enable chrisbouchard/upliftinglemma
$ dnf install copr-nightly-trigger
```


## Configuration

The `copr-nightly@.service` service template lets you configure multiple build
triggers from the same server. So if we're building a package `foo`, we may
instantiate the service as `copr-nightly@foo.service`. The name is purely for
organization; it's not used by the build.

This service is configured using standard systemd configuration techniques.
There are two properties and a credential that must be supplied. So, for the
service mentioned above, we'd create the directory
`/etc/systemd/system/copr-nightly@foo.service.d` and create a configuration
file, e.g., `00-copr.conf` (the name does not matter).

###### /etc/systemd/system/copr-nightly@foo.service.d/00-copr.conf
```systemd
[service]
Environment=PROJECT=awesomeproject
Environment=PACKAGE=foo
LoadCredential=copr:/etc/copr-nightly/your-copr-credentials.conf
```

(The `/etc/copr-nightly` directory is reserved for Copr credentials files, and
is owned by root with 0600 permissions to keep out prying eyes, but the path
can technically be anywhere.)

Copr credentials are obtained from [Copr API][copr-api] after logging in. The
same credentials file can be used by multiple services, as it only identifies
your user. Note credentials expire after a few months, so make sure to keep
them up-to-date.

You can test that the service is configured correctly by starting it manually:

```console
$ systemctl start copr-nightly@foo.service
```


## Running Nightly

As the name implies, the goal is to trigger a build every night around
midnight. The `copr-nightly@.timer` timer template will trigger the
corresponding service at that time. Once the service is configured, you must
enable the timer. For our `foo` example, we'd enable the timer as follows:

```console
$ systemctl enable copr-nightly@foo.timer
```


[copr-api]: https://copr.fedorainfracloud.org/api/
[copr-nightly-trigger-package]: https://copr.fedorainfracloud.org/coprs/chrisbouchard/upliftinglemma/package/copr-nightly-trigger/
[copr-status-image]: https://copr.fedorainfracloud.org/coprs/chrisbouchard/upliftinglemma/package/copr-nightly-trigger/status_image/last_build.png
[upliftinglemma-project]: https://copr.fedorainfracloud.org/coprs/chrisbouchard/upliftinglemma

