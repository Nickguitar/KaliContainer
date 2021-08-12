# KaliContainer
Script to automate deploying a kali container

Simply run this script as root and it will install docker, pull kali image, create the container, start it and add an alias into .bashrc.

If you want to open publish a container's port, you can specify them:

E.g: publish ports 80, 22 and 1000-1005 and map them to themselves:

`# ./script.sh 80:80 22:22 1000-1005:1000-1005`

```bash
#!/bin/bash

if [[ $EUID -ne 0 ]]; then echo "Must be run as root"; exit; fi

rel=$(lsb_release -cs)
x=""
for i in "$@"; do x=$x" -p $i"; done

apt update
apt upgrade -y
apt install apt-transport-https ca-certificates curl gnupg lsb-release -y
curl -fsSL https://download.docker.com/linux/debian/gpg | gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
list="deb [arch=amd64 signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/debian $rel stable"
echo $list > /etc/apt/sources.list.d/docker.list
apt update
apt install docker-ce docker-ce-cli containerd.io -y
docker pull kalilinux/kali-rolling
docker run -dit --name kali --hostname kali$(echo $x) kalilinux/kali-rolling
docker container kali start
echo "alias kali='docker exec -it kali /bin/bash'" >> ~/.bashrc
source ~/.bashrc
echo "Done! Use 'kali' to enter the container." 
```
