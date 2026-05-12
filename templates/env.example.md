# Plantilla — Variables de Entorno

> Copia este archivo como `.env` en el directorio correspondiente y rellena los valores.  
> **Nunca subas archivos `.env` reales a Git.**

---

## Backend — `/home/LocalXpress-APP/backend/.env`

```env
# ─────────────────────────────────────────
# SERVIDOR
# ─────────────────────────────────────────
NODE_ENV=production
PORT=3001

# ─────────────────────────────────────────
# BASE DE DATOS — PostgreSQL
# ─────────────────────────────────────────
DB_HOST=127.0.0.1
DB_PORT=5432
DB_NAME=localxpress
DB_USER=localxpress
DB_PASSWORD=contraseña_segura_aqui

# ─────────────────────────────────────────
# AUTENTICACIÓN — JWT
# ─────────────────────────────────────────
# Generar con: openssl rand -hex 64
JWT_SECRET=clave_secreta_larga_y_aleatoria_aqui
JWT_EXPIRES_IN=8h

# ─────────────────────────────────────────
# CORS
# ─────────────────────────────────────────
CORS_ORIGIN=https://tudominio.com

# ─────────────────────────────────────────
# ALMACENAMIENTO DE IMÁGENES
# ─────────────────────────────────────────
UPLOADS_DIR=uploads/proofs
MAX_FILE_SIZE_MB=10
IMAGE_QUALITY=70
IMAGE_MAX_WIDTH=1200

# ─────────────────────────────────────────
# LIMPIEZA AUTOMÁTICA DE FOTOS
# ─────────────────────────────────────────
PHOTO_RETENTION_DAYS=20
CLEANUP_CRON=30 2 * * *

# ─────────────────────────────────────────
# SOCKET.IO
# ─────────────────────────────────────────
SOCKET_CORS_ORIGIN=https://tudominio.com

# ─────────────────────────────────────────
# WEBHOOKS — Token de seguridad para n8n
# ─────────────────────────────────────────
# Generar con: openssl rand -hex 32
WEBHOOK_SECRET=token_secreto_para_webhooks_n8n
```

---

## APP principal — `/home/LocalXpress-APP/.env`

```env
# ─────────────────────────────────────────
# FRONTEND — Variables de Vite
# ─────────────────────────────────────────
VITE_API_URL=https://tudominio.com/api
VITE_SOCKET_URL=https://tudominio.com
VITE_APP_NAME=LocalXpress
VITE_APP_VERSION=1.0.0
```

---

## APP particulares — `/home/LocalXpress-APP-IND/.env`

```env
# ─────────────────────────────────────────
# FRONTEND — Variables de Vite
# ─────────────────────────────────────────
VITE_API_URL=https://particulares.tudominio.com/api
VITE_SOCKET_URL=https://particulares.tudominio.com
VITE_APP_NAME=LocalXpress — Particulares
VITE_APP_VERSION=1.0.0

# ─────────────────────────────────────────
# BACKEND
# ─────────────────────────────────────────
NODE_ENV=production
PORT=3002
DB_HOST=127.0.0.1
DB_PORT=5432
DB_NAME=localxpress
DB_USER=localxpress
DB_PASSWORD=contraseña_segura_aqui
JWT_SECRET=clave_secreta_larga_y_aleatoria_aqui
JWT_EXPIRES_IN=8h
```

---

## Notas de seguridad

- `JWT_SECRET` debe ser una cadena aleatoria de al menos 64 caracteres. Generar con:
  ```bash
  openssl rand -hex 64
  ```
- `WEBHOOK_SECRET` debe ser diferente al JWT_SECRET. Generar con:
  ```bash
  openssl rand -hex 32
  ```
- Los archivos `.env` deben tener permisos restrictivos:
  ```bash
  chmod 600 /home/LocalXpress-APP/backend/.env
  ```
- Verificar que `.env` está en `.gitignore`:
  ```bash
  echo ".env" >> .gitignore
  ```
