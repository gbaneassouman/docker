## TP-1 : Installation et Test de Docker
Ce TP consiste à mettre en place un environnement Docker pour la réalisation des travaux pratiques .

- Installation de Docker sur Centos
- Tache de post Installation
- Vérification de l'Installation
- Utiliser la documentation Docker afin d'etre à jour
- Lancement de votre premier coteneur (nginx)
- Réaliser la même tâche sur la plateforme de Labs
- Utilisation des variables d'environnement

### TAF
- Utiliser Virtualbox comme hyperviseur pour créer la machine virtuell
- Utiliser Vagrant pour proisionner la VM
- Installer Docker sur la VM

### Plan de travail
Dans un premier temps, il faut créer le répertoire de base qui va contenir les deux principaux fichiers : `install_docker.sh` et `Vagrantfile` 

- **Création des fichiers**
```
mkdir -p tp-1-installation-docker/{install_docker.sh, Vagrantfile}
```
**Description**

- `install_docker.sh`:  va contenir le script d'installation de Docker
- `vagrantfile`: va contenir les paramètres servant la création de la machine virtuelle.

**Contenu des fichiers**
- 1. install_docker.sh
```
#!/bin/bash
sudo yum -y update

# install docker
sudo yum install -y git
curl -fsSL https://get.docker.com -o get-docker.sh
sh get-docker.sh
sudo usermod -aG docker vagrant
sudo systemctl enable docker
sudo systemctl start docker
sudo curl -L "https://github.com/docker/compose/releases/download/1.29.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose

if [[ !(-z "$ENABLE_ZSH")  &&  ($ENABLE_ZSH == "true") ]]
then
    echo "We are going to install zsh"
    sudo yum -y install zsh git
    echo "vagrant" | chsh -s /bin/zsh vagrant
    su - vagrant  -c  'echo "Y" | sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"'
    su - vagrant  -c "git clone https://github.com/zsh-users/zsh-syntax-highlighting.git ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting"
    sed -i 's/^plugins=/#&/' /home/vagrant/.zshrc
    echo "plugins=(git  docker docker-compose colored-man-pages aliases copyfile  copypath dotenv zsh-syntax-highlighting jsontools)" >> /home/vagrant/.zshrc
    sed -i "s/^ZSH_THEME=.*/ZSH_THEME='agnoster'/g"  /home/vagrant/.zshrc
  else
    echo "The zsh is not installed on this server"    
fi

echo "For this Stack, you will use $(ip -f inet addr show enp0s8 | sed -En -e 's/.*inet ([0-9.]+).*/\1/p') IP Address"
```
**Description**

- Mise à jour de la liste des dépots
- installation de git
- Récupération du script d'installation de docker (ici, nous allons utiliser la - méthode d'installation par script)
- Exécution du script
- Ajout de l'utilisateur vagrant au groupe docker
- Activer le démarrage automatique du service docker au démarrage de la machine - virtuelle
- Démarrage du service docker
- Récupération du script d'installation de docker-compose
- Donner le droit d'exécution sur le script à l'utilisateur
- Exécution du script

- 2. vagrantfile
```
# -*- mode: ruby -*-
# vi: set ft=ruby :
# To enable zsh, please set ENABLE_ZSH env var to "true" before launching vagrant up 
#   + On windows => $env:ENABLE_ZSH="true"
#   + On Linux  => export ENABLE_ZSH="true"

Vagrant.configure("2") do |config|
  config.vm.define "docker" do |docker|
    docker.vm.box = "geerlingguy/centos7"
    docker.vm.network "private_network", type: "dhcp"
    docker.vm.hostname = "docker"
    docker.vm.provider "virtualbox" do |v|
      v.name = "docker"
      v.memory = 1024
      v.cpus = 2
    end
    docker.vm.provision :shell do |shell|
      shell.path = "install_docker.sh"
      shell.env = { 'ENABLE_ZSH' => ENV['ENABLE_ZSH'] }
    end
  end
end

```
**Description**

- Image de base => geerlingguy/centos7
- Hostname => docker
- CPU => 2
- RAM => 2Go
- Type d'adressage IP => static
- IP fixe privé => 192.168.99.20

## Installation 

```
cd tp-1-installation-docker && vagrant up
```
### Provisionning de la VM

