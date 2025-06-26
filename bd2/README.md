# **Base de datos 2**
<div align="justify">

**Objetivo dentro del proyecto:** 

La base de datos 2 como esclava, está configurada para conectarse al maestro y replicar en tiempo real todos los cambios realizados sobre la base de datos tienda. Funciona solo en modo lectura, asegurando la disponibilidad de datos sin afectar la carga del maestro. Se configuró con su propio server-id, se importó el respaldo inicial desde el maestro y se estableció la conexión mediante el usuario replicador. Una vez iniciada, la esclava mantiene la sincronización automáticamente, actuando como respaldo o punto de lectura para balanceo de carga.

## **Instalación y configuración de MariaDB**
* **Instalar MariaDB Server:**
```bash
sudo apt update
sudo apt install mariadb-server -y
   ```

* **Configuración:**
```bash
sudo nano /etc/mysql/mariadb.conf.d/50-server.cnf
   ```
Dentro añadir las configuraciones:
```bash
bind-address = 0.0.0.0
server-id = 2
relay-log = /var/log/mysql/mariadb-relay-bin
   ```
Asegurarse de que el directorio /var/log/mysql/ exista o se pueda crear.

* **Reiniciar el servicio:**
```bash
sudo systemctl restart mariadb
   ```
## **Configuración de MariaDB como esclava**
* **Copiar el dump desde el maestro:**
```bash
scp -P 2205 tienda.sql ubu2@192.168.100.50:/home/usuario/
   ```
* **En caso de problemas dar permisos a la carpeta donde se copiara la bd:**
```bash
sudo chown bd2:bd2 /home/usuario/
sudo chmod u+w /home/usuario/
   ```


* **Importar el dump en la esclava:**
```bash
sudo mysql < /home/usuario/tienda.sql
   ```
* **Configurar replicación (conectar al maestro):**
```bash
sudo mysql < /home/usuario/tienda.sql
   ```
* **En caso de problemas:**
```bash
sudo mysql -u root tienda < /home/usuario/tienda.sql

sudo mysql -u root -p tienda < /home/usuario/tienda.sql
   ```
O:
```bash
sudo mysql -u root

CREATE DATABASE tienda;


USE tienda;
SOURCE /home/usuario/tienda.sql;
   ```

Y volver a importar: 
```bash
sudo mysql -u root -p tienda < /home/usuario/tienda.sql
   ```


Asegurarse de que /var/log/mysql existe, sino:
```bash
sudo mkdir -p /var/log/mysql
sudo chown mysql:mysql /var/log/mysql
sudo systemctl restart mariadb

   ```


* **Configurar replicación (conectar al maestro):**
Entrar a MariaDB
```bash
sudo mariadb
   ```

Y dentro de MariaDB:
```bash
CHANGE MASTER TO
  MASTER_HOST='192.168.100.40',
  MASTER_USER='replicador',
  MASTER_PASSWORD='TuPasswordFuerte',
  MASTER_LOG_FILE='mariadb-bin.000001',
  MASTER_LOG_POS=330;
   ```
* **Iniciar replicación:**
```bash
START SLAVE;
   ```

* **Verificar estado de replicación:**
```bash
SHOW SLAVE STATUS\G
   ```
De ser todo correcto se visualizaría:
```bash
Slave_IO_Running: Yes
Slave_SQL_Running: Yes
Seconds_Behind_Master: 0
   ```


# **RAID para la bd:**
* **Verificar estado de discos:**
```bash
sudo fdisk -l
   ```


* **Instalar manejador de mdadm:**
```bash
sudo apt update
sudo apt install mdadm
   ```

* **Crear el RAID:**
 ```bash
sudo mdadm --create --verbose /dev/md0 --level=1 --raid-devices=2 /dev/sdb /dev/sdc
   ```

* **Verificar estado del raid:**
 ```bash
cat /proc/mdstat
sudo mdadm --detail /dev/md0
   ```

* **Crear sistema de archivos:**
 ```bash
sudo mkfs.ext4 /dev/md0
   ```

* **Montar el RAID en una carpeta:**
 ```bash
sudo mkdir /mnt/raid1
sudo mount /dev/md0 /mnt/raid1
   ```

* **Añadir a /etc/fstab:**
 ```bash
sudo nano /etc/fstab


UUID=tu-uuid-obtenido /mnt/raid1 ext4 defaults 0 0
   ```


* **Añadir a /etc/fstab:**
 ```bash
sudo nano /etc/fstab


UUID=tu-uuid-obtenido /mnt/raid1 ext4 defaults 0 0
   ```


* **Crear el punto de montaje y montar el RAID ya sin reiniciar:**
 ```bash
sudo mkdir -p /mnt/raid1
sudo mount -a
   ```


* **Detener servicio de MariaDB:**
 ```bash
sudo systemctl stop mariadb
   ```

* **Mover la carpeta de datos actual de MariaDB a la nueva ubicación:**
 ```bash
sudo rsync -av /var/lib/mysql /mnt/raid1/
   ```

* **Cambiar el propietario de los datos en la nueva ubicación:**
 ```bash
sudo chown -R mysql:mysql /mnt/raid1/mysql
   ```

* **Cambiar el archivo de configuración para que MariaDB use el nuevo datadir:**
 ```bash
sudo nano /etc/mysql/mariadb.conf.d/50-server.cnf

datadir = /var/lib/mysql #cambiar a:
datadir = /mnt/raid1/mysql
   ```

* **Reiniciar MariaDB y verificar que funcione:**
 ```bash
sudo systemctl start mariadb
sudo systemctl status mariadb
   ```
* **Verificar que MariaDB esté usando el nuevo directorio de datos:**
 ```bash
sudo lsof -n | grep /mnt/raid1/mysql
   ```

* **Verificar que MariaDB esté funcionando correctamente y no haya perdido la base de datos:**
 ```bash
sudo systemctl status mariadb
mysql -u root -p -e "SHOW DATABASES;"
   ```
</div>
