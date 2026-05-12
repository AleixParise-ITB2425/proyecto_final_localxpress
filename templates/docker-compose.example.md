# Plantilla — Docker Compose

> Referencia de la configuración Docker Compose del proyecto LocalXpress.  
> Los servicios de backend y PostgreSQL corren de forma **nativa** en el servidor (no en Docker).  
> Docker se usa exclusivamente para los servicios auxiliares: n8n y EasyPanel.

---

## docker-compose.yml — Producción

```yaml
version: '3.8'

services:

  # ─────────────────────────────────────────
  # n8n — Motor de automatizaciones
  # ─────────────────────────────────────────
  n8n:
    image: n8nio/n8n:stable
    container_name: n8n
    restart: always
    ports:
      - "5678:5678"
    environment:
      - N8N_BASIC_AUTH_ACTIVE=true
      - N8N_BASIC_AUTH_USER=${N8N_USER}
      - N8N_BASIC_AUTH_PASSWORD=${N8N_PASSWORD}
      - N8N_HOST=${N8N_HOST}
      - N8N_PORT=5678
      - N8N_PROTOCOL=https
      - WEBHOOK_URL=https://${N8N_HOST}/
      - GENERIC_TIMEZONE=Europe/Madrid
      - TZ=Europe/Madrid
      # Conexión a PostgreSQL nativo del host
      - DB_TYPE=postgresdb
      - DB_POSTGRESDB_HOST=host-gateway
      - DB_POSTGRESDB_PORT=5432
      - DB_POSTGRESDB_DATABASE=n8n
      - DB_POSTGRESDB_USER=${DB_USER}
      - DB_POSTGRESDB_PASSWORD=${DB_PASSWORD}
    volumes:
      - n8n_data:/home/node/.n8n
    extra_hosts:
      - "host-gateway:host-gateway"
    networks:
      - localxpress_net

# ─────────────────────────────────────────
# Volúmenes persistentes
# ─────────────────────────────────────────
volumes:
  n8n_data:
    driver: local

# ─────────────────────────────────────────
# Red interna
# ─────────────────────────────────────────
networks:
  localxpress_net:
    driver: bridge
```

---

## docker-compose.dev.yml — Desarrollo local

```yaml
version: '3.8'

services:

  # PostgreSQL para desarrollo (solo en entorno local)
  postgres:
    image: postgres:16
    container_name: localxpress-postgres-dev
    restart: unless-stopped
    ports:
      - "5432:5432"
    environment:
      POSTGRES_DB: localxpress
      POSTGRES_USER: localxpress
      POSTGRES_PASSWORD: ${DB_PASSWORD}
    volumes:
      - postgres_dev_data:/var/lib/postgresql/data
      - ./database/migrations:/docker-entrypoint-initdb.d
    networks:
      - localxpress_dev

  # Backend en desarrollo con hot-reload
  backend:
    build:
      context: ./backend
      dockerfile: Dockerfile.dev
    container_name: localxpress-backend-dev
    restart: unless-stopped
    ports:
      - "3001:3001"
    environment:
      NODE_ENV: development
      PORT: 3001
      DB_HOST: postgres
      DB_PORT: 5432
      DB_NAME: localxpress
      DB_USER: localxpress
      DB_PASSWORD: ${DB_PASSWORD}
      JWT_SECRET: ${JWT_SECRET}
    volumes:
      - ./backend:/app
      - /app/node_modules
    depends_on:
      - postgres
    networks:
      - localxpress_dev

  # n8n para desarrollo
  n8n:
    image: n8nio/n8n:stable
    container_name: localxpress-n8n-dev
    restart: unless-stopped
    ports:
      - "5678:5678"
    environment:
      - GENERIC_TIMEZONE=Europe/Madrid
    volumes:
      - n8n_dev_data:/home/node/.n8n
    networks:
      - localxpress_dev

volumes:
  postgres_dev_data:
  n8n_dev_data:

networks:
  localxpress_dev:
    driver: bridge
```

---

## docker-compose.backup.yml — Operaciones de backup

```yaml
version: '3.8'

services:

  # Contenedor temporal para ejecutar pg_dump
  backup:
    image: postgres:16
    container_name: localxpress-backup
    restart: "no"
    environment:
      PGPASSWORD: ${DB_PASSWORD}
    volumes:
      - ./backups:/backups
    command: >
      pg_dump
        -h host-gateway
        -U ${DB_USER}
        -d localxpress
        -f /backups/backup_$(date +%Y%m%d_%H%M%S).sql
        --no-password
    extra_hosts:
      - "host-gateway:host-gateway"
```

---

## Variables de entorno para Docker Compose

Crear un archivo `.env` en el mismo directorio que `docker-compose.yml`:

```env
# n8n
N8N_USER=admin
N8N_PASSWORD=contraseña_segura
N8N_HOST=n8n.tudominio.com

# Base de datos (para contenedores que necesitan conectar al PG nativo)
DB_USER=localxpress
DB_PASSWORD=contraseña_bd_segura

# JWT
JWT_SECRET=clave_jwt_larga_y_aleatoria
```

---

## Comandos habituales

```bash
# Levantar servicios en producción
docker compose up -d

# Ver estado
docker compose ps

# Ver logs de n8n
docker compose logs n8n -f

# Parar todos los servicios
docker compose down

# Parar y eliminar volúmenes (¡cuidado en producción!)
docker compose down -v

# Actualizar imagen de n8n
docker compose pull n8n
docker compose up -d n8n

# Ejecutar backup manual
docker compose -f docker-compose.backup.yml up --abort-on-container-exit
```
