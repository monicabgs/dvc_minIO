# dvc_minIO
The objective of this project is used dvc in ML and Data Science projects. For this we'll store large files in MinIO.

**1.Install Go**
sudo passwd root
su root

apt update
wget -c https://dl.google.com/go/go1.14.2.linux-amd64.tar.gz
tar xvf go1.14.2.linux-amd64.tar.gz
sudo chown -R root:root ./go
sudo mv go /usr/local
sudo echo 'export PATH=$PATH:/usr/local/go/bin' >> /etc/profile
source /etc/profile
rm go1.14.2.linux-amd64.tar.gz
go version

**2. Install MinIO server** 
cd ~
wget https://dl.min.io/server/minio/release/linux-amd64/minio

sudo useradd --system minio --shell /sbin/nologin
sudo usermod -L minio
sudo chage -E0 minio

sudo mv minio /usr/local/bin
sudo chmod +x /usr/local/bin/minio
sudo chown minio:minio /usr/local/bin/minio

sudo touch /etc/default/minio
sudo echo 'MINIO_ACCESS_KEY="minio"' >> /etc/default/minio
sudo echo 'MINIO_VOLUMES="/usr/local/share/minio/"' >> /etc/default/minio
sudo echo 'MINIO_OPTS="-C /etc/minio --address :9000"' >> /etc/default/minio
sudo echo 'MINIO_SECRET_KEY="guttest"' >> /etc/default/minio

sudo mkdir /usr/local/share/minio
sudo mkdir /etc/minio

sudo chown minio:minio /usr/local/share/minio
sudo chown minio:minio /etc/minio

**3. Install initialize script Minio's systemd**
I had some problems with this file, so I get it and modified creating
a new minio_service. You'll find it in **"minio_service.py"** in this repository.

cd ~
wget https://raw.githubusercontent.com/minio/minio-service/master/linux-systemd/minio.service
vim minio_service
[delete all and paste the code available in **/setup/minio_service.py**]

sudo mv minio.service /etc/systemd/system

sudo systemctl daemon-reload
sudo systemctl enable minio

**3. Starting Minio's service** 
sudo systemctl start minio
sudo systemctl status minio

**4.Firewall setup**
cd ~
sudo ufw default deny incoming
sudo ufw default allow outgoing
sudo ufw allow ssh
sudo ufw allow 9000
sudo ufw allow http
sudo ufw allow https
sudo ufw enable

**5. Protecing access with TLS certificate**
cd ~
sudo ufw allow 80
sudo ufw allow 443
sudo ufw status verbose
sudo apt install software-properties-common
sudo add-apt-repository universe


**6.Encrypt certificate using Certbot for MinIO**
sudo apt update
sudo add-apt-repository ppa:certbot/certibot
sudo apt update
sudo apt install certbot
sudo apt install certbot python3-certbot-apache
sudo certbot --apache

sudo certbot certonly --standalone -d minio.ubuntu-VirtualBox
sudo cp /etc/letsencrypt/live/minio-server.your_domain_name/privkey.pem /etc/minio/certs/private.key
sudo cp /etc/letsencrypt/live/minio-server.your_domain_name/fullchain.pem /etc/minio/certs/public.crt
sudo chown minio:minio /etc/minio/certs/private.key
sudo chown minio:minio /etc/minio/certs/public.crt
sudo systemctl restart minio

**7.Integrate project with MinIO**














