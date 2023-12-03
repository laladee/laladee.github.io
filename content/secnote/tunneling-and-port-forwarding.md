+++
title = "Tunneling and Port Forwarding"
date = "2021-05-27"
categories = [
    "network",
]

+++

## SSH Tunnel

- config in `/etc/ssh/sshd_config`

- `N - do not execute a remote command`

### Socket tunnel with SSH

```bash
ssh -ND 8080 username@12.12.12.12
```

Now configure SOCKS proxy to 127.0.0.1 port 8080, all the traffic is now tunneled through 12.12.12.12 as a VPN.

### Port forwarding with SSH

**Local port forwarding:**

```bash
ssh -g -NL 1337:12.12.12.150:8000 username@12.12.12.12
```

Now connect to `[::]/127.0.0.1` on port 1337 will get tunneled to 12.12.12.150:8000 via 12.12.12.12

**Remote port forwarding:** `GatewayPorts yes`

```bash
ssh -g -NR 1337:127.0.0.1:8000 username@12.12.12.12
```

Now connect to 12.12.12.12:1337 will get tunneled to 127.0.0.1:8000 via my computer. This often used to give access my local network from internet.
