# LocalXpress — Documentación Técnica

> Plataforma de reparto local de última milla para negocios y particulares en Barcelona.

---

## Índice de documentación

| Documento | Descripción |
|---|---|
| [DOCUMENTACION_TECNICA.md](./DOCUMENTACION_TECNICA.md) | Arquitectura completa, stack, servicios y funcionamiento interno |
| [GUIA_DESPLIEGUE.md](./GUIA_DESPLIEGUE.md) | Cómo está desplegado el sistema en el VPS paso a paso |
| [OPERACION_MANTENIMIENTO.md](./OPERACION_MANTENIMIENTO.md) | Operativa diaria, backups, logs, PM2 y tareas de mantenimiento |
| [SEGURIDAD.md](./SEGURIDAD.md) | Medidas de seguridad aplicadas por capas |
| [CHANGELOG.md](./CHANGELOG.md) | Historial de versiones y cambios del proyecto |

### Diagramas

| Diagrama | Descripción |
|---|---|
| [arquitectura-general.md](./diagrams/arquitectura-general.md) | Vista completa del sistema |
| [arquitectura-red.md](./diagrams/arquitectura-red.md) | Puertos, servicios públicos y privados |
| [flujo-pedido.md](./diagrams/flujo-pedido.md) | Ciclo de vida completo de un pedido |
| [automatizacion-n8n.md](./diagrams/automatizacion-n8n.md) | Flujos de automatización con n8n |
| [modelo-base-datos.md](./diagrams/modelo-base-datos.md) | Esquema entidad-relación de PostgreSQL |

### Plantillas

| Plantilla | Descripción |
|---|---|
| [env.example.md](./templates/env.example.md) | Variables de entorno necesarias |
| [docker-compose.example.md](./templates/docker-compose.example.md) | Estructura de Docker Compose |
| [incidencia-template.md](./templates/incidencia-template.md) | Plantilla para reportar incidencias |

---

## Resumen del proyecto

**LocalXpress** es una plataforma de reparto local construida completamente desde cero, autoalojada en un VPS propio. Digitaliza la gestión de pedidos y entregas para pequeños negocios (floristerías, panaderías, catering) y particulares en el área de Barcelona.

### Aplicaciones

| Aplicación | Ruta en servidor | Puerto backend | Usuarios |
|---|---|---|---|
| LocalXpress APP | `/home/LocalXpress-APP` | `:3001` | Admin, comercios, repartidores |
| LocalXpress APP-IND | `/home/LocalXpress-APP-IND` | `:3002` | Particulares |

### Stack tecnológico

| Capa | Tecnología |
|---|---|
| Frontend | React + Vite |
| Backend | Node.js + Express + Socket.io |
| Base de datos | PostgreSQL 16 (nativo) |
| Proxy inverso | Nginx + Certbot (Let's Encrypt) |
| Procesos | PM2 |
| Contenedores | Docker (n8n, EasyPanel) |
| Automatizaciones | n8n |
| Servidor | Hostinger VPS — Ubuntu 24.04 LTS |

### Estado del sistema

```
✅ Backend operativo       ✅ PostgreSQL operativo
✅ Nginx + SSL activo      ✅ Backup diario → Google Drive
✅ PM2 gestionando         ✅ n8n activo
✅ Compresión de imágenes  ✅ Limpieza automática cada 20 días
✅ Monarx (seguridad)      ✅ Certbot (renovación SSL)
```

---

*Servidor: `srv1651179` — IP: `187.77.171.221` — Ubuntu 24.04.4 LTS*
*Documentación generada: Mayo 2026*
