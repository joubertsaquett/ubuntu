### Passo 1 — Instalação do Apache e Atualização do Firewall
Instale o Apache utilizando o gerenciador de pacotes do Ubuntu, apt:
- sudo apt update
- sudo apt install apache2

### Ajustar o Firewall para Permitir Tráfego Web
- sudo ufw app list

> "Available applications:
  Apache
  Apache Full
  Apache Secure
  OpenSSH"

- sudo ufw app info "Apache Full"
- sudo ufw allow in "Apache Full"
- http://ip_do_seu_servidor


### Como Encontrar o Endereço IP Público do seu Servidor
- ip addr show eth0 | grep inet | awk '{ print $2; }' | sed 's/\/.*$//'


### Passo 2 — Instalação do MySQL
- sudo apt install mysql-server
	- Apert Y para continuar
- sudo mysql_secure_installation
	- Você será perguntado se você quer configurar o VALIDATE PASSWORD PLUGIN. Reponda Y.
	- Será perguntado sobre a senha o grau de dificuldade 1,2 e 3. E exibirá o campo para alteração da senha.
	- Para o restante das perguntas, pressione Y.


### Passo 3 — Instalação do PHP
- sudo add-apt-repository ppa:ondrej/php
- sudo apt-get update
- sudo apt install php7.1
- sudo apt install php7.1-cli php7.1-common php7.1-json php7.1-opcache php7.1-mysql php7.1-mbstring php7.1-mcrypt php7.1-zip
- sudo nano /etc/php/7.1/apache2/php.ini

> "file_uploads = On
allow_url_fopen = On
memory_limit = 256M
upload_max_filesize = 64M
max_execution_time = 30
display_errors = Off
max_input_vars = 1500
date.timezone = America/Chicago"

- caso tenha outra versão do PHP
	- sudo a2dismod php7.2
- sudo a2enmod php7.1
- sudo systemctl restart apache2.service

- sudo nano /etc/apache2/mods-enabled/dir.conf
Altere para este:
> "< IfModule mod_dir.c >
    DirectoryIndex index.php index.html index.cgi index.pl index.xhtml index.htm
< /IfModule >"

- sudo systemctl restart apache2
- sudo systemctl status apache2

- sudo apt install php-cli

### Passo 4 — Testando o Processamento PHP no seu Servidor Web
- sudo nano /var/www/html/info.php
> "<?php
phpinfo();
?>"
- http://ip_do_seu_servidor/info.php
	- para remover o arquivo use: sudo rm /var/www/html/info.php


### Passo 5 criar mais sites
Entre na pasta www
- cd /var/www
Crie a pasta do projeto
- mkdir teste
Conceder permissões
- sudo chmod  -R  777   /var/www/teste

- nano /var/www/teste/index.html
	- preencha/escreva o arquivo.

vá para esta pasta 
- cd /etc/apache2/sites-available/
- nano /etc/apache2/sites-available/teste.conf
conteudo:
< VirtualHost *:80>
ServerAdmin suporte@tecsysteminformatica.com
ServerName teste.com
ServerAlias www.teste.com
DocumentRoot /var/www/teste
ErrorLog ${APACHE_LOG_DIR}/error.log
CustomLog ${APACHE_LOG_DIR}/access.log combined

< Directory /var/www/teste>
Options Indexes FollowSymLinks MultiViews
AllowOverride All
Order allow,deny
allow from all
< /Directory>

criando os aqruivos de Virtual Host
- sudo a2ensite teste.conf
	- sudo a2dissite 000-default.conf
- sudo systemctl restart apache2

Antes de reiniciar o Apache Adicione o seguimte usuario para que possa subir imagens e alteras arquivos via web:
- chown  -R  www-data:www-data  /var/www/teste

obs. Caso o wordpress pessa configurações de ftp use este comando acima para a pasta uploads.
- sudo service apache2 restart

Tambem precisamos alteras o arquivo hosts:
- sudo nano /etc/hosts
Acrescenten no final do arquivo:
- 111.111.111.111 test.com
pronto
- http://teste.com


### Passo 6 Installar phpMyAdmin
- sudo apt-get update
- sudo apt-get install phpmyadmin
- sudo php5enmod mcrypt
- sudo service apache2 restart

Alterar a senha de roo:
- sudo mysql
- SELECT user,authentication_string,plugin,host FROM mysql.user;
- ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'password';
- FLUSH PRIVILEGES;
- SELECT user,authentication_string,plugin,host FROM mysql.user;
- http://domain_name_or_IP/phpmyadmin
- Interessante bloquear a visualização da tela de login. (em breve)
Caso não carregue (404):
- sudo cp /etc/phpmyadmin/apache.conf /etc/apache2/conf-enabled/phpmyadmin.conf
	- em alguns casos:
		- nano /etc/apache2/apache2.conf
			acrescente: Include /etc/phpmyadmin/apache.conf


### Ativar SOAP
- sudo apt-get install php7.1-soap
- apt-cache policy php7.1-soap
- sudo service apache2 restart

### Install DOMDocument
- sudo apt-get install php7.1-xml
- sudo service apache2 restart


###Tradução
https://www.vivaolinux.com.br/topico/SQL-em-geral-1/problema-no-locale-settings
