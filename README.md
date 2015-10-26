# reverse-ssh-tunnel

## NAME

**reverse-ssh-tunnel** - Reverse SSH tunnel with optional SSH over HTTP

## SYNOPSIS

```
reverse-ssh-tunnel [user@]host[:port] [-f port] [-t port] [-n time] [--http] [-d]
```

## DESCRIPTION

**reverse-ssh-tunnel** opens a port on a remote client forwarding to a local port so that the remote client can connect to the local host even if the latter is not visible from the outside network.

## OPTIONS

* **host**: client hostname
* **user**: username on client host (should have limited rights) [default: same as local username]
* **port**: open port on client host [default: 22 for ssh, 80 for http]
* **-f port**: free port on client host which will redirect to the local host [default: 22000]
* **-t port**: open ssh port on local host to forward on the client host [default: 22]
* **-n time**: restore connection *time* seconds after failure [default: 5]
* **--http**: use ssh over http instead of plain ssh. The client port must forward http traffic to an open ssh port (using httptunnel's hts)
* **-d**: run as daemon

## EXAMPLES

```
reverse-ssh-tunnel rsshuser@httptunnel.example.com:80 -f 12345 -t 22 --http -d
```

## NOTES

Using a dedicated user on the client host, with no right appart from being able to connect via SSH is recommended. This prevents the local host from doing anything nasty on the client host.

Creating such a user depends on the system. On Debian-based systems, use the following on the client host:

```
sudo adduser rsshuser # Create the user
sudo usermod -s /bin/false rsshuser # Forbid anything else than SSH
sudo mkdir /home/rsshuser/.ssh # Create the SSH configuration directory
sudo cat local_host_ssh_key.pub >> /home/rsshuser/.ssh/authorized_keys # Allow the local host to connect on the client host as rsshuser
sudo chown -R rsshuser:rsshuser /home/rsshuser/.ssh # Restore correct ownership
sudo sed -i 's/AllowUsers .*/& rsshuser/' /etc/ssh/sshd_config # Allow rsshuser to connect via SSH
sudo restart ssh # Restart SSH
```

## BUGS

Please report bugs and feature requests on [Github](https://github.com/Arkanosis/reverse-ssh-tunnel/issues)

## COPYRIGHT

reverse-ssh-tunnel is Copyright (C) 2015 Jérémie Roquet <jroquet@arkanosis.net>

## SEE ALSO

openssh, corkscrew, httptunnel
