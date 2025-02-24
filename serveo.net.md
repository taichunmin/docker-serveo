# Expose local servers to the internet

> 2025/02/25 backup in case of [serveo.net](https://serveo.net) is down.

No installation, no signup. For help using Serveo, use the [Serveo Google Group](https://groups.google.com/g/serveo).

```bash
$ ssh -R 80:localhost:3000 serveo.net
# Try it! Copy and paste this into your terminal.
```

## How does it work?

Serveo is an SSH server just for remote port forwarding. When a user connects to Serveo, they get a public URL that anybody can use to connect to their localhost server.

![](https://i.imgur.com/hFzuofo.png)

## Manual

### Basic use

```bash
$ ssh -R 80:localhost:3000 serveo.net
```

The `-R` option instructs your SSH client to request port forwarding from the server and proxy requests to the specified host and port (usually localhost). A subdomain of serveo.net will be assigned to forward HTTP traffic.

### Request multiple tunnels at once

```bash
$ ssh -R 80:localhost:8888 -R 80:localhost:9999 serveo.net
```

### The target server doesn't have to be on localhost

```bash
$ ssh -R 80:example.com:80 serveo.net
```

### Request a particular subdomain

The subdomain is chosen deterministically based on your IP address, the provided SSH username, and subdomain availability, so you'll often get the same subdomain between restarts. You can also request a particular subdomain:

```bash
$ ssh -R incubo:80:localhost:8888 serveo.net
$ ssh -R incubo.serveo.net:80:localhost:8888 serveo.net
```

Change the SSH username to get assigned a different subdomain:

```bash
$ ssh -R 80:localhost:8888 foo@serveo.net
$ ssh -R 80:localhost:8888 -l foo serveo.net
```

### Private TCP and SSH forwarding

Serveo can be used to route private TCP traffic, almost like a lightweight VPN. To set up the tunnel, specify an alias as the hostname and some port:

```bash
$ ssh -R myalias:5901:localhost:5900 serveo.net
```

Then to connect to that port from another machine, use `ssh -L`:

```bash
$ ssh -L 5902:myalias:5901 serveo.net
```

Then connect to `localhost:5902` on the remote machine, and SSH will send traffic through Serveo, which will forward it to the target machine, ultimately connecting you to port 5900 on the target machine.

If you're using this to connect to an SSH server, then you can use OpenSSH's JumpHost feature. On the target machine, you might start the tunnel like this:

```bash
$ ssh -R myalias:22:localhost:22 serveo.net
```

Then you can establish an SSH connection using serveo.net as an intermediary like this:

```bash
$ ssh -J serveo.net user@myalias
```

The `-J` option was introduced in the OpenSSH client version 7.3. If you have an older client, you can use the ProxyCommand option instead:

```bash
$ ssh -o ProxyCommand="ssh -W myalias:22 serveo.net" user@myalias
```

### Public TCP forwarding

If you request a port other than 80 or 443, raw TCP traffic will be forwarded. (In this case, there's no way to route connections based on hostname, and the host, if specified, will trigger private TCP forwarding.)

```bash
$ ssh -R 1492:localhost:1492 serveo.net
```

If port 0 is requested, a random TCP port will be forwarded:

```bash
$ ssh -R 0:localhost:1492 serveo.net
```

### Connect on port 443

In some environments, outbound port 22 connections are blocked. For this reason, you can also connect on port 443.

```bash
$ ssh -p 443 -R 80:localhost:8888 serveo.net
```

### Automatically reconnect

Use [autossh](https://web.archive.org/web/20250222135742/http://www.harding.motd.ca/autossh/) for more persistent tunnels. Use `-M 0` to disable autossh's connectivity checking:

```bash
$ autossh -M 0 -R 80:localhost:8888 serveo.net
```

See <https://www.everythingcli.org/ssh-tunnelling-for-fun-and-profit-autossh/> for more about autossh.

### Custom Domain

To use your own domain or subdomain, you'll first need an SSH key pair. Use the ssh-keygen program to generate a key pair if you don't already have one.

Next, use `ssh-keygen -l` and note your key's fingerprint. Here's an example output:

```
2048 SHA256:pmc7ZRv7ymCmghUwHoJWEm5ToSTd33ryeDeps5RnfRY no comment (RSA)
```

In this example, the fingerprint is `SHA256:pmc7ZRv7ymCmghUwHoJWEm5ToSTd33ryeDeps5RnfRY`.

Now you need to add two DNS records for the domain or subdomain you'd like to use:

1. A **CNAME** record pointing to `serveo.net`.
2. For each SSH key to allow, a TXT record at `_serveo-authkey.[domain]` = `[fingerprint]`.
3. Once your DNS records are in place, you can request your subdomain/domain from Serveo:

```bash
$ ssh -R subdomain.example.com:80:localhost:3000 serveo.net
```

When you request port forwarding for subdomain.example.com, Serveo will fetch the TXT records from your DNS server and only allow forwarding if you've provided a public key with the same fingerprint as specified in TXT records.

## Alternatives

### ngrok

Serveo is an excellent alternative to ngrok. Serveo was inspired by ngrok and attempts to serve many of the same purposes. The primary advantage of Serveo over ngrok is the use of your existing SSH client, so there's no client application to install.

Other slight advantages include preservation of URLs across reconnect for free (ngrok allows this only for paid accounts) and in-terminal request inspection and replay (ngrok uses a web interface).

### OpenSSH Server

Using Serveo instead of OpenSSH frees you from having to configure and maintain a server. It also handles HTTPS and subdomain generation, two features that complicate a typical SSH port-forwarding setup.

If Serveo doesn't meet your needs, [this guide](https://web.archive.org/web/20250222135742/https://dev.to/k4ml/poor-man-ngrok-with-tcp-proxy-and-ssh-reverse-tunnel-1fm) has some ideas for setting up OpenSSH.