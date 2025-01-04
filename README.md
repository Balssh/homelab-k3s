# Setting up k3s homelab

> [!NOTE]
> This template repo is used to illustrate a [flux-managed Kubernetes cluster](http://localhost:8123/kubernetes/deployment/flux/), in Funky Penguin's Geek Cookbook

## Setting up Oracle VPS

Install ufw and configure it like:

- default deny incoming traffic
- default enable outgoing traffic

Install Wireguard and create the config like:

```config
[Interface]
PrivateKey = <key>
Address = <server ip>
ListenPort = <port>
PostUp = ufw allow in on wg0; ufw route allow in on wg0 out on ens3; ufw route allow in on ens3 out on wg0; iptables -t nat -A POSTROUTING -o ens3 -j MASQUERADE; iptables -t nat -A PREROUTING -i ens3 -p tcp -j DNAT --to-destination <client ip>
PostDown = ufw delete allow in on wg0; ufw route delete allow in on wg0 out on ens3; ufw route delete allow in on ens3 out on wg0; iptables -t nat -D POSTROUTING -o ens3 -j MASQUERADE; iptables -t nat -D PREROUTING -i ens3 -p tcp -j DNAT --to-destination <client ip>

[Peer]
PublicKey = <key>
AllowedIPs = <client ip>
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

- [ ] SSO
- [ ] media server
- [ ] recipe manager
- [ ] paperless
- [ ] bookmark manager
- [ ] photo storage
- [ ] monitoring
