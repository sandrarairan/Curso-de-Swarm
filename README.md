# Curso-de-Swarm
Curso de Swarm - Curso platzi
# TABLA DE CONTENIDO
- [Conceptos básicos](#Conceptos-básicos)
- [Primeros pasos](#Primeros-pasos)
- [Administrando Servicios](#Administrando-Servicios)
- [Swarm avanzado](#Swarm-avanzado)
- [Swarm productivo](#Swarm-productivo)
<!-- toc -->
## Conceptos básicos
# El problema de la escala: qué pasa cuando una computadora sóla no alcanza
La escalabilidad es el poder aumentar la capacidad de potencia de computo para poder servir a más usuarios o a procesos más pesados a medida que la demanda avanza.

A la hora de hablar de escalabilidad encontramos dos tipos de soluciones, escalabilidad vertical, que consiste en adquirir un mejor hardware que soporte mi solución o una escalabilidad horizontal, en la cual varias máquinas están corriendo el mismo software y por lo tanto es la solución más utilizada en los últimos tiempos.

La disponibilidad es la capacidad de una aplicación o un servicio de poder estar siempre disponible (24 horas del día), aún cuando suceda un improvisto.

Es mayor la disponibilidad cuando se realiza escalabilidad horizontal

Swarm no ofrece la solución a estos problemas.



# Arquitectura de Docker Swarm
La arquitectura de Swarm tiene un esquema de dos tipos de servidores involucrados: los managers y los workers.

Los managers son encargados de administrar la comunicación entre los contenedores para que sea una unidad homogénea.

Los workers son nodos donde se van a ejecutar contenedores, funciona como un núcleo, los contenedores estarán corriendo en los workers.

**Todos deben tener Docker Daemon (idealmente la misma versión) y deben ser visibles entre sí. **
# Preparando tus aplicaciones para Docker Swarm: los 12 factores
**¿ Está tu aplicación preparada para Docker Swarm ? **
Para saberlo, necesitas comprobarlo con los 12 factores

Codebase : el código debe estar en un repositorio
Dependencies : deben estar declaradas en un archivo de formato versionable, suele ser un archivo de código
Configuration : debe formar parte de la aplicación cuando esté corriendo, puede ser dentro de un archivo
Backing services : debe estar conectada a tu aplicación sin que esté dentro, se debe tratar como algo externo
Build, release, run : deben estar separadas entre sí.
Processes : todos los procesos los puede hacer como una unidad atómica
Port binding : tu aplicación debe poder exponerse a sí misma sin necesidad de algo intermediario
Concurrency : que pueda correr con múltiples instancias en paralelo
Disposabilty : debe estar diseñada para que sea fácilmente destruible
Dev/Prod parity : lograr que tu aplicación sea lo más parecido a lo que estará en producción
Logs : todos los logs deben tratarse como flujos de bytes
Admin processes : la aplicación tiene que poder ser ejecutable como procesos independientes de la aplicación

https://12factor.net/

## Primeros pasos
Instalación de Docker
# Tu primer Docker Swarm
```
// Iniciar docker swarm

docker swarm init

// Obtener token para unir manager

docker swarm join-token manager

// Ver los nodos que tenemos

docker node ls

// Ver información del nodo

docker node inspect  -pretty self

// Salir del modo swarm
docker swarm leave
//si un worker node se va, dará error, podemos forzarlo.
docker swarm leave --force 
Docker info
```

https://docs.docker.com/engine/swarm/how-swarm-mode-works/pki/

# Fundamentos de Docker Swarm: servicios
// Crear Servicio con el nombre pinger, realizando un ping a google.com
docker service create --name pinger alpine ping www.google.com
// lista los servicios
docker service ls
# Entendiendo el ciclo de vida de un servicio
Desde el Cliente , ‘docker service create’ le envía al Nodo Manager el servicio: se crea, se verifican cuántas tareas tendrá, se le otorga una IP virtual y asigna tareas a nodos; esta información es recibida por el Nodo Worker, quien prepara la tarea y luego ejecuta los contenedores.
https://docs.docker.com/engine/swarm/how-swarm-mode-works/services/

// Ver estado de un servicio
docker service ps nombreServicio

// Ver información de un servicio
docker service inspect nombreServicio

// Ver logs
docker service logs -f nombreServicio

// Eliminar servicio
docker service rm nombreServicio
https://docs.docker.com/engine/swarm/how-swarm-mode-works/services/

# Un playground de docker swarm gratuito: play-with-docker
¡Play with docker es una herramienta de otro mundo! Te permitirá colaborar entre distintos usuarios en una mismas sesión de docker, y lo mejor, ¡puedes incluir tu propia terminal!
https://labs.play-with-docker.com/

1. Ingresa a play. docker
2. + ADD NEW INSTANCE
3. Se copia el ssh que paarece en play-docker y o pega en su terminal

# Docker Swarm multinodo
// iniciar docker swarm
docker swarm init
// iniciar docker swarm en caso de tener mas de una interfaz de red
docker swarm init --advertise-addr [“ip de la interfaz donde va a escuchar peticiones para unirse al swarm”]
- Se debe crear 2 nodos con  + ADD NEW INSTANCE
- Para crear worker hay que pegar o add a worker to this swarm, run the following command:

    docker swarm join --token 
    en el node2 y node 3 
// ver nodos del swarm *solo se puede visualizar desde un docker swarm manager, esdecir node 1
// Todo lo relativo al estado del swarm, a la administracion del swarm y todo lo que tiene que ver con el swarm en si lo van a manejar exclusivamente los managers
docker node ls
- manager se corre
docker service create --name pinger alpine ping www.google.com
docker service ls

## Administrando Servicios
# Administrando servicios en escala
Comandos de la clase, con el ejemplo del servicio pinger

- Cambia el numero de tareas
docker service scale pinger=5

- Ver las tareas del servicio
docker service ps pinger

- Ver los logs del servicio
docker service logs -f pinger

#-Ver la configuración del servicio
docker service inspect pinger

- actualizar alguna configuración del servicio
docker service update --args "ping www.amazon.com" pinger

- realiza rollback o cambia al spec anterior 
docker service rollback pinger

# Controlando el despliegue de servicios
Comandos usados en la clase con el servicio ejemplo pinger

-  Actualizar las replicas de un servicio
docker service update --replicas=20 pinger

-  Actualizar paralelismo y orden dela configuracióndeupdateen el servicio pinger
docker service update --update-parallelism 4 --update-order start-first pinger

 - Actualizar accion en fallo y radio maximo de falla dela configuracióndeupdateen el servicio pinger
docker service update --update-failure-action rollback --update-max-failure-ratio 0.5 pinger

 - Actualizar paralelismo dela configuracion de rollback en el servicio de pinger
docker service update --rollback-parallelism 0 pinger

# Exponiendo aplicaciones al mundo exterior


 swarm git:(master) cd hostname
➜ hostname git:(master) docker build -t usuariodeDocker/swarm-hostname .
➜ hostname git:(master) docker run --rm -p 3000:3000 s/swarm-hostname
Server listening on port 3000!

- ➜ hostname git:(master) docker push usuariodeDocker/swarm-hostname
- se ingresa a labs.play-with-docker se da click en la llave 3 MANAGER AND 2 WORKRES
- Les dejo el comando para lanzar la aplicación
- Manager docker node ls

docker service create -d --name app --publish 3000:3000 --replicas=3 usuariodeDocker/swarm-hostname

- docker service ps app

## Swarm avanzado
# El Routing Mesh
Routing Mesh nos ayuda a que, teniendo un servicio escalado en swarm que tiene mas nodos que servicios y esos servicios están expuestos en un puerto; cuando hacemos un request a un servicio en ese puerto de alguna manera la petición llega y no se pierden en algún nodo que no puede contenerlo en ese puerto o en un contenedor.

Routing Mesh ayuda a llevar la petición y que esta no se pierda si la cantidad de los contenedores es diferente a la cantidad de nodos.
https://docs.docker.com/engine/swarm/ingress/

manager:
- docker service scale app=6
- docker service ps app
- vamos worker2 
docker ps
docker network ls
 (la red ingress)
 
 # Restricciones de despliegue
  (donde:
–constraint-add node.role => indica el tipo de nodo donde quiero reubicar


 docker service create -d --name viz -p 8080:8080 --constraint=node.role==manager --mount=type=bind,src=/var/run/docker.sock,dst=/var/run/docker.sock dockersamples/visualizer
 
 - docker service ps viz
 
se va aa https://labs.play-with-docker.com y en el port 8080 

- Comando para reubicar los servicios para que sean ejecutados solo en los workers

docker service update --constraint-add node.role==worker --update-parallelism=0 app
donde:
–constraint-add node.role => indica el tipo de nodo donde quiero reubicar
–update-parallelism=0 => indica que se harán todos en simultaneo

# Disponibilidad de nodos
En el visualizer podemos ver que todas las tareas siguen corriendo en el "“worker 1"”, esto sucede porque el planificador de Docker Swarm no va a replanificar o redistribuir la carga de un servicio o de contenedores a menos que tenga que hacerlo; para solucionar esto, debemos forzar un redeployment o una actualización que se logra cambiando el valor de una variable que no sirva para nada.

docker service update -d --env-add UNA_VARIABLE=de-entorno --update-parallelism=0 app

docker service ps app

-- 
docker node update --availability drain worker2

# Networking y service discovery
vamos a ver como es el networking al momento de trabajar con swarm, vamos a poder inspeccionar que tenemos en la red. Todo esto, utilizando el repositorio que encuentras en los enlaces del curso

https://docs.docker.com/network/overlay/

- docker create --driver overlay app-net
docker network ls
docker network inspect app-net

- Vamos a ir a la terminal y la carpeta networing
➜ networking git:(master) docker build -t s/swarm-networing
➜ networking git:(master) docker push s/swarm-networking

- volvemos a swarm manager
docker service create -d --name db --network app-net mongo

- docker service ps db
- docker service create -d --name app --network app-net -p 3000:3000 s/swarm-networking
- docker service ls
- docker service update --env--add MONGO_URL=mongodb://db/test app

- - Vamos aworker 1
docker ps
docker exec -it "id conainier" bash
**PARA UNA APLICACION PRODUCTIVA NO USAR SWARM PARA BD

# Docker Swarm stacks
Con Docker Swarm Stacks (un archivo) se puede controlar cómo se van a despliegan los servicios utilizando los stacks. Siempre es bueno utilizar un archivo porque este puede ser versionado (Git) y se tiene un archivo que va a describir la arquitectura de la aplicación.

docker service rm app db
docker network rm app-net
- vamos manager
vim stackfile.yml
```
version: "3"

services:
  app:
    image: gvilarino/swarm-networking
    environment:
      MONGO_URL: "mongodb://db:27017/test"
    depends_on:
      - db
    ports:
      - "3000:3000"
    deploy:
        placement:
          contraints:[node.role==worker]

  db:
    image: mongo
  ```
  - docker stack deploy --compose-file stackfile.yml app
  CREA CREATING NETWORK app_default
  creating service app_app
  creating service app_db
  - docker service ls
  - docker stack ls
  - docker stack ps app
  - docker stack services app
  
  - En el archivo stackfile.yml
     deploy:
        placement:
          contraints:[node.role==worker]
          
   - docker stack deploy --compose-file stackfile.yml app
   - docker stack rm app
   
 # Reverse proxy: muchas aplicaciones, un sólo dominio
Reverse proxy es una técnica, es un servicio que está escuchando y que toma una decisión con la petición que esta entrando y hace un proxy hacia uno de los servicios que tiene que atender esa petición.

Existe una herramienta llamada traefik, el cual es un intermediario entre las peticiones que vienen del internet a nuestra infraestructura.

https://traefik.io/
- vamos swarm Manager:
docker network create --driver overlay proxy-net

docker service create --name proxy --constraint=node==manager -p 80:80 -p 9000:8080 --mount type=bind,src=/var/run/docker.sock,dst=/var/run/docker.sock --network proxy-net traefik --docker --docker.swarm.Mode --dokcer.domain=guido.com --docker.watch --api

- docker service create --name app1 --network proxy-net --label traefic.prt=3000 s/swarm-hostname
- curl -H "Host: app1.guido.com" http://localhost

- docker service create --name app2 --network proxy-net --label traefic.prt=3000 s/swarm-hostname
- curl -H "Host: app2.guido.com" http://localhost

- docker service update --image  s/swarm-networking app2


## Swarm productivo
# Arquitectura de un swarm productivo
https://www.freecodecamp.org/news/in-search-of-an-understandable-consensus-algorithm-a-summary-4bc294c97e0d/

https://success.docker.com/article/using-contraints-and-labels-to-control-the-placement-of-containers
# Administración remota de swarm productivo
Las herramientas de administración en Docker Swarm deben persistir en disco (su estado interno, la administración) y la mejor manera de almacenar cosas en Docker son los volúmenes.

En esta clase aprenderemos una forma fácil simple e intituiva de administrar nuestro docker swarm de manera remota. No es la única que existe, así que te invitamos a probar y a dejarnos en los comentarios otras formas que encuentres.

https://www.portainer.io/

manager:
- docker volume create portainer_data
- docker service create --name portainer -p 9000:9000 --constraint node.role==manager
–mount type=bind,src=/var/run/docker.sock,dst=/var/run/docker.sock
–mount type=volume,src=portainer_data,dst=/data portainer/portainer
-H unix:///var/run/docker.sock



# Consideraciones adicionales para un swarm produtivo
https://docs.docker.com/config/containers/logging/configure/




