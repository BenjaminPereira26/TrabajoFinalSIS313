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

* **Importar el dump en la esclava:**
```bash
sudo mysql < /home/usuario/tienda.sql
   ```
* **Configurar replicación (conectar al maestro):**
```bash
sudo mysql < /home/usuario/tienda.sql
   ```
Asegurarse de tener la carpeta /home/usuario

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
</div>
