# Diagrama — Modelo de Base de Datos

## Entidad-Relación completo

```mermaid
erDiagram
    USERS {
        uuid id PK
        varchar email
        varchar password_hash
        varchar role
        varchar name
        varchar phone
        timestamp created_at
    }

    PROFILES {
        uuid id PK
        uuid user_id FK
        varchar avatar_url
        varchar address
        jsonb preferences
        timestamp updated_at
    }

    USER_ROLES {
        uuid id PK
        uuid user_id FK
        varchar role
        timestamp assigned_at
    }

    STOPS {
        uuid id PK
        uuid store_id FK
        uuid driver_id FK
        uuid zone_id FK
        varchar status
        text pickup_address
        text delivery_address
        decimal price
        decimal driver_price
        text notes
        timestamp scheduled_at
        timestamp created_at
        timestamp updated_at
    }

    STOPS_ARCHIVE {
        uuid id PK
        uuid original_stop_id
        jsonb stop_data
        timestamp archived_at
    }

    ORDER_PHOTOS {
        uuid id PK
        uuid stop_id FK
        uuid driver_id FK
        varchar file_path
        integer file_size
        varchar mime_type
        timestamp created_at
    }

    PRICING_ZONES {
        uuid id PK
        varchar name
        jsonb coordinates
        decimal base_price
        decimal price_per_km
        decimal driver_price
        boolean active
        timestamp created_at
    }

    DRIVER_LOCATIONS {
        uuid id PK
        uuid driver_id FK
        decimal latitude
        decimal longitude
        timestamp recorded_at
    }

    AUDIT_LOGS {
        uuid id PK
        uuid user_id FK
        varchar action
        varchar entity_type
        uuid entity_id
        jsonb changes
        timestamp created_at
    }

    EMAIL_IDEMPOTENCY {
        varchar message_id PK
        uuid stop_id FK
        timestamp processed_at
    }

    DELIVERY_NOTIFICATIONS {
        uuid id PK
        uuid stop_id FK
        varchar type
        varchar channel
        varchar status
        timestamp sent_at
    }

    USERS ||--o| PROFILES : "tiene perfil"
    USERS ||--o{ USER_ROLES : "tiene roles"
    USERS ||--o{ STOPS : "reparte (driver_id)"
    USERS ||--o{ STOPS : "crea (store_id)"
    USERS ||--o{ DRIVER_LOCATIONS : "tiene ubicaciones"
    USERS ||--o{ AUDIT_LOGS : "genera logs"
    PRICING_ZONES ||--o{ STOPS : "aplica tarifa"
    STOPS ||--o| ORDER_PHOTOS : "tiene foto confirmación"
    STOPS ||--o{ DELIVERY_NOTIFICATIONS : "genera notificaciones"
    STOPS ||--o| EMAIL_IDEMPOTENCY : "referenciado por email"
    STOPS ||--o| STOPS_ARCHIVE : "archivado en"
```

## Descripción de tablas

| Tabla | Propósito | Filas estimadas |
|---|---|---|
| `users` | Usuarios del sistema (admin, tienda, repartidor) | Decenas |
| `profiles` | Datos extendidos de perfil | 1 por usuario |
| `user_roles` | Asignación de roles por usuario | 1-2 por usuario |
| `stops` | Pedidos activos y recientes | Cientos (rotación continua) |
| `stops_archive` | Histórico de pedidos completados | Creciente |
| `order_photos` | Referencias a fotos de entrega | ~600 máximo (limpieza 20 días) |
| `pricing_zones` | Zonas de reparto con tarifas | Decenas |
| `driver_locations` | Posición en tiempo real de repartidores | Alta rotación |
| `audit_logs` | Trazabilidad de acciones críticas | Creciente |
| `email_idempotency` | Control de emails procesados por n8n | Creciente (bajo volumen) |
| `delivery_notifications` | Registro de notificaciones enviadas | Creciente |

## Estados de un pedido (stops.status)

```mermaid
stateDiagram-v2
    [*] --> pending: INSERT (nuevo pedido)
    pending --> assigned: Admin asigna repartidor
    assigned --> in_transit: Repartidor sale a recoger
    in_transit --> delivered: Foto de entrega subida
    in_transit --> failed: No se puede entregar
    delivered --> [*]
    failed --> [*]
```

## Índices de rendimiento aplicados (migración 002)

| Índice | Tabla | Campo | Justificación |
|---|---|---|---|
| `idx_stops_store_id` | stops | store_id | Filtrado de pedidos por comercio |
| `idx_stops_driver_id` | stops | driver_id | Filtrado de pedidos por repartidor |
| `idx_stops_status` | stops | status | Filtrado por estado activo/completado |
| `idx_stops_created_at` | stops | created_at | Ordenación cronológica |
| `idx_stops_zone_id` | stops | zone_id | Agrupación por zona |
| `idx_order_photos_stop_id` | order_photos | stop_id | Acceso a foto por pedido |
| `idx_order_photos_created_at` | order_photos | created_at | Limpieza por antigüedad |
| `idx_driver_locations_driver` | driver_locations | driver_id | Localización por repartidor |

## Migraciones aplicadas

| Migración | Descripción |
|---|---|
| `001_schema_inicial.sql` | Tablas base: users, profiles, stops, pricing_zones |
| `002_performance_indexes.sql` | Índices de rendimiento para consultas frecuentes |
| `004_pricing_zone_driver_price.sql` | Campo driver_price en stops y pricing_zones |
| `007_delivery_notifications.sql` | Tabla delivery_notifications |
| `008_email_idempotency.sql` | Tabla email_idempotency para n8n |
