rssht
=====

NAME
----

**rssht** - Reverse SSH tunnel with optional SSH over HTTP

SYNOPSIS
--------

::

    rssht [user@]host[:port] [-f port] [-t port] [-n time] [--http] [-d]


DESCRIPTION
-----------

**rssht** opens a port on a remote client forwarding to a local port so that the remote client can connect to the local host even if the latter is not visible from the outside network.

OPTIONS
-------

* **host**: client hostname
* **user**: username on client host (should have limited rights) [default: same as local username]
* **port**: open port on client host [default: 22 for ssh, 80 for http]
* **-f port**: free port on client host which will redirect to the local host [default: 22000]
* **-t port**: open ssh port on local host to forward on the client host [default: 22]
* **-n time**: restore connection *time* seconds after failure [default: 5]
* **--http**: use ssh over http instead of plain ssh. The client port must forward http traffic to an open ssh port (using httptunnel's hts)
* **-d**: run as daemon

EXAMPLES
--------

::

    rssht rssht-user@httptunnel.example.com:80 -f 12345 -t 22 --http -d

NOTES
-----

Using a dedicated user on the client host, with no right appart from being able to connect via SSH is recommended. This prevents the local host from doing anything nasty on the client host.

Creating such a user depends on the system. On Debian-based systems, use the following on the client host:::

    sudo adduser rssht-user # Create the user
    sudo usermod -s /bin/false rssht-user # Forbid anything else than SSH
    sudo mkdir /home/rssht-user/.ssh # Create the SSH configuration directory
    sudo cat local_host_ssh_key.pub >> /home/rssht-user/.ssh/authorized_keys # Allow the local host to connect on the client host as rssht-user
    sudo chown -R rssht-user:rssht-user /home/rssht-user/.ssh # Restore correct ownership
    sudo sed -i 's/AllowUsers .*/& rssht-user/' /etc/ssh/sshd_config # Allow rssht-user to connect via SSH
    sudo restart ssh # Restart SSH

BUGS
----

Please report bugs and feature requests on `Github <https://github.com/Arkanosis/rssht/issues>`_.

COPYRIGHT
---------

rssht is Copyright (C) 2015 Jérémie Roquet <jroquet@arkanosis.net>.

rssht is licensed under the MIT license.

THANKS
------

Thanks to Xavier Roche <roche@httrack.com>, the author of pepette, the script from which the inspiration for rssht comes from.

SEE ALSO
--------

openssh, corkscrew, httptunnel
