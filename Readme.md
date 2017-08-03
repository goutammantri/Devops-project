#Here we are installing httpd server and generating self signed certificates to secure the httpd server. when the request is http it will run on port 80 if the request is https then it will run on port 443.


httpd server
============

Playbook to install and configure httpd server

Requirement
-----------

Redhat,centos

Playbook Example
---------------

hosts: webserver
roles:
    - install Webserver

Role Variables
--------------

Devops-project/group_vars/all

# Installation Variables
---------------------------
http_port: 80
https_port: 443
ssl_certs_privkey_path: /etc/pki/tls/private/ca.key
ssl_certs_key_size: 2048
ssl_certs_csr_path: /etc/pki/tls/private/ca.csr
ssl_certs_cert_path: /etc/pki/tls/certs/ca.crt
ssl_certs_days: 365
ssl_certs_country: "US"
ssl_certs_locality: "New York"
ssl_certs_organization: "Your company"
ssl_certs_state: "New York"
ssl_certs_common_name: "{{ansible_fqdn}}"
ssl_certs_fields: "/C={{ssl_certs_country}}/ST={{ssl_certs_state}}/L={{ssl_certs_locality}}/O={{ssl_certs_organization}}/CN={{ssl_certs_common_name}}"
ip_address: 

Steps for the Installing the HTTPD server
-------------------------------------------------
Step 1- Install httpd 
sudo yum install httpd

Step 2- Install SSL 
sudo yum install mod_ssl && openssl

Step 3- Generate SSL Certs
create ca.key
openssl genrsa -out {{ ssl_certs_privkey_path }} {{ ssl_certs_key_size }}

create ca.csr
openssl req -new -sha256 -subj "{{ ssl_certs_fields }}" -key {{ ssl_certs_privkey_path }} -out {{ ssl_certs_csr_path }}

create ca.crt
openssl x509 -req -days {{ ssl_certs_days }} -in {{ ssl_certs_csr_path }} -signkey {{ ssl_certs_privkey_path }} -out {{ ssl_certs_cert_path }}

Step 4- Edit httpd.conf and ssl.conf

<VirtualHost *:443>
 SSLEngine on
 SSLCertificateFile /etc/pki/tls/certs/ca.crt
 SSLCertificateKeyFile /etc/pki/tls/private/ca.key
 servername {{ ip_address }}
 Documentroot /var/www/html
</VirtualHost>


Step 5- Install Firewalld 
sudo yum install firewalld

Step 6- Add firewalld rule
Add the https  port number/TCP

Step 7- Restart the httpd server

Running the tests
------------------

Using the URI module ping to the server ip and register the output into a variable and using the debug module you can see the stored output.If the ping is failed we get a message web application installation 
unsuccessful.

Dependencies
------------
 NONE
 
Author
-----
Mantri Goutam @goutammantri@gmail.com