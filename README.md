# Virtualização através de containers para plataformas de computação em nuvem
> Oficina ministrada na Escola Regional de Redes de Computadores (ERRC - 2019)

# Slides
http://links.leiria.inf.br/errc2019-slides

# Instalação
```
adduser --disabled-password --gecos "stack,666,666,666" stack
echo "stack:stack" | chpasswd
echo "stack ALL=(ALL) NOPASSWD: ALL" >> /etc/sudoers
curl -fsSL https://get.docker.com/ | sh # Run as root
usermod -aG docker stack
su - stack
git clone "https://github.com/openstack/devstack.git" -b "stable/rocky" "devstack"
cd devstack
echo "[[local|localrc]]
SERVICE_TOKEN=\"stack\"
ADMIN_PASSWORD=\"stack\"
RABBIT_PASSWORD=\"stack\"
SERVICE_PASSWORD=\"stack\"
DATABASE_PASSWORD=\"stack\"
GIT_BASE=\"https://github.com\"
VOLUME_BACKING_FILE_SIZE=\"60G\"

\# Logging
VERBOSE=\"True\"
ENABLE_DEBUG_LOG_LEVEL=\"True\"
ENABLE_VERBOSE_LOG_LEVEL=\"True\"
LOGFILE=\"/opt/stack/logs/stack.sh.log\"

\# Network
HOST_IP=\"172.17.2.84\"
Q_USE_SECGROUP=\"False\"
ENABLE_TENANT_VLANS=\"False\"

\# Plugins
enable_plugin \"zun\" \"https://github.com/openstack/zun\" \"stable/rocky\"
enable_plugin \"heat\" \"https://github.com/openstack/heat\" \"stable/rocky\"
enable_plugin \"zun-ui\" \"https://github.com/openstack/zun-ui\" \"stable/rocky\"
enable_plugin \"kuryr-libnetwork\" \"https://github.com/openstack/kuryr-libnetwork\" \"stable/rocky\"
enable_plugin \"devstack-plugin-container\" \"https://github.com/openstack/devstack-plugin-container\" \"stable/rocky\"" > local.conf
cat local.conf
./stack.sh
```

# Gerenciamento
```
su - stack
source devstack/openrc admin admin
openstack appcontainer service list
openstack appcontainer host list
zun host-show 172-17-2-84
zun create --name Test01 cirros ping -c 4 8.8.8.8
zun list
docker ps -a
zun start Test01
zun logs Test01
zun pull ubuntu 172-17-2-84
zun run -i --name Test02 ubuntu /bin/bash
zun image-list
zun delete Test01
zun delete Test02
```
