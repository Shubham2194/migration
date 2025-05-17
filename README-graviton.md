Docker supports building container images targeting multiple platforms, here we are going to create a docker image which supports
multiple platforms i.e intel,linux/amd64 and linux/arm64.

docker version
You should see a version >= 20.10.x.

Uninstall docker and reinstall arm64 comapatible docker

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
