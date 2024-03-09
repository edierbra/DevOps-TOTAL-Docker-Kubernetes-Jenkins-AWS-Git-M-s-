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
