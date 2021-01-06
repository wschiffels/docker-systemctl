# debian10

this Dockerfile creates a Debian 10 based container for tests using molecule.
The container contains a replacement for systemctl and can be used to test
deployment of services with a docker container as the target host. Just as on a
real machine you can use "systemctl start" and "systemctl enable" and other
commands to bring up services for further configuration and testing

## Prerequisites

* Windows 10  build 18917 or higher
* WSL2
* Docker
* ansible
* molecule

## WLS

### Enable WSL and VMP

PowerShell:

```
Enable-WindowsOptionalFeature -Online -FeatureName Microsoft-Windows-Subsystem-Linux
Enable-WindowsOptionalFeature -Online -FeatureName VirtualMachinePlatform
```

## Set WSL 2 as default

PowerShell:

```
wsl --set-default-version 2
```

Install Debian 10

## WIN / Docker
Install Docker Desktop.

Make sure `Enable the experimental WSL 2 based engine` is enabled.

## WSL / Docker

Install docker inside your WLS

```bash
sudo apt-get update
sudo apt-get install apt-transport-https ca-certificates curl software-properties-common
 curl -fsSL https://download.docker.com/linux/debian/gpg | sudo apt-key add -sudo  add-apt-repository    "deb [arch=amd64] https://download.docker.com/linux/debian \
   $(lsb_release -cs) \
   stable"
sudo apt-get update
sudo apt-get install docker-ce
sudo gpasswd -a $USER docker
sudo mkdir /sys/fs/cgroup/systemd
sudo mount -t cgroup -o none,name=systemd cgroup /sys/fs/cgroup/systemd
sudo service docker start
docker info
docker run hello-world
```

Since 1.8.1 by default the nf_tables backend is used instead of the xtables backend.
So you might need to switch to legacy mode if docker doesn't start

```bash
 sudo update-alternatives --set iptables /usr/sbin/iptables-legacy
 ```

## Build the container

Clone this repository and build the container:

```bash
docker build -t debian10:systemctl .
```

## molecule

```bash
pip install molecule molecule-docker
```

molecule.yml

```yml
---
dependency:
  name: galaxy
driver:
  name: docker
platforms:
  - name: instance
    image: debian10:systemctl
    pre_build_image: True
    privileged: True
    volumes:
      - /sys/fs/cgroup:/sys/fs/cgroup:ro
    published_ports:
      - "0.0.0.0:8080:8080/tcp"
    command: "/sbin/init"
    environment: { container: docker }
provisioner:
  name: ansible
  env:
    ANSIBLE_ROLES_PATH: "../../provisioning"
verifier:
  name: ansible
```

```bash
molecule list
molecule converge
```


## Links

* https://github.com/gdraheim/docker-systemctl-replacement
* https://molecule.readthedocs.io/en/latest/

