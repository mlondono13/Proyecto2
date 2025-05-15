# Proyecto2
Estudiantes: Marcela Londoño Leon, mlondonol@eafit.edu.co - Luis Fernando Posada, lfposadac@eafit.edu.co - Juan Martin Betancur, jbetancur5@eafit.edu.co - Jose Miguel Burgos, jmburgoc@eafit.edu.co
Profesor: Edwin Nelson Montoya Munera, emontoya@eafit.edu.co

# Sistema Escalable con AWS Auto Scaling y Almacenamiento Compartido (EFS)

## 1. Breve descripción

Este proyecto despliega una aplicación web monolítica de Flask en AWS utilizando una arquitectura escalable y tolerante a fallos. Se implementa el uso de Auto Scaling Groups (ASG), Elastic Load Balancer (ELB), Amazon RDS y Amazon EFS. Además, se utiliza NGINX como servidor web y reverse proxy. El objetivo principal es asegurar alta disponibilidad y almacenamiento compartido entre instancias EC2.

### 1.1 Aspectos cumplidos

* Despliegue exitoso de una aplicación Flask usando Docker Compose.
* Configuración y uso de un Auto Scaling Group con un Elastic Load Balancer.
* Montaje exitoso de Amazon EFS en múltiples instancias EC2.
* Configuración de NGINX para servir la aplicación y actuar como reverse proxy.
* Configuración de HTTPS con Certbot y NGINX.
* Comunicación entre la app y Amazon RDS para persistencia de datos.

### 1.2 Aspectos no cumplidos

* No se utilizó CI/CD automatizado para el despliegue del entorno.
* No se utilizó ECS o Kubernetes para el orquestamiento de contenedores.

## 2. Información general de diseño de alto nivel, arquitectura, patrones, mejores prácticas utilizadas

La arquitectura del sistema sigue un enfoque de escalabilidad horizontal y almacenamiento compartido. Se usaron las siguientes tecnologías y prácticas:

**Componentes:**

* **Instancia EC2 base:** contiene la app Flask en Docker.
* **Auto Scaling Group:** permite escalar horizontalmente en función de la carga.
* **Elastic Load Balancer (ELB):** distribuye tráfico entre las instancias.
* **Amazon RDS (MySQL):** almacenamiento de base de datos centralizado.
* **Amazon EFS:** almacenamiento compartido entre instancias EC2 montado en `/mnt/shared`.
* **NGINX:** servidor web y reverse proxy configurado en cada instancia.

**Mejores prácticas:**

* Uso de almacenamiento persistente compartido (EFS) para mantener archivos comunes.
* Balanceo de carga para distribuir tráfico y mejorar disponibilidad.
* Separación de capas (servidor web, backend, base de datos).
* HTTPS habilitado con Certbot.

## 3. Descripción del ambiente de desarrollo y técnico

**Lenguaje de programación:**

* Python 3.9
* Bash

**Paquetes y herramientas utilizadas:**

* Flask
* Docker, Docker Compose
* NGINX 1.22
* Certbot (para HTTPS)
* MySQLclient
* AWS CLI
* nfs-utils

**Detalles técnicos importantes:**

* Montaje de EFS:

  ```bash
  sudo mount -t nfs4 fs-xxxxxxxx.efs.us-east-1.amazonaws.com:/ /mnt/shared
  ```
* Docker Compose ejecuta la app y NGINX en contenedores.
* Archivos estáticos y logs almacenados en EFS compartido entre instancias.
* `cloud-init` usado para automatizar la configuración en el launch template.

## 4. Descripción del ambiente de ejecución (en producción)

**Proveedor:** AWS Academy
**Tipo de instancia:** t2.micro
**Región:** us-east-1
**Sistema operativo:** Amazon Linux 2

### IPs y configuración

* IP pública de ejemplo: `34.204.123.45`
* Dominio temporal: `http://<elb-dns>.amazonaws.com`
* Puerto NGINX: 80 (HTTP) / 443 (HTTPS)
* Puerto Flask (interno): 5000
* Puerto RDS: 3306

### Parámetros clave

* RDS: `DB_HOST`, `DB_NAME`, `DB_USER`, `DB_PASSWORD` en `.env`
* Variables de entorno cargadas en el contenedor Flask
* EFS montado en `/mnt/shared` con `nfs-utils`

## 5. Información relevante

### Estructura del repositorio (ejemplo con `tree`)

```
.
├── docker-compose.yml
├── nginx
│   └── nginx.conf
├── app
│   ├── main.py
│   ├── templates/
│   └── static/
├── cloud-init.sh
├── requirements.txt
```

### Resultados

* La aplicación puede escalar automáticamente según la carga del CPU.
* Todos los contenedores Flask montan el mismo volumen EFS, permitiendo compartir archivos.

## Referencias

* [Documentación de AWS EFS](https://docs.aws.amazon.com/efs/latest/ug/mounting-fs.html)
* [Certbot + NGINX en Amazon Linux](https://certbot.eff.org/)
* [Docker Compose](https://docs.docker.com/compose/)
* [ST0263 - EAFIT](https://github.com/st0263eafit/st0263-251)
