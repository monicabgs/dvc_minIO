# dvc_minIO<br></p>
The objective of this project is used dvc in ML and Data Science projects. For this we'll store large files in MinIO.<br></p>


**1.Install Go**
sudo passwd root<br></p>
su root<br></p>

apt update<br></p>
wget -c https://dl.google.com/go/go1.14.2.linux-amd64.tar.gz<br></p>
tar xvf go1.14.2.linux-amd64.tar.gz<br></p>
sudo chown -R root:root ./go<br></p>
sudo mv go /usr/local<br></p>
sudo echo 'export PATH=$PATH:/usr/local/go/bin' >> /etc/profile<br></p>
source /etc/profile<br></p>
rm go1.14.2.linux-amd64.tar.gz<br></p>
go version<br></p>


**2. Install MinIO server** <br></p>
cd ~<br></p>
wget https://dl.min.io/server/minio/release/linux-amd64/minio<br></p>

sudo useradd --system minio --shell /sbin/nologin<br></p>
sudo usermod -L minio<br></p>
sudo chage -E0 minio<br></p>

sudo mv minio /usr/local/bin<br></p>
sudo chmod +x /usr/local/bin/minio<br></p>
sudo chown minio:minio /usr/local/bin/minio<br></p>

sudo touch /etc/default/minio<br></p>
sudo echo 'MINIO_ACCESS_KEY="minio"' >> /etc/default/minio<br></p>
sudo echo 'MINIO_VOLUMES="/usr/local/share/minio/"' >> /etc/default/minio<br></p>
sudo echo 'MINIO_OPTS="-C /etc/minio --address :9000"' >> /etc/default/minio<br></p>
sudo echo 'MINIO_SECRET_KEY="gutteste"' >> /etc/default/minio<br></p>

sudo mkdir /usr/local/share/minio<br></p>
sudo mkdir /etc/minio<br></p>

sudo chown minio:minio /usr/local/share/minio<br></p>
sudo chown minio:minio /etc/minio<br></p>


**3. Install initialize script Minio's systemd**<br></p>
cd ~<br></p>
wget https://raw.githubusercontent.com/minio/minio-service/master/linux-systemd/minio.service<br></p>
vim minio_service<br></p>
[delete all and paste the code available in **/setup/minio_service.py**]<br></p>

sudo mv minio.service /etc/systemd/system<br></p>

**4.Firewall setup**<br></p>
cd ~<br></p>
sudo ufw default deny incoming<br></p>
sudo ufw default allow outgoing<br></p>
sudo ufw allow ssh<br></p>
sudo ufw allow 9000<br></p>
sudo ufw allow http<br></p>
sudo ufw allow https<br></p>
sudo ufw enable<br></p>


**5. Protecing access with TLS certificate**<br></p>
cd ~<br></p>
sudo ufw allow 80<br></p>
sudo ufw allow 443<br></p>
sudo ufw status verbose<br></p>
sudo apt install software-properties-common<br></p>
sudo add-apt-repository universe<br></p>


**6. Install Minio Customer (MC)**<br></p>
cd ~<br></p>
wget https://dl.min.io/client/mc/release/linux-amd64/mc<br></p>
chmod +x mc<br></p>

**6. Start Minio's service**<br></p>
cd ~ 
sudo systemctl daemon-reload<br></p>
sudo systemctl enable minio<br></p>
sudo systemctl start minio<br></p>
sudo systemctl status minio<br></p>
q (to quit)

./mc alias set minio http://127.0.0.1:9000 minio gutteste
./mc alias set minio http://172.17.0.1:9000 minio gutteste
./mc alias set minio http://10.0.2.15:9000 minio gutteste

./mc admin info minio

**6. Generate a certificate**<br></p>
mkdir -p /etc/openssl/<br></p>
cd /etc/openssl/<br></p>
touch openssl.conf<br></p>
vim touch openssl.conf<br></p>
[Paste the code available in **/setup/openssl.conf.txt**]<br></p>
openssl req -x509 -nodes -days 730 -newkey rsa:2048 -keyout private.key -out public.crt -config openssl.conf<br></p>

mv private.key /root/.minio/certs/CAs/<br></p>
mv public.crt /root/.minio/certs/CAs/<br></p>

./mc share upload s3/backup/2006-Mar-1/backup.tar.gz<br></p>


--------------------------------------------------------------------------------------------


















**6. Create a hostname**<br></p>
sudo hostnamectl set-hostname **gut**<br></p>
echo '127.0.0.1 **gut.com** **gut**' | sudo tee -a /etc/hosts<br></p>

**7. DNS server configuration**<br></p>
cd ~
mkdir -m /etc/cloud/
cd /cloud
touch cloud.cfg
vim cloud.cfg
[Paste the code available in **/setup/cloud.cfg.txt**]<br></p>
reboot

vim /etc/resolv.conf
[Paste the code available in **/setup/resolv.conf.txt**]<br></p>

apt-get install bind9 bind9utils bind9-doc -y
cd /etc/bind/
cp named.conf.local named.conf.local.back
cp db.local db.fwd.gut.com
cp db.local db.rev.gut.com
mkdir /etc/bind/zones
mv db.fwd.gut.com zones
mv db.rev.gut.com zones
vim named.conf.local
[Paste the code available in **/setup/name.config.local.txt**]<br></p>

cd zones
sudo vim db.fwd.gut.com
[Delete all and paste the code available in **/setup/db.fwd.gut.com.txt**]<br></p>

vim db.rev.gut.com
[Delete all and paste the code available in **/setup/db.rev.gut.com.txt**]<br></p>

service bind9 restart








**7. Install and configure Apache**<br></p>
sudo apt install apache2
sudo ufw allow 'Apache'

**8. Create Virtualhost**<br></p>
sudo mkdir /var/www/gut.com<br></p>
sudo chown -R $USER:$USER /var/www/gut.com<br></p>
sudo chmod -R 755 /var/www/gut.com <br></p>
vim /var/www/gut.com/index.html<br></p>
[Paste the code available in **/setup/index.html**]<br></p>

sudo vim /etc/apache2/sites-available/gut.com.conf<br></p>
[Paste the code available in **/setup/gut.com.conf**]<br></p>
sudo mkdir -p /var/www/gut.com/documentroot<br></p>
sudo a2ensite gut.com<br></p>
systemctl reload apache2<br></p>

sudo a2dissite 000-default.conf<br></p>
systemctl reload apache2<br></p>

vim /etc/apache2/apache2.conf<br></p>
[Paste the code available in **/setup/server_name.txt*]<br></p>
sudo systemctl restart apache2<br></p>




**9. Encrypt certificate using Certbot for MinIO**<br></p>
sudo apt-install software-properties-common<br></p>
sudo add-apt-repository universe<br></p>
sudo apt update<br></p>
sudo apt install certbot<br></p>
sudo service apache2 stop<br></p>
sudo certbot certonly --standalone -d minio.**gut.com**<br></p>

sudo cp /etc/letsencrypt/live/minio-server.your_domain_name/privkey.pem /etc/minio/certs/private.key<br></p>
sudo cp /etc/letsencrypt/live/minio-server.your_domain_name/fullchain.pem /etc/minio/certs/public.crt<br></p>
sudo chown minio:minio /etc/minio/certs/private.key<br></p>
sudo chown minio:minio /etc/minio/certs/public.crt<br></p>
sudo systemctl restart minio<br></p>


**8.Integrate project with MinIO**<br></p>














