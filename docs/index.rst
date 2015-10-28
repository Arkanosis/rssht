rssht
=====

**rssht** creates and maintains a reverse SSH tunnel with optional SSH over HTTP.

In other words, it opens a port on a *remote client* (ie. the machine you want to connect from) forwarding to a port on the *local host* (ie. the machine you want to connect to) so that the remote client can connect to the local host even if the latter is not visible from the outside network (because it is behind some proxy, gateway, firewall or some NAT device).

When the connection is lost (because the remote client has been shut down, or because of a network issue), rssht takes care of re-establishing the tunnel.

Installation
------------

Getting rssht and its dependencies
``````````````````````````````````

rssht is not yet packaged, but the latest version is available for download `on Github <https://raw.githubusercontent.com/Arkanosis/rssht/master/rssht>`_.

If you plan to use the optional SSH over HTTP feature, you'll also need to install `httptunnel <https://www.gnu.org/software/httptunnel/httptunnel.html>`_ on both the local host *and* the client host.

On Debian-based systems, use the following on the local host:

::

     sudo wget 'https://raw.githubusercontent.com/Arkanosis/rssht/master/rssht' -O /usr/bin/rssht
     sudo chmod a+x /usr/bin/rssht

And if you want to use SSH over HTTP, use the following on both the local host *and* the client host:

::

     sudo apt-get install httptunnel

Note that you can of course install httptunnel and rssht anywhere, as long as the binaries are in your :code:`$PATH`.

Setting up SSH
``````````````

Using rssht may involve as many as four different users A, B, C and D:

* user A connecting from the *local host* to the *remote client* as user B using :code:`rssht` to establish the reverse SSH tunnel;
* user C connecting from the *remote client* to the *local host* as user D using :code:`ssh` to establish the SSH connexion.

A and D on the *local host* may be the same user — and will be, in most cases, and B and C on the *remote client* may be the same user too  — but this is not recommended, for security reasons (see `Security considerations`_ below).

Obviously, user C on the *remote client* must be able to connect to the *local host* as user D, as it would have to in a normal SSH connection. Any authentification method would work.

Maybe less obviously, user A on the *local host* must be able to connect to the *remote client* as user B. Since it will connect unattendedly, the authentification should be passwordless and passphraseless. It is recommended to use public key authentification with a passphraseless public key.

.. TODO ssh-keygen, authorized_keys, ControlMaster / ControlPersist,  and so on…

Security considerations
```````````````````````

Using a dedicated user on the client host (user B in the `Setting up SSH`_ section), with no right appart from being able to connect via SSH is recommended. This prevents the local host from doing anything nasty on the client host. This is particulary important if the local host is outside of your control (eg. a corporate host), and even more important as it will connect using a passphraseless public key.

Creating such a user depends on the system. On Debian-based systems, use the following on the client host to create a user named “rssht-user”:

::

    sudo adduser rssht-user # Create the user
    sudo usermod -s /bin/false rssht-user # Forbid anything else than SSH
    sudo mkdir /home/rssht-user/.ssh # Create the SSH configuration directory
    sudo cat local_host_ssh_key.pub >> /home/rssht-user/.ssh/authorized_keys # Allow the local host to connect on the client host as rssht-user
    sudo chown -R rssht-user:rssht-user /home/rssht-user/.ssh # Restore correct ownership
    sudo sed -i 's/AllowUsers .*/& rssht-user/' /etc/ssh/sshd_config # Allow rssht-user to connect via SSH
    sudo restart ssh # Restart SSH

Usage
-----

:code:`rssht` must be run on the *local host* (ie. the machine you want to connect to). It is used as follow:

::

    rssht [user@]host[:port] [-f port] [-t port] [-n time] [--http] [-d]

The following options are supported:

* **host**: client hostname
* **user**: username on client host (should have limited rights) [default: same as local username]
* **port**: open port on client host [default: 22 for SSH, 80 for HTTP]
* **-f port**: free port on client host which will redirect to the local host [default: 22000]
* **-t port**: open SSH port on local host to forward on the client host [default: 22]
* **-n time**: restore connection *time* seconds after failure [default: 5]
* **--http**: use SSH over HTTP instead of plain ssh. The client port must forward HTTP traffic to an open SSH port (using httptunnel's :code:`hts`)
* **-d**: run as daemon

Example:

::

    rssht rssht-user@httptunnel.example.com:80 -f 12345 -t 22 --http -d

If you want to use SSH over HTTP, you also need to have :code:`hts` running on the *client host* (ie. the machine you want to connect from):

::

    hts -F localhost:22 80

Then, you can use ssh on the *client host* to connect to the *local host* as follow:

::

    ssh localhost -p 12345

If the connection is lost, rssht will restore the tunnel after a few seconds, so you can connect again.

If you're using SSH over HTTP and for some reason :code:`hts` is hanging after losing the connection (it happens), kill it, start it again and wait for rssht to restore the tunnel.

You can monitor rssht's attempts to establish the tunnel by running the following command on the *client host*:

::

    tail -f /var/log/auth.log

Software recommendations
------------------------

Keeping sessions alive
``````````````````````

Since a SSH connection is not particulary reliable, especially if established within a HTTP tunnel to traverse a HTTP proxy, it is highly recommended to use some screen-like software to keep a detached session alive on the local host that can be re-attached later.

The author recommends the use of `tmux <https://tmux.github.io/>`_ which handles this task very well and provides a handful of additional features, such as terminal sharing, screen splitting…

Using graphical applications
````````````````````````````

SSH is great for terminal applications, but not so much for graphical applications. While it is possible to run some graphical applications on the *client host* and have them use the resources of the *local host* like network (eg. using a SOCKS proxy) or filesystem (eg. using sshfs), sometimes, one may want to run an application entirely on the *local host* and only show its GUI on the *client host*.

The author recommends the use of `xpra <https://www.xpra.org/>`_ which handles this task as well as it can be and provides some interesting features such as keeping the application alive if the connection is lost.

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


Acknowledgements
----------------

The author would like to thank the following people:

* `Anne-Sophie Denomme-Pichon <https://github.com/Oodnadatta>`_, for her precious feedback and extensive testing;
* `Richard Groux <https://github.com/rgroux>`_, for his tips with SSH's ControlMaster and ControlPersist;
* `Xavier Roche <https://github.com/xroche>`_, the author of pepette, the script from which the inspiration for rssht comes from.

Related projects
----------------

The following projects are related: `OpenSSH <http://www.openssh.com/>`_, `autossh <http://www.harding.motd.ca/autossh/>`_, `Corkscrew <http://www.agroman.net/corkscrew/>`_, `httptunnel <https://www.gnu.org/software/httptunnel/httptunnel.html>`_.

The current version of rssht is heavily based on OpenSSH and relies on httptunnel for the optional SSH over HTTP.
