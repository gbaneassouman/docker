# TP-3 : Gérez vos réseaux Docker
- Créez un réseau docker de type bridge
- Créez deux conteneurs ubuntu dans le réseau créé précédemment
- Installer la commande ping et tentez de joinfre les conteneurs entre eux avec leur ip et leur nom

## TAF
**- Création du réseau**
```
docker network create --driver=bridge --subnet=192.168.10.0/24 ping-network
```
**- Création du 1er conteneur**

```
docker run -itd --name ubuntu1 --network ping-network ubuntu /bin/bash
```

**- Création du 2è conteneur**

```
docker run -itd --name ubuntu2 --network ping-network ubuntu /bin/bash
```

**- Se connecter au 1er conteneur et ping le 2è conteneur**

```
docker exec -it ubuntu1 /bin/bash
```
**Installation ping**

```
root@01616871232e:/# apt-get update && apt-get install iputils-ping && apt-get install net-tools
Get:1 http://security.ubuntu.com/ubuntu noble-security InRelease [89.7 kB]
Get:2 http://archive.ubuntu.com/ubuntu noble InRelease [256 kB]           
Get:3 http://security.ubuntu.com/ubuntu noble-security/main amd64 Packages [24.8 kB]
Get:4 http://security.ubuntu.com/ubuntu noble-security/universe amd64 Packages [9153 B]
Get:5 http://archive.ubuntu.com/ubuntu noble-updates InRelease [89.7 kB]
Get:6 http://archive.ubuntu.com/ubuntu noble-backports InRelease [90.8 kB]
Get:7 http://archive.ubuntu.com/ubuntu noble/universe amd64 Packages [19.3 MB]
Get:8 http://archive.ubuntu.com/ubuntu noble/multiverse amd64 Packages [331 kB]                                                                                                               
Get:9 http://archive.ubuntu.com/ubuntu noble/main amd64 Packages [1808 kB]                                                                                                                    
Get:10 http://archive.ubuntu.com/ubuntu noble/restricted amd64 Packages [117 kB]                                                                                                              
Get:11 http://archive.ubuntu.com/ubuntu noble-updates/main amd64 Packages [25.1 kB]                                                                                                           
Get:12 http://archive.ubuntu.com/ubuntu noble-updates/universe amd64 Packages [14.5 kB]                                                                                                       
Fetched 22.2 MB in 33s (680 kB/s)                                                                                                                                                             
Reading package lists... Done
```

```
- ping ubuntu2
```

```
root@01616871232e:/# ping ubuntu2 -c3
PING ubuntu2 (192.168.10.3) 56(84) bytes of data.
64 bytes from ubuntu2.ping-network (192.168.10.3): icmp_seq=1 ttl=64 time=0.068 ms
64 bytes from ubuntu2.ping-network (192.168.10.3): icmp_seq=2 ttl=64 time=0.136 ms
64 bytes from ubuntu2.ping-network (192.168.10.3): icmp_seq=3 ttl=64 time=0.134 ms

--- ubuntu2 ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 2035ms
rtt min/avg/max/mdev = 0.068/0.112/0.136/0.031 ms
root@01616871232e:/# 
```

**- Se connecter au 2è conteneur et ping le 1er conteneur**

```
docker exec -it ubuntu2 /bin/bash
```

**Installation ping**

```
root@7b1df5d202f0:/# apt-get update && apt-get install iputils-ping && apt-get install net-tools
Get:1 http://archive.ubuntu.com/ubuntu noble InRelease [256 kB]
Get:2 http://security.ubuntu.com/ubuntu noble-security InRelease [89.7 kB]
Get:3 http://archive.ubuntu.com/ubuntu noble-updates InRelease [89.7 kB]                                                                                                                      
Get:4 http://security.ubuntu.com/ubuntu noble-security/universe amd64 Packages [9153 B]                                                                                                       
Get:5 http://security.ubuntu.com/ubuntu noble-security/main amd64 Packages [24.8 kB]                                                                                                          
Get:6 http://archive.ubuntu.com/ubuntu noble-backports InRelease [90.8 kB]                                                                                                                    
Get:7 http://archive.ubuntu.com/ubuntu noble/universe amd64 Packages [19.3 MB]                                                                                                                
Get:8 http://archive.ubuntu.com/ubuntu noble/multiverse amd64 Packages [331 kB]                                                                                                               
Get:9 http://archive.ubuntu.com/ubuntu noble/main amd64 Packages [1808 kB]                                                                                                                    
Get:10 http://archive.ubuntu.com/ubuntu noble/restricted amd64 Packages [117 kB]   
```

```
ping ubuntu1
```

```
root@7b1df5d202f0:/# ping ubuntu1 -c3
PING ubuntu1 (192.168.10.2) 56(84) bytes of data.
64 bytes from ubuntu1.ping-network (192.168.10.2): icmp_seq=1 ttl=64 time=0.082 ms
64 bytes from ubuntu1.ping-network (192.168.10.2): icmp_seq=2 ttl=64 time=0.128 ms
64 bytes from ubuntu1.ping-network (192.168.10.2): icmp_seq=3 ttl=64 time=0.101 ms

--- ubuntu1 ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 2056ms
rtt min/avg/max/mdev = 0.082/0.103/0.128/0.018 ms

```