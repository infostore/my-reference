# Kubernetes

## Kubernetes Node 설정

```sh
# SSH 접속 허용
sudo apt install openssh-server

# root 로그인 허용
sudo passwd root
sudo vi /etc/ssh/sshd_config
  PermitRootLogin yes
sudo service ssh restart

# IPv4 포워딩 활성화
sudo vi /etc/sysctl.conf
  # 주석 제거
  net.ipv4.ip_forward=1
```

## Deploy 서버 설정

```sh
# 공개키 등록
sudo -i
ssh-keygen -t rsa

ssh-copy-id -i /root/.ssh/id_rsa.pub root@192.168.0.78
ssh-copy-id -i /root/.ssh/id_rsa.pub root@192.168.0.79
ssh-copy-id -i /root/.ssh/id_rsa.pub root@192.168.0.80
```

## NFS Server 설정

```sh
sudo -i

# nfs 설치
apt-get update
apt-get install nfs-common nfs-kernel-server portmap

# nfs 폴더 생성
mkdir /data
chmod 777 /data

# nfs 설정 파일 변경
vi /etc/exports

/data 10.0.0.0/24(rw,no_root_squash,sync) 10.1.0.0/16(rw,no_root_squash,sync)

# nfs 커널 재시작
/etc/init.d/nfs-kernel-server restart
```

## NFS Client 설정

```sh
sudo apt install -y nfs-common
sudo mkdir /mnt/nfs
sudo chmod 777 /mnt/nfs
sudo mount -t nfs 10.0.0.121:/data /mnt/nfs
```

## Kubespray 설치

```sh
sudo apt-get install -y python3-pip
sudo pip3 install --upgrade pip
sudo pip install ansible netaddr

git clone https://github.com/kubernetes-sigs/kubespray.git
cd kubespray
sudo pip install -r requirements.txt
cp -rfp inventory/sample inventory/deepphi
declare -a IPS=(192.168.0.78 192.168.0.79 192.168.0.80)
CONFIG_FILE=inventory/deepphi/hosts.yml python3 contrib/inventory_builder/inventory.py ${IPS[@]}

# helm 활성화
vi inventory/deepphi/group_vars/k8s-cluster/addons.yml

    ---
    # Helm deployment
    helm_enabled: true

# kubernetes 설치
ansible-playbook -i inventory/deepphi/hosts.yml --become --become-user=root cluster.yml
```

## Kubernetes 환경설정

```sh
# 마스터 접속

# 실행 권한 부여
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config

# helm repository 등록
helm repo add incubator http://storage.googleapis.com/kubernetes-charts-incubator
helm repo add bitnami https://charts.bitnami.com/bitnami

# custom-chart 폴더 생성
mkdir custom-chart
```

## NFS StorageClass 설치

```sh
cd custom-chart


```