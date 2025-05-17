Docker images Compatible with multiple CPU architectures  i.e intel,linux/amd64 and linux/arm64

According to Docker official Documents Docker supports building container images targeting multiple platforms, here we are going to create a docker image which supports
multiple platforms i.e intel,linux/amd64 and linux/arm64.

STEP 1: Make sure you are using Docker >= 20.10
```
docker version
```


<img width="656" alt="image" src="https://github.com/user-attachments/assets/1d520b01-d64d-4906-bf7a-193adfd41e10" />



(i have Docker version 20.10.5+dfsg1, build 55c4c88, The +dfsg1 indicates Debian-distributed version of Docker, which is known to disable some features, including CLI plugins like buildx)

STEP 2: Uninstall docker and reinstall arm64 comapatible docker version

```
dpkg -l | grep -i docker

for pkg in docker.io docker-doc docker-compose docker-compose-v2 podman-docker containerd runc; do sudo apt-get remove $pkg; done
sudo apt-get purge -y docker-engine docker docker.io docker-ce docker-ce-cli docker-compose-plugin
sudo apt-get autoremove -y --purge docker-engine docker docker.io docker-ce docker-compose-plugin

sudo rm -rf /var/lib/docker /etc/docker
sudo rm /etc/apparmor.d/docker
sudo groupdel docker
sudo rm -rf /var/run/docker.sock
sudo rm -rf /var/lib/containerd
sudo rm -r ~/.docker
```

STEP 3: Set up Docker's apt repository 

For Ubuntu :

```
# Add Docker's official GPG key:
sudo apt-get update
sudo apt-get install ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc

# Add the repository to Apt sources:
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "${UBUNTU_CODENAME:-$VERSION_CODENAME}") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update
```

For Debian:

```
# Add Docker's official GPG key:
sudo apt-get update
sudo apt-get install ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/debian/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc

# Add the repository to Apt sources:
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/debian \
  $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update
```


STEP 4: Install docker 

```
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

<img width="1556" alt="image" src="https://github.com/user-attachments/assets/a5d27f0b-f8bc-49b9-9482-230af26bdbf4" />

STEP 5: docker version 

```
docker version
```

<img width="1532" alt="image" src="https://github.com/user-attachments/assets/11fe7541-9482-4883-a083-95e238e17261" />


STEP 6: 
