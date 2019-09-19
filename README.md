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

