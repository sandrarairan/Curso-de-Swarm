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
https://github.com/platzi/swarm

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


