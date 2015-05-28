# reverse-ssh-tunnel

## NAME

**reverse-ssh-tunnel** - Reverse SSH tunnel with optional SSH over HTTP

## SYNOPSIS

```
reverse-ssh-tunnel [user@]host[:port] [-f port] [-t port] [--http]
```

## DESCRIPTION

**reverse-ssh-tunnel** opens a port on a remote client forwarding to a local port so that the remote client can connect to the local host even if the latter is not visible from the outside network.

## OPTIONS

* **host**: client hostname
* **user**: username on client host (should have limited rights) (default: same as local username)
* **port**: open ssh port on client host (default: 22)
* **-f port**: free port on client host which will redirect to the local host (default: 22000)
* **-t port**: open ssh port on local host to forward on the client host (default: 22)
* **--http**: use ssh over http instead of plain ssh. The client port must forward http traffic to an open ssh port

## EXAMPLES

## BUGS

No bug has been reported yet.

## COPYRIGHT

reverse-ssh-tunnel is Copyright (C) 2015 Jérémie Roquet <jroquet@arkanosis.net>

## SEE ALSO

openssh, corkscrew, httptunnel
