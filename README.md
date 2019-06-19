# howto-openvpn
Mini-Howto for setting up openvpn with static key for POC

## https://openvpn.net/community-resources/static-key-mini-howto/

## Create Firewall Rule on Cloud for OpenVPN
- Example
    - name: create the firewall rule (openvpn)

        --direction=INGRESS
        --network=default
        --action=ALLOW
        --rules=udp:1194
        --source-ranges=0.0.0.0/0



## Install Openvpn package on Server
yum, age-get install etc..

### Generate static secret key for openvpn
- Static Key advantages: Simple Setup, No X509 PKI (Public Key Infrastructure) to maintain

command: openvpn --genkey --secret secret.key


### Edit configuration file for OpenVPN Server
- Local_Tunnel_IP:  172.16.253.1

- Remote_Tunnel_IP: 172.16.253.2

filename: /etc/openvpn/server/server.conf
```bash
dev tun
proto udp

secret secret.key 0
ifconfig 172.16.253.1 172.16.253.2

user nobody
group nobody
persist-tun
persist-key
compress lz4
keepalive 10 60
ping-timer-rem

verb 3
```

- Restart OpenVPN Service

-------------------------------------------------
## Install OpenVPN package on Client

### Create a folder for your target remote site
```bash
cd \"C:\Program Files\OpenVPN\config"
mkdir "demo-vpn"
```

### Create openvpn client config file

filename:"C:\Program Files\OpenVPN\config\demo-vpn\demo-vpn.ovpn"
```bash
dev tun
proto udp
nobind
compress lz4
auth-nocache
remote {Remote Public IP or FQDN}
rport 1194

secret secret.key 1
ifconfig 172.16.253.2 172.16.253.1

persist-tun
persist-key
keepalive 10 60

verb 3
```

### Copy secret.key from server to the openvpn client config folder
  copy:
  
    src (Server): /etc/openvpn/server/secret.key

    dst (client): "C:\Program Files\OpenVPN\config\demo-vpn\secret.key"
