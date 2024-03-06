# DevOps TOTAL, Docker, Kubernetes, Jenkins, AWS, Git y More

[DevOps TOTAL Docker, Kubernetes, Jenkins, AWS, Git y Más!](https://www.udemy.com/course/devops-total/?couponCode=KEEPLEARNING)

## Seccion 3: Requisitos previos (Informacion y Configuracion)

### Herramientas de Windows

Instalar chocolatey desde las instrucciones dadas en este [Link](https://chocolatey.org/install).

Ejecutar todos los comandos a continuación en Powershell (Abrir Powershell como Administrador)

```choco install virtualbox

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

```sudo apt update
sudo apt install virtualbox
```

Install Vagrant

```
curl -O https://releases.hashicorp.com/vagrant/2.2.9/vagrant_2.2.9_x86_64.deb
sudo apt install ./vagrant_2.2.9_x86_64.deb
```

Install Git

```
apt install git
```

Install jdk8

```
sudo apt-get install openjdk-8-jdk
```

Install Maven

```
sudo apt-get install maven
```

Install awscli

```
sudo apt-get install awscli
```

Install Intellij community

```
sudo snap install intellij-idea-community --classic
```

Install Sublime Text

```sudo apt update
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

Carpetas de sincronizacion, añadir `config.vm.synced_folder "F:\\vagrant-vms\\shellsscripts", "/vagrant_data"`en el vagrantfile, donde se especifica la ruta host y la ruta en el guest.