```
tp-1-installation-docker$ vagrant up
Bringing machine 'docker' up with 'virtualbox' provider...
==> docker: Checking if box 'geerlingguy/centos7' version '1.2.27' is up to date...
==> docker: Setting the name of the VM: docker
==> docker: Clearing any previously set network interfaces...
==> docker: Preparing network interfaces based on configuration...
    docker: Adapter 1: nat
    docker: Adapter 2: hostonly
==> docker: Forwarding ports...
    docker: 22 (guest) => 2222 (host) (adapter 1)
==> docker: Running 'pre-boot' VM customizations...
==> docker: Booting VM...
==> docker: Waiting for machine to boot. This may take a few minutes...
    docker: SSH address: 127.0.0.1:2222
    docker: SSH username: vagrant
    docker: SSH auth method: private key
```
### Installation de Docker
```
[docker] GuestAdditions versions on your host (7.0.12) and guest (6.1.32) do not match.
Loaded plugins: fastestmirror
Loading mirror speeds from cached hostfile
 * base: fr2.rpmfind.net
 * epel: level66.mm.fcix.net
 * extras: mirror.karneval.cz
 * updates: mirror.checkdomain.de
Package binutils-2.27-44.base.el7_9.1.x86_64 already installed and latest version
Package 1:make-3.82-24.el7.x86_64 already installed and latest version
Package 4:perl-5.16.3-299.el7_9.x86_64 already installed and latest version
Package bzip2-1.0.6-13.el7.x86_64 already installed and latest version
Package elfutils-libelf-devel-0.176-5.el7.x86_64 already installed and latest version
Resolving Dependencies
--> Running transaction check
---> Package gcc.x86_64 0:4.8.5-44.el7 will be installed
--> Processing Dependency: cpp = 4.8.5-44.el7 for package: gcc-4.8.5-44.el7.x86_64
--> Processing Dependency: glibc-devel >= 2.2.90-12 for package: gcc-4.8.5-44.el7.x86_64
---> Package kernel-devel.x86_64 0:3.10.0-1160.81.1.el7 will be installed
---> Package kernel-devel.x86_64 0:3.10.0-1160.118.1.el7 will be installed
--> Running transaction check
---> Package cpp.x86_64 0:4.8.5-44.el7 will be installed
---> Package glibc-devel.x86_64 0:2.17-326.el7_9 will be installed
--> Processing Dependency: glibc-headers = 2.17-326.el7_9 for package: glibc-devel-2.17-326.el7_9.x86_64
--> Processing Dependency: glibc-headers for package: glibc-devel-2.17-326.el7_9.x86_64
--> Running transaction check
---> Package glibc-headers.x86_64 0:2.17-326.el7_9 will be installed
--> Processing Dependency: kernel-headers >= 2.2.1 for package: glibc-headers-2.17-326.el7_9.x86_64
--> Processing Dependency: kernel-headers for package: glibc-headers-2.17-326.el7_9.x86_64
--> Running transaction check
---> Package kernel-headers.x86_64 0:3.10.0-1160.118.1.el7 will be installed
--> Finished Dependency Resolution

Dependencies Resolved

================================================================================
 Package             Arch        Version                     Repository    Size
================================================================================
Installing:
 gcc                 x86_64      4.8.5-44.el7                base          16 M
 kernel-devel        x86_64      3.10.0-1160.81.1.el7        updates       18 M
 kernel-devel        x86_64      3.10.0-1160.118.1.el7       updates       18 M
Installing for dependencies:
 cpp                 x86_64      4.8.5-44.el7                base         5.9 M
 glibc-devel         x86_64      2.17-326.el7_9              updates      1.1 M
 glibc-headers       x86_64      2.17-326.el7_9              updates      691 k
 kernel-headers      x86_64      3.10.0-1160.118.1.el7       updates      9.1 M

Transaction Summary
================================================================================
Install  3 Packages (+4 Dependent packages)

Total download size: 69 M
Installed size: 136 M
Downloading packages:
Delta RPMs disabled because /usr/bin/applydeltarpm not installed.
```
### Connexion à la VM
```
tp-1-installation-docker$ vagrant ssh docker
```

```
[vagrant@localhost ~]$ 
```
![](../images/docker.png)