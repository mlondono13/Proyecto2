# Proyecto2
# BookStore Monolithic App - Despliegue en AWS

Este proyecto despliega y escala horizontalmente una aplicación monolítica Flask usando tecnologías de contenedores y servicios de AWS como EC2, Auto Scaling Groups, Amazon RDS y Amazon EFS.

---

## Objetivos Cumplidos

### Objetivo 1: Escalamiento de una aplicación monolítica en AWS

- Se configuró **Docker Compose** para levantar una aplicación monolítica con Flask + MySQL.
- La base de datos fue migrada de un contenedor local a **Amazon RDS** (MySQL), garantizando persistencia y disponibilidad.
- Se creó un **Launch Template** con User Data para instanciar automáticamente la aplicación desde GitHub y levantar los contenedores con Docker Compose.
- Se creó un **Auto Scaling Group (ASG)** con instancias EC2 en múltiples zonas de disponibilidad.
- Se implementó un **Elastic Load Balancer (ELB)** para distribuir tráfico entre instancias del ASG.
- Se configuró **Route 53** para apuntar un dominio personalizado (`bookstoreedu.online`) al Load Balancer.
- Se instaló un certificado SSL usando **Certbot + NGINX** para servir tráfico por HTTPS.

---

### Objetivo 2: Sistema de archivos compartido entre instancias (NFS/EFS)

- Se creó un **sistema de archivos compartido en Amazon EFS** montado por todas las instancias EC2 del Auto Scaling Group.
- Se configuró un **Security Group** que permite tráfico TCP 2049 para NFS entre EFS y las instancias.
- En el script de **User Data del Launch Template**, se incluyó la instalación de `nfs-utils` y el montaje automático del sistema de archivos:
  
  ```bash
  yum install -y nfs-utils docker git
  systemctl start docker
  mkdir -p /mnt/shared
  mount -t nfs4 fs-xxxx.efs.us-east-1.amazonaws.com:/ /mnt/shared
  cd /home/ec2-user
  git clone https://github.com/mlondono13/Proyecto2.git
  cd Proyecto2/BookStore-monolith
  docker compose up -d
