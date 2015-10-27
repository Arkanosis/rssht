rssht
=====

**rssht** creates a reverse SSH tunnel with optional SSH over HTTP.

In other words, it opens a port on a remote client forwarding to a local port so that the remote client can connect to the local host even if the latter is not visible from the outside network (because it's behind some proxy, gateway, firewall or some NAT device).

Usage
-----

**rssht** is used as follow:::

    rssht [user@]host[:port] [-f port] [-t port] [-n time] [--http] [-d]


The following options are supported:

* **host**: client hostname
* **user**: username on client host (should have limited rights) [default: same as local username]
* **port**: open port on client host [default: 22 for ssh, 80 for http]
* **-f port**: free port on client host which will redirect to the local host [default: 22000]
* **-t port**: open ssh port on local host to forward on the client host [default: 22]
* **-n time**: restore connection *time* seconds after failure [default: 5]
* **--http**: use ssh over http instead of plain ssh. The client port must forward http traffic to an open ssh port (using httptunnel's hts)
* **-d**: run as daemon

Examples
--------

::

    rssht rssht-user@httptunnel.example.com:80 -f 12345 -t 22 --http -d

Security considerations
-----------------------

Using a dedicated user on the client host, with no right appart from being able to connect via SSH is recommended. This prevents the local host from doing anything nasty on the client host.

Creating such a user depends on the system. On Debian-based systems, use the following on the client host:::

    sudo adduser rssht-user # Create the user
    sudo usermod -s /bin/false rssht-user # Forbid anything else than SSH
    sudo mkdir /home/rssht-user/.ssh # Create the SSH configuration directory
    sudo cat local_host_ssh_key.pub >> /home/rssht-user/.ssh/authorized_keys # Allow the local host to connect on the client host as rssht-user
    sudo chown -R rssht-user:rssht-user /home/rssht-user/.ssh # Restore correct ownership
    sudo sed -i 's/AllowUsers .*/& rssht-user/' /etc/ssh/sshd_config # Allow rssht-user to connect via SSH
    sudo restart ssh # Restart SSH

Contributing
------------

You can contribute by reporting bugs and feature requests on `Github <https://github.com/Arkanosis/rssht/issues>`_.

`Pull requests <https://github.com/Arkanosis/rssht/pulls>`_ for code and documentation are welcome too.

License
-------

Copyright (C) 2015 Jérémie Roquet <jroquet@arkanosis.net>

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in
all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN
THE SOFTWARE.


Acknowledgments
---------------

The author would like to thank Xavier Roche <roche@httrack.com>, the author of pepette, the script from which the inspiration for rssht comes from.

Related projects
----------------

The following projects are related: `OpenSSH <http://www.openssh.com/>`_, `autossh <http://www.harding.motd.ca/autossh/>`_, `Corkscrew <http://www.agroman.net/corkscrew/>`_, `httptunnel <https://www.gnu.org/software/httptunnel/httptunnel.html>`_.

The current version of rssht is heavily based on OpenSSH and rely on httptunnel for the optional SSH over HTTP.
