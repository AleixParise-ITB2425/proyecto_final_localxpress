# Operación y Mantenimiento — LocalXpress

**Servidor:** `srv1651179` — `187.77.171.221`  
**Fecha:** Mayo 2026  

---

## Tabla de contenidos

1. [Comandos de operación diaria](#1-comandos-de-operación-diaria)
2. [Gestión de procesos PM2](#2-gestión-de-procesos-pm2)
3. [Gestión de PostgreSQL](#3-gestión-de-postgresql)
4. [Gestión de contenedores Docker](#4-gestión-de-contenedores-docker)
5. [Sistema de backups](#5-sistema-de-backups)
6. [Gestión de imágenes de entrega](#6-gestión-de-imágenes-de-entrega)
7. [Logs del sistema](#7-logs-del-sistema)
8. [Nginx y SSL](#8-nginx-y-ssl)
9. [Monitorización del servidor](#9-monitorización-del-servidor)
10. [Tareas de mantenimiento periódico](#10-tareas-de-mantenimiento-periódico)
11. [Procedimientos de recuperación](#11-procedimientos-de-recuperación)

---

## 1. Comandos de operación diaria

### Estado general del sistema

```bash
# Estado de todos los procesos Node.js
pm2 status

# Contenedores Docker activos
docker ps

# Uso de disco
df -h

# Uso de memoria
free -h

# Procesos activos
htop
```

### Verificación rápida de servicios

```bash
# Comprobar que el backend responde
curl -s http://localhost:3001/api/health
curl -s http://localhost:3002/api/health

# Comprobar Nginx
systemctl status nginx

# Comprobar PostgreSQL
systemctl status postgresql

# Comprobar n8n
docker ps | grep n8n
```

---

## 2. Gestión de procesos PM2

### Comandos principales

```bash
# Ver estado de todos los procesos
pm2 status

# Ver logs en tiempo real
pm2 logs localxpress-backend
pm2 logs localxpress-ind-backend

# Ver logs con timestamp
pm2 logs localxpress-backend --timestamp

# Ver las últimas N líneas de log
pm2 logs localxpress-backend --lines 50

# Reiniciar un proceso (con breve downtime)
pm2 restart localxpress-backend

# Recargar sin downtime (recomendado en producción)
pm2 reload localxpress-backend

# Detener un proceso
pm2 stop localxpress-backend

# Reiniciar todos los procesos
pm2 restart all

# Ver métricas de CPU y memoria
pm2 monit
```

### Limpiar logs de PM2

```bash
pm2 flush localxpress-backend
```

### Si PM2 no arranca al reiniciar el servidor

```bash
pm2 startup
pm2 save
```

---

## 3. Gestión de PostgreSQL

### Conectarse a la base de datos

```bash
# Con el usuario de la aplicación
psql -U localxpress -d localxpress -h 127.0.0.1

# Como superusuario (si se necesita)
psql -U postgres -h 127.0.0.1
```

### Consultas de operación habituales

```sql
-- Ver todas las bases de datos y su tamaño
SELECT datname, pg_size_pretty(pg_database_size(datname))
FROM pg_database ORDER BY pg_database_size(datname) DESC;

-- Ver tamaño de cada tabla
SELECT relname, pg_size_pretty(pg_total_relation_size(relid))
FROM pg_catalog.pg_statio_user_tables
ORDER BY pg_total_relation_size(relid) DESC;

-- Ver pedidos recientes
SELECT id, status, created_at FROM stops ORDER BY created_at DESC LIMIT 10;

-- Ver fotos pendientes de limpieza (más de 20 días)
SELECT COUNT(*), pg_size_pretty(SUM(file_size)::bigint)
FROM order_photos
WHERE created_at < NOW() - INTERVAL '20 days';

-- Ver conexiones activas
SELECT pid, usename, application_name, state, query_start
FROM pg_stat_activity WHERE state = 'active';
```

### Gestión del servicio

```bash
# Estado
systemctl status postgresql

# Reiniciar
systemctl restart postgresql

# Recargar configuración sin reiniciar
systemctl reload postgresql

# Ver logs
journalctl -u postgresql -f
tail -f /var/log/postgresql/postgresql-16-main.log
```

---

## 4. Gestión de contenedores Docker

### Comandos habituales

```bash
# Ver contenedores activos
docker ps

# Ver todos los contenedores (incluidos parados)
docker ps -a

# Ver logs de n8n
docker logs n8n
docker logs n8n -f          # En tiempo real
docker logs n8n --tail 50   # Últimas 50 líneas

# Reiniciar n8n
docker restart n8n

# Detener n8n
docker stop n8n

# Iniciar n8n
docker start n8n

# Ver uso de disco de volúmenes
docker system df
```

### Actualizar n8n

```bash
# Parar el contenedor
docker stop n8n

# Eliminar el contenedor (el volumen n8n_data se conserva)
docker rm n8n

# Descargar la nueva imagen
docker pull n8nio/n8n:stable

# Levantar con docker compose (recrea el contenedor con los mismos volúmenes)
cd /home/LocalXpress-APP
docker compose up -d n8n
```

---

## 5. Sistema de backups

### Verificar el último backup

```bash
# Ver el log completo de backups
cat /var/log/localxpress-backup.log

# Ver solo las últimas ejecuciones
tail -30 /var/log/localxpress-backup.log

# Ver cuándo fue el último backup exitoso
grep -i "success\|completado\|OK" /var/log/localxpress-backup.log | tail -5
```

### Lanzar backup manual

```bash
/usr/local/bin/backup-localxpress.sh
```

### Verificar cron de backup

```bash
cat /etc/cron.d/localxpress-backup
```

Debe contener:
```
0 3 * * *   root   /usr/local/bin/backup-localxpress.sh >> /var/log/localxpress-backup.log 2>&1
```

### Restaurar desde backup

```bash
# Descargar el backup de Google Drive al servidor
# (proceso según configuración del script de backup)

# Restaurar el dump en PostgreSQL
gunzip backup_YYYYMMDD.sql.gz
psql -U localxpress -d localxpress -h 127.0.0.1 < backup_YYYYMMDD.sql
```

---

## 6. Gestión de imágenes de entrega

### Ver imágenes actuales en disco

```bash
# Listar todas las fotos con fecha y tamaño
ls -lh /home/LocalXpress-APP/backend/uploads/proofs/

# Contar cuántas hay
ls /home/LocalXpress-APP/backend/uploads/proofs/ | wc -l

# Ver espacio total ocupado
du -sh /home/LocalXpress-APP/backend/uploads/proofs/
```

### Verificar el sistema de limpieza automática

```bash
# Ver log de limpieza en los logs de PM2
pm2 logs localxpress-backend | grep photo-cleanup

# Simular la limpieza sin borrar nada (dry run)
curl -X POST "http://localhost:3001/api/uploads/cleanup?dry_run=true"
```

### Ver fotos en la base de datos

```sql
-- Total de fotos y espacio referenciado
SELECT COUNT(*), pg_size_pretty(SUM(file_size)::bigint) AS total_size
FROM order_photos;

-- Fotos con más de 20 días (candidatas a limpieza)
SELECT COUNT(*) FROM order_photos
WHERE created_at < NOW() - INTERVAL '20 days';
```

---

## 7. Logs del sistema

### Rutas de logs principales

| Servicio | Ruta del log |
|---|---|
| Backend APP | `pm2 logs localxpress-backend` |
| Backend APP-IND | `pm2 logs localxpress-ind-backend` |
| Nginx accesos | `/var/log/nginx/access.log` |
| Nginx errores | `/var/log/nginx/error.log` |
| PostgreSQL | `/var/log/postgresql/postgresql-16-main.log` |
| n8n | `docker logs n8n` |
| Backup | `/var/log/localxpress-backup.log` |
| Sistema | `journalctl -f` |

### Ver logs en tiempo real

```bash
# Backend
pm2 logs localxpress-backend

# Nginx errores
tail -f /var/log/nginx/error.log

# Sistema completo
journalctl -f

# Filtrar errores del sistema de las últimas 2 horas
journalctl --since "2 hours ago" -p err
```

---

## 8. Nginx y SSL

### Gestión de Nginx

```bash
# Estado
systemctl status nginx

# Verificar configuración antes de aplicar cambios
nginx -t

# Recargar sin interrumpir conexiones (recomendado)
systemctl reload nginx

# Reiniciar completamente
systemctl restart nginx
```

### Verificar certificados SSL

```bash
# Ver estado de todos los certificados
certbot certificates

# Renovar manualmente si es necesario
certbot renew --dry-run    # Simular renovación
certbot renew              # Renovar
```

### Verificar renovación automática

```bash
cat /etc/cron.d/certbot
```

---

## 9. Monitorización del servidor

### Recursos del sistema

```bash
# Uso de CPU y memoria en tiempo real
htop

# Uso de disco
df -h

# Procesos que más consumen
ps aux --sort=-%cpu | head -10
ps aux --sort=-%mem | head -10

# Estadísticas de red
ss -tlnp
```

### Monarx (seguridad)

Monarx Agent se ejecuta como demonio en el sistema y se actualiza automáticamente cada sábado. No requiere intervención manual.

```bash
# Ver estado del agente
systemctl status monarx-agent 2>/dev/null || ps aux | grep monarx
```

---

## 10. Tareas de mantenimiento periódico

### Diario (automático)
- ✅ Backup PostgreSQL a Google Drive (03:00h)
- ✅ Limpieza de fotos con más de 20 días (02:30h)
- ✅ Renovación de certificados SSL si procede (cada 12h)
- ✅ Estadísticas del sistema con sysstat (cada 10 min)

### Semanal (manual recomendado)

```bash
# Verificar que los backups se están ejecutando
tail -10 /var/log/localxpress-backup.log

# Verificar estado de todos los servicios
pm2 status
docker ps
systemctl status nginx postgresql

# Revisar logs en busca de errores
pm2 logs localxpress-backend --lines 100 | grep -i error
journalctl --since "7 days ago" -p err | tail -20
```

### Mensual (manual recomendado)

```bash
# Actualizar paquetes del sistema
apt update && apt upgrade -y

# Limpiar imágenes Docker no usadas
docker system prune -f

# Verificar espacio en disco
df -h

# Revisar tamaño de base de datos
psql -U localxpress -d localxpress -h 127.0.0.1 \
  -c "SELECT relname, pg_size_pretty(pg_total_relation_size(relid)) FROM pg_catalog.pg_statio_user_tables ORDER BY pg_total_relation_size(relid) DESC;"
```

---

## 11. Procedimientos de recuperación

### El backend no responde

```bash
# 1. Ver estado
pm2 status

# 2. Ver los últimos logs para identificar el error
pm2 logs localxpress-backend --lines 50

# 3. Reiniciar
pm2 reload localxpress-backend

# 4. Si sigue sin funcionar, reinicio completo
pm2 restart localxpress-backend

# 5. Verificar
curl http://localhost:3001/api/health
```

### Nginx devuelve 502 Bad Gateway

Significa que Nginx está activo pero no llega al backend:

```bash
# Comprobar que el backend está activo
pm2 status

# Si está parado, iniciarlo
pm2 start localxpress-backend

# Verificar que escucha en el puerto correcto
ss -tlnp | grep 3001
```

### PostgreSQL no responde

```bash
# Ver estado
systemctl status postgresql

# Ver logs de error
tail -30 /var/log/postgresql/postgresql-16-main.log

# Reiniciar
systemctl restart postgresql

# Verificar conectividad
psql -U localxpress -d localxpress -h 127.0.0.1 -c "SELECT 1;"
```

### Restaurar estado completo del servidor tras fallo grave

```bash
# 1. Verificar que PostgreSQL está activo
systemctl start postgresql

# 2. Iniciar backend con PM2
pm2 start all

# 3. Iniciar Nginx
systemctl start nginx

# 4. Iniciar contenedores Docker
docker start n8n easypanel

# 5. Verificar estado general
pm2 status && docker ps && systemctl status nginx postgresql
```

### Revertir una actualización de código

```bash
# Ver ramas de seguridad disponibles
git branch | grep backup

# Volver a la rama anterior
cd /home/LocalXpress-APP
git checkout backup-vps-before-update-YYYYMMDD-HHMMSS
cd backend && npm run build
pm2 reload localxpress-backend
```
