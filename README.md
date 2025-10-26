# Proyecto: Aplicación Spring Boot "Hello World" en Docker y AWS EC2

## 📘 Descripción General

Este proyecto consiste en una aplicación básica desarrollada con **Spring Boot** utilizando **Maven**, cuyo objetivo es mostrar un mensaje `"Hello, World!"` cuando se realiza una solicitud HTTP al endpoint `/greeting`.

Posteriormente, esta aplicación se **dockerizó** y se crearon múltiples contenedores a partir de la imagen generada.  
Finalmente, la imagen fue **publicada en DockerHub** y desplegada en una instancia **AWS EC2**, abriendo el puerto necesario para su acceso remoto.

---

## 🧱 Arquitectura del Proyecto

La arquitectura general se compone de los siguientes elementos:

- **Aplicación principal (Spring Boot)**: Genera el mensaje `"Hello, World!"`.
- **Contenedores Docker (3 instancias)**: Cada uno ejecuta la aplicación en diferentes puertos.
- **Repositorio DockerHub**: Contiene la imagen pública del proyecto (`seanot26/firstspringapp`).
- **Instancia EC2 en AWS**: Donde se ejecuta la imagen desde DockerHub.
- **Contenedor adicional MongoDB (`mongo:3.6.1`)**: Creado para pruebas, pero sin interacción con la aplicación.
- **Contenedor adicional “web” (`virtualizacionydocker-web`)**: No participa directamente en la ejecución principal.

**Estructura visual del sistema desplegado:**

<img width="278" height="212" alt="image" src="https://github.com/user-attachments/assets/3fe9d351-58eb-40d3-ad4d-0c715cf81bfa" />



---

## ⚙️ Configuración Local

### 1. Clonar el proyecto y compilar con Maven

```bash
mvn clean package
```

### 2. Ejecutar la aplicación localmente

El siguiente comando ejecuta la aplicación directamente con Java, usando el classpath generado por Maven:

```bash
java -cp "target/classes;target/dependency/*" com.taller.virtualizacionydocker.VirtualizacionydockerApplication
```

Al acceder a `http://localhost:8080/greeting` se mostrará el mensaje:

```
Hello, World!
```

<img width="337" height="149" alt="image" src="https://github.com/user-attachments/assets/8c6ee876-3f37-4890-a592-17db7daf6433" />


---

## 🐳 Creación y ejecución de contenedores Docker

### 1. Construir la imagen Docker

Se creó una imagen Docker a partir del proyecto con el nombre `virtualizacionydocker`.

### 2. Crear contenedores a partir de la imagen

Se generaron tres contenedores independientes, cada uno escuchando en un puerto diferente:

```bash
docker run -d -p 34000:8080 --name firstdockercontainer virtualizacionydocker
docker run -d -p 34001:8080 --name seconddockercontainer virtualizacionydocker
docker run -d -p 34002:8080 --name thirddockercontainer virtualizacionydocker
```



Además, se crearon dos contenedores adicionales para propósitos secundarios:
- **web** (imagen: `virtualizacionydocker-web`)
- **db** (imagen: `mongo:3.6.1`)

<img width="1282" height="381" alt="image" src="https://github.com/user-attachments/assets/ac679866-9c8a-4b86-8703-f2ed64e9db89" />

---

## ☁️ Despliegue en AWS EC2

### 1. Publicación en DockerHub

La imagen fue subida al repositorio público:

👉 **[DockerHub Repository: seanot26/firstspringapp](https://hub.docker.com/r/seanot26/firstspringapp)**

### 2. Ejecución en EC2

En la instancia EC2 se descargó y ejecutó la imagen con el siguiente comando:

```bash
docker run -d -p 42000:8080 --name firstdockerimageaws seanot26/firstspringapp
```

<img width="2404" height="559" alt="image" src="https://github.com/user-attachments/assets/780d1fa7-0167-4a7d-aac5-4dbe1fa4f9c4" />

---

### 3. Configuración del Grupo de Seguridad en AWS

Fue necesario habilitar el puerto **42000** en el grupo de seguridad asociado a la instancia, permitiendo el acceso desde cualquier dirección IP (`0.0.0.0/0`).

<img width="1755" height="467" alt="image" src="https://github.com/user-attachments/assets/d552eaf2-43e3-4495-a4f3-1659dd61540e" />


---

## 🔍 Pruebas y Validación

Una vez ejecutado el contenedor en EC2, se validó el correcto despliegue accediendo desde el navegador:

```
http://<IP_PUBLICA_EC2>:42000/greeting
```

El resultado mostrado fue:

```
Hello, World!
```

[imagen de salida Hello World en navegador aquí]

---

## 🧾 Logs y Estado de Contenedores

Desde la terminal de la instancia EC2 se listaron los contenedores en ejecución, verificando que el contenedor `firstdockerimageaws` estaba **activo** y mapeado correctamente al puerto 42000:

```bash
docker ps
```

---

## 📤 Generación de nuevas imágenes de despliegue

Para crear y subir una nueva versión de la imagen:

```bash
docker build -t seanot26/firstspringapp:v2 .
docker push seanot26/firstspringapp:v2
```

Luego se puede desplegar la nueva versión en EC2:

```bash
docker run -d -p 42000:8080 --name firstdockerimageaws_v2 seanot26/firstspringapp:v2
```

---

## 🧩 Conclusión

Este laboratorio demuestra el flujo completo desde el desarrollo de una aplicación básica en Spring Boot hasta su despliegue en la nube utilizando Docker y AWS EC2.

El proceso incluye:
- Compilación y ejecución local.  
- Dockerización del proyecto.  
- Ejecución múltiple en contenedores.  
- Publicación en DockerHub.  
- Despliegue remoto en AWS con acceso público.  

---

**Autor:** Sergio Andrés Otero Herrera  
**Repositorio DockerHub:** [seanot26/firstspringapp](https://hub.docker.com/r/seanot26/firstspringapp)
