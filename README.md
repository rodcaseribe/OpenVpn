# OpenVpn
Open VPN - TUN

# Configurar VLAN 10.10.3.0/24 para utilizar essa faixa

# configuracao do tun0.conf
dev tun

tls-server

port 1194

ca /etc/openvpn/ca.crt

cert /etc/openvpn/servidor.crt

key /etc/openvpn/servidor.key

dh /etc/openvpn/dh1024.pem

server 10.10.3.0 255.255.255.0

push "dhcp-option DNS 10.10.1.1"

push "dhcp-option DNS 8.8.4.4"

push "redirect-gateway def1"

client-to-client

max-clients 15

#Compressao de dados

comp-lzo

user nobody

group nogroup

#Mantem a interface tun carregada quando a vpn e reiniciada

persist-tun

#Mantem a chave carregada quando a vpn e reiniciada

persist-key

#Caso o IP mude, o tunel continua estabelecido

float

push "explicit-exit-notify 3"

duplicate-cn

push "ping 10"

push "ping-restart 120"

client-config-dir config_clientes

verb 3

log /var/log/openvpn.log

sndbuf 393216

rcvbuf 393216

push "sndbuf 393216"

push "rcvbuf 393216"

tun-mtu 1500

mssfix 1360


# Configuracao IPtables para acesso a internet e acesso ao srv de arquivos

iptables -t filter -I INPUT -p udp --dport 1194 -j ACCEPT

iptables -t nat -I POSTROUTING -s 10.10.3.0/24 -o eth0 -j MASQUERADE

iptables -t nat -s 10.10.3.0/24 -I POSTROUTING -o eth0 -j MASQUERADE

iptables -I INPUT -s 10.10.3.0/24 -d 10.10.0.196 -j ACCEPT

iptables -I FORWARD -s 10.10.3.0/24 -d 10.10.0.196 -j ACCEPT

iptables -I INPUT -s 10.10.0.196 -d 10.10.3.0/24 -j ACCEPT

iptables -I FORWARD -s 10.10.0.196 -d 10.10.3.0/24 -j ACCEPT

# Permitir acesso ao samba dentro do global em 10.10.0.196

[global]

        hosts allow = 10.10.150.0/23, 10.10.160.0/23, 10.10.0.0/24, 10.0.252.0/23, 10.0.0.0/23, 10.10.1.0/24, 10.10.4.0/24, 10.10.3.0/24
        

# Configuracao do certificado dentro de C:\Program Files\OpenVPN\config

# Deve conter ca.crt, vai.key,vai.cert, vai.csr e o arquivo vai.ovpn com a configuracao abaixo

client

dev tun

proto udp

remote XXX.XXX.XXX.XXX

port 1194

comp-lzo

keepalive 10 120

tls-client

persist-tun

persist-key

ca ca.crt

cert vai.crt

key vai.key

verb 3








