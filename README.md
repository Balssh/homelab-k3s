# Setting up k3s homelab

> [!NOTE]
> This template repo is used to illustrate a [flux-managed Kubernetes cluster](http://localhost:8123/kubernetes/deployment/flux/), in Funky Penguin's Geek Cookbook

## Setting up Oracle VPS

Install ufw and configure it like:

- default deny incoming traffic
- default enable outgoing traffic

Install Wireguard and create the config like:

```config
``` [Interface]
Address = 10.10.0.1
PrivateKey = <key>
ListenPort = 51820

# packet forwarding

PreUp = sysctl -w net.ipv4.ip_forward=1

# port forwarding (assuming ens3 is your public interface)

PreUp = iptables -t nat -A PREROUTING -i ens3 -p tcp --dport 80 -j DNAT --to-destination 10.10.0.2:80
PreUp = iptables -t nat -A PREROUTING -i ens3 -p tcp --dport 443 -j DNAT --to-destination 10.10.0.2:443
PreUp = iptables -A FORWARD -i ens3 -o wg0 -p tcp --dport 80 -j ACCEPT
PreUp = iptables -A FORWARD -i ens3 -o wg0 -p tcp --dport 443 -j ACCEPT

# packet masquerading

PreUp = iptables -t nat -A POSTROUTING -o ens3 -j MASQUERADE
PreUp = iptables -t nat -A POSTROUTING -o wg0 -j MASQUERADE

PostDown = iptables -t nat -D PREROUTING -i ens3 -p tcp --dport 80 -j DNAT --to-destination 10.10.0.2:80
PostDown = iptables -t nat -D PREROUTING -i ens3 -p tcp --dport 443 -j DNAT --to-destination 10.10.0.2:443
PostDown = iptables -D FORWARD -i ens3 -o wg0 -p tcp --dport 80 -j ACCEPT
PostDown = iptables -D FORWARD -i ens3 -o wg0 -p tcp --dport 443 -j ACCEPT
PostDown = iptables -t nat -D POSTROUTING -o ens3 -j MASQUERADE
PostDown = iptables -t nat -D POSTROUTING -o wg0 -j MASQUERADE

[Peer]
PublicKey = <key>
AllowedIPs = 10.10.0.2
```

> [!WARNING]
> Make sure you also allow whatever ports you use in the oracle vps admin panel

## Setting up client VPN

Install ufw and Wireguard like on the server and the config like:

```config
[Interface]
PrivateKey = <key>
Address = <client ip>

[Peer]
PublicKey = <key>
AllowedIPs = 0.0.0.0/0
Endpoint = <public ip of vps>:<port>
PersistentKeepalive = 25
```

> [!NOTE]
> To check if the tunnel is working: ping each machine from the other side and
also ping an external service from the client (like google.com)

## To do

- Ansible playbooks to automatize things
- [x] SSO
- [x] DNS
- [ ] budgeting app
- [ ] media server
- [ ] recipe manager
- [ ] paperless
- [ ] bookmark manager
- [ ] photo storage
- [ ] monitoring

## SSO

Got Authentik to work after many struggles (and a detour to Authelia). Key steps:

- setup secret-key for users and password for postgres
- make the redis/postgres PVCs smaller (default is 8Gi)
- the embedded outpost automatically creates a Traefik Middleware,
but I prefer declaring my own
- for OIDC make sure the group name matches the one in the RBAC
