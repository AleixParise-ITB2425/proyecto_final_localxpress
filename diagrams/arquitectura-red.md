# Diagrama — Arquitectura de Red

```mermaid
flowchart TD
    subgraph INTERNET["🌐 Internet"]
        CLI[Cliente web / App]
        N8NEXT[Servicios externos\nWhatsApp, Email SMTP]
    end

    subgraph VPS["VPS srv1651179 — 187.77.171.221"]

        subgraph PUBLIC["Puertos expuestos a internet"]
            P22[":22 SSH\nAdministración"]
            P80[":80 HTTP\nNginx"]
            P443[":443 HTTPS\nNginx + SSL"]
            P3000[":3000 EasyPanel\nPanel servidor"]
            P5678[":5678 n8n\nAutomatizaciones"]
        end

        subgraph PRIVATE["Solo accesibles desde localhost"]
            P3001["localhost:3001\nBackend LocalXpress-APP\n(Node.js + PM2)"]
            P3002["localhost:3002\nBackend LocalXpress-APP-IND\n(Node.js + PM2)"]
            P5432["⚠️ :5432 PostgreSQL\n(recomendado restringir\na localhost)"]
            P65529["localhost:65529\nMonarx Agent\n(interno)"]
        end

        subgraph FILESYSTEM["Filesystem"]
            FS1["/home/LocalXpress-APP/\nbackend/uploads/proofs/"]
            FS2["/var/lib/postgresql/\n(datos BD)"]
            FS3["/var/lib/docker/volumes/\nn8n_data/"]
        end
    end

    subgraph EXTERNAL["Servicios externos"]
        GDRIVE["☁️ Google Drive\n(backups)"]
    end

    CLI -->|HTTPS| P443
    CLI -->|HTTP redirect| P80
    P80 -->|301 HTTPS| P443
    P443 -->|proxy_pass| P3001
    P443 -->|proxy_pass| P3002
    P3001 -->|localhost| P5432
    P3002 -->|localhost| P5432
    P3001 -->|lectura/escritura| FS1
    P5432 -->|datos| FS2
    P5678 -->|n8n_data| FS3
    N8NEXT --> P5678
    P5678 -->|REST API| P3001
    P5678 -->|queries| P5432
    VPS -->|pg_dump diario| GDRIVE

    style PUBLIC fill:#e8f5e9,stroke:#2e7d32
    style PRIVATE fill:#fce4ec,stroke:#c62828
    style FILESYSTEM fill:#f3e5f5,stroke:#6a1b9a
    style INTERNET fill:#e3f2fd,stroke:#1565c0
    style EXTERNAL fill:#e0f2f1,stroke:#00695c
```

## Tabla de puertos

| Puerto | Servicio | Exposición | Notas |
|---|---|---|---|
| 22 | SSH | 🌐 Público | Acceso administrativo al servidor |
| 80 | Nginx HTTP | 🌐 Público | Redirige siempre a HTTPS |
| 443 | Nginx HTTPS | 🌐 Público | Entrada principal de la aplicación |
| 3000 | EasyPanel | 🌐 Público | Panel de gestión del servidor |
| 5678 | n8n | 🌐 Público | Interfaz de automatizaciones |
| 3001 | Backend APP | 🔒 Localhost | Solo accesible desde Nginx o el propio servidor |
| 3002 | Backend APP-IND | 🔒 Localhost | Solo accesible desde Nginx o el propio servidor |
| 5432 | PostgreSQL | ⚠️ Expuesto | Pendiente restringir a localhost |
| 65529 | Monarx Agent | 🔒 Localhost | Agente de seguridad interno |
