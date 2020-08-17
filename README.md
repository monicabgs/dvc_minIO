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
cd ~
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

sudo systemctl daemon-reload<br></p>
sudo systemctl enable minio<br></p>
sudo systemctl start minio<br></p>
sudo systemctl status minio<br></p>


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

**6. Create a domain configuration**<br></p>
sudo hostnamectl set-hostname **gut**<br></p>
echo '127.0.0.1 **gut.com** **gut**' | sudo tee -a /etc/hosts<br></p>


**7. Encrypt certificate using Certbot for MinIO**<br></p>
cd ~
sudo apt-get install openssh-server
vim /etc/ssh/sshd_config
[include the code available in **/setup/sshd_config.txt**]<br></p>

sudo service ssh restart
ssh localhost

sudo apt install snapd

sudo snap install --classic certbot
apt install python3-certbot-apache 

cd /etc/apache2/sites-available
touch gut.conf
vim gut.conf
[include the code available in **/setup/virtual_host.txt**]<br></p>
sudo a2ensite gut.conf
sudo service apache2 reload

cd /var/www/
mkdir gut
cd gut
touch gut.public_html
vim gut.public_html
[include the code available in **/setup/gut.public_html.txt**]<br></p>
sudo mkdir -p /var/www/gut/documentroot
cd ~




sudo certbot --apache






**7. Encrypt certificate using Certbot for MinIO**<br></p>
sudo add-apt-repository ppa:certbot/certibot<br></p>
sudo apt update<br></p>
sudo apt install certbot<br></p>
sudo apt install certbot python3-certbot-apache<br></p>

sudo certbot certonly --standalone -d minio.**gut**<br></p>
sudo cp /etc/letsencrypt/live/minio-server.your_domain_name/privkey.pem /etc/minio/certs/private.key<br></p>
sudo cp /etc/letsencrypt/live/minio-server.your_domain_name/fullchain.pem /etc/minio/certs/public.crt<br></p>
sudo chown minio:minio /etc/minio/certs/private.key<br></p>
sudo chown minio:minio /etc/minio/certs/public.crt<br></p>
sudo systemctl restart minio<br></p>


**8.Integrate project with MinIO**<br></p>














