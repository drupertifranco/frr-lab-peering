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
## Acceder a los RT
**Comandos**
- docker exec -it clab-frr-bgp-lab-rtr1 vtysh
- docker exec -it clab-frr-bgp-lab-rtr2 vtysh
- docker exec -it clab-frr-bgp-lab-ifx vtysh
- docker exec -it clab-frr-bgp-lab-gd vtysh
## Configuraciones
- *Router 1*
  ```sh
    servidor:
    puerto: 8080
    nombre: "Ejemplo"
    ```
- *Routers 2*
  ```sh
    servidor:
    puerto: 8080
    nombre: "Ejemplo"
    ```