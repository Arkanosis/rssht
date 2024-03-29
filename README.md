# rssht [![Version](https://img.shields.io/badge/version-v0.3.0-orange.svg)](https://semver.org/spec/v2.0.0.html) [![License](http://img.shields.io/badge/license-ISC-blue.svg)](/LICENSE) [![Documentation Status](https://readthedocs.org/projects/rssht/badge/?version=latest)](http://rssht.readthedocs.org/en/latest/?badge=latest)

**rssht** creates and maintains a reverse SSH tunnel with optional SSH over HTTP.

In other words, it opens a port on a *remote client* (ie. the machine you want to connect from) forwarding to a port on the *local host* (ie. the machine you want to connect to) so that the remote client can connect to the local host even if the latter is not visible from the outside network (because it's behind some proxy, gateway, firewall or some NAT device).

When the connection is lost (because the remote client has been shut down, or because of a network issue), rssht takes care of re-establishing the tunnel.

It is useful when all you have on the machine you want to connect to is a SSH client and no root access. If you are root on the machine you want to connect to, you may prefer using a tool like [Nebula](https://github.com/slackhq/nebula).

## Documentation

An extensive documentation is available on [Read the Docs](http://rssht.readthedocs.io/).

## Contributing and reporting bugs

Contributions are welcome through [GitHub pull requests](https://github.com/Arkanosis/rssht/pulls).

Please report bugs and feature requests on [GitHub issues](https://github.com/Arkanosis/rssht/issues).

## License

rssht is copyright (C) 2015-2023 Jérémie Roquet <jroquet@arkanosis.net> and licensed under the ISC license.

## Related projects

openssh, autossh, corkscrew, httptunnel
