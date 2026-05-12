# Seguridad — LocalXpress

**Versión:** 1.0 Final  
**Fecha:** Mayo 2026  
**Servidor:** `srv1651179` — `187.77.171.221`  

---

## Tabla de contenidos

1. [Modelo de seguridad por capas](#1-modelo-de-seguridad-por-capas)
2. [Capa 1 — Red y exposición de servicios](#2-capa-1--red-y-exposición-de-servicios)
3. [Capa 2 — Autenticación y autorización](#3-capa-2--autenticación-y-autorización)
4. [Capa 3 — Cifrado en tránsito](#4-capa-3--cifrado-en-tránsito)
5. [Capa 4 — Gestión de secretos](#5-capa-4--gestión-de-secretos)
6. [Capa 5 — Monitorización activa](#6-capa-5--monitorización-activa)
7. [Seguridad de la base de datos](#7-seguridad-de-la-base-de-datos)
8. [Seguridad en automatizaciones n8n](#8-seguridad-en-automatizaciones-n8n)
9. [Seguridad en el sistema de imágenes](#9-seguridad-en-el-sistema-de-imágenes)
10. [Recomendaciones pendientes](#10-recomendaciones-pendientes)

---

## 1. Modelo de seguridad por capas

LocalXpress aplica seguridad en cinco capas independientes. Un fallo en una capa no compromete el sistema completo.

```
┌─────────────────────────────────────────────┐
│  Capa 5 — Monitorización activa (Monarx)    │
├─────────────────────────────────────────────┤
│  Capa 4 — Gestión de secretos (.env)        │
├─────────────────────────────────────────────┤
│  Capa 3 — Cifrado en tránsito (SSL/TLS)     │
├─────────────────────────────────────────────┤
│  Capa 2 — Autenticación y autorización JWT  │
├─────────────────────────────────────────────┤
│  Capa 1 — Red y exposición de servicios     │
└─────────────────────────────────────────────┘
```

---

## 2. Capa 1 — Red y exposición de servicios

### Mapa de puertos

| Puerto | Servicio | Acceso externo | Justificación |
|---|---|---|---|
| 22 | SSH | ✅ Sí | Administración del servidor |
| 80 | Nginx HTTP | ✅ Sí | Redirige a HTTPS automáticamente |
| 443 | Nginx HTTPS | ✅ Sí | Tráfico de aplicación cifrado |
| 3000 | EasyPanel | ✅ Sí | Panel de administración del servidor |
| 5678 | n8n | ✅ Sí | Gestión de automatizaciones |
| 3001 | Backend APP | ❌ Solo localhost | Nunca expuesto directamente a internet |
| 3002 | Backend APP-IND | ❌ Solo localhost | Nunca expuesto directamente a internet |
| 5432 | PostgreSQL | ⚠️ Expuesto | Ver recomendaciones |
| 65529 | Monarx Agent | ❌ Solo localhost | Agente interno de seguridad |

### Principio de mínima exposición

El backend (`:3001` y `:3002`) nunca recibe peticiones directamente desde internet. Todo el tráfico externo pasa obligatoriamente por Nginx, que valida el dominio, termina el SSL y reenvía la petición al proceso interno correspondiente. Esto elimina una superficie de ataque significativa.

---

## 3. Capa 2 — Autenticación y autorización

### JWT — JSON Web Tokens

Toda la API está protegida mediante JWT. El flujo de autenticación es el siguiente:

```
1. Cliente envía credenciales → POST /api/auth/login
2. Backend verifica email y compara contraseña con hash bcrypt
3. Si es correcto, genera JWT firmado con JWT_SECRET
4. Cliente almacena el token y lo incluye en cada petición:
   Authorization: Bearer <token>
5. Middleware del backend verifica la firma del token
6. Si es válido, extrae userId y role
7. Middleware RBAC comprueba que el rol tiene permiso para el endpoint
```

### RBAC — Control de acceso por roles

Cada endpoint tiene asociado uno o más roles permitidos. Un repartidor no puede acceder a endpoints de administración, y una tienda no puede ver pedidos de otras tiendas.

| Rol | Permisos |
|---|---|
| `admin` | Acceso total al sistema |
| `store` | Solo sus propios pedidos y datos |
| `driver` | Solo pedidos asignados a él |
| `public` | Solo endpoints de autenticación |

### Contraseñas — bcrypt

Las contraseñas nunca se almacenan en texto plano. Se usa bcrypt con 10 rondas de sal, lo que hace computacionalmente inviable un ataque por fuerza bruta incluso si se obtuviera acceso a la base de datos.

```
contraseña_usuario → bcrypt(10 rondas) → hash almacenado en BD
```

---

## 4. Capa 3 — Cifrado en tránsito

### HTTPS con Let's Encrypt

Todo el tráfico entre los clientes y el servidor viaja cifrado mediante TLS. Los certificados son emitidos gratuitamente por Let's Encrypt y gestionados por Certbot.

### Renovación automática

```bash
# Cron configurado en /etc/cron.d/certbot
0 */12 * * *   certbot -q renew
```

Certbot comprueba cada 12 horas si el certificado está próximo a expirar (menos de 30 días) y renueva automáticamente. El servidor nunca opera con certificado caducado.

### Verificar el estado del certificado

```bash
certbot certificates
```

---

## 5. Capa 4 — Gestión de secretos

### Variables de entorno

Todas las credenciales y configuraciones sensibles se gestionan exclusivamente mediante archivos `.env`:

| Archivo | Contenido |
|---|---|
| `/home/LocalXpress-APP/backend/.env` | DB credentials, JWT_SECRET, API keys |
| `/home/LocalXpress-APP/.env` | Configuración global de la APP |
| `/home/LocalXpress-APP-IND/.env` | Configuración APP particulares |

**Reglas aplicadas:**

- Los archivos `.env` están incluidos en `.gitignore` y nunca se versionan en Git.
- Cada entorno (desarrollo, producción) tiene su propio `.env` con credenciales distintas.
- Las credenciales de servicios externos (Google Drive, WhatsApp API, SMTP) se almacenan cifradas en el almacén interno de n8n, nunca en texto plano en el repositorio.

### Variables sensibles críticas

| Variable | Descripción |
|---|---|
| `JWT_SECRET` | Clave de firma de tokens JWT — debe ser aleatoria y larga |
| `DB_PASSWORD` | Contraseña del usuario PostgreSQL de la aplicación |
| `GOOGLE_DRIVE_*` | Credenciales para backup automático |

---

## 6. Capa 5 — Monitorización activa

### Monarx Agent

Monarx es un agente de seguridad instalado de forma nativa en el servidor que proporciona:

- Detección de malware y código malicioso en tiempo real.
- Análisis de comportamiento anómalo del sistema.
- Alertas automáticas ante amenazas detectadas.

Se actualiza automáticamente cada sábado a las 12:14h:

```bash
# /etc/cron.d/monarx-update
14 12 * * 6   root   apt-get install -y -qq monarx-agent monarx-protect monarx-protect-autodetect
```

### sysstat — Estadísticas del sistema

sysstat recoge métricas de rendimiento del servidor cada 10 minutos, permitiendo analizar el comportamiento histórico ante cualquier incidencia:

```bash
# Ver estadísticas de CPU
sar -u

# Ver estadísticas de disco
sar -d

# Ver estadísticas de memoria
sar -r
```

---

## 7. Seguridad de la base de datos

### Usuario de aplicación con permisos mínimos

La aplicación no se conecta a PostgreSQL con el superusuario `postgres`. Utiliza un usuario dedicado (`localxpress`) con permisos únicamente sobre las tablas necesarias, siguiendo el principio de mínimo privilegio.

### Autenticación md5

El archivo `pg_hba.conf` exige autenticación por contraseña (md5) para todas las conexiones, incluidas las locales. No se permite acceso anónimo ni por peer auth a la base de datos de la aplicación.

### Auditoría de acciones

La tabla `audit_logs` registra las acciones críticas realizadas en el sistema (creación de pedidos, cambios de estado, accesos administrativos), proporcionando trazabilidad completa.

---

## 8. Seguridad en automatizaciones n8n

- n8n se ejecuta en su propio contenedor Docker, aislado del sistema operativo host.
- Las credenciales de servicios externos (SMTP, WhatsApp, Google Drive) se almacenan en el almacén cifrado interno de n8n, nunca en texto plano.
- Los webhooks que reciben datos externos validan un token secreto en los headers antes de procesar cualquier carga útil.
- La tabla `email_idempotency` previene la creación de pedidos duplicados por procesamiento repetido del mismo email.

---

## 9. Seguridad en el sistema de imágenes

- Las fotos de confirmación de entrega se almacenan en el servidor con nombres generados automáticamente (timestamp + hash aleatorio), haciendo imposible predecir las URLs de otros archivos.
- El acceso a las imágenes requiere autenticación JWT válida.
- La limpieza automática cada 20 días elimina tanto los archivos físicos como las referencias en base de datos, sin dejar datos huérfanos.
- La compresión con sharp reduce el tamaño de las imágenes antes de almacenarlas, minimizando la superficie de datos expuesta.

---

## 10. Recomendaciones pendientes

| Mejora | Prioridad | Descripción |
|---|---|---|
| Restringir PostgreSQL a localhost | 🔴 Alta | El puerto 5432 está expuesto a internet. Restringir `listen_addresses` en `postgresql.conf` a `127.0.0.1` o proteger con firewall |
| Firewall UFW | 🔴 Alta | Configurar UFW permitiendo solo los puertos necesarios: 22, 80, 443, 3000, 5678 |
| Fail2ban | 🟡 Media | Protección automática contra fuerza bruta en SSH y endpoint de login |
| Alertas de backup fallido | 🟡 Media | Notificación por email si el cron de backup nocturno no se ejecuta correctamente |
| Restringir acceso a EasyPanel y n8n | 🟡 Media | Limitar acceso por IP o añadir autenticación adicional a los puertos 3000 y 5678 |
| Rotación de JWT_SECRET | 🟢 Baja | Política de rotación periódica de la clave de firma JWT |

### Aplicar firewall UFW

```bash
# Habilitar UFW
ufw enable

# Permitir solo los puertos necesarios
ufw allow 22/tcp      # SSH
ufw allow 80/tcp      # HTTP
ufw allow 443/tcp     # HTTPS
ufw allow 3000/tcp    # EasyPanel
ufw allow 5678/tcp    # n8n

# Bloquear PostgreSQL al exterior (solo localhost)
ufw deny 5432/tcp

# Ver estado
ufw status verbose
```

### Restringir PostgreSQL a localhost

```bash
# Editar postgresql.conf
nano /etc/postgresql/16/main/postgresql.conf

# Cambiar la línea:
# listen_addresses = '*'
# Por:
listen_addresses = 'localhost'

# Recargar
systemctl reload postgresql
```
