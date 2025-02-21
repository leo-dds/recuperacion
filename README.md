
# recuperacion
1. Explica métodos para 'abrir' unha consola/shell a un contenedor en execución.
   docker exec -it nome_contenedor sh
   docker exec -it nome_contenedor bash
   
2. No contenedor anterior (en execución), qué opciones tes que ter usado ó arrincalo para
poder interactuar coas súas entradas e salidas
   Debo de utilizar -it:
   i→Manten a entrada estandar aberta
   t→ asigna unha terminal
3. Cómo sería un ficheiro docker-compose para que dous contenedores se comuniquen
entre si nunha rede só deles?
  version: '3.8'
services:
  app:
    image: nginx
    networks:
      - rede_privada

  db:
    image: mysql
    networks:
      - rede_privada

networks:
  rede_privada:
    driver: bridge
    
4. Qué tes que engadir ó ficheiro anterior para que un contenedor teña unha IP fixa?
   services:
  meu_contenedor:
    image: nginx
    networks:
      rede_privada:
        ipv4_address: 172.28.5.10

networks:
  rede_privada:
    ipam:
      config:
        - subnet: 172.28.5.0/24

5. Qué comando de consola podo usar para sabe-las ips dos contenedores anteriores?
   Ver todas las ip: docker inspect -f '{{.Name}} - {{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' $(docker ps -aq)

Inspeccionar una red especifica: docker network inspect rede_privada

6. Cál é a funcionalidade do apartado "ports" en docker compose?
   Sirve para que os contenedores se poidan comunicar co esterior
   
7. Para qué serve o rexistro CNAME? Pon un exemplo
   Serve como alias para outro nome de dominio
   
8. Cómo podo facer para que a configuración dun contenedor DNS non se borre se creo
outro contenedor?
Usando volumenes no documento .yml para que poidan copiar os archivos alojados no host que conteñen as configuracions necesarias.

9. Engade unha zoa tendaelectronica.int no teu docker DNS que teña:
- www á IP 172.16.0.1
- owncloud sexa un CNAME de www
- un rexistro de texto có contido "1234ASDF"
Comproba que todo funciona có comando "dig"
Mostra nos logs que o servicio funciona ben usando a saída da terminal ó levantar o
compose ou có comando "docker logs [nomeContenedorOuID]"
(o apartado 9 realízase na máquina virtual)
zonas/db.tendaelectronica.int
$TTL 1D
@   IN  SOA  ns.tendaelectronica.int. admin.tendaelectronica.int. (
    2024010101 ; Serial
    8H         ; Refresh
    2H         ; Retry
    4W         ; Expire
    1D         ; Minimum TTL
)

@        IN  NS   ns.tendaelectronica.int.
ns       IN  A    172.16.0.1
www      IN  A    172.16.0.1
owncloud IN  CNAME www

--Comprobacion--
dig @172.28.5.1 www.tendaelectronica.int
dig @172.28.5.1 owncloud.tendaelectronica.int 
dig @172.28.5.1 tendaelectronica.int TXT 

--Ver los logs--
docker logs asir_bind9 
