# mtls_poc </p>
We are configuring the certificates with mtls. The objective is server a service where the client must use a certificate to acess a endpoint and in parallel the service must use a certificate to ensure a https endpoint. </p>

### Installing and Configuring Nginx</p>
sudo apt update</p>
sudo apt install nginx</p>
sudo ufw allow 'Nginx Full'</p>

### Creating Certificates</p>
mkdir /certs</p>
cd /certs</p>

### Generate Certificate Authority (CA) files</p>
###### For this step we need choose a password, in this case I'm using ubuntu</p>
###### You can see the output of each step in setup/output and must set the fields like the configuration</p>
openssl genrsa -des3 -out ca.key 4096</p>
openssl req -new -x509 -days 3650 -key ca.key -out ca.crt</p>
cat ca.key > ca.pem</p>

### Generating Client/User Certificates</p>
###### For this step we need choose a password, in this case I'm using ubuntu</p>
###### You can see the output of each step in setup/output and must set the fields like the configuration</p>
openssl genrsa -des3 -out user.key 4096</p>
openssl req -new -key user.key -out user.csr</p>
openssl x509 -req -days 365 -in user.csr -CA ca.crt -CAkey ca.key -set_serial 01 -out user.crt</p>
openssl pkcs12 -export -out user.pfx -inkey user.key -in user.crt -certfile ca.crt</p>

### Generating Server Certificates</p>
* ###### For this POC I'm using a fake DNS, but you must to change the "nginx.mssl.com" for the GUT endpoint</p>
###### You can see the output of each step in setup/output and must set the fields like the configuration</p>
openssl genrsa -out nginx.mssl.com.key 4096</p>
openssl req -new -key nginx.mssl.com.key -out nginx.mssl.com.csr</p>
openssl x509 -req -days 365 -sha256 -in nginx.mssl.com.csr -CA ca.crt -CAkey ca.key -set_serial 1 -out nginx.mssl.com.crt</p>

### Setting up our endpoint with Nginx</p>
* ###### For this step, the files will be in setup/endpoint</p>
sudo vim /etc/hosts</p>
mkdir -p /usr/share/nginx/mssl</p>
cd /usr/share/nginx/mssl</p>
touch index.html</p>

### Configuring the Nginx server to enable two way SSL </p>
###### * The files for this step will be in setup/server</p>
cp -r /certs /etc/nginx</p>
cd / </p>
chmod 777 -R /certs</p>
cd /etc/nginx/sites-available</p>
touch proxy.conf</p>
vim proxy.conf</p>
ln -s /etc/nginx/sites-available/proxy.conf /etc/nginx/sites-enabled/proxy.conf</p>

systemctl restart nginx</p>
systemctl status nginx</p>

### Get request to the domain endpoint by terminal</p>










