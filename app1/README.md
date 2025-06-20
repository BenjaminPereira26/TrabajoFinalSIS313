# **Aplicación 1**
<div align="justify">

**Objetivo dentro del proyecto:** 

La aplicación app1 en el proyecto es una instancia del servicio que provee funcionalidades específicas de un CRUD, como gestionar y mostrar los productos almacenados en la base de datos. Su rol principal es procesar las solicitudes que recibe, acceder a la base de datos para leer o modificar información, y devolver respuestas al usuario o al balanceador. Forma parte del backend que, junto con otras instancias como app2, garantiza que la aplicación sea accesible, confiable y escalable cuando se usa en conjunto con el balanceador.

**Pasos realizados:**

## **Instalación y configuración de Nginx:**
* **Instalación de Nginx:**
```bash
   sudo apt update && sudo apt install nginx
   ```

* **Verificación del Estado del Servicio Nginx:**
```bash
   sudo systemctl status nginx
   ```

En caso de que este no este en estado de running:
 ```bash
   sudo systemctl start nginx
   ```

En caso de que este no este en estado de enable:
 ```bash
   sudo systemctl enable nginx
   ```

* **Instalación de módulos:**
```bash
 sudo apt install php8.3-fpm php8.3-mbstring php8.3-curl php8.3-xml php8.3-mysql php8.3-zip
```

## **Instalación de node.js con nvm**

* **Pasos para la instalación**
```bash
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.40.3/install.sh | bash
```

```bash
\. "$HOME/.nvm/nvm.sh"
```

```bash
node -v
```
debería responder: v22.16.0

```bash
nvm current
```
debería responder: v22.16.0

```bash
npm -v
```
debería responder: 10.9.2

## **Instalación de express js**

</div>
