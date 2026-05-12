# Documentación Técnica — LocalXpress

**Versión:** 1.0 Final  
**Fecha:** Mayo 2026  
**Servidor:** `srv1651179` — `187.77.171.221`  

---

## Tabla de contenidos

1. [Resumen ejecutivo](#1-resumen-ejecutivo)
2. [Objetivos del proyecto](#2-objetivos-del-proyecto)
3. [Aplicaciones del sistema](#3-aplicaciones-del-sistema)
4. [Stack tecnológico](#4-stack-tecnológico)
5. [Arquitectura del sistema](#5-arquitectura-del-sistema)
6. [Backend y API REST](#6-backend-y-api-rest)
7. [Base de datos PostgreSQL](#7-base-de-datos-postgresql)
8. [Gestión de imágenes de entrega](#8-gestión-de-imágenes-de-entrega)
9. [Proxy inverso y SSL](#9-proxy-inverso-y-ssl)
10. [Servicios Docker](#10-servicios-docker)
11. [Gestión de procesos con PM2](#11-gestión-de-procesos-con-pm2)
12. [Automatizaciones con n8n](#12-automatizaciones-con-n8n)
13. [Sistema de backup automático](#13-sistema-de-backup-automático)
14. [Problemas resueltos](#14-problemas-resueltos)

---

## 1. Resumen ejecutivo

LocalXpress es una plataforma de reparto local de última milla construida completamente desde cero, sin depender de plantillas ni soluciones cerradas de terceros. Cada componente — servidor, base de datos, APIs, automatizaciones, backups y sistema de imágenes — fue diseñado, configurado y desplegado a medida.

La plataforma conecta a pequeños negocios (floristerías, panaderías, empresas de catering) con repartidores, y permite la gestión completa de pedidos y entregas en tiempo real. Existe también una segunda aplicación independiente orientada a particulares que necesitan envíos puntuales.

Todo el sistema corre en un VPS propio en Hostinger (Ubuntu 24.04 LTS) con control total sobre datos, configuración y coste.

---

## 2. Objetivos del proyecto

- Digitalizar la gestión de pedidos eliminando dependencias de WhatsApp, llamadas y papel.
- Centralizar toda la información en una base de datos propia bajo control total.
- Mejorar la trazabilidad de cada entrega con confirmación fotográfica.
- Automatizar procesos operativos: captura de pedidos, notificaciones y backups.
- Construir una infraestructura escalable y autoalojada sin costes variables de terceros.
- Aplicar seguridad por capas en red, aplicación, base de datos y automatizaciones.

---

## 3. Aplicaciones del sistema

El sistema está compuesto por dos aplicaciones independientes, cada una con su propio proceso backend, configuración y entorno:

### LocalXpress APP — Aplicación principal

| Parámetro | Valor |
|---|---|
| Directorio | `/home/LocalXpress-APP/` |
| Backend | Node.js/Express — PM2 — `:3001` |
| Frontend | React + Vite |
| Usuarios | Administradores, comercios, repartidores |
| Variables de entorno | `/home/LocalXpress-APP/backend/.env` |

**Roles de usuario:**

| Rol | Funciones principales |
|---|---|
| Administrador | Gestión global: usuarios, zonas, tarifas, métricas |
| Comercio / Tienda | Crear pedidos, seguimiento, confirmaciones |
| Repartidor | Recibir asignaciones, actualizar estado, subir foto de entrega |

### LocalXpress APP-IND — Aplicación para particulares

| Parámetro | Valor |
|---|---|
| Directorio | `/home/LocalXpress-APP-IND/` |
| Backend | Node.js — PM2 — `:3002` |
| Frontend | React + Vite |
| Usuarios | Particulares que solicitan envíos |
| Variables de entorno | `/home/LocalXpress-APP-IND/.env` |

---

## 4. Stack tecnológico

| Capa | Tecnología | Modo de despliegue |
|---|---|---|
| Frontend | React 18 + Vite | Build estático servido por Nginx |
| Backend | Node.js + Express | Nativo, gestionado por PM2 |
| Tiempo real | Socket.io | Integrado en el proceso backend |
| Base de datos | PostgreSQL 16 | Nativo en el SO |
| Proxy inverso | Nginx | Nativo en el SO |
| SSL | Let's Encrypt + Certbot | Nativo, renovación automática |
| Procesos | PM2 + pm2-logrotate | Nativo en el SO |
| Contenedores | Docker + Docker Compose | Motor Docker nativo |
| Automatizaciones | n8n | Contenedor Docker |
| Panel servidor | EasyPanel | Contenedor Docker |
| Seguridad activa | Monarx Agent | Nativo en el SO |
| Compresión imágenes | sharp (Node.js) | Integrado en el backend |

---

## 5. Arquitectura del sistema

### Vista general

```
Internet
    │
    ▼
┌─────────────────────────────────────────────────────────────┐
│  VPS Hostinger — srv1651179 — Ubuntu 24.04 LTS — 96GB       │
│                                                             │
│  ┌──────────────────────────────────────────────────────┐  │
│  │  Nginx — Proxy inverso + SSL (Certbot/Let's Encrypt) │  │
│  │  :80 (redirige) → :443 (HTTPS)                       │  │
│  └────────────────┬─────────────────────┬───────────────┘  │
│                   │                     │                   │
│       ┌───────────▼──────┐   ┌──────────▼──────┐          │
│       │  Backend APP     │   │  Backend APP-IND │          │
│       │  Node.js :3001   │   │  Node.js :3002   │          │
│       │  (PM2)           │   │  (PM2)           │          │
│       └───────────┬──────┘   └──────────┬───────┘          │
│                   │                     │                   │
│       ┌───────────▼─────────────────────▼───────┐          │
│       │        PostgreSQL 16 :5432 (nativo)      │          │
│       │   BD principal + tabla order_photos      │          │
│       └─────────────────────────────────────────┘          │
│                                                             │
│  ┌──────────────┐  ┌──────────────┐  ┌─────────────────┐  │
│  │  n8n :5678   │  │ EasyPanel    │  │  Monarx Agent   │  │
│  │  (Docker)    │  │ :3000(Docker)│  │  (seguridad)    │  │
│  └──────────────┘  └──────────────┘  └─────────────────┘  │
│                                                             │
│  Imágenes: /home/LocalXpress-APP/backend/uploads/proofs/   │
│  Cron backup: 03:00h diario → Google Drive                  │
│  Cron limpieza fotos: 02:30h diario → borra +20 días        │
└─────────────────────────────────────────────────────────────┘
```

### Recursos del servidor

| Recurso | Total | En uso | Disponible |
|---|---|---|---|
| Disco | 96 GB | 11 GB (11%) | 85 GB |
| RAM | 7.8 GB | 1.6 GB | 6.1 GB |
| SO | Ubuntu 24.04.4 LTS | Kernel 6.8.0-107 | — |

---

## 6. Backend y API REST

### Arquitectura interna

El backend sigue el patrón MVC ligero con Express. Las peticiones pasan por middlewares globales (CORS, body-parser), luego por el router, después por los middlewares de autenticación y autorización, y finalmente llegan al controlador que interactúa con PostgreSQL.

```
Petición HTTP
    │
    ▼
Middleware global (CORS, body-parser)
    │
    ▼
Router (/api/auth, /api/orders, /api/uploads...)
    │
    ▼
Middleware auth (verifyToken JWT → checkRole)
    │
    ▼
Controlador (lógica de negocio)
    │
    ▼
Pool pg → PostgreSQL
```

### Autenticación JWT

El login devuelve un token JWT firmado con `JWT_SECRET` que incluye `userId` y `role`. El cliente lo incluye en cada petición como `Authorization: Bearer <token>`. Un middleware verifica la firma y extrae el rol para aplicar RBAC.

### Endpoints principales

| Método | Endpoint | Rol | Descripción |
|---|---|---|---|
| POST | `/api/auth/login` | Público | Login, devuelve JWT |
| POST | `/api/auth/register` | Admin | Registro de usuarios |
| GET | `/api/orders` | Admin / Tienda | Lista de pedidos |
| POST | `/api/orders` | Tienda | Crear pedido |
| PATCH | `/api/orders/:id/status` | Repartidor | Actualizar estado |
| POST | `/api/uploads/proof` | Repartidor | Subir foto de entrega (comprimida) |
| POST | `/api/uploads/cleanup?dry_run=true` | Admin | Lanzar limpieza manual de fotos |
| GET | `/api/zones` | Admin | Zonas de reparto |
| GET | `/api/rates` | Admin / Tienda | Tarifas por zona |

### WebSocket — Socket.io

| Evento | Dirección | Descripción |
|---|---|---|
| `new_order` | Servidor → Repartidores | Nuevo pedido disponible |
| `order_updated` | Servidor → Tienda/Admin | Cambio de estado |
| `order_confirmed` | Servidor → Tienda | Entrega confirmada con foto |

### Migraciones SQL versionadas

```
database/migrations/
├── 001_schema_inicial.sql
├── 002_performance_indexes.sql
├── 004_pricing_zone_driver_price.sql
├── 007_delivery_notifications.sql
└── 008_email_idempotency.sql
```

---

## 7. Base de datos PostgreSQL

### Configuración

| Parámetro | Valor |
|---|---|
| Motor | PostgreSQL 16 |
| Despliegue | Nativo en el SO (máximo rendimiento I/O) |
| Puerto | 5432 |
| Ubicación datos | `/var/lib/postgresql/16/main/` |
| Gestión | systemd |

### Tablas principales

| Tabla | Descripción |
|---|---|
| `users` | Usuarios del sistema (admin, tienda, repartidor) |
| `profiles` | Perfil extendido de cada usuario |
| `user_roles` | Asignación de roles |
| `stops` | Paradas/entregas de cada pedido |
| `stops_archive` | Histórico de paradas completadas |
| `order_photos` | Referencias a fotos de confirmación de entrega |
| `pricing_zones` | Zonas de reparto y configuración de precios |
| `driver_locations` | Ubicación en tiempo real de repartidores |
| `audit_logs` | Registro de auditoría de acciones críticas |

### Tabla order_photos (imágenes de entrega)

```sql
order_photos (
    id          UUID PRIMARY KEY,
    stop_id     UUID REFERENCES stops(id),
    driver_id   UUID REFERENCES users(id),
    file_path   VARCHAR,   -- ruta relativa: proofs/nombre-archivo.jpg
    file_size   INTEGER,   -- tamaño en bytes
    mime_type   VARCHAR,   -- image/jpeg
    created_at  TIMESTAMPTZ
)
```

### Idempotencia para procesamiento de emails

La tabla `email_idempotency` (migración 008) evita la creación de pedidos duplicados cuando n8n procesa el mismo email más de una vez. Antes de crear un pedido, se comprueba si el `message_id` del email ya fue procesado.

---

## 8. Gestión de imágenes de entrega

### Arquitectura del sistema de imágenes

Las imágenes de confirmación de entrega se almacenan en el **filesystem del servidor**, con sus referencias en PostgreSQL. Este patrón es el correcto: nunca se guardan binarios en la base de datos.

```
Repartidor sube foto
        │
        ▼
Middleware multer (memoryStorage)
        │
        ▼
sharp — compresión automática
  • Calidad JPEG: 70%
  • Ancho máximo: 1200px
  • Formato: jpeg siempre
        │
        ▼
Guardado en disco:
/home/LocalXpress-APP/backend/uploads/proofs/{timestamp}-{hash}.jpg
        │
        ▼
INSERT en order_photos (stop_id, driver_id, file_path, file_size, mime_type)
```

### Impacto de la compresión

| Escenario | Tamaño por foto | 600 fotos/ciclo |
|---|---|---|
| Sin compresión | ~2 MB | ~1.2 GB |
| Con sharp (70%) | ~100 KB | ~60 MB |

### Limpieza automática cada 20 días

Un job programado con `node-cron` dentro del propio backend se ejecuta cada día a las **02:30h** y:

1. Localiza archivos en `uploads/proofs/` con más de 20 días de antigüedad.
2. Los elimina del filesystem.
3. Elimina las filas correspondientes en `order_photos`.
4. Registra cuántos archivos borró en el log de PM2.

El endpoint `POST /api/uploads/cleanup?dry_run=true` permite simular la limpieza sin borrar nada, útil para verificar el funcionamiento.

**Estimación de disco en régimen estacionario:** ~60 MB máximo en todo momento, independientemente del volumen de entregas.

---

## 9. Proxy inverso y SSL

### Nginx

Nginx es la única puerta de entrada al sistema desde internet. Gestiona:

- Terminación SSL (descifra HTTPS antes de pasar la petición al backend).
- Redirección automática de HTTP a HTTPS.
- Enrutamiento hacia los backends internos (`localhost:3001` y `localhost:3002`).
- Servicio de archivos estáticos del frontend compilado.

Los puertos `:3001` y `:3002` del backend no están expuestos a internet — solo son accesibles desde `localhost`.

### Certbot — Renovación automática SSL

Los certificados Let's Encrypt se renuevan automáticamente mediante cron:

```
0 */12 * * *   certbot -q renew
```

Certbot comprueba cada 12 horas si el certificado está a menos de 30 días de expirar y renueva solo cuando es necesario. El servidor nunca opera con certificado caducado.

---

## 10. Servicios Docker

Los servicios auxiliares corren en contenedores Docker, aislados del sistema operativo:

| Contenedor | Imagen | Puerto | Estado | Persistencia |
|---|---|---|---|---|
| `n8n` | `n8nio/n8n:stable` | 5678 | ✅ Activo | Volumen `n8n_data` |
| `easypanel` | `easypanel/easypanel:latest` | 3000 | ✅ Activo | Interno |
| `easypanel-traefik` | `traefik:3.6.7` | — | Standby | — |

### Volumen n8n_data

```
/var/lib/docker/volumes/n8n_data/_data/
```

Contiene toda la configuración de n8n: flujos de trabajo, credenciales cifradas de servicios externos y base de datos interna. Persiste ante reinicios y actualizaciones del contenedor.

### Archivos Docker Compose

| Archivo | Uso |
|---|---|
| `docker-compose.yml` | Producción |
| `docker-compose.dev.yml` | Desarrollo local |
| `docker-compose.backup.yml` | Operaciones de backup |

---

## 11. Gestión de procesos con PM2

PM2 gestiona los procesos Node.js de ambos backends en producción:

| Proceso | Puerto | Directorio |
|---|---|---|
| `localxpress-backend` | 3001 | `/home/LocalXpress-APP/backend/` |
| Backend APP-IND | 3002 | `/home/LocalXpress-APP-IND/` |

**Funciones de PM2:**
- Reinicio automático si el proceso cae.
- Arranque automático al reiniciar el servidor.
- Gestión centralizada de logs con rotación (pm2-logrotate).
- Monitorización de CPU y memoria por proceso.

---

## 12. Automatizaciones con n8n

n8n actúa como motor de integración entre los canales externos de entrada de pedidos y el backend de LocalXpress.

### Flujos implementados

| Flujo | Disparador | Acciones |
|---|---|---|
| Captura de pedidos por email | Email entrante | Validar idempotencia → POST /api/orders → notificar tienda |
| Captura por WhatsApp | Mensaje WhatsApp Business API | Interpretar → crear pedido vía API |
| Notificaciones de entrega | Webhook backend (evento entrega) | Email o WhatsApp al cliente final |
| Reporte diario | Cron n8n 20:00h | Consultar BD → generar resumen → enviar por email |

### Idempotencia en procesamiento de emails

Antes de crear un pedido desde un email, n8n consulta la tabla `email_idempotency` para verificar si el `message_id` ya fue procesado. Si existe, omite la creación y evita duplicados.

---

## 13. Sistema de backup automático

### Componentes

| Elemento | Ruta / Valor |
|---|---|
| Script | `/usr/local/bin/backup-localxpress.sh` |
| Log | `/var/log/localxpress-backup.log` |
| Cron | `/etc/cron.d/localxpress-backup` |
| Frecuencia | Diaria a las 03:00h |
| Destino | Google Drive |
| Herramienta | `pg_dump` (volcado completo PostgreSQL) |

### Flujo del backup

```
03:00h — Cron dispara el script
    │
    ▼
pg_dump — Volcado completo de PostgreSQL
    │
    ▼
Compresión del archivo (.sql.gz)
    │
    ▼
Subida automática a Google Drive
    │
    ▼
Registro en /var/log/localxpress-backup.log
```

### Backup de código — Ramas Git de seguridad

Antes de cada cambio crítico en producción se crea una rama Git con nombre descriptivo y fecha:

```
backup-vps-before-scheduled-time-20260511-143451
backup-vps-before-final-pickup-fix-20260511-160817
```

Esto permite revertir cualquier cambio en producción en cuestión de segundos.

---

## 14. Problemas resueltos

| Problema | Causa | Solución aplicada |
|---|---|---|
| Docker no disponible en entorno local | Windows sin WSL | Migración a VPS Ubuntu |
| Pérdida de archivos en AWS EC2 | Volúmenes Docker no persistentes | Migración a Hostinger VPS con volúmenes montados correctamente |
| Frontend no conectaba al backend | CORS no configurado, URL apuntando a Supabase | CORS en Express + variables de entorno actualizadas |
| Login fallaba en frontend | CSP bloqueaba peticiones al backend | Corrección de directivas CSP en Vite |
| JWT no llegaba al backend | Header Authorization no incluido en Axios | Interceptor Axios para inyectar token en cada petición |
| Socket.io no reconectaba tras refresco | Token no recuperado al inicializar socket | Lógica de inicialización ajustada para esperar token |
| Fotos ocupando demasiado espacio | Sin compresión al subir | sharp integrado: calidad 70%, máximo 1200px |
| Acumulación ilimitada de fotos | Sin política de borrado | node-cron: limpieza diaria de archivos >20 días |
| Contraseña PostgreSQL perdida | md5 en pg_hba.conf + contraseña cambiada | Reset via peer auth temporal + nueva contraseña establecida |
