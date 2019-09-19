# Virtualização através de containers para plataformas de computação em nuvem
Oficina ministrada na Escola Regional de Redes de Computadores (ERRC - 2019).

### Versões de softwares utilizadas
> Ubuntu Server 16.04 64 Bits

> OpenStack Rocky

## Slides
http://links.leiria.inf.br/errc2019-slides

## Acesso a nuvem
http://scherm.leiria.inf.br/

## Instalação
```
sudo -s
adduser --disabled-password --gecos "stack,666,666,666" stack
echo "stack:stack" | chpasswd
echo "stack ALL=(ALL) NOPASSWD: ALL" >> /etc/sudoers
curl -fsSL https://get.docker.com/ | sh
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

## Gerenciamento
```
su - stack
source devstack/openrc admin admin
zun --version
zun host-list
zun host-show "172-17-2-84"
zun service-list

zun image-list
zun pull "ubuntu:18.04" "172-17-2-84"
zun image-list
zun image-show "c275edee-a958-42fd-bcb5-1c580092fd91"
zun image-search "ubuntu"
zun help image-search
docker search "ubuntu"
wget -q https://registry.hub.docker.com/v1/repositories/ubuntu/tags -O - | jq -r ".[].name"
zun image-delete "c275edee-a958-42fd-bcb5-1c580092fd91" "172-17-2-84"
zun image-list

zun run --name "Test01" "ubuntu" "ls" # Create, execute & stop
zun list # Wait for it stops
zun logs "Test01"
zun show "Test01"
zun list
zun start "Test01"
zun logs "Test01"
zun list

zun run -i --name "Test02" "ubuntu:16.04" "/bin/bash" # Create, execute, interact & stop
zun list # Wait for it stops
zun start "Test02"
zun list
zun stats "Test02"
zun exec -i "Test02" "/bin/bash"
zun restart "Test02"
zun show "Test02" | grep "status "
zun top "Test02"
zun pause "Test02"
zun show "Test02" | grep "status "
zun unpause "Test02"
zun show "Test02" | grep "status "
zun delete "Test02"
zun stop "Test02"
zun delete "Test02"
zun list

zun create --name "Test03" "ubuntu" # Just create
zun start "Test03" # Start && stop
zun list
zun exec -i "Test03" "/bin/bash"
zun logs "Test03"
zun delete "Test03"
zun list

zun run -i --name "Test04" "ubuntu:16.04" "/bin/bash" # Create, execute, interact & stop
zun list # Wait for it stops
zun start "Test04"
zun commit "Test04" "local/commited-image:0.0.1"
docker image list
zun image-list
zun run -i --name "Test05" "local/commited-image:0.0.1" "/bin/bash" # Create, execute, interact & stop
zun list
```
