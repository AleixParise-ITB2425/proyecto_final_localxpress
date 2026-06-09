# Manual de Usuario — LocalXpress

**Versión:** 1.0 Final  
**Fecha:** Mayo 2026  
**Servidor:** `srv1651179` — `187.77.171.221`  
**SO:** Ubuntu 24.04.4 LTS  

---

## Tabla de contenidos

1. [Manual del Administrador de Sistemas](#1-manual-del-administrador-de-sistemas)
   - 1.1 [Acceso al servidor](#11-acceso-al-servidor)
   - 1.2 [Gestión de procesos con PM2](#12-gestión-de-procesos-con-pm2)
   - 1.3 [Gestión de PostgreSQL](#13-gestión-de-postgresql)
   - 1.4 [Gestión de contenedores Docker](#14-gestión-de-contenedores-docker)
   - 1.5 [Gestión de Nginx y SSL](#15-gestión-de-nginx-y-ssl)
   - 1.6 [Sistema de backups](#16-sistema-de-backups)
   - 1.7 [Gestión de imágenes de entrega](#17-gestión-de-imágenes-de-entrega)
   - 1.8 [Monitorización del sistema](#18-monitorización-del-sistema)
   - 1.9 [Gestión de logs](#19-gestión-de-logs)
   - 1.10 [Despliegue y actualización de código](#110-despliegue-y-actualización-de-código)
   - 1.11 [Seguridad del sistema](#111-seguridad-del-sistema)
   - 1.12 [Procedimientos de recuperación ante fallos](#112-procedimientos-de-recuperación-ante-fallos)
   - 1.13 [Tareas de mantenimiento periódico](#113-tareas-de-mantenimiento-periódico)
2. [Manual del Comercio / Tienda](#2-manual-del-comercio--tienda)
3. [Manual del Repartidor](#3-manual-del-repartidor)

---

# 1. Manual del Administrador de Sistemas

Este manual está dirigido al **administrador de sistemas** responsable del mantenimiento, monitorización y operación del servidor donde está desplegado LocalXpress. Se asume conocimiento de Linux, SSH y administración de servidores.

---

## 1.1 Acceso al servidor

### Conexión SSH

```bash
# Conexión directa como root
ssh root@187.77.171.221

# Conexión como usuario administrador (recomendado)
ssh localxpress-admin@187.77.171.221

# Conexión con clave privada específica
ssh -i ~/.ssh/id_ed25519 localxpress-admin@187.77.171.221

# Túnel SSH para acceder a n8n desde local (puerto 5678)
ssh -L 5678:localhost:5678 root@187.77.171.221

# Túnel SSH para acceder a EasyPanel desde local (puerto 3000)
ssh -L 3000:localhost:3000 root@187.77.171.221
```

### Verificación del estado general del sistema tras conectarse

```bash
# Resumen completo del estado del sistema
echo "=== PROCESOS PM2 ===" && pm2 status
echo "=== CONTENEDORES DOCKER ===" && docker ps
echo "=== NGINX ===" && systemctl status nginx --no-pager
echo "=== POSTGRESQL ===" && systemctl status postgresql --no-pager
echo "=== DISCO ===" && df -h
echo "=== MEMORIA ===" && free -h
echo "=== CARGA CPU ===" && uptime
```

### Información del sistema

```bash
# Versión del sistema operativo
lsb_release -a

# Kernel
uname -a

# Tiempo activo del servidor
uptime

# Usuarios conectados actualmente
who

# Últimos accesos al servidor
last | head -20

# Intentos de acceso fallidos
grep "Failed password" /var/log/auth.log | tail -20
```

---

## 1.2 Gestión de procesos con PM2

PM2 gestiona los dos procesos Node.js del backend en producción.

| Proceso | Puerto | Directorio |
|---|---|---|
| `localxpress-backend` | 3001 | `/home/LocalXpress-APP/backend/` |
| Backend APP-IND | 3002 | `/home/LocalXpress-APP-IND/` |

### Comandos de estado

```bash
# Ver estado de todos los procesos
pm2 status

# Ver estado con más detalle
pm2 show localxpress-backend

# Monitorización en tiempo real (CPU, RAM, logs)
pm2 monit

# Listar todos los procesos con PID
pm2 list
```

### Comandos de control

```bash
# Reiniciar un proceso (con breve downtime)
pm2 restart localxpress-backend

# Recargar sin downtime (recomendado en producción)
pm2 reload localxpress-backend

# Recargar todos los procesos sin downtime
pm2 reload all

# Detener un proceso
pm2 stop localxpress-backend

# Iniciar un proceso parado
pm2 start localxpress-backend

# Eliminar un proceso de PM2
pm2 delete localxpress-backend

# Reiniciar todos los procesos
pm2 restart all
```

### Configuración de arranque automático

```bash
# Generar script de arranque automático al iniciar el servidor
pm2 startup

# Guardar la lista de procesos actual para que arranquen automáticamente
pm2 save

# Verificar que el arranque automático está configurado
systemctl status pm2-root
```

### Gestión de logs con PM2

```bash
# Ver logs en tiempo real de un proceso
pm2 logs localxpress-backend

# Ver logs en tiempo real de todos los procesos
pm2 logs

# Ver las últimas N líneas de log
pm2 logs localxpress-backend --lines 100

# Ver logs con timestamp
pm2 logs localxpress-backend --timestamp

# Limpiar los logs de un proceso
pm2 flush localxpress-backend

# Limpiar logs de todos los procesos
pm2 flush

# Ver ruta de los archivos de log
pm2 info localxpress-backend | grep log
```

### Ubicación de los archivos de log de PM2

```bash
# Logs de salida estándar
/root/.pm2/logs/localxpress-backend-out.log

# Logs de errores
/root/.pm2/logs/localxpress-backend-error.log

# Ver errores recientes
tail -50 /root/.pm2/logs/localxpress-backend-error.log

# Seguir errores en tiempo real
tail -f /root/.pm2/logs/localxpress-backend-error.log
```

---

## 1.3 Gestión de PostgreSQL

PostgreSQL está instalado de forma **nativa** en el sistema operativo (no en Docker).

### Control del servicio

```bash
# Estado del servicio
systemctl status postgresql

# Iniciar PostgreSQL
systemctl start postgresql

# Detener PostgreSQL
systemctl stop postgresql

# Reiniciar PostgreSQL
systemctl restart postgresql

# Recargar configuración sin reiniciar (pg_hba.conf, postgresql.conf)
systemctl reload postgresql

# Verificar que PostgreSQL escucha en el puerto 5432
ss -tlnp | grep 5432
```

### Conexión a la base de datos

```bash
# Conectar con el usuario de la aplicación
psql -U localxpress -d localxpress -h 127.0.0.1

# Conectar como superusuario postgres
psql -U postgres -h 127.0.0.1

# Conectar directamente a una BD específica
psql -U localxpress -d localxpress -h 127.0.0.1 -p 5432
```

### Comandos útiles dentro de psql

```sql
-- Listar todas las bases de datos
\l

-- Conectar a una base de datos
\c localxpress

-- Listar todas las tablas
\dt

-- Ver estructura de una tabla
\d stops
\d order_photos
\d users

-- Ver tamaño de cada tabla
SELECT relname, pg_size_pretty(pg_total_relation_size(relid))
FROM pg_catalog.pg_statio_user_tables
ORDER BY pg_total_relation_size(relid) DESC;

-- Ver tamaño total de la base de datos
SELECT pg_size_pretty(pg_database_size('localxpress'));

-- Ver conexiones activas
SELECT pid, usename, application_name, state, query_start, query
FROM pg_stat_activity
WHERE state = 'active';

-- Terminar una conexión específica
SELECT pg_terminate_backend(pid) FROM pg_stat_activity WHERE pid = <PID>;

-- Ver los últimos pedidos
SELECT id, status, created_at FROM stops ORDER BY created_at DESC LIMIT 10;

-- Contar pedidos por estado
SELECT status, COUNT(*) FROM stops GROUP BY status;

-- Ver fotos pendientes de limpieza (más de 20 días)
SELECT COUNT(*), pg_size_pretty(SUM(file_size)::bigint)
FROM order_photos
WHERE created_at < NOW() - INTERVAL '20 days';

-- Salir de psql
\q
```

### Gestión de usuarios de PostgreSQL

```bash
# Crear un nuevo usuario
psql -U postgres -h 127.0.0.1 -c "CREATE USER nuevo_usuario WITH PASSWORD 'contraseña';"

# Cambiar contraseña de un usuario
psql -U postgres -h 127.0.0.1 -c "ALTER USER localxpress PASSWORD 'nueva_contraseña';"

# Cambiar contraseña del superusuario postgres
# Paso 1: cambiar temporalmente pg_hba.conf a peer auth
sed -i 's/^local   all             postgres.*md5/local   all             postgres                                peer/' /etc/postgresql/16/main/pg_hba.conf
systemctl reload postgresql

# Paso 2: entrar sin contraseña y cambiar
su - postgres -c "psql"
# Dentro de psql:
# ALTER USER postgres PASSWORD 'nueva_contraseña_segura';
# \q

# Paso 3: restaurar md5
sed -i 's/^local   all             postgres.*peer/local   all             postgres                                md5/' /etc/postgresql/16/main/pg_hba.conf
systemctl reload postgresql
```

### Configuración de PostgreSQL

```bash
# Ver configuración actual
cat /etc/postgresql/16/main/postgresql.conf | grep -v "^#" | grep -v "^$"

# Ver configuración de autenticación
cat /etc/postgresql/16/main/pg_hba.conf | grep -v "^#" | grep -v "^$"

# Editar configuración principal
nano /etc/postgresql/16/main/postgresql.conf

# Editar configuración de autenticación
nano /etc/postgresql/16/main/pg_hba.conf

# Ubicación de los datos
ls -la /var/lib/postgresql/16/main/

# Ver logs de PostgreSQL
tail -50 /var/log/postgresql/postgresql-16-main.log
tail -f /var/log/postgresql/postgresql-16-main.log
```

### Migraciones de base de datos

```bash
# Aplicar una migración manualmente
psql -U localxpress -d localxpress -h 127.0.0.1 \
  -f /home/LocalXpress-APP/database/migrations/009_nueva_migracion.sql

# Verificar qué migraciones están aplicadas
ls /home/LocalXpress-APP/database/migrations/

# Ver el esquema completo de la BD
pg_dump -U localxpress -h 127.0.0.1 -s localxpress > /tmp/schema_actual.sql
cat /tmp/schema_actual.sql
```

---

## 1.4 Gestión de contenedores Docker

### Estado y control general

```bash
# Ver contenedores activos
docker ps

# Ver todos los contenedores (incluidos parados)
docker ps -a

# Ver uso de recursos por contenedor
docker stats

# Ver imágenes descargadas
docker images

# Ver volúmenes
docker volume ls

# Ver redes Docker
docker network ls

# Información detallada del sistema Docker
docker system df
docker info
```

### Gestión de n8n

```bash
# Ver estado de n8n
docker ps | grep n8n

# Ver logs de n8n
docker logs n8n
docker logs n8n -f              # En tiempo real
docker logs n8n --tail 100      # Últimas 100 líneas
docker logs n8n --since 1h      # Última hora

# Reiniciar n8n
docker restart n8n

# Detener n8n
docker stop n8n

# Iniciar n8n
docker start n8n

# Entrar al contenedor de n8n (shell interactiva)
docker exec -it n8n sh

# Ver variables de entorno del contenedor
docker inspect n8n | grep -A 20 "Env"

# Actualizar n8n a la última versión
docker stop n8n
docker rm n8n
docker pull n8nio/n8n:stable
cd /home/LocalXpress-APP
docker compose up -d n8n
docker logs n8n -f
```

### Gestión de EasyPanel

```bash
# Ver estado de EasyPanel
docker ps | grep easypanel

# Logs de EasyPanel
docker logs easypanel -f

# Reiniciar EasyPanel
docker restart easypanel
```

### Gestión con Docker Compose

```bash
# Levantar todos los servicios definidos en docker-compose.yml
cd /home/LocalXpress-APP
docker compose up -d

# Ver estado de los servicios del Compose
docker compose ps

# Ver logs de todos los servicios
docker compose logs -f

# Parar todos los servicios del Compose
docker compose down

# Reconstruir y levantar
docker compose up -d --build

# Actualizar imágenes y recrear contenedores
docker compose pull
docker compose up -d
```

### Limpieza de Docker

```bash
# Eliminar contenedores parados
docker container prune -f

# Eliminar imágenes sin usar
docker image prune -f

# Eliminar volúmenes sin usar (¡CUIDADO — no ejecutar si hay datos importantes!)
docker volume prune -f

# Limpieza completa del sistema Docker (sin afectar a volúmenes activos)
docker system prune -f

# Ver cuánto espacio ocupa Docker
docker system df -v
```

### Inspección y diagnóstico

```bash
# Inspeccionar un contenedor en detalle
docker inspect n8n

# Ver el punto de montaje del volumen n8n_data
docker volume inspect n8n_data

# Verificar que el volumen tiene datos
ls -la /var/lib/docker/volumes/n8n_data/_data/

# Ver la red Docker
docker network inspect bridge
```

---

## 1.5 Gestión de Nginx y SSL

### Control del servicio Nginx

```bash
# Estado de Nginx
systemctl status nginx

# Iniciar Nginx
systemctl start nginx

# Detener Nginx
systemctl stop nginx

# Reiniciar Nginx (con breve downtime)
systemctl restart nginx

# Recargar configuración sin downtime (recomendado)
systemctl reload nginx

# Verificar sintaxis de la configuración antes de aplicar cambios
nginx -t

# Ver la versión de Nginx
nginx -v

# Ver módulos compilados
nginx -V 2>&1 | grep -o 'with[^ ]*'
```

### Configuración de Nginx

```bash
# Ver los sitios habilitados
ls -la /etc/nginx/sites-enabled/

# Ver los sitios disponibles
ls -la /etc/nginx/sites-available/

# Ver configuración de un sitio
cat /etc/nginx/sites-enabled/localxpress

# Editar configuración de un sitio
nano /etc/nginx/sites-enabled/localxpress

# Habilitar un nuevo sitio
ln -s /etc/nginx/sites-available/nuevo-sitio /etc/nginx/sites-enabled/
nginx -t && systemctl reload nginx

# Deshabilitar un sitio
rm /etc/nginx/sites-enabled/sitio-a-deshabilitar
nginx -t && systemctl reload nginx

# Configuración global de Nginx
cat /etc/nginx/nginx.conf
nano /etc/nginx/nginx.conf
```

### Logs de Nginx

```bash
# Ver log de accesos en tiempo real
tail -f /var/log/nginx/access.log

# Ver log de errores en tiempo real
tail -f /var/log/nginx/error.log

# Filtrar errores 500
grep " 500 " /var/log/nginx/access.log | tail -20

# Filtrar errores 502 (backend caído)
grep " 502 " /var/log/nginx/access.log | tail -20

# Ver las IPs con más peticiones
awk '{print $1}' /var/log/nginx/access.log | sort | uniq -c | sort -rn | head -20

# Ver las rutas más solicitadas
awk '{print $7}' /var/log/nginx/access.log | sort | uniq -c | sort -rn | head -20
```

### Gestión de certificados SSL con Certbot

```bash
# Ver todos los certificados instalados
certbot certificates

# Renovar certificados (solo si están a menos de 30 días de expirar)
certbot renew

# Simular renovación sin aplicarla
certbot renew --dry-run

# Forzar renovación aunque no esté próxima a expirar
certbot renew --force-renewal

# Obtener un nuevo certificado para un dominio
certbot --nginx -d tudominio.com

# Revocar un certificado
certbot revoke --cert-path /etc/letsencrypt/live/tudominio.com/cert.pem

# Ver el cron de renovación automática
cat /etc/cron.d/certbot

# Verificar que el certificado SSL es válido
openssl s_client -connect tudominio.com:443 -servername tudominio.com < /dev/null 2>/dev/null | \
  openssl x509 -noout -dates

# Ver la fecha de expiración del certificado
certbot certificates | grep "Expiry Date"
```

---

## 1.6 Sistema de backups

### Script de backup

```bash
# Ver el script de backup
cat /usr/local/bin/backup-localxpress.sh

# Ejecutar backup manualmente
/usr/local/bin/backup-localxpress.sh

# Ver el log de backups
cat /var/log/localxpress-backup.log

# Ver las últimas ejecuciones del backup
tail -30 /var/log/localxpress-backup.log

# Seguir el log en tiempo real durante un backup manual
tail -f /var/log/localxpress-backup.log

# Verificar el cron de backup
cat /etc/cron.d/localxpress-backup
```

### Backup manual de PostgreSQL

```bash
# Backup completo de la base de datos localxpress
pg_dump -U localxpress -h 127.0.0.1 -d localxpress \
  -f /tmp/backup_localxpress_$(date +%Y%m%d_%H%M%S).sql

# Backup comprimido (recomendado)
pg_dump -U localxpress -h 127.0.0.1 -d localxpress | \
  gzip > /tmp/backup_localxpress_$(date +%Y%m%d_%H%M%S).sql.gz

# Backup de todas las bases de datos
pg_dumpall -U postgres -h 127.0.0.1 | \
  gzip > /tmp/backup_todas_bds_$(date +%Y%m%d_%H%M%S).sql.gz

# Backup solo del esquema (sin datos)
pg_dump -U localxpress -h 127.0.0.1 -d localxpress -s \
  -f /tmp/schema_$(date +%Y%m%d).sql

# Backup de una tabla específica
pg_dump -U localxpress -h 127.0.0.1 -d localxpress \
  -t stops -f /tmp/backup_stops_$(date +%Y%m%d).sql
```

### Restauración de backup

```bash
# Restaurar desde un fichero SQL
psql -U localxpress -h 127.0.0.1 -d localxpress \
  -f /tmp/backup_localxpress_20260515_030000.sql

# Restaurar desde un fichero comprimido
gunzip -c /tmp/backup_localxpress_20260515_030000.sql.gz | \
  psql -U localxpress -h 127.0.0.1 -d localxpress

# Restaurar en una base de datos nueva (para no machacar la actual)
createdb -U postgres -h 127.0.0.1 localxpress_restauracion
psql -U postgres -h 127.0.0.1 -d localxpress_restauracion \
  -f /tmp/backup_localxpress_20260515_030000.sql

# Verificar que la restauración fue correcta
psql -U localxpress -h 127.0.0.1 -d localxpress_restauracion \
  -c "SELECT COUNT(*) FROM stops;"
```

### Verificación de backups

```bash
# Verificar que el último backup se ejecutó correctamente
grep -E "OK|ERROR|completado|failed" /var/log/localxpress-backup.log | tail -10

# Verificar que el fichero de backup existe y tiene tamaño razonable
ls -lh /tmp/backup_*.sql.gz 2>/dev/null

# Verificar la integridad de un backup comprimido
gunzip -t /tmp/backup_localxpress_20260515_030000.sql.gz && echo "Backup íntegro" || echo "Backup corrupto"
```

---

## 1.7 Gestión de imágenes de entrega

### Estado del sistema de imágenes

```bash
# Ver todas las fotos almacenadas
ls -lh /home/LocalXpress-APP/backend/uploads/proofs/

# Contar cuántas fotos hay
ls /home/LocalXpress-APP/backend/uploads/proofs/ | wc -l

# Ver espacio total ocupado por las fotos
du -sh /home/LocalXpress-APP/backend/uploads/proofs/

# Ver las fotos más antiguas
ls -lt /home/LocalXpress-APP/backend/uploads/proofs/ | tail -10

# Ver las fotos más recientes
ls -lt /home/LocalXpress-APP/backend/uploads/proofs/ | head -10

# Encontrar fotos con más de 20 días (candidatas a limpieza)
find /home/LocalXpress-APP/backend/uploads/proofs/ -type f -mtime +20 | wc -l
```

### Limpieza manual de imágenes

```bash
# Simular la limpieza sin borrar nada (dry run)
curl -X POST "http://localhost:3001/api/uploads/cleanup?dry_run=true"

# Ejecutar la limpieza real
curl -X POST "http://localhost:3001/api/uploads/cleanup" \
  -H "Authorization: Bearer <token_admin>"

# Ver el log de limpiezas ejecutadas
pm2 logs localxpress-backend | grep photo-cleanup

# Eliminar manualmente fotos con más de 20 días del filesystem
# (usar solo si el endpoint no está disponible)
find /home/LocalXpress-APP/backend/uploads/proofs/ \
  -type f -mtime +20 -delete

# Verificar en la BD cuántas referencias hay sin fichero físico
psql -U localxpress -h 127.0.0.1 -d localxpress -c "
SELECT COUNT(*) as referencias_huerfanas
FROM order_photos op
WHERE NOT EXISTS (
  SELECT 1 FROM pg_stat_file('/home/LocalXpress-APP/backend/uploads/' || op.file_path)
);"
```

### Verificar el cron de limpieza automática

```bash
# El cron de limpieza está dentro del proceso Node.js (node-cron)
# Se ejecuta cada día a las 02:30h
# Verificar en los logs de PM2
pm2 logs localxpress-backend | grep -i "cleanup\|photo\|limpieza"
```

---

## 1.8 Monitorización del sistema

### Recursos del sistema

```bash
# Uso de CPU, memoria y procesos en tiempo real
htop

# Alternativa más simple
top

# Uso de CPU por proceso
ps aux --sort=-%cpu | head -15

# Uso de memoria por proceso
ps aux --sort=-%mem | head -15

# Uso de disco
df -h

# Uso de disco por directorio (los más grandes)
du -sh /* 2>/dev/null | sort -rh | head -15
du -sh /home/* 2>/dev/null | sort -rh
du -sh /var/lib/docker/* 2>/dev/null | sort -rh

# Estadísticas de red
ss -tlnp
netstat -tlnp 2>/dev/null || ss -tlnp

# Tráfico de red en tiempo real
iftop 2>/dev/null || nethogs 2>/dev/null
```

### Estadísticas históricas con sysstat

```bash
# Ver uso de CPU de las últimas horas
sar -u

# Ver uso de memoria
sar -r

# Ver estadísticas de disco
sar -d

# Ver estadísticas de red
sar -n DEV

# Ver estadísticas de ayer
sar -u -f /var/log/sysstat/sa$(date -d yesterday +%d)
```

### Monarx Agent (seguridad)

```bash
# Ver estado del agente Monarx
ps aux | grep monarx
systemctl status monarx-agent 2>/dev/null

# Ver el cron de actualización de Monarx
cat /etc/cron.d/monarx-update

# Verificar que Monarx está escuchando
ss -tlnp | grep 65529
```

### Verificación de conectividad

```bash
# Comprobar que el backend responde
curl -s -o /dev/null -w "%{http_code}" http://localhost:3001/api/health
curl -s -o /dev/null -w "%{http_code}" http://localhost:3002/api/health

# Comprobar que Nginx responde
curl -s -o /dev/null -w "%{http_code}" http://localhost

# Comprobar tiempo de respuesta del backend
curl -w "@-" -o /dev/null -s http://localhost:3001/api/health <<'EOF'
    time_namelookup:  %{time_namelookup}s\n
       time_connect:  %{time_connect}s\n
    time_appconnect:  %{time_appconnect}s\n
   time_pretransfer:  %{time_pretransfer}s\n
      time_redirect:  %{time_redirect}s\n
 time_starttransfer:  %{time_starttransfer}s\n
                    ----------\n
         time_total:  %{time_total}s\n
EOF

# Comprobar que PostgreSQL acepta conexiones
pg_isready -h 127.0.0.1 -U localxpress
```

---

## 1.9 Gestión de logs

### Rutas principales de logs

| Servicio | Ruta del log | Comando de seguimiento |
|---|---|---|
| Backend APP | PM2 logs | `pm2 logs localxpress-backend` |
| Backend APP-IND | PM2 logs | `pm2 logs localxpress-ind` |
| Nginx accesos | `/var/log/nginx/access.log` | `tail -f /var/log/nginx/access.log` |
| Nginx errores | `/var/log/nginx/error.log` | `tail -f /var/log/nginx/error.log` |
| PostgreSQL | `/var/log/postgresql/postgresql-16-main.log` | `tail -f /var/log/postgresql/postgresql-16-main.log` |
| n8n | Docker logs | `docker logs n8n -f` |
| Backup | `/var/log/localxpress-backup.log` | `tail -f /var/log/localxpress-backup.log` |
| Sistema | journald | `journalctl -f` |
| Autenticación SSH | `/var/log/auth.log` | `tail -f /var/log/auth.log` |

### Comandos avanzados de análisis de logs

```bash
# Ver errores del sistema de las últimas 2 horas
journalctl --since "2 hours ago" -p err

# Ver logs de un servicio systemd específico
journalctl -u nginx -f
journalctl -u postgresql -f

# Buscar errores en los logs de PM2
pm2 logs localxpress-backend --lines 500 | grep -i "error\|fatal\|uncaught"

# Ver las IPs bloqueadas o con más errores en Nginx
grep " 4[0-9][0-9] " /var/log/nginx/access.log | \
  awk '{print $1}' | sort | uniq -c | sort -rn | head -20

# Ver errores de PostgreSQL de hoy
grep "$(date +%Y-%m-%d)" /var/log/postgresql/postgresql-16-main.log | \
  grep -i "error\|fatal"

# Contar peticiones por código HTTP en Nginx
awk '{print $9}' /var/log/nginx/access.log | sort | uniq -c | sort -rn

# Ver los últimos intentos de login fallidos al sistema
grep "authentication failure\|Failed password" /var/log/auth.log | tail -30
```

### Rotación de logs

```bash
# Ver configuración de logrotate
ls /etc/logrotate.d/
cat /etc/logrotate.d/nginx

# Forzar rotación de logs manualmente
logrotate -f /etc/logrotate.conf

# Limpiar logs de PM2 manualmente
pm2 flush

# Ver configuración de rotación de PM2
cat /root/.pm2/modules/pm2-logrotate/config.json 2>/dev/null
```

---

## 1.10 Despliegue y actualización de código

### Proceso estándar de actualización

```bash
# 1. Crear rama de seguridad en Git ANTES de cualquier cambio
cd /home/LocalXpress-APP
git checkout -b backup-vps-before-update-$(date +%Y%m%d-%H%M%S)
git checkout main

# 2. Obtener los cambios del repositorio
git pull origin main

# 3. Verificar qué cambió
git log --oneline -10
git diff HEAD~1

# 4. Instalar nuevas dependencias si hay cambios en package.json
cd backend
npm install --production

# 5. Compilar si hay cambios en TypeScript
npm run build

# 6. Recargar el proceso sin downtime
pm2 reload localxpress-backend

# 7. Verificar que arrancó correctamente
pm2 status
pm2 logs localxpress-backend --lines 20

# 8. Comprobar que el backend responde
curl -s -o /dev/null -w "%{http_code}" http://localhost:3001/api/health
```

### Actualización del frontend

```bash
cd /home/LocalXpress-APP

# Obtener cambios
git pull origin main

# Instalar dependencias si procede
npm install

# Compilar el frontend
npm run build

# Nginx sirve el build directamente, no necesita reiniciarse
# Verificar que el nuevo build está en su sitio
ls -la dist/
```

### Rollback — Volver a la versión anterior

```bash
# Ver las ramas de seguridad disponibles
cd /home/LocalXpress-APP
git branch | grep backup

# Volver a una rama de seguridad específica
git checkout backup-vps-before-update-20260511-143451

# Reconstruir y recargar
cd backend && npm run build
pm2 reload localxpress-backend

# Verificar
curl -s http://localhost:3001/api/health
```

### Variables de entorno

```bash
# Ver las variables de entorno actuales (sin mostrar valores sensibles)
cat /home/LocalXpress-APP/backend/.env | grep -v "PASSWORD\|SECRET\|KEY"

# Editar variables de entorno
nano /home/LocalXpress-APP/backend/.env

# Después de editar el .env, recargar el proceso para que tome los nuevos valores
pm2 reload localxpress-backend

# Verificar que una variable específica está cargada
pm2 show localxpress-backend | grep -A 5 "env"
```

---

## 1.11 Seguridad del sistema

### Gestión de usuarios del sistema

```bash
# Ver usuarios del sistema
cat /etc/passwd | grep -v nologin | grep -v false

# Crear un nuevo usuario administrador
adduser nuevo_admin
usermod -aG sudo nuevo_admin

# Ver grupos de un usuario
groups localxpress-admin

# Bloquear un usuario
usermod -L usuario_a_bloquear

# Desbloquear un usuario
usermod -U usuario_bloqueado

# Ver los últimos accesos
last | head -20

# Ver intentos de acceso fallidos
faillog -a | head -20
```

### Gestión de SSH

```bash
# Ver configuración SSH
cat /etc/ssh/sshd_config | grep -v "^#" | grep -v "^$"

# Editar configuración SSH
nano /etc/ssh/sshd_config

# Parámetros de seguridad recomendados en sshd_config:
# PermitRootLogin no
# PasswordAuthentication no
# PubkeyAuthentication yes
# MaxAuthTries 3

# Reiniciar SSH tras cambios (¡tener otra sesión abierta como precaución!)
systemctl reload sshd

# Ver las claves autorizadas
cat ~/.ssh/authorized_keys
cat /home/localxpress-admin/.ssh/authorized_keys

# Añadir una nueva clave pública
echo "ssh-ed25519 AAAA... usuario@maquina" >> ~/.ssh/authorized_keys
chmod 600 ~/.ssh/authorized_keys
```

### Cortafuegos UFW

```bash
# Ver estado del cortafuegos
ufw status verbose

# Activar UFW
ufw enable

# Permitir puertos necesarios
ufw allow 22/tcp      # SSH
ufw allow 80/tcp      # HTTP
ufw allow 443/tcp     # HTTPS
ufw allow 3000/tcp    # EasyPanel
ufw allow 5678/tcp    # n8n

# Bloquear PostgreSQL al exterior (solo localhost)
ufw deny 5432/tcp

# Ver las reglas activas numeradas
ufw status numbered

# Eliminar una regla por número
ufw delete 5

# Desactivar UFW (en caso de emergencia)
ufw disable

# Resetear todas las reglas
ufw reset
```

### Verificación de puertos expuestos

```bash
# Ver todos los puertos en escucha
ss -tlnp

# Verificar qué proceso usa un puerto específico
ss -tlnp | grep :5432
ss -tlnp | grep :3001
ss -tlnp | grep :80

# Escanear puertos abiertos desde el exterior (requiere nmap)
nmap -sV 187.77.171.221
```

### Auditoría de seguridad rápida

```bash
# Verificar que no hay usuarios con UID 0 además de root
awk -F: '($3 == 0) {print}' /etc/passwd

# Buscar ficheros con permisos SUID sospechosos
find / -perm -4000 -type f 2>/dev/null | grep -v "^/usr\|^/bin\|^/sbin"

# Verificar permisos de los ficheros .env
ls -la /home/LocalXpress-APP/backend/.env
ls -la /home/LocalXpress-APP-IND/.env

# Ver conexiones de red activas
ss -tnp

# Verificar los cron jobs activos
crontab -l
ls -la /etc/cron.d/
cat /etc/cron.d/localxpress-backup
cat /etc/cron.d/certbot
```

---

## 1.12 Procedimientos de recuperación ante fallos

### Escenario 1: El backend no responde (502 Bad Gateway en Nginx)

```bash
# 1. Verificar estado de PM2
pm2 status

# 2. Ver los últimos errores del proceso
pm2 logs localxpress-backend --lines 50

# 3. Si está parado, iniciarlo
pm2 start localxpress-backend

# 4. Si hay errores de código, recargar
pm2 reload localxpress-backend

# 5. Verificar que está escuchando en el puerto correcto
ss -tlnp | grep 3001

# 6. Comprobar conectividad
curl -s http://localhost:3001/api/health

# 7. Si persiste el error, revisar variables de entorno
cat /home/LocalXpress-APP/backend/.env | grep -v "SECRET\|PASS"

# 8. En caso extremo, reinicio completo del proceso
pm2 delete localxpress-backend
cd /home/LocalXpress-APP/backend
pm2 start npm --name "localxpress-backend" -- start
pm2 save
```

### Escenario 2: PostgreSQL no responde

```bash
# 1. Ver estado del servicio
systemctl status postgresql

# 2. Ver los últimos logs de error
tail -30 /var/log/postgresql/postgresql-16-main.log

# 3. Intentar reiniciar
systemctl restart postgresql

# 4. Verificar que está escuchando
pg_isready -h 127.0.0.1
ss -tlnp | grep 5432

# 5. Verificar espacio en disco (fallo común)
df -h /var/lib/postgresql/

# 6. Si hay problema de espacio, limpiar logs
journalctl --vacuum-size=500M

# 7. Comprobar conectividad desde el backend
psql -U localxpress -h 127.0.0.1 -d localxpress -c "SELECT 1;"
```

### Escenario 3: Nginx devuelve error 500 o no arranca

```bash
# 1. Verificar la sintaxis de la configuración
nginx -t

# 2. Si hay error de sintaxis, ver cuál es
nginx -t 2>&1

# 3. Ver los últimos errores de Nginx
tail -20 /var/log/nginx/error.log

# 4. Restaurar la última configuración que funcionaba
# (si hay backup de la configuración)
cp /etc/nginx/sites-available/localxpress.bak \
   /etc/nginx/sites-available/localxpress
nginx -t && systemctl reload nginx

# 5. Reiniciar Nginx
systemctl restart nginx

# 6. Verificar que está activo
systemctl status nginx
curl -s -o /dev/null -w "%{http_code}" http://localhost
```

### Escenario 4: n8n no responde o no arranca

```bash
# 1. Ver estado del contenedor
docker ps | grep n8n
docker ps -a | grep n8n

# 2. Ver los últimos logs
docker logs n8n --tail 50

# 3. Reiniciar el contenedor
docker restart n8n

# 4. Si el contenedor está parado, iniciarlo
docker start n8n

# 5. Si hay que recrear el contenedor (los datos del volumen se conservan)
docker stop n8n
docker rm n8n
cd /home/LocalXpress-APP
docker compose up -d n8n

# 6. Verificar que el volumen de datos está intacto
ls -la /var/lib/docker/volumes/n8n_data/_data/

# 7. Comprobar acceso
curl -s -o /dev/null -w "%{http_code}" http://localhost:5678
```

### Escenario 5: El servidor se reinicia inesperadamente

```bash
# Al acceder al servidor tras el reinicio, verificar:

# 1. Estado general
pm2 status
docker ps
systemctl status nginx postgresql

# 2. Si PM2 no arrancó los procesos automáticamente
pm2 resurrect
# o
pm2 start all

# 3. Si Docker no arrancó los contenedores
docker start n8n easypanel

# 4. Verificar los servicios systemd
systemctl start nginx
systemctl start postgresql

# 5. Comprobar el último reinicio
last reboot | head -5
journalctl --since "1 hour ago" | grep -i "error\|fail"
```

### Escenario 6: Restauración completa desde backup

```bash
# Situación: el servidor ha fallado y hay que restaurar desde cero

# 1. Aprovisionar nuevo VPS con Ubuntu 24.04
# 2. Instalar dependencias base
apt update && apt upgrade -y
apt install -y nginx postgresql nodejs npm git docker.io docker-compose-plugin

# 3. Clonar el repositorio
cd /home
git clone https://github.com/skilax27/LocalXpress-APP.git
git clone https://github.com/skilax27/LocalXpress-APP-IND.git

# 4. Restaurar el backup de PostgreSQL descargado de Google Drive
gunzip -c backup_localxpress_YYYYMMDD.sql.gz | \
  psql -U postgres -h 127.0.0.1 -d localxpress

# 5. Restaurar los ficheros .env
# (copiar desde copia de seguridad segura o reconfigurar)
nano /home/LocalXpress-APP/backend/.env

# 6. Instalar dependencias y compilar
cd /home/LocalXpress-APP/backend && npm install --production && npm run build

# 7. Instalar PM2 y arrancar los procesos
npm install -g pm2
pm2 start npm --name "localxpress-backend" -- start
pm2 startup && pm2 save

# 8. Configurar Nginx y SSL
certbot --nginx -d tudominio.com

# 9. Levantar servicios Docker
cd /home/LocalXpress-APP
docker compose up -d

# 10. Verificar todo
pm2 status && docker ps && systemctl status nginx postgresql
```

---

## 1.13 Tareas de mantenimiento periódico

### Checklist diario (automático)

| Tarea | Hora | Verificación |
|---|---|---|
| Backup PostgreSQL → Google Drive | 03:00h | `tail -5 /var/log/localxpress-backup.log` |
| Limpieza de fotos >20 días | 02:30h | `pm2 logs localxpress-backend \| grep cleanup` |
| Renovación SSL si procede | Cada 12h | `certbot certificates` |
| Estadísticas del sistema (sysstat) | Cada 10 min | `sar -u` |
| Actualización Monarx | Sábados 12:14h | `ps aux \| grep monarx` |

### Checklist semanal (manual)

```bash
# 1. Verificar que los backups se ejecutaron correctamente
grep -E "OK|ERROR" /var/log/localxpress-backup.log | tail -10

# 2. Revisar estado de todos los servicios
pm2 status && docker ps && systemctl status nginx postgresql

# 3. Buscar errores en los logs
pm2 logs localxpress-backend --lines 200 | grep -i error
grep "ERROR\|FATAL" /var/log/postgresql/postgresql-16-main.log | tail -20

# 4. Revisar uso de disco
df -h
du -sh /home/LocalXpress-APP/backend/uploads/proofs/

# 5. Verificar el certificado SSL
certbot certificates | grep "Expiry Date"

# 6. Revisar intentos de acceso fallidos al servidor
grep "Failed password\|Invalid user" /var/log/auth.log | wc -l
```

### Checklist mensual (manual)

```bash
# 1. Actualizar paquetes del sistema
apt update && apt list --upgradable
apt upgrade -y

# 2. Actualizar imágenes Docker
docker pull n8nio/n8n:stable
docker compose up -d n8n

# 3. Limpiar recursos Docker sin usar
docker system prune -f

# 4. Revisar y rotar logs manualmente si es necesario
logrotate -f /etc/logrotate.conf
pm2 flush

# 5. Verificar tamaño de la base de datos
psql -U localxpress -h 127.0.0.1 -d localxpress -c "
SELECT relname, pg_size_pretty(pg_total_relation_size(relid))
FROM pg_catalog.pg_statio_user_tables
ORDER BY pg_total_relation_size(relid) DESC;"

# 6. Ejecutar VACUUM en PostgreSQL para optimizar
psql -U localxpress -h 127.0.0.1 -d localxpress -c "VACUUM ANALYZE;"

# 7. Revisar y actualizar dependencias npm
cd /home/LocalXpress-APP/backend
npm audit
npm outdated

# 8. Comprobar que PM2 está configurado para arrancar con el sistema
pm2 startup
pm2 save
```

---

# 2. Manual del Comercio / Tienda

Este manual está dirigido a los **comercios** (floristerías, panaderías, catering, etc.) que utilizan LocalXpress para gestionar sus pedidos de reparto.

## 2.1 Acceso a la aplicación

1. Abrir el navegador y acceder a `https://localxpress.app`
2. Introducir el email y contraseña proporcionados por el administrador
3. Hacer clic en **Iniciar sesión**

## 2.2 Crear un nuevo pedido

1. En el panel principal, hacer clic en **Nuevo pedido**
2. Rellenar los campos obligatorios:
   - **Dirección de recogida:** donde el repartidor recogerá el pedido
   - **Dirección de entrega:** donde se entregará al cliente final
   - **Notas:** instrucciones especiales (timbre, piso, teléfono del cliente)
3. El sistema calculará automáticamente el precio según la zona
4. Hacer clic en **Confirmar pedido**
5. El pedido aparecerá en el listado con estado **Pendiente**

## 2.3 Seguimiento de pedidos

Desde el panel principal se puede ver en tiempo real el estado de cada pedido:

| Estado | Significado |
|---|---|
| **Pendiente** | Pedido creado, esperando asignación de repartidor |
| **Asignado** | Repartidor asignado, en camino a recoger |
| **En ruta** | Pedido recogido, en camino a la entrega |
| **Entregado** | Entrega confirmada con foto |
| **Fallido** | No se pudo entregar |

Los cambios de estado se actualizan **en tiempo real** sin necesidad de recargar la página.

## 2.4 Ver confirmación de entrega

Cuando el repartidor entrega el pedido y sube la foto de confirmación:
1. Aparece una notificación en tiempo real
2. El estado del pedido cambia a **Entregado**
3. Hacer clic en el pedido para ver la foto de confirmación

## 2.5 Historial de pedidos

- Acceder a **Historial** en el menú lateral
- Filtrar por fecha, estado o repartidor
- Exportar el listado si es necesario

---

# 3. Manual del Repartidor

Este manual está dirigido a los **repartidores** que utilizan LocalXpress para gestionar sus entregas.

## 3.1 Acceso a la aplicación

1. Abrir el navegador en el móvil o tableta y acceder a `https://localxpress.app`
2. Introducir el email y contraseña proporcionados
3. Hacer clic en **Iniciar sesión**

## 3.2 Recibir un nuevo pedido

- Al haber un nuevo pedido disponible, aparece una **notificación en tiempo real**
- En el panel principal se muestra la dirección de recogida y de entrega
- Hacer clic en **Aceptar pedido** para confirmar la asignación

## 3.3 Flujo de una entrega

| Paso | Acción en la app |
|---|---|
| 1 | Aceptar el pedido → estado cambia a **Asignado** |
| 2 | Ir a recoger el pedido a la dirección indicada |
| 3 | Pulsar **Pedido recogido** → estado cambia a **En ruta** |
| 4 | Entregar el pedido al cliente final |
| 5 | Hacer foto de la entrega (paquete en la puerta o con el cliente) |
| 6 | Subir la foto desde la app → estado cambia a **Entregado** |

## 3.4 Subir foto de confirmación de entrega

1. Al llegar a la dirección de entrega, pulsar **Confirmar entrega**
2. Hacer la foto o seleccionarla de la galería
3. Pulsar **Subir foto**
4. La app confirma que la entrega está registrada

> La foto se comprime automáticamente antes de subirse — no es necesario preocuparse por el tamaño del archivo.

## 3.5 Incidencias en la entrega

Si no se puede entregar el pedido:
1. Pulsar **Marcar como fallido**
2. Seleccionar el motivo (cliente ausente, dirección incorrecta, etc.)
3. Añadir una nota descriptiva
4. Confirmar

La tienda recibirá una notificación inmediata del fallo.

---

*Manual generado en Mayo de 2026 — LocalXpress*  
*Administración de Sistemas Informáticos en Red (ASIX) — Proyecto Final*
