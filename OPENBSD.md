Shairport Sync on OpenBSD using `sndio`
----
This is an initial note about installing Shairport Sync on OpenBSD. Shairport Sync compiles and runs natively on OpenBSD using the `sndio` back end.

General
----
This build was done on a default build of `OpenBSD 6.2 GENERIC.MP#134 amd64`. Following [this guide](https://www.openbsd.org/faq/faq15.html), `/etc/installurl` was created with the contents:
```
https://ftp.openbsd.org/pub/OpenBSD
```
Next, although it may not be necessary, [update the packages](https://unix.stackexchange.com/questions/23579/how-to-apply-updates-on-openbsd-netbsd-and-freebsd).
```
# pkg_add -Uu
```
Install the Avahi subsystem ([search](https://www.openbsd.org/faq/faq15.html) using, for example, `# pkg_info -Q avahi`). 
```
# pkg_add avahi-0.7p0
```
A number of libraries will be installed to support Avahi, including the D-Bus system.
Enable the D-Bus and Avahi subsystems to [start automatically](http://openbsd-archive.7691.n7.nabble.com/starting-avahi-the-proper-way-td311612.html):
```
# rcctl enable messagebus avahi_daemon 
# rcctl start messagebus avahi_daemon 
```
Building
----

Install the following packages (e.g. using `pkg_add` in superuser mode) that are needed for Shairport Sync to be downloaded and built successfully:
```
git autoconf automake automake popt libconfig
```
Add the relevant shell variable definitions for Autoconf and Automake -- they could be placed in the user's `.profile` file to be automatically executed at login:
```
export AUTOCONF_VERSION=2.69
export AUTOMAKE_VERSION=1.15
```
Now, download Shairport Sync from GitHub:
```
$ git clone https://github.com/mikebrady/shairport-sync.git
$ cd shairport-sync
```
Next, switch to the `development` branch, configure the build and compile it:
```
$ git checkout development
$ autoreconf -i -f
$ ./configure  --with-avahi --with-ssl=openssl --with-sndio --with-os=openbsd
$ make
```
There is no make install yet -- you're on your own.

Using the `sndio` backend
----
The `sndio` back end does not have a hardware volume control facility.
You should set the volume to maximum before use, using, for example, the `mixerctl` command.