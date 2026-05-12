# Changelog — LocalXpress

Historial de versiones y cambios del proyecto.

---

## [1.0.0] — Mayo 2026 — Versión final

### Infraestructura
- Migración completa a VPS Hostinger (Ubuntu 24.04 LTS)
- Instalación nativa de PostgreSQL 16, Nginx, Node.js y PM2
- Configuración de Docker para servicios auxiliares (n8n, EasyPanel)
- SSL con Let's Encrypt + Certbot con renovación automática cada 12h
- Monarx Agent instalado para monitorización de seguridad activa

### Backend
- API REST completa con Node.js/Express
- Autenticación JWT con control de roles (RBAC): admin, store, driver
- WebSocket con Socket.io para actualizaciones en tiempo real
- Sistema de migraciones SQL versionadas (001-008)
- Gestión de procesos con PM2 y reinicio automático

### Base de datos
- Esquema PostgreSQL completo con 9 tablas principales
- Índices de rendimiento aplicados (migración 002)
- Modelo de precios por zona con precio para repartidor (migración 004)
- Sistema de notificaciones de entrega (migración 007)
- Control de idempotencia para procesamiento de emails (migración 008)

### Sistema de imágenes
- Compresión automática con sharp al subir (calidad 70%, máximo 1200px)
- Almacenamiento en filesystem con referencias en tabla `order_photos`
- Limpieza automática diaria a las 02:30h de archivos con más de 20 días
- Endpoint de limpieza manual con modo dry_run para verificación

### Backup automático
- Script `/usr/local/bin/backup-localxpress.sh` operativo
- Cron diario a las 03:00h con subida automática a Google Drive
- Log de ejecución en `/var/log/localxpress-backup.log`
- Ramas Git de seguridad antes de cada cambio crítico en producción

### Automatizaciones n8n
- n8n desplegado en Docker con volumen persistente `n8n_data`
- Flujos configurados: captura de pedidos por email y WhatsApp
- Idempotencia implementada para evitar pedidos duplicados

### Aplicaciones
- LocalXpress APP (admin, tienda, repartidor) — backend en :3001
- LocalXpress APP-IND (particulares) — backend en :3002
- Frontend React + Vite con autenticación JWT integrada

---

## Problemas resueltos durante el desarrollo

| Versión | Problema | Solución |
|---|---|---|
| 0.1 | Docker no disponible en entorno local Windows | Migración a VPS Ubuntu |
| 0.2 | Pérdida de archivos en AWS EC2 | Migración a Hostinger con volúmenes persistentes |
| 0.3 | Frontend no conectaba al backend | CORS en Express + variables de entorno corregidas |
| 0.4 | Login fallaba en frontend | Corrección de directivas CSP en Vite |
| 0.5 | JWT no llegaba al backend | Interceptor Axios para inyectar token automáticamente |
| 0.6 | Socket.io no reconectaba tras refresco | Lógica de inicialización ajustada para esperar token |
| 0.7 | Fotos sin comprimir ocupando demasiado espacio | sharp integrado: calidad 70%, máximo 1200px |
| 0.8 | Sin política de borrado de imágenes antiguas | node-cron: limpieza diaria de archivos con más de 20 días |
| 0.9 | Contraseña PostgreSQL perdida (md5 en pg_hba.conf) | Reset via peer auth temporal + nueva contraseña |
