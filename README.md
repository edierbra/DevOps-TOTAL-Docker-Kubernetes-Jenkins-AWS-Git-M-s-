# DevOps TOTAL, Docker, Kubernetes, Jenkins, AWS, Git y More

[DevOps TOTAL Docker, Kubernetes, Jenkins, AWS, Git y Más!](https://www.udemy.com/course/devops-total/?couponCode=KEEPLEARNING)

## Seccion 3: Requisitos previos (Informacion y Configuracion)

### Herramientas de Windows

Instalar chocolatey desde las instrucciones dadas en este [Link](https://chocolatey.org/install).

Ejecutar todos los comandos a continuación en Powershell (Abrir Powershell como Administrador)

```cosole
choco install virtualbox

choco install vagrant

choco install git

choco install jdk8

choco install maven

choco install awscli

choco install intellijidea-community

choco install sublimetext3.app
```

### Herramientas para Ubuntu

Install Virtualbox

```cosole
sudo apt update
sudo apt install virtualbox
```

Install Vagrant

```cosole
curl -O https://releases.hashicorp.com/vagrant/2.2.9/vagrant_2.2.9_x86_64.deb
sudo apt install ./vagrant_2.2.9_x86_64.deb
```

Install Git

```cosole
apt install git
```

Install jdk8

```cosole
sudo apt-get install openjdk-8-jdk
```

Install Maven

```cosole
sudo apt-get install maven
```

Install awscli

```cosole
sudo apt-get install awscli
```

Install Intellij community

```cosole
sudo snap install intellij-idea-community --classic
```

Install Sublime Text

```cosole
sudo apt update
sudo apt install dirmngr gnupg apt-transport-https ca-certificates software-properties-common
curl -fsSL https://download.sublimetext.com/sublimehq-pub.gpg | sudo apt-key add -
sudo add-apt-repository "deb https://download.sublimetext.com/ apt/stable/"
sudo apt install sublime-text
```

## Seccion 4: Maquina virtual, Vagrant y Servidores

### Virtualizacion

Permite crear multiple computadoras logicas en un mismo disposi6tivo fisico.

- Host OS: sistema operativo de la maquina fisica.
- Gueste OS: sistema operativo de la maquina virtual.
- VM: maquina virtual
- Snapshot: BackUp d ela maquina virtual
- Hypervisor: habilita la virtualizacion y permite la creacion de VM. Tipo 1 o Bare metal que correo como OS y solo se usa para produccion. Tipo 2 que corre como software, solo es para aprendizaje y testeo.

### Configuracion de VM's con Vagrant (Automatizacion)

- Encontrar nombre para Box en la nube vagrant (vagrant cloud). Por ejemplo, "geerlingguy/centos7".
- Crear un directorio de proyectos (una carpeta)
- Crear Vagranfile en el directorio de proyectos `vagrant init geerlingguy/centos7`
- Comandos de Vagrant como `vagrant up`
- Iniciar sesion con `vagrant ssh` para las VMs de Linux

Para apagar la VM `vagrant halt`
Para borrar la VM `vagrant destroy`
para reiniciar la VM `vagrant reload`

**Configuracion de red:**
Red privada `config.vm.network "private_network", ip: "192.168.33.10"`
Red publica o nat `config.vm.network "public_network"`

#### Carpeta compartida o de sincronizacion

Tener encvuenta que todo lo que tengamos en el directorio de trabajo en el host, se vera en lamcarpeta *vagrant* en el guest.

Para crear la carpeta de sincronizacion, añadir `config.vm.synced_folder "F:\\vagrant-vms\\shellsscripts", "/vagrant_data"`en el vagrantfile, donde se especifica la ruta host y la ruta en el guest.

Para acceder a la carpeta de sincronizacion, ejecutar `cd /vagrant_data` en el guest, se observara todo lo que este en *F:\\vagrant-vms\\shellsscripts*.

#### Aprovicionamiento en Vagrant

Colocar los comandos que queramos dentro de los dos SHELL y luego ejecutar `vagrant reload --provision`

```cosole
config.vm.provision "shell", inline: <<-SHELL
     yum install httpd wget unzip -y
     mkdir /opt/devopsdir
     free -m
     uptime
   SHELL
```

#### Administracion de servidores

Instalar los paquetes HTTPD, wget y unzip

##### HTTPD

```cosole
yum install httpd wget unzip
```

Habilitar httpd. Una vez habilitado se podra acceder a la pagina por defecto en el navegador mediante la ip privada.

```cosole
systemctl start httpd
systemctl enable httpd
```

Si queremos crear una nueva pagina web debemos colocar todos los archivos en */var/www/html*. Para esto podemos usar los comandos wget, unzip si tenemos un template en internet para descargar, el link de descarga se optiene en la pestaña de red de la herramientas de desarrollador.

##### WordPress

Instalar dependencias y wordpress siguienedo el siguiente [Link](https://ubuntu.com/tutorials/install-and-configure-wordpress#1-overview)

#### Varias VM con un solo Vagrantfile

Configuracion de ejemplo para dos VMs en un solo Vagrantfile, parea mayor informacion ver la [Documentacion de Vagrant](https://developer.hashicorp.com/vagrant/docs/multi-machine)

```cosole
Vagrant.configure("2") do |config|
    config.vm.provision "shell", inline: "echo Hello"
  
    config.vm.define "web01" do |web01|
        web01.vm.box = "ubuntu/bionic64"
        web01.vm.network "private_network", ip: "192.168.33.50"
        web01.vm.provider "virtualbox" do |vb|
          vb.memory = "1024"
          vb.cpus = 2
        end

        web01.vm.provision "shell", inline: <<-SHELL
            apt update
            apt install apache2 wget unzip -y
            systemctl start apache2
            systemctl enable apache2
            cd /tmp/
            wget https://www.tooplate.com/zip-templates/2108_dashboard.zip
            unzip -o 2108_dashboard.zip
            cp -r 2108_dashboard/* /var/www/html/
            systemctl restart apache2
        SHELL
    end
  
    config.vm.define "db01" do |db01|
        db01.vm.box = "centos/7"
        db01.vm.network "private_network", ip: "192.168.33.51"
        db01.vm.provider "virtualbox" do |vb|
          vb.memory = "1024"
          vb.cpus = 2
        end

        db01.vm.provision "shell", inline: <<-SHELL
            yum install mariadb-server -y
            systemctl start mariadb
            systemctl enable mariadb

            sudo mysql -u root -e 'CREATE DATABASE wordpress;'
            sudo mysql -u root -e 'CREATE USER wordpress@localhost IDENTIFIED BY "root";'
            sudo mysql -u root -e 'GRANT SELECT,INSERT,UPDATE,DELETE,CREATE,DROP,ALTER ON wordpress.* TO wordpress@localhost;'
            sudo mysql -u root -e 'FLUSH PRIVILEGES;'
        SHELL
    end
  end
```

para ejecutar comandos a una VM debemos especificar el nombre de la VM, por ejemplo:

```cosole
vagrant destroy web01
vagrant ssh web01
vagarn reload web01
```

## Seccion 5: Redes

- [Comandos windows](https://openwebinars.net/blog/20-comandos-de-red-mas-importantes-en-windows/)
- [Comandos ubuntu](https://aprenderlinux.org/los-20-principales-comandos-de-red-de-linux/)

## Seccion 6: Git & GitHub Principios basicos

`git init` iniciar git.

`git status` estado de git

`git add file1`, `git add .` o `git add -A` agrega archivos al area de preparacion.

`git commit file1 -m "Descripcion"` o `git commit -m "Descripcion"` agrega cambios al repositorio local.

`git log` muestra historial de commits.

`git log --all` muestra todo el historial de commits.

`git log --graph` muestra todo el historial de commits graficamente.

`git log --oneline` muestra historial de commits en una linea.

`git log --merged` ver ramas fusionadas.

`git log --no-merged` ver ramas  no fusionadas.

`git remote add origin url_https` agrega repositorio remoto por https.

`git remote add origin url_ssh` agrega repositorio remoto por ssh.

`git diff` muestra cambios realizados

### Ramificacion y fusion

`git branch nombre_rama` Crear una rama.

`git switch nombre_rama` o `git checkout nombre_rama` cambiar de rama.

`git merge nombre_rama` fusiona los cambios de la rama *nombre_rama* a la rama actual.

`git branch -d nombre_rama` elimina una rama.

`git branch nombre_rama2 nombre_rama1` crea una nueva rama *nombre_rama2* a partir de *nombre_rama1* pero el head se queda en la rama donde se hizo el comando.

### Clonar, Fusionar y Sincronizar

`git clone url_repositorio` clona un repositorio remoto.

`git branch -r` ver ramas del repositorio remoto.

`git fetch origin` sincroniza el repositorio local con el remoto.

`git pull origin nombre_rama_remota` trae los datros de la rama *nombre_rama_remota* a la rama local.

`git branch -M nuevo_nombre` renombra la rama *master* a *main*.

### Explorando el Historial y los Commits

**Git Graph** es una extencion de VS para ver las ramas visualmente

`git show id_commit` muestra los detalles de un commit.

`git show id_commit:nombre_archivo` muestra los detalles de un archivo en un determinado commit.

`git log --oneline --all -stat` muestra los archivos modificados.

`git ls-tree id_commit` muestra los nombre de los archivos en un determinado commit.

`rm file1` y `git add .` es igual a `git rm file1`

### Fork

- Hacer Fork repositorio, lo que crea un nuevpo repositorio en tu cuenta
- Hacer clone al repositorio creado en tu cuenta
- Hacer modificaciones
- Hacer push al repositorio en tu cuenta
- Hacer pull request
- Esperar que el propietario del repositorio original verifique los cambios y acepte el pull request

Si el repositorio  original esta por delante de tu repositorio en tu cuante:

- Clickear la opcion *Sunc fork* para fucionar los cambios. Para tener los cambios localmente hacer `git pull`.

- `git remote add upstream url_repositorio_principal`, `git fetch upstream`, nos movemos a la rama local deseada `git switch main`, hacemos merge `git merge upstream/main`, actualizamos nuestro repositorio forkeado `git push origin`.

### Gitignore

Crear archivo *.gitignore* y colocar lo que quremos ignorar en git:

- log\
- log\file.txt
- *.xslx
- file.xslx

### Issues

Para mandar solicitudes de modificacion a laos colaboradores del proyecto.

### Integraciones

Existe integraciones con Discord y Jenkins.

### GitFlow

![GitFlow](/git-github/imgs/gitflow.png)

## Secsion 10 : Docker - Teoria General e Introduccion

No se puede ejecutar un Contenedor si este tiene un Kernel diferente al Host.

- DockerHub: repositorio de imagenes
- imagen: plantilla para crear contenedores

### Instalar ubuntu y acceder con ssh

```console
sudo apt-get install ssh
sudo apt install nmap
sudo apt install net-tools
ifconfig
sudo nmap <ip del pc>
```

Si no funciona ssh instalar `openssh-server`.

### conectarse mediante ssh

```console
ssh -p 22 user@ip
```

### Instalacion de docker en ubuntu

Seguir la documentacion de docker para instalar ubuntu siguiendo el siguiente [Link](https://docs.docker.com/engine/install/ubuntu/)

```console
# Desistalar versiones de Docker existentes:
for pkg in docker.io docker-doc docker-compose docker-compose-v2 podman-docker containerd runc; do sudo apt-get remove $pkg; done

# Add Docker's official GPG key:
sudo apt-get update
sudo apt-get install ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc

# Add the repository to Apt sources:
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update

# Install the Docker packages:
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

# Verify that the Docker Engine installation:
sudo docker run hello-world
```

## Secsion 11: Docker en Windows

### Instalacion de docker en windows

Seguir el suguiente tutorial de [Docker Docs](https://docs.docker.com/desktop)

## Seccion 12: Docker: Comandos

- `docker run <nombre de la imagen>` ejecuta un contenedor ya sea localmente o de docker hub.
- `docker ps` lista los contenedores en funcionamiento.
- `docker ps -a` lista los contenedores en y no funcionamiento.
- `docker stop <id o nombre del contendor>` Detiene el contenedor.
- `docker rm <id o nombre del contendor>` elimina permanentemente un contenedor detenido o en salida. Se pueden eliminar varios si colocamos varios en la lista.
- `docker images` lista todas la simagenes disponibles.
- `docker pull <nombre de la imagen>` descarga una imagen de docker hub para luego poderla usar.
- `docker rmi <nombre de la imagen>` elimina una imagen, pero esta no debe estar utilizada por un contenedor.
- `docker exec <nombre del contenedor> <comando>` permite ejecutar un comando en el contenedor.
- `docker attach <id o nombre de docker>` adjunta de vuerlta al contenedor en ejecucion.

## Seccion 13: Docker: Comando Run

### Docker run

- `docker run <nombre de la imagen>` ejecuta un contenedor ya sea localmente o de docker hub.
- `docker run <Nombre de la imagen>:<version>` ejecura una imagen con una version determinada, la version por defecto es *latest*.
- `docker run ubuntu sleep 5` elcontenedor se manteine en suspencion por 5 segundos y luego se cierra.
- `docker run CursoDockerr/appweb` ejecuta una imagen personalizada para una apk web, se ejecuta en primer plano o de forma adjunta y estara conectado a la consola o por fuera del contenedor docker pudiendo ver la salida.
- `docker run -d CursoDockerr/appweb` ejecuta en el backend una imagen personalizada para una apk web.
- Banderas despues del run:
  - `-i` bandera para correr un contenedor en modo interactivo.
  - `-t` bandera para conectarse a la terminal del contenedor.
  - `-d` bandera para correr un contenedor en el bacground.
- `docker run --CPUS=.5 --memory=100m ubuntu` limita el numero de cpus y memoria usados por el contenedor.

### Mapeo de puertos

- `docker run -p <puerto del host de docker>:<puerto del contenedor de docker> CursoJenkis0/appweb`, el usuario podra acceder a la palicacion en la URL `http://<ip del contenedor>:<puerto del host de docker>`. El `<puerto del host de docker>` debe estar libre.

### Datos en Docker

- `docker run -v <directorio del host>:<directorio del contenedor>` configura un volumen en un contenedor. Por ejemplo, `docker run -v /opt/datadir:/var/lib/mysql mysql` guarda el contendido  **/var/lib/mysql mysql** en el volumen exterior **/opt/datadir**.

- `docker inspect <nombre o id del contenedor>` permite inspeccionar un contenedor.

- `docker logs <id o nombre del contenedor>` permite ver los registros de un contenedor ejecutado en segundo plano.

## Seccion 14: Docker imagenes

### Crear una imagen

Pasos para crear una imagen de una App web de forma manual:

- Comenzar con un sistema operativo como Ubuntu
- Actualizar los repositorios de origen con el comando apt
- Instalar dependecias con el comando apt
- Instalar dependencias de python usando el comando pip
- Copiar el codigo fuente de mi aplicacion a una ubicacion como una carpeta apt
- ejecutar el servidor web usando el comando flask

Se crea un archivo de docker llamado **dockerfile** y escribimos las intruciones para configurar la app.

`docker build . -f Dockerfile -t CursoDockerr/my-custom-app` construye una image a partir del Dockerfile.

`docker tag ubuntu:platzi edierbra/ubuntu:platzi` cambia el tag a una imagen.

`docker push Cursodockerr/my-custom-app` Publica el contenedor en docker Hub. Tener encuenta que antes se debe iniciar sesion con `docker login`

Estructura del dockerfile:

- `FROM` define el sistema operativo del contenedor.
- `RUN` indica comandos en particular que se deben ejecutar en la imagen base.
- `COPY` copia los archivos locales al contenedor.
- `ENTRYPOINT` permite especificar un comando a ejecutar cuando la imagen se ejecute como un contenedor.

`docker history Cursodockerr/my-custom-app` para observar los detalles al construir una imagen con dockerfile.

Al construir la imagen se podran ver las capas, si hay un error docker no construye todo de nuevo, sino desde la capa que ocurrio el error.

## Seccion 15: Docker Compose y Registros

Ejemplo apk de votacion:

- `docker build . -t voting-app` construir la imagen de el frontend.
- `docker run -d --name redis redis` ejecutar un contendor de redis para la db.
- `docker run -d --name=db -e POSTGRES_PASSWORD=postgres -e POSTGRES_USER=postgres postgres:9.4` ejecutar el contenedor de postgress.
- `docker run -p 5000:80 --link redis:redis voting-app` construir el contenedor del frontend y enlazarlo con redis.

Ejemplo apk de resultados de la apk de votacion:

- `docker build . -t worker-app` construir la imagen worker-app
- `docker run --link redis:redis --link db:db worker-app` construir el contenedor worker-app y enlazarlo con redis y db.
- `docker build . -t result-app`construir la imagen del frontend de la apk de resultados.
- `docker run -p 5001:80 --link db:db result-app`ejecutar la imagen del frontend de resultados y enlazarlo con db.

### Docker Compose

Ejemplo simple:

```console
version: '3'

services:
  redis:
    image: redis

  db:
    image: postgres:9.4
    environment:
      POSTGRES_USER: "postgres"
      POSTGRES_PASSWORD: "postgres"

  vote:
    image: voting-app
    ports:
      - "5000:80"
    links:
      - redis

  worker:
    image: worker-app
    links:
      - redis
      - db

  result:
    image: result-app
    ports:
      - "5001:80"
    links:
      - db
```

Ejemplo completo:

```console
# version is now using "compose spec"
# v2 and v3 are now combined!
# docker-compose v1.27+ required

services:
  vote:
    build: ./vote
    # use python rather than gunicorn for local dev
    command: python app.py
    depends_on:
      redis:
        condition: service_healthy
    volumes:
     - ./vote:/app
    ports:
      - "5000:80"
    networks:
      - front-tier
      - back-tier

  result:
    build: ./result
    # use nodemon rather than node for local dev
    command: nodemon server.js
    depends_on:
      db:
        condition: service_healthy
    volumes:
      - ./result:/app
    ports:
      - "5001:80"
      - "5858:5858"
    networks:
      - front-tier
      - back-tier

  worker:
    build:
      context: ./worker
    depends_on:
      redis:
        condition: service_healthy
      db:
        condition: service_healthy
    networks:
      - back-tier

  redis:
    image: redis:5.0-alpine3.10
    volumes:
      - "./healthchecks:/healthchecks"
    healthcheck:
      test: /healthchecks/redis.sh
      interval: "5s"
    ports: ["6379"]
    networks:
      - back-tier

  db:
    image: postgres:9.4
    environment:
      POSTGRES_USER: "postgres"
      POSTGRES_PASSWORD: "postgres"
    volumes:
      - "db-data:/var/lib/postgresql/data"
      - "./healthchecks:/healthchecks"
    healthcheck:
      test: /healthchecks/postgres.sh
      interval: "5s"
    networks:
      - back-tier

volumes:
  db-data:

networks:
  front-tier:
  back-tier:
```

## Seccion 17: Servicio de almacenamiento

Estructura de almacenamiento:

- var/lib/docker
  - ausf
  - contenedores
  - imagenes
  - volumenes

Como las imagenes estan conformadas por capas, las capas se pueden reutilizar en otras imagenes. Los controladores de almacenamiento gestionan las capas, algunas de los controladores son: AUSF, ZF, BTRFS, DEVICE MAPPER, OVERLAY Y OVERLAY 2.

- `docker colume create date_volume` crea un volumen.
- `docker run -v data_volume:var/lib/mysql mysql` (Metodo anticuado) guardar los datos del contenedor en el volumen creado, y asi no perderlos cuando se elimine el contenedor. Si no se crea el volumen anteriormente, docker lo creara automaticamente. **data_volume** puede ser un montaje de union, es decir guardar los datos en otro directorio.
- `docker run / --mount type=bind, source=/data/mysql, target=/var/lib/mysql mysql`

## Seccion 18: Redes

Al instalar Docker  se crean 3 redes iniciales: BRIDGE, HOST, NONE

El servidor DNS de docker esta en la direcion 127.0.0.11 y auda a los contenedores acceder a los unos con los otros a partir de el nombre del contenedor, por lo que la direcion ip de cada contenedor puede cambiar. Por lo tanto, no se recomienda enlazar contenedores con su propia ip.

- `docker network ls` lista las redes creadas.
- `docker run ubuntu` simpre asocia al contenedor a la red  predeterminada **BRIDGE** (172.17.0.1).
- `docker run ubuntu --network=host` agrega al contenedo a la red host, Esto significa que el contenedor comparte la misma interfaz de red, la dirección IP y las puertas de enlace que el host. Es decir, utiliza directamente la interfaz de red del sistema host.
- `docker run ubuntu --network=host` los contenedores no estan conectados a ninguna red. estan sin acceso al exterior u otros contenedores. Es decir, estan aislados.
- `docker network create / driver bridge subnet 182.18.0.2 custom-network` crea una red llamada **custom-network**.
- `docker inspect <id o nombre del contenedor>` ver detalles del contenedor como la red a la cual esta asociado.

## Seccion 19: Orquestacion

Herramientas y guiines que pueden ayudar a alojar contenedores en un entorno de produccion. Permite equilibrio de cargas y crear instancias de acuerdo al numero de solicitudes.

- `docker service create --replicas=100 nodejs` crea 100 replicas de nodejs.

Existen varios orkestadores:

- Docker swarm:
  - Facil de configurar e iniciar.
  - No tiene funciones avanzadas de auto escalas.
  - Menor uso de recursos.
  - Pensado para los desarrolladores.
  - Seguridad desde el comienzo.
  - Mas facil de resolver cualquier problema.
- Mesos:
  - Dificil de configurar e iniciar.
  - Admite algunas opciones avanzadas.
- Kubernetes:
  - Muchos tipos de uso
  - Un poco dificil de configurar e iniciar
  - Muchas opciones para configurar el despliegue, soporte para proveedores.
  - compatible con todos los proveedores de servicios en la nube.
  - Cubre mas casos extremos que Swarm.

## Seccion 20: Docker Swarm

### Swarm

Se deben tener varios host con docker instalado y asignar a uno de ellos cono administrador swarm y los otros como nodos trabajadores.

Configuracion:

- `docker run my-web-server` para crear el administrador swarm.
- `docker service create --replicas=3 my-web-server` para crear las instancias de nodos trabajadores. **Se debe ejecutra en el host administrador swarm.**
- `docker swarm init` en el administrador swarm.
- `docker swarm join --token <token>` en los nodos trabajadores.

## Seccion 21: Kubernetes - Introduccion y Teoria

Utiliza los host de docker para alojar apps en forma de contenedores. Es utilizada para organizar la impementacion y administracion de miles de contenedores en un entorno en cluster.

### Conceptos basicos

- Nodos: Maquina fisica o virtual en la que un soffware de Kubernetes instala las herramientas. Es una maquina de trabajo donde se lanzan los contenedores.
- Cluster: grupo de nodos agrupados. Si un nodo falla se puede acceder a otro.
- Maestro: Nodo con los componentes del plano de kubernetes ya instalados.
  - Vigila los nodos del grupo y orquesta los contenedores de los nodos trabajadores.
  - Tiene el **servidor API**, **Scheduler o Programador**, **Controlador**, **Servicio Etcd** y **Core DNS**.
- Trabajadores o Minios: es donde se alojan los contenedores. Por ejemplo, contenedores Docker.
  - Para ejecutar estos contenedores es necesario que se instale el **runtime**, en este caso docker.
  - tienen el ajente **kubelet**, el cual interactua con el maestro para proporcionar informacion vital del nodo trabajador y llevar las acciones solicitradas por el maestro en los minions.

### Componentes basicos

- Servidor Api: frontend para kubernetes, todos se comunican con el para interactuar con el grupo de kubernetes.
- Etcd: Almacena los datos de forma distribuida (toda la informacion recopilada, valores clave). Administran el grupo e implementan los registros dentro del grupo para que no hayna conflictos entre los maestros.
- Kubelet: agente que se ejecuta en cada nodo del cluster. asegura que los contenedores esten ejecutandose correctamente en los nodos.
- Contenedor runtime: software subjacente para ejecutar contendores. Ejemplo, Docker.
- Controlador: cerebro detreas de la orquestacion. Responsable de notar y responder cuando los contenedore de nodos o puntos finales caen.
- Scheduler o Programador: distribuye en trabajo o contenedores en multiples nodos. busca contenedores recien creados y los asigna a los nodos.
- Core DNS: herramienta por defecto para controlar DNS.

### Conocimientos basicos

- Pod: es la unidad basica de implementacion o deployment en ingles. Uno o mas contenedores siendo ejecutados en conjunto en un mismo nodo. Tecnicamente se hace deployments de contenedores directamente en kubernetes, solo se hace deployments de pops y se suan cosas por env¿cima de estos, como controladores para controlar esos pods.
- Controlador: para crear o actualizar pods y otros objetos. Casi siempre creara un controlador que es un objeto para validar si lo que sucede dentro de kubernetes es lo que se le ha pedido que haga. Hay varios tipos:
  - Deployments, ReplicaSet: se usan casi siempre y controlan a los pods en los niveles mas bajos.
  - Se pueden hacer diferentes tipos de controladores, hasta de terceros: StatefulSet, DaemonSet, Job, CrobJob, etc.
- Servicio: punto final que se le da aun conjunto de pods. Cuando se usa un controlador de deployments para hacer un deployment en un conjunto de pods replica, se esta implementando un servicio. El servicion significa que le esta dando un punto final persistente en el cluster, para que todo lo demas pueda acceder a ese conjunto de pods en un nombre y puerto DNS especifico.
- Namespace: es un filtro de vista en la linea de comandos. Filtra las vistas cuando se usa la linea de comando **kubectl** y solo queremos ver ciertas cosas que nos interesan en ese preciso momento.

### Comandos basicos

- `kubectl` comando para implementar y administarr aplicaciones en un cluster de kubernetes, obtine informacion del cluster, el estado de otros nodos del cluster, etc.
- `kubectl run`crea un pod.
- `kubectl run ngnix --image nginx`crea un pod de ngnix llamado nginx.
- `kubectl create <pod / deploiment>` crea recurso via CLIO o YAML.
- `kubectk create deployment nginx --image ngnix`crea un deploymen a partir de la version v1.18
- `kubectl delete <pod / deployment> <name del recurso a eliminar>` Elimina un pod o deployment.
- `kubectl apply` crea y actualiza sobre todo via YAML.
- `kubectl version`ver version de kubectl.
- `kubectl.
- `kubectl cluster-info`informacion de los clusters.
- `kubectl get all`lista todos los recursos del cluster
- `kubectl get nodes` lista los nodos disponibles de un cluster.
- `kubectl get pods` lista los pods disponibles de un cluster.
- `kubectl get all -o wide` **-o wide** muestra informacion adicional.
- `kubectl run my-web-app --imag`ejecuta miles de instancias de nustra aplicacion en miles de nodos.
- `kubectl run hello-minikube` despliega una aplicacion en el cluster.
- `kubectl logs pod/<nombre del pod>` ver los logs de un pod.
- `kubectl describe pods` ver info de los pods (nombre, nodo creado, labels, estado y el los contenedores y eventos).

### Comandos iniciales

- `docker run my-web-server` crear el contenedor adminsitrador.
- `kubectl run --replicas=1000 my-web-server` crea 1000 instancias de **my-web-server**.
- `kubectl run --replicas=2000 my-web-server` lo escala a 2000.
- `kubectl rolling-update my-web-server --web-server2` actualiza las instancias de forma continua y de a una.
- `kubectl rolling-update my-web-server --roll back` si algo sale mal revierte la actualizacion de las instancias.

### Pods

- Los contenedores se encansulan en pods.
- Es una instancia unica en una aplicacion.
- Es el objeto mas pequeño que puedes crear en kubernetes.
- Usualmente un pod tiene un contenedor.
- No esta restringido a tener un contenedor por pod.
- Un pod puede tener multiples contenedores, usualmente son contenedores de diferente tipo.
- Para escalar la aplicacoion se necesitan crear pods adicionales.
- Si decrece el numero de usuarios, simplemente eliminamos pods.
- No se deben añadir contenedores a un pod existente para escalar la aplicacion.
- Aveces pueden haber contenedores auxiliares, los cuales hacen algun tipo de tarea de sopprte. Estos contenedores auxiliares estan en el mismo pod. Cuando se crea un contenedor de la aplicacion, se crea el auxiliar.

### Introduccion a YAML

- Kubernetes usa archivos YAML como entradas para la creacionde objetos (Pods, Replicas Sets, Deployments, servicios y mas).
- Los espacios antes de cada propiedad son importantes.
- La estructura del archivo YAML debe tener 4 campos obligatorios en el nivel superior:
  - apiVersion: version de la Api de kubernetes.
  - kind: tipo de objeto a crear.
  - metadata: datos sobre el objeto, y es un diccionario con las propiedades del objeto. Por ejemplo, name, labels.
  - spec: informacion adicional. Es un diccionario con propiedades como, containers, el cual es un array de diccionarios con propiedades name e image.

Ejemplo:

Extenciones recomendadas en visual studio: YAML, KUBERNETES SUPORT

```yml
apiVersion: v1
kind: Pod
metadata:
  name: app1-pod
  labels:
    app: app1
    type: front-end
spec:
  containers:
    - name:  nginx-container
      image:  nginx
```

[codebeautify.org](https://codebeautify.org/yaml-validator) ayuda a verificar si el archivo .yml esta bien estructurado.

- `kubectl create -f <archivo yaml>` ejecuta el archivo .yml para crear el recurso. -f indica que se debe crear el recurso con el archivo proporcionado. Podemos reemplazar **create** por **apply** y el resultado es el mismo.

### Replicacion Controller

- Ejecuta varias instancias de un mismo pod en un mismo cluster de kubernetes.
- Brinda una gran disposicion de la app para que podamos acceder.
- Si tememos un solo pod y este falla, Replication controller ayuda a crear automaticamente un nuevo pod.
- Se encarga y aseguira que el numero especificado de pods se este ejecutando en todo momento.
- Balancea y Escala la aplicacion de acuerdo al numero de usuarios que quieren entrar a la aplicacion.

Ejemplo:

```yaml
apiVersion: v1

kind: ReplicationController

metadata:
  name: app1-rc
  labels:
    app: app1
    type: front-end

spec:
  template:
    metadata:
      name: app1-pod
      labels:
        app: app1
        type: front-end
        
    spec:
      containers:
        - name:  nginx-container
          image:  nginx

  replicas: 2 
```

- **template** especifica que y como debe ser la instancia que debe replicar.
- **replicas** establece la cantidad de replicas que queremos.
- `kubectl apply -f <archivo yaml>` crea o actualiza el recurso.
- `kubectl get replicationcontroller` ver los ReplicationController creados.

### ReplicaSet

- Es la forma actual que se utiliza en kubernetes para hacer lo de Replication Controller.

Ejemplo:

```yaml
apiVersion: apps/v1

kind: ReplicaSet

metadata:
  name: app1-rs
  labels:
    app: app1
    type: front-end

spec:
  template:
    metadata:
      name: app1-pod
      labels:
        app: app1
        type: front-end

    spec:
      containers:
        - name: nginx-container
          image: nginx

  replicas: 4

  selector:
    matchLabels:
      type: front-end
      app: app1
```

- **apiVersion: apps/v1** es la version para ReplicaSet.
- **selector** si tenemos muchas aplicaciones, tenemos que etiquetarlas para encontrarlas facilmente y poder filtrarlas. Ai, el selector sabe que pods buscar si hay disponibles, y si no crearlas para llegar al numero de replicas requeridas. Los labels tambien sirven para identificar los pods, y asi monitorearlos.
- `kubectl apply -f <archivo yaml>` crea o actualiza el recurso.
- `kubectl get replicaset` ver los ReplicaSet creados.
- `kubectl scale --replicas=2 -f <archivo yaml>` escala a dos replicas.
