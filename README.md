# Práctica 1.4 - Docker y primer contenedor

## Objetivo

El objetivo de esta práctica es desplegar el backend TaskFlow utilizando Docker para comprender el funcionamiento de la virtualización basada en contenedores y compararla con una instalación tradicional sobre el sistema operativo.

---

# Instalación de Docker

Se instaló Docker Engine en Ubuntu Server.

Comprobación de la instalación:

```bash
docker --version
```
<img width="986" height="68" alt="image" src="https://github.com/user-attachments/assets/6cb4aa20-aed0-4950-a337-91e1f18565d8" />

Comprobación del servicio:

```bash
systemctl status docker
```
<img width="1081" height="113" alt="image" src="https://github.com/user-attachments/assets/b87798dd-52b7-49e8-b54b-08214233d03e" />

Habilitación del arranque automático:

```bash
sudo systemctl enable docker
```
<img width="1120" height="79" alt="image" src="https://github.com/user-attachments/assets/172b5b71-2c9e-409b-b706-9e162771028f" />

---

# Postinstalación

Se añadió el usuario al grupo Docker para evitar el uso de `sudo` en cada comando.

```bash
sudo usermod -aG docker $USER
```

Aplicación inmediata de cambios:

```bash
newgrp docker
```

Verificación:

```bash
docker ps
```
<img width="1721" height="100" alt="image" src="https://github.com/user-attachments/assets/724e0e58-674f-4036-98db-679562a0b6a0" />

La ejecución correcta de este comando sin utilizar privilegios de administrador confirma que la postinstalación se ha realizado correctamente.

---

# Construcción de la imagen Docker

Para contenerizar la aplicación se creó un fichero `Dockerfile`.

## Dockerfile utilizado

```dockerfile
FROM python:3.13-slim

WORKDIR /app

COPY requirements.txt .

RUN pip install --no-cache-dir -r requirements.txt

COPY . .

EXPOSE 8000

CMD ["gunicorn","app.main:app","--worker-class","uvicorn.workers.UvicornWorker","--bind","0.0.0.0:8000"]
```

Construcción de la imagen:

```bash
docker build -t taskflow-api .
```

Comprobación:

```bash
docker images
```

Resultado 
<img width="936" height="158" alt="image" src="https://github.com/user-attachments/assets/fefb62f8-d6a7-4b68-b2a7-295c94f2cf69" />

---

# Ejecución del contenedor

Arranque del contenedor:

```bash
docker run -d --name taskflow -p 8000:8000 taskflow-api
```

Comprobación:

```bash
docker ps
```
<img width="1727" height="72" alt="image" src="https://github.com/user-attachments/assets/514f1cd8-58e8-45bd-967e-5fa39ecd9583" />

La salida muestra el contenedor en ejecución y el puerto 8000 publicado en el host.

---

# Verificación del funcionamiento

## Endpoint health

```bash
curl http://localhost:8000/health
```

Respuesta obtenida:

<img width="1178" height="48" alt="image" src="https://github.com/user-attachments/assets/080fc641-dd55-435d-844d-d22305480c6a" />


## Endpoint ping

```bash
curl http://localhost:8000/ping
```

Respuesta obtenida:

<img width="1154" height="51" alt="image" src="https://github.com/user-attachments/assets/3c9f2f6f-c1fe-4109-b25e-198e79c10dab" />


## Endpoint version

```bash
curl http://localhost:8000/version
```

Respuesta obtenida:

<img width="1195" height="55" alt="image" src="https://github.com/user-attachments/assets/0609f252-de08-4b37-a69e-17871e87f516" />


---

# Acceso a Swagger

FastAPI genera automáticamente una interfaz Swagger para la documentación y prueba de la API.

Acceso:

```text
http://IP_DEL_SERVIDOR:8000/docs
```

Ejemplo:

```text
http://192.168.1.152:8000/docs
```
<img width="1897" height="959" alt="image" src="https://github.com/user-attachments/assets/ef3be5e5-c045-4f99-a99c-810edfc37a20" />

Desde Swagger es posible consultar y probar todos los endpoints disponibles de la aplicación.

---

# Ciclo de vida de un contenedor Docker

## Iniciar

```bash
docker start taskflow
```

## Detener

```bash
docker stop taskflow
```

## Reiniciar

```bash
docker restart taskflow
```

## Eliminar

```bash
docker rm -f taskflow
```

Estas operaciones permiten gestionar completamente el ciclo de vida del contenedor.

---

# Diferencia entre contenedor y máquina virtual

## Máquina virtual

Una máquina virtual ejecuta un sistema operativo completo sobre un hipervisor.

Características:

- Incluye kernel y sistema operativo propios.
- Mayor consumo de RAM y almacenamiento.
- Arranque más lento.
- Mayor aislamiento.

Ejemplos:

- VirtualBox
- VMware
- Hyper-V

## Contenedor

Un contenedor comparte el kernel del sistema anfitrión.

Características:

- Menor consumo de recursos.
- Arranque muy rápido.
- Despliegue sencillo.
- Alta portabilidad.

Ejemplo:

- Docker

---

# Aplicación al proyecto TaskFlow

En este proyecto Docker permite empaquetar en una única imagen:

- Código fuente.
- Dependencias Python.
- Configuración.
- Entorno de ejecución.

Esto garantiza que la aplicación funcione exactamente igual en cualquier servidor que disponga de Docker.

La principal ventaja es eliminar problemas relacionados con diferencias entre sistemas operativos o versiones de dependencias.

---

# Comparación: instalación directa frente a Docker

## Instalación directa

### Ventajas

- Simplicidad inicial.
- Menor número de herramientas necesarias.
- Acceso directo al sistema operativo.

### Inconvenientes

- Dependencias instaladas directamente en el servidor.
- Riesgo de conflictos entre aplicaciones.
- Menor portabilidad.
- Configuraciones difíciles de reproducir.

---

## Instalación mediante Docker

### Ventajas

- Entorno reproducible.
- Portabilidad total.
- Aislamiento entre aplicaciones.
- Facilidad para desplegar actualizaciones.
- Escalabilidad.

### Inconvenientes

- Curva de aprendizaje inicial.
- Necesidad de gestionar imágenes y contenedores.
- Ligera complejidad adicional respecto a una instalación tradicional.

---

# Relación con la nube

Docker se utiliza ampliamente en plataformas cloud modernas como:

- Microsoft Azure
- Amazon Web Services (AWS)
- Google Cloud Platform (GCP)

Los contenedores facilitan:

- Escalado automático.
- Alta disponibilidad.
- Despliegues rápidos.
- Integración continua.
- Infraestructura como código.

Gracias a Docker, una aplicación desarrollada localmente puede desplegarse en la nube de forma prácticamente idéntica.

---
