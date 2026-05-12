# Diagrama — Automatizaciones n8n

## Arquitectura general de n8n en LocalXpress

```mermaid
flowchart TD
    subgraph ENTRADA["Canales de entrada"]
        EMAIL[📧 Email entrante]
        WA[💬 WhatsApp Business API]
        WEBHOOK[🔗 Webhook del backend]
        CRON_N8N[⏰ Cron n8n 20:00h diario]
    end

    subgraph N8N["n8n — Motor de automatización (Docker :5678)"]
        FLOW1[Flujo: Captura pedidos por email]
        FLOW2[Flujo: Captura pedidos por WhatsApp]
        FLOW3[Flujo: Notificación de entrega]
        FLOW4[Flujo: Reporte diario operativo]
    end

    subgraph SALIDA["Acciones de salida"]
        API[Backend API POST /api/orders]
        DB[(PostgreSQL)]
        SMTP[📧 Email notificación cliente]
        WA_OUT[💬 WhatsApp notificación cliente]
    end

    EMAIL --> FLOW1
    WA --> FLOW2
    WEBHOOK --> FLOW3
    CRON_N8N --> FLOW4

    FLOW1 --> API
    FLOW2 --> API
    FLOW3 --> SMTP
    FLOW3 --> WA_OUT
    FLOW4 --> DB
    FLOW4 --> SMTP
```

## Flujo 1 — Captura de pedidos por email

```mermaid
flowchart TD
    EMAIL[📧 Email entrante] --> TRIGGER[n8n Email Trigger IMAP]
    TRIGGER --> EXTRACT[Extraer datos del email]
    EXTRACT --> IDEMPOTENCY{message_id ya en email_idempotency?}
    IDEMPOTENCY -->|Sí — duplicado| SKIP[Ignorar — no crear pedido]
    IDEMPOTENCY -->|No — nuevo| PARSE[Parsear pickup, delivery, zona]
    PARSE --> VALIDATE{Datos válidos?}
    VALIDATE -->|No| ERROR[Notificación de error al admin]
    VALIDATE -->|Sí| CREATE[POST /api/orders]
    CREATE --> RECORD[INSERT email_idempotency]
    CREATE --> NOTIFY[Socket.io notifica a repartidores]
```

## Flujo 2 — Captura de pedidos por WhatsApp

```mermaid
flowchart TD
    WA[💬 Mensaje WhatsApp Business API] --> WEBHOOK_WA[n8n Webhook recibe mensaje]
    WEBHOOK_WA --> VALIDATE_TOKEN{Token secreto válido en header?}
    VALIDATE_TOKEN -->|No| REJECT[401 Rechazar]
    VALIDATE_TOKEN -->|Sí| PARSE_WA[Interpretar mensaje y extraer datos]
    PARSE_WA --> CREATE_WA[POST /api/orders]
    CREATE_WA --> CONFIRM_WA[Responder por WhatsApp: Pedido recibido ID]
```

## Flujo 3 — Notificación de entrega al cliente

```mermaid
flowchart TD
    BACKEND[Backend emite evento de entrega] --> WEBHOOK_DEL[n8n Webhook recibe confirmación]
    WEBHOOK_DEL --> GET_DATA[Obtener datos: cliente, dirección, foto]
    GET_DATA --> CHANNEL{Canal de notificación preferido}
    CHANNEL -->|Email| SEND_EMAIL[📧 Enviar email de entrega confirmada]
    CHANNEL -->|WhatsApp| SEND_WA[💬 Enviar WhatsApp con foto]
    SEND_EMAIL --> LOG_N8N[Registrar en delivery_notifications]
    SEND_WA --> LOG_N8N
```

## Flujo 4 — Reporte diario operativo

```mermaid
flowchart TD
    CRON_20[⏰ Cron n8n 20:00h] --> QUERY[Consulta PostgreSQL: pedidos del día]
    QUERY --> BUILD[Construir resumen: total, completados, fallidos, repartidores, zonas]
    BUILD --> SEND_REPORT[📧 Enviar reporte a administradores]
```

## Configuración de n8n en el servidor

| Parámetro | Valor |
|---|---|
| Imagen Docker | `n8nio/n8n:stable` |
| Puerto | `5678` |
| Volumen de datos | `/var/lib/docker/volumes/n8n_data/_data/` |
| Credenciales externas | Almacenadas cifradas en n8n credentials store |

## Seguridad en los webhooks

Todos los webhooks que reciben datos externos validan un token secreto en el header de la petición:

```
Header requerido: X-LocalXpress-Token: <token_secreto>
```

Si el token no está presente o no coincide, el webhook devuelve `401 Unauthorized`.
