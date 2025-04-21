# frr-lab-peering
FRR is a fully featured, high performance, free software IP routing suite. It implements all standard routing protocols such as BGP, RIP, OSPF, IS-IS and more (see Feature Matrix), as well as many of their extensions. It can handle full Internet routing tables and is suitable for use on hardware ranging from cheap SBCs to commercial grade routers, and is actively used in production by hundreds of companies, universities, research labs and governments.
## Routers
- https://docs.frrouting.org/en/latest/installation.html
## Entorno Container Lab 
With the growing number of containerized Network Operating Systems grows the demand to easily run them in the user-defined, versatile lab topologies.

Unfortunately, container orchestration tools like docker-compose are not a good fit for that purpose, as they do not allow a user to easily create connections between the containers which define a topology.

Containerlab provides a CLI for orchestrating and managing container-based networking labs. It starts the containers, builds a virtual wiring between them to create lab topologies of users choice and manages labs lifecycle.

**link:** <https://containerlab.dev>
## iniciar el Lab
```linux
    sudo clab deploy frr-lab.clab.yml
```
### Codespace
---
<div align=center markdown>
<a href="https://codespaces.new/drupertifranco/frr-lab-peering?quickstart=1">
<img src="https://gitlab.com/rdodin/pics/-/wikis/uploads/d78a6f9f6869b3ac3c286928dd52fa08/run_in_codespaces-v1.svg?sanitize=true" style="width:50%"/></a>

**[Run](https://codespaces.new/drupertifranco/frr-lab-peering?quickstart=1) this lab in GitHub Codespaces for free**.  
[Learn more](https://containerlab.dev/manual/codespaces) about Containerlab for Codespaces.  
<small>Machine type: 2 vCPU · 8 GB RAM</small>
</div>
---
## Acceder a los RT
**Comandos**
- docker exec -it clab-frr-bgp-lab-rtr1 vtysh
- docker exec -it clab-frr-bgp-lab-rtr2 vtysh
- docker exec -it clab-frr-bgp-lab-ifx vtysh
- docker exec -it clab-frr-bgp-lab-gd vtysh
## Configuraciones
- *Router 1*
  ```sh
    rtr1# show run
    Building configuration...

    Current configuration:
    !
    frr version 8.4_git
    frr defaults traditional
    hostname rtr1
    log stdout
    no ipv6 forwarding
    !
    ip route 203.0.113.0/24 Null0
    !
    router bgp 263806
    bgp router-id 1.1.1.1
    no bgp ebgp-requires-policy
    neighbor 10.0.0.2 remote-as 263806
    neighbor 10.0.0.2 description rtr2
    neighbor 192.168.10.1 remote-as 8053
    neighbor 192.168.10.1 description IFX
    neighbor 192.168.20.1 remote-as 394684
    neighbor 192.168.20.1 description GD
    !
    address-family ipv4 unicast
      network 203.0.113.0/24
      neighbor 192.168.10.1 route-map PREF-IFX in
      neighbor 192.168.20.1 route-map PREF-GD in
      neighbor 192.168.20.1 route-map PREPEND-GD out
    exit-address-family
    exit
    !
    route-map PREF-IFX permit 10
    set local-preference 300
    exit
    !
    route-map PREF-GD permit 10
    set local-preference 100
    exit
    !
    route-map PREPEND-GD permit 10
    set as-path prepend 263806 263806 263806
    exit
    !
    end
    ```
- *Routers 2*
  ```sh
        rtr2# show run
    Building configuration...

    Current configuration:
    !
    frr version 8.4_git
    frr defaults traditional
    hostname rtr2
    no ip forwarding
    no ipv6 forwarding
    !
    ip route 203.0.113.0/24 Null0
    !
    interface eth1
    ip address 10.0.0.2/30
    exit
    !
    interface eth2
    ip address 192.168.30.2/30
    exit
    !
    router bgp 263806
    bgp router-id 2.2.2.2
    no bgp ebgp-requires-policy
    neighbor 10.0.0.1 remote-as 263806
    neighbor 10.0.0.1 description rtr1
    neighbor 192.168.30.1 remote-as 394684
    neighbor 192.168.30.1 description GD2
    !
    address-family ipv4 unicast
      network 203.0.113.0/24
      neighbor 192.168.30.1 route-map PREF-GD in
      neighbor 192.168.30.1 route-map PREPEND-GD out
    exit-address-family
    exit
    !
    route-map PREF-GD permit 10
    set local-preference 100
    exit
    !
    route-map PREPEND-GD permit 10
    set as-path prepend 263806 263806 263806
    exit
    !
    end
    ```
- *ifx*
  ```sh
    frr version 8.4
    frr defaults traditional
    hostname ifx
    log stdout
    no ip forwarding
    no ipv6 forwarding
    !
    interface eth1
      ip address 192.168.10.1/30     ! Enlace hacia rtr1
    !
    interface lo
      ip address 100.64.0.1/32       ! Loopback que simula ruta "Internet"
    !
    router bgp 8053
      bgp router-id 8.8.8.8
      no bgp ebgp-requires-policy
    
      ! Relación eBGP con rtr1
      neighbor 192.168.10.2 remote-as 263806
      neighbor 192.168.10.2 description rtr1
    
      ! Anuncio de la loopback simulada
      network 100.64.0.1/32
    !
    line vty
  ```
  - *gd*
  ```sh
    frr version 8.4
    frr defaults traditional
    hostname gd
    log stdout
    no ip forwarding
    no ipv6 forwarding
    !
    interface eth1
      ip address 192.168.20.1/30     ! Enlace a rtr1
    !
    interface eth2
      ip address 192.168.30.1/30     ! Enlace a rtr2
    !
    interface lo
      ip address 198.51.100.1/32     ! Loopback GD simulada
    !
    router bgp 394684
      bgp router-id 9.9.9.9
      no bgp ebgp-requires-policy
    
      ! Sesión eBGP con rtr1
      neighbor 192.168.20.2 remote-as 263806
      neighbor 192.168.20.2 description rtr1
    
      ! Sesión eBGP con rtr2
      neighbor 192.168.30.2 remote-as 263806
      neighbor 192.168.30.2 description rtr2
    
      ! Anuncio de la red loopback GD
      network 198.51.100.1/32
    !
    line vty
  ```





  
