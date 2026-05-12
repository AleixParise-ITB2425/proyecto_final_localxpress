# Guía de Despliegue — LocalXpress

**Servidor:** `srv1651179` — `187.77.171.221`  
**SO:** Ubuntu 24.04.4 LTS  
**Fecha:** Mayo 2026  

---

## Tabla de contenidos

1. [Requisitos del servidor](#1-requisitos-del-servidor)
2. [Software base instalado](#2-software-base-instalado)
3. [Estructura de directorios](#3-estructura-de-directorios)
4. [Despliegue del backend](#4-despliegue-del-backend)
5. [Configuración de Nginx](#5-configuración-de-nginx)
6. [Configuración de PostgreSQL](#6-configuración-de-postgresql)
7. [Configuración de PM2](#7-configuración-de-pm2)
8. [Despliegue de servicios Docker](#8-despliegue-de-servicios-docker)
9. [Sistema de backup](#9-sistema-de-backup)
10. [Actualizar el sistema en producción](#10-actualizar-el-sistema-en-producción)

---

## 1. Requisitos del servidor

| Recurso | Mínimo recomendado | Actual |
|---|---|---|
| CPU | 2 vCPU | — |
| RAM | 4 GB | 7.8 GB |
| Disco | 40 GB | 96 GB |
| SO | Ubuntu 22.04+ | Ubuntu 24.04.4 LTS |
| Acceso | SSH root o sudo | ✅ |

---

## 2. Software base instalado

El servidor tiene instalado de forma nativa (sin Docker) el siguiente software:

```bash
# Node.js
node --version    # v20 LTS o superior

# npm
npm --version

# PM2
pm2 --version

# PostgreSQL
psql --version    # PostgreSQL 16

# Nginx
nginx -v

# Certbot
certbot --version

# Docker
docker --version
docker compose version

# Git
git --version
```

---

## 3. Estructura de directorios

```
/
├── home/
│   ├── LocalXpress-APP/              # Aplicación principal
│   │   ├── backend/                  # Servidor Node.js
│   │   │   ├── .env                  # Variables de entorno backend
│   │   │   ├── uploads/
│   │   │   │   └── proofs/           # Fotos de confirmación de entrega
│   │   │   └── ...
│   │   ├── database/
│   │   │   └── migrations/           # Migraciones SQL versionadas
│   │   ├── src/                      # Frontend React
│   │   ├── docker-compose.yml
│   │   ├── docker-compose.dev.yml
│   │   ├── docker-compose.backup.yml
│   │   └── .env
│   │
│   └── LocalXpress-APP-IND/          # Aplicación para particulares
│       ├── .env
│       └── ...
│
├── etc/
│   ├── nginx/
│   │   └── sites-enabled/            # Configuración Nginx
│   ├── cron.d/
│   │   ├── localxpress-backup        # Cron backup diario
│   │   └── certbot                   # Cron renovación SSL
│   └── postgresql/16/main/
│       ├── postgresql.conf
│       └── pg_hba.conf
│
├── usr/local/bin/
│   └── backup-localxpress.sh         # Script de backup
│
└── var/
    ├── log/
    │   └── localxpress-backup.log    # Log de backups
    └── lib/
        ├── postgresql/               # Datos PostgreSQL
        └── docker/volumes/
            └── n8n_data/             # Datos persistentes n8n
```

---

## 4. Despliegue del backend

### Clonar los repositorios

```bash
cd /home
git clone https://github.com/skilax27/LocalXpress-APP.git
git clone https://github.com/skilax27/LocalXpress-APP-IND.git
```

### Configurar variables de entorno

```bash
# Backend APP principal
cp /home/LocalXpress-APP/backend/.env.example /home/LocalXpress-APP/backend/.env
nano /home/LocalXpress-APP/backend/.env

# APP-IND
cp /home/LocalXpress-APP-IND/.env.example /home/LocalXpress-APP-IND/.env
nano /home/LocalXpress-APP-IND/.env
```

Ver plantilla completa en [templates/env.example.md](./templates/env.example.md).

### Instalar dependencias y compilar

```bash
# APP principal — backend
cd /home/LocalXpress-APP/backend
npm install
npm run build

# APP principal — frontend
cd /home/LocalXpress-APP
npm install
npm run build

# APP-IND
cd /home/LocalXpress-APP-IND
npm install
npm run build
```

### Aplicar migraciones de base de datos

```bash
cd /home/LocalXpress-APP/database/migrations

# Aplicar en orden
psql -U localxpress -d localxpress -h 127.0.0.1 -f 001_schema_inicial.sql
psql -U localxpress -d localxpress -h 127.0.0.1 -f 002_performance_indexes.sql
psql -U localxpress -d localxpress -h 127.0.0.1 -f 004_pricing_zone_driver_price.sql
psql -U localxpress -d localxpress -h 127.0.0.1 -f 007_delivery_notifications.sql
psql -U localxpress -d localxpress -h 127.0.0.1 -f 008_email_idempotency.sql
```

---

## 5. Configuración de Nginx

### Estructura de configuración

```bash
# Ver configuración activa
ls /etc/nginx/sites-enabled/

# Verificar sintaxis antes de aplicar cambios
nginx -t

# Recargar sin interrumpir conexiones activas
systemctl reload nginx
```

### Configuración tipo para un dominio

```nginx
server {
    listen 80;
    server_name tudominio.com;
    return 301 https://$host$request_uri;
}

server {
    listen 443 ssl;
    server_name tudominio.com;

    ssl_certificate     /etc/letsencrypt/live/tudominio.com/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/tudominio.com/privkey.pem;

    # Frontend estático
    location / {
        root /home/LocalXpress-APP/dist;
        try_files $uri $uri/ /index.html;
    }

    # API backend
    location /api/ {
        proxy_pass http://localhost:3001;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
    }

    # WebSocket Socket.io
    location /socket.io/ {
        proxy_pass http://localhost:3001;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";
    }
}
```

### Obtener certificado SSL

```bash
certbot --nginx -d tudominio.com
```

---

## 6. Configuración de PostgreSQL

### Crear base de datos y usuario

```bash
# Entrar como postgres
su - postgres
psql

# Dentro de psql
CREATE USER localxpress WITH PASSWORD 'contraseña_segura';
CREATE DATABASE localxpress OWNER localxpress;
GRANT ALL PRIVILEGES ON DATABASE localxpress TO localxpress;
\q
```

### Verificar configuración pg_hba.conf

```
# /etc/postgresql/16/main/pg_hba.conf
local   all   postgres   peer
local   all   all        md5
host    all   all        127.0.0.1/32   md5
```

> ⚠️ **Importante:** El puerto 5432 no debe estar expuesto a internet. Verificar que `listen_addresses` en `postgresql.conf` esté restringido o protegido por firewall.

### Recargar configuración

```bash
systemctl reload postgresql
```

---

## 7. Configuración de PM2

### Iniciar los procesos

```bash
# Backend APP principal
cd /home/LocalXpress-APP/backend
pm2 start npm --name "localxpress-backend" -- start

# Backend APP-IND
cd /home/LocalXpress-APP-IND
pm2 start npm --name "localxpress-ind-backend" -- start

# Guardar configuración para arranque automático
pm2 save
pm2 startup
```

### Comandos de gestión habituales

```bash
pm2 status                          # Estado de todos los procesos
pm2 logs localxpress-backend        # Logs en tiempo real
pm2 restart localxpress-backend     # Reiniciar proceso
pm2 reload localxpress-backend      # Reinicio sin downtime
pm2 stop localxpress-backend        # Detener proceso
```

---

## 8. Despliegue de servicios Docker

### n8n

```bash
cd /home/LocalXpress-APP
docker compose up -d n8n
```

n8n estará disponible en `http://servidor:5678`. Acceder para configurar credenciales y flujos de trabajo.

### EasyPanel

EasyPanel se instala con su propio script de instalación. Una vez activo, es accesible en `http://servidor:3000`.

### Verificar contenedores activos

```bash
docker ps
docker volume ls
```

---

## 9. Sistema de backup

### Instalar el script de backup

```bash
# Copiar el script al sistema
cp /home/LocalXpress-APP/scripts/backup-localxpress.sh /usr/local/bin/
chmod +x /usr/local/bin/backup-localxpress.sh
```

### Configurar el cron

```bash
# Crear archivo de cron
cat > /etc/cron.d/localxpress-backup << 'EOF'
0 3 * * *   root   /usr/local/bin/backup-localxpress.sh >> /var/log/localxpress-backup.log 2>&1
EOF
```

### Verificar que el backup funciona

```bash
# Ejecutar manualmente
/usr/local/bin/backup-localxpress.sh

# Ver el log
tail -20 /var/log/localxpress-backup.log
```

---

## 10. Actualizar el sistema en producción

### Proceso estándar de actualización

```bash
# 1. Crear rama de seguridad en Git antes de cualquier cambio
cd /home/LocalXpress-APP
git checkout -b backup-vps-before-update-$(date +%Y%m%d-%H%M%S)
git checkout main

# 2. Obtener los cambios
git pull origin main

# 3. Instalar dependencias si hay cambios en package.json
cd backend && npm install

# 4. Compilar
npm run build

# 5. Reiniciar el proceso sin downtime
pm2 reload localxpress-backend

# 6. Verificar que arrancó correctamente
pm2 status
pm2 logs localxpress-backend --lines 20
```

### Rollback ante problemas

```bash
# Volver a la rama de seguridad creada antes del cambio
git checkout backup-vps-before-update-YYYYMMDD-HHMMSS
npm run build
pm2 reload localxpress-backend
```
