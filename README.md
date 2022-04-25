# Geek_Brains_Services
Zabbix Server install

Install and configure Zabbix server for your platform
a. Install Zabbix repository
Documentation
# rpm -Uvh https://repo.zabbix.com/zabbix/5.0/rhel/7/x86_64/zabbix-release-5.0-1.el7.noarch.rpm
# yum clean all
b. Install Zabbix server and agent
# yum install zabbix-server-mysql zabbix-agent
c. Install Zabbix frontend
Documentation
Enable Red Hat Software Collections

# yum-config-manager --enable rhel-server-rhscl-7-rpms
Edit file /etc/yum.repos.d/zabbix.repo and enable zabbix-frontend repository.

[zabbix-frontend]
...
enabled=1
...
Install Zabbix frontend packages.
# yum install centos-release-scl
# yum install zabbix-web-mysql-scl zabbix-nginx-conf-scl
# yum install maria-db-server

d. Create initial database
Documentation
Make sure you have database server up and running.

Run the following on your database host.

# mysql -uroot -p
password
mysql> create database zabbix character set utf8 collate utf8_bin;
mysql> create user zabbix@localhost identified by 'password';
mysql> grant all privileges on zabbix.* to zabbix@localhost;
mysql> quit;
On Zabbix server host import initial schema and data. You will be prompted to enter your newly created password.

# zcat /usr/share/doc/zabbix-server-mysql*/create.sql.gz | mysql -uzabbix -p zabbix
e. Configure the database for Zabbix server
Edit file /etc/zabbix/zabbix_server.conf

DBPassword=password
f. Configure PHP for Zabbix frontend
Edit file /etc/opt/rh/rh-nginx116/nginx/conf.d/zabbix.conf, uncomment and set 'listen' and 'server_name' directives.

# listen 80;
# server_name example.com;
Edit file /etc/opt/rh/rh-php72/php-fpm.d/zabbix.conf, add nginx to listen.acl_users directive.

listen.acl_users = apache,nginx
Then uncomment and set the right timezone for you.

; php_value[date.timezone] = Europe/Riga
g. Start Zabbix server and agent processes
Start Zabbix server and agent processes and make it start at system boot.

# systemctl restart zabbix-server zabbix-agent rh-nginx116-nginx rh-php72-php-fpm
# systemctl enable zabbix-server zabbix-agent rh-nginx116-nginx rh-php72-php-fpm
h. Configure Zabbix frontend
Connect to your newly installed Zabbix frontend: http://server_ip_or_name
Follow steps described in Zabbix documentation: Installing frontend
