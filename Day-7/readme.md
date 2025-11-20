# 🚀 Installation of Zabbix Server, Agent, and Database

This guide walks you through the **step-by-step process** to install and configure **Zabbix server**, **Zabbix agent**, and the **PostgreSQL database** on Ubuntu.

## 🛠️ Step 1: Update System Packages
```bash
sudo apt update
sudo apt upgrade -y
```
## 🛠️ Step 2: Install and Configure PostgreSQL
```bash
sudo apt install postgresql postgresql-contrib -y
```
```bash
sudo -u postgres psql
```
```sql
CREATE USER zabbix WITH PASSWORD 'root';
CREATE DATABASE zabbix OWNER zabbix;
\q
```
## 🛠️ Step 3: Add Zabbix Repository 
- Please check for version before running this i used `6.0` also download the databases of same version.
```bash
wget https://repo.zabbix.com/zabbix/6.0/ubuntu/pool/main/z/zabbix-release/zabbix-release_6.0-1+ubuntu22.04_all.deb
sudo dpkg -i zabbix-release_6.0-1+ubuntu22.04_all.deb
sudo apt install software-properties-common -y
sudo add-apt-repository ppa:ondrej/php
sudo apt update
```
## 🛠️ Step 4: Install Zabbix Components
```bash
sudo apt install zabbix-server-pgsql zabbix-frontend-php zabbix-proxy-pgsql zabbix-agent php8.1-pgsql -y
sudo apt install zabbix-sql-scripts
sudo apt install php-pgsql php-mbstring php-bcmath php-gd php-xml php-json apache2 -y
sudo apt install --reinstall zabbix-frontend-php
```
## 🗄️ Step 5: Import Zabbix Database Schema & Data
- **Download The zabbix-6.0.42.tr.gz** from : https://www.zabbix.com/download_sources#60LTS
- or you can download the v7.0 as per the Previuos zabbix Repository added or downloaded db file from zabbix.
- After that Extract the .gz file using tar
- Then get data.sql,images.sql,schema.sql from `/zabbix-6.0.42/database/postgres/`
- Store the 3 files somewhere and delete the extarcted folder
- **Import schema, images, and data**:
```bash
sudo -u zabbix psql zabbix < /home/aveshpadaya/databases-zabbix/schema.sql
sudo -u zabbix psql zabbix < /home/aveshpadaya/databases-zabbix/images.sql
sudo -u zabbix psql zabbix < /home/aveshpadaya/databases-zabbix/data.sql
```
## ⚙️ Step 6: Configure Zabbix Server
- Edit `/etc/zabbix/zabbix_server.conf`:
```bash
sudo vim /etc/zabbix/zabbix_server.conf
```
- zabbix_server.conf
```conf
DBHost=localhost
DBName=zabbix
DBUser=zabbix
DBPassword=root
```
## 🖥️ Step 7: Start and enable the zabbix-server
```bash
sudo systemctl start zabbix-server
sudo systemctl enable zabbix-server
sudo systemctl status zabbix-server
```
## 🖥️ Step 8: Configure Zabbix Agent
- Check the host name and host ip and server : `/etc/zabbix/zabbix_agentd.conf`
```conf
Server=YOUR_SERVER_IP
ServerActive=YOUR_SERVER_IP
Hostname=your_hostname
```
```bash
sudo systemctl start zabbix-agent
sudo systemctl enable zabbix-agent
sudo systemctl status zabbix-agent
```
## 🌐 Step 8: Start the apache service And access the Zabbix server
```bash
sudo systemctl start apache2
sudo systemctl enable apache2
sudo systemctl status apache2
```
for any error in apche check `/etc/apache2/conf-available/zabbix.conf`
- zabbix.conf 
```conf
Alias /zabbix /usr/share/zabbix

<Directory "/usr/share/zabbix">
    Options FollowSymLinks
    AllowOverride None
    Require all granted
</Directory>

<Directory "/usr/share/zabbix/conf">
    Require all denied
</Directory>

<Directory "/usr/share/zabbix/include">
    Require all denied
</Directory>
```
```bash
sudo systemctl reload apache2
```
- `http://127.0.0.1/zabbix` Access the Zabbix UI
  - USERNAME: `Admin`
  - PASSWORD: `zabbix`
- The Ui Ask to connect the Database Select the below details
  - Database: postgresql
  - Schema: public
  - port: 0
  - Database: zabbix
  - user: zabbix
  - password: root
  - untick the tls 
  - Server name as per your choice 
