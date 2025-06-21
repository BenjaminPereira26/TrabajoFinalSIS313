# Base de datos 1

<div align="justify">
La base de datos bd1 se encarga de gestionar y almacenar toda la información central del sistema, específicamente los datos relacionados con los productos de la tienda. Su función principal dentro del proyecto es ofrecer un respaldo estructurado a las aplicaciones (app1 y app2), permitiendo realizar operaciones CRUD (crear, leer, actualizar y eliminar productos) a través de consultas SQL. Esta base de datos corre en un servidor MariaDB configurado para aceptar conexiones remotas, lo que permite que las aplicaciones accedan a ella desde otras máquinas dentro de la red, facilitando así la disponibilidad y coherencia de los datos en todo el sistema.

## **Instalación y configuración de MariaDB**
* **Instalar MariaDB Server:**
```bash
sudo apt update
sudo apt install mariadb-server -y
   ```
* **Ejecutar el script de seguridad inicial:**
```bash
sudo mysql_secure_installation
   ```
- Se desactiva el usuario anónimo.
- Se deshabilita el acceso remoto de root.
- Se elimina la base de datos de prueba.
- Se mantiene la autenticación por contraseña.
- Se recargan los privilegios.
- 
## **Base de datos en MariaDB:**
* **Ingresar al cliente de MariaDB:**
```bash
sudo mysql -u root -p
   ```
* **Crear la base de datos:**
En el caso de proyectos es una BD simple que simula un catálogo de productos y sus precios, alterables por medio de CRUD en las Apps
* **Ingresar al cliente de MariaDB:**
```bash
CREATE DATABASE tienda;
   ```
* **Crear BD:**
```bash
CREATE DATABASE tienda;
   ```
* **Crear BD usuario para la aplicación:**

 ```bash
CREATE USER 'appuser'@'%' IDENTIFIED BY 'App123@';
GRANT ALL PRIVILEGES ON tienda.* TO 'appuser'@'%';
FLUSH PRIVILEGES;
  ```

* **Usar la base de datos y crear tabla:**
 ```bash
USE tienda;

CREATE TABLE productos (
  id INT AUTO_INCREMENT PRIMARY KEY,
  nombre VARCHAR(100),
  precio DECIMAL(10,2)
);

  ```

* **Insertar productos de ejemplo:**
 ```bash
INSERT INTO productos (nombre, precio) VALUES
('Laptop', 4500.00),
('Mouse', 99.90),
('Teclado', 120.50);
  ```

## **Habilitar conexión remota**

* **Editar archivo de configuración:**
 ```bash
sudo nano /etc/mysql/mariadb.conf.d/50-server.cnf
  ```

Dentro cambiar:
 ```bash
bind-address = 127.0.0.1
  ```
Por:
 ```bash
bind-address = 0.0.0.0
  ```

* **Reiniciar el servicio MariaDB:**
 ```bash
sudo systemctl restart mariadb
  ```

* **Verificar que el puerto 3306 esté escuchando:**
 ```bash
ss -tlnp | grep 3306
  ```

# **Configuración de la Base de Datos como maestra:**
* **Editar el archivo de configuración:**
 ```bash
sudo nano /etc/mysql/mariadb.conf.d/50-server.cnf
  ```
Dentro se añade:
 ```bash
bind-address = 0.0.0.0
server-id = 1
log_bin = /var/log/mysql/mariadb-bin
binlog_do_db = tienda       # (opcional si solo replicas esa base)
  ```
Asegúrurase de que el archivo de log exista o que MariaDB lo pueda crear.
* **Reiniciar el servicio:**
 ```bash
sudo systemctl restart mariadb
  ```

## **Crear el usuario de replicación**
* **Conectar a MariaDB:**
 ```bash
sudo mariadb
  ```
Ejecutar:
 ```bash
CREATE USER 'replicador'@'%' IDENTIFIED BY 'TuPasswordFuerte';
GRANT REPLICATION SLAVE ON *.* TO 'replicador'@'%';
FLUSH PRIVILEGES;
  ```
* **Ver estado del log binario:**
 ```bash
SHOW MASTER STATUS;
  ```
Debería devolver algo como:
 ```bash
+--------------------+----------+--------------+------------------+
| File               | Position | Binlog_Do_DB | Binlog_Ignore_DB |
+--------------------+----------+--------------+------------------+
| mariadb-bin.000001 |      330 | tienda       |                  |
+--------------------+----------+--------------+------------------+
  ```

* **Crear dump de la base tienda con metadatos para replicación:**
 ```bash
sudo mysqldump --databases tienda --master-data > tienda.sql
  ```

* **Copiar el dump a la esclava:**
 ```bash
scp -P 2205 tienda.sql ubu2@192.168.100.50:/home/usuario/
  ```
</div>
