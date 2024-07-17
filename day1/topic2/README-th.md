# Instana Backend Installation

Please Select Language

[![en](https://img.shields.io/badge/lang-en-green.svg)](./README.md)

<!-- [![th](https://img.shields.io/badge/lang-th-red.svg)](./README-th.md) -->

## Description

IBM Instana Workshop (**Please don't share Customer!**)

Learning how to install Instana Server (Classic Edition).

Recommend for PoX Activity, small and medium size of environment.

## Lab Index

0. Access Instana-Server VM 👉 [Click Here](./topic1/README.md)
1. Mount additional disk space 👉 [Click Here](./topic1/README.md)
2. Prepare folder for instana 👉 [Click Here](./topic1/README.md)
3. Install Docker 👉 [Click Here](./topic1/README.md)
4. Install Instana Server 👉 [Click Here](./topic1/README.md)
5. First Login 👉 [Click Here](./topic1/README.md)
6. Import License 👉 [Click Here](./topic1/README.md)
7. Instana self-monitoring 👉 [Click Here](./topic1/README.md)
8. Zone Configuration 👉 [Click Here](./topic1/README.md)

## 0. Access Instana-Server VM

```
ssh itzuser@<PUBLIC IP> -p 2223 -i <PATH TO SSH KEY>
```

## 1. Mount additional disk space

```
lsblk
```

```
sudo fdisk /dev/xvde
```

```
sudo mkfs -t ext4 /dev/xvde1
```

```
sudo mount -t ext4 /dev/xvde1 /mnt
```

```
sudo vi /etc/fstab
```

```
/dev/xvde1 /mnt ext4 defaults,noatime 0 0
```

## 2. Prepare folder for instana

```
sudo mkdir /opt/{data,metrics,traces}
sudo mkdir /opt/log
sudo mkdir /opt/log/instana
```

## 3. Install Docker

### Uninstall all conflicting packages

```
for pkg in docker.io docker-doc docker-compose docker-compose-v2 podman-docker containerd runc; do sudo apt-get remove $pkg; done
```

### Add Docker"s official GPG key:

```
sudo apt-get update -y
sudo apt-get install -y ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc
```

### Add the repository to Apt sources:

```
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update
```

### Install the latest version

```
sudo apt-get install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

```
sudo docker run hello-world
sudo usermod -aG docker $USER
```

## 4. Install Instana Server

```
sudo -i
```

```
echo 'deb [signed-by=/usr/share/keyrings/instana-archive-keyring.gpg] https://artifact-public.instana.io/artifactory/rel-debian-public-virtual generic main' > /etc/apt/sources.list.d/instana-product.list
```

```
INSTANA_DOWNLOAD_KEY=<PLEASE INSERT DOWNLOAD KEY> && /
cat << EOF > /etc/apt/auth.conf
machine artifact-public.instana.io
  login _
  password ${INSTANA_DOWNLOAD_KEY}
EOF

wget -nv -O- --user=_ --password="${INSTANA_DOWNLOAD_KEY}" https://artifact-public.instana.io/artifactory/api/security/keypair/public/repositories/rel-debian-public-virtual | gpg --dearmor > /usr/share/keyrings/instana-archive-keyring.gpg
```

```
INSTANA_VERSION=<INSTANA VERSION TO PIN> && \
cat > /etc/apt/preferences.d/instana-console <<EOF
Package: instana-console
Pin: version ${INSTANA_VERSION}
Pin-Priority: 1000
EOF
```

```
apt update -y
apt install -y instana-console
```

```
instana init
```

## 5. First Login

## 6. Import License

```
sudo instana license download
```

```
sudo instana license import -f license
```

```
sudo instana license verify
```

## 7. Instana self-monitoring

## 8. Zone Configuration

```
sudo touch /mnt/instana/agent/etc/instana/configuration-zone.yaml
```

```
INSTANA_ZONE="Instana Server" && \
cat <<EOF | sudo tee /mnt/instana/agent/etc/instana/configuration-zone.yaml
# Hardware & Zone
com.instana.plugin.generic.hardware:
  enabled: true
  availability-zone: "${INSTANA_ZONE}"
EOF
```
