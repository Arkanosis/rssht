# rssht [![License](http://img.shields.io/badge/license-MIT-blue.svg)](/LICENSE) [![Documentation Status](https://readthedocs.org/projects/rssht/badge/?version=latest)](http://rssht.readthedocs.org/en/latest/?badge=latest)

**rssht** creates a reverse SSH tunnel with optional SSH over HTTP.

In other words, it opens a port on a remote client forwarding to a local port so that the remote client can connect to the local host even if the latter is not visible from the outside network (because it's behind some proxy, gateway, firewall or some NAT device).

## Usage

**rssht** is used as follow:

```
rssht [user@]host[:port] [-f port] [-t port] [-n time] [--http] [-d]
```

The following options are supported:

* **host**: client hostname
* **user**: username on client host (should have limited rights) [default: same as local username]
* **port**: open port on client host [default: 22 for ssh, 80 for http]
* **-f port**: free port on client host which will redirect to the local host [default: 22000]
* **-t port**: open ssh port on local host to forward on the client host [default: 22]
* **-n time**: restore connection *time* seconds after failure [default: 5]
* **--http**: use ssh over http instead of plain ssh. The client port must forward http traffic to an open ssh port (using httptunnel's hts)
* **-d**: run as daemon

Example:

```
rssht rssht-user@httptunnel.example.com:80 -f 12345 -t 22 --http -d
```

## Documentation

The complete documentation is available on [Read the Docs](http://rssht.readthedocs.org/).

## Reporting bugs

Please report bugs and feature requests on [Github](https://github.com/Arkanosis/rssht/issues).

## License

rssht is copyright (C) 2015 Jérémie Roquet <jroquet@arkanosis.net> and licensed under the MIT license.

## Related projects

openssh, autossh, corkscrew, httptunnel
