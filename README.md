# Actividad 2.2 — Instalación y despliegue con Nginx

## Despregamento de aplicacións web  
**Autor:** Matías Pérez Bustelo

En esta actividad instalaremos y configuraremos **Nginx** en una máquina virtual Ubuntu 22.04 y desplegaremos un sitio web estático sencillo con navegación, imágenes y estilos.

---

## 1. Preparación de la máquina
Hacemos un **clon enlazado** de la máquina proporcionada para hacer el ejercicio.

<img width="1004" height="42" alt="image" src="https://github.com/user-attachments/assets/09b94bd6-848c-4226-b4c0-22c0ed70040b" />

---

### 1.1 Configuración de la red
- Configuramos la **redirección de puertos**:
  - Eliminamos la configuración anterior.  
<img width="997" height="91" alt="image" src="https://github.com/user-attachments/assets/b2d18e6c-6d52-46e7-a21f-1aa5ae1ee6a6" />

  - Añadimos las nuevas reglas de NAT (para Nginx en el puerto 8082).  
   <img width="1004" height="83" alt="image" src="https://github.com/user-attachments/assets/499230a2-2922-4f97-a28e-cc4a3e00519d" />


- Iniciamos la máquina.

---

## 2. Conexión por SSH
Nos conectamos a la VM mediante SSH usando el puerto configurado:

```bash
ssh -p 2224 usuario@localhost
```


<img width="727" height="47" alt="image" src="https://github.com/user-attachments/assets/1f7d50d2-c27a-44e8-b10f-4f2338cf0f3d" />


---

## 3. Instalación de Nginx y configuración del firewall

1. Actualizamos los paquetes:

```bash
sudo apt update && sudo apt upgrade -y
```
<img width="919" height="55" alt="image" src="https://github.com/user-attachments/assets/e2d8d411-bad5-43b3-8736-3b737fc4df85" />

2. Instalamos **Nginx**:

```bash
sudo apt install nginx -y
```

<img width="1004" height="335" alt="image" src="https://github.com/user-attachments/assets/105d664e-c0e2-4467-86c4-52939253fe7e" />

3. Permitimos Nginx y SSH en el firewall:

```bash
sudo ufw allow 'Nginx Full'
sudo ufw allow ssh
sudo ufw allow http
sudo ufw allow https
sudo ufw enable
```

<img width="1004" height="250" alt="image" src="https://github.com/user-attachments/assets/95e76cd7-c50d-4125-bfb8-c64956139e45" />

<img width="794" height="428" alt="image" src="https://github.com/user-attachments/assets/59852537-76e0-4960-b4ec-ead7ccc1b01c" />


4. Comprobamos el servicio:

```bash
sudo systemctl status nginx
```

<img width="1004" height="317" alt="image" src="https://github.com/user-attachments/assets/f5e76072-defe-4641-aa7c-3919dd303666" />


---

## 4. Comprobación de la página por defecto
- Navegamos a: **http://localhost:8082/**
- Deberíamos ver la página por defecto de Nginx.

<img width="1004" height="360" alt="image" src="https://github.com/user-attachments/assets/5fa990ff-86dd-4373-973a-63a7573c99b5" />


---

## 5. Despliegue de nuestra web

### 5.1 Copiar los archivos del sitio web
Copiamos los archivos del proyecto desde nuestra máquina host hacia la máquina virtual (o desde otra carpeta interna).

<img width="1004" height="181" alt="image" src="https://github.com/user-attachments/assets/67d725cb-6785-41d9-b6ea-34dc05c8110d" />


---

### 5.2 Crear el directorio raíz del sitio

Creamos el directorio raíz del sitio web y borramos lo que ya tenga si existía :

```bash
sudo rm -rf /var/www/sitio_ejemplo
sudo mkdir -p /var/www/sitio_ejemplo
```

<img width="916" height="286" alt="image" src="https://github.com/user-attachments/assets/f6604e6e-e425-4d09-a6b1-fcf40d819dd1" />

---

### 5.3 Desactivar el sitio por defecto de Nginx

```bash
sudo unlink /etc/nginx/sites-enabled/default
```

<img width="966" height="34" alt="image" src="https://github.com/user-attachments/assets/fa1ec8a1-3d4e-4af2-beb0-a365d5b06ea4" />

---

### 5.4 Activar nuestro sitio
Creamos el archivo de configuración de nuestro sitio:

```bash
sudo nano /etc/nginx/sites-available/sitio_ejemplo
```




Con algo como:

```nginx
server {
    listen 80;
    server_name localhost;

    root /var/www/sitio_ejemplo;
    index index.html;
}
```

<img width="916" height="286" alt="image" src="https://github.com/user-attachments/assets/4f8193c4-e5d6-4117-a5e5-9d5e1485611c" />


Lo habilitamos:

```bash
sudo ln -s /etc/nginx/sites-available/sitio_ejemplo /etc/nginx/sites-enabled/
```

<img width="1004" height="21" alt="image" src="https://github.com/user-attachments/assets/e597f4ae-ddbd-4d46-9b6a-8844f75142c9" />

---

### 5.5 Mover los archivos al directorio de Nginx
Movemos la carpeta de la web a `/var/www` y asignamos permisos:

```bash
sudo mv ~/practicas/nginx/ejemplo_sitio_nginx /var/www/sitio_ejemplo
sudo chown -R www-data:www-data /var/www/sitio_ejemplo
```

<img width="1004" height="42" alt="image" src="https://github.com/user-attachments/assets/1a04c85c-77b6-4164-b102-c5fcb9194908" />

---

## 6. Comprobación de la configuración

Validamos la sintaxis de la configuración:

```bash
sudo nginx -t
```

<img width="950" height="100" alt="image" src="https://github.com/user-attachments/assets/ba22aea6-8c44-434f-9b3f-9973d4696ec6" />


Si es correcta, reiniciamos:

```bash
sudo systemctl restart nginx
```

---

## 7. Resultado final
- Accedemos a: **http://localhost:8082**
- La web desplegada debería mostrarse correctamente con imágenes, estilos y navegación.

<img width="1004" height="667" alt="image" src="https://github.com/user-attachments/assets/5213a015-1a70-4b4e-82f1-4efbd9467be6" />

