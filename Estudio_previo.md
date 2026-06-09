# Estudio Previo — LocalXpress

---

## Tabla de contenidos

1. [Identificación de necesidades técnicas del proyecto](#1-identificación-de-necesidades-técnicas-del-proyecto)
2. [Estudio de tecnologías de virtualización y contenedores](#2-estudio-de-tecnologías-de-virtualización-y-contenedores)
3. [Estudio de tecnologías de base de datos](#3-estudio-de-tecnologías-de-base-de-datos)
4. [Estudio de tecnologías de backend y API](#4-estudio-de-tecnologías-de-backend-y-api)
5. [Estudio de tecnologías de proxy inverso y SSL](#5-estudio-de-tecnologías-de-proxy-inverso-y-ssl)
6. [Estudio de tecnologías de seguridad](#6-estudio-de-tecnologías-de-seguridad)
7. [Estudio de tecnologías de automatización](#7-estudio-de-tecnologías-de-automatización)
8. [Estudio de soluciones de infraestructura en el mercado](#8-estudio-de-soluciones-de-infraestructura-en-el-mercado)
9. [Análisis comparativo y justificación de decisiones](#9-análisis-comparativo-y-justificación-de-decisiones)
10. [Conclusiones](#10-conclusiones)

---

## 1. Identificación de necesidades técnicas del proyecto

### 1.1 Descripción del proyecto

LocalXpress es una plataforma de reparto local que requiere la puesta en marcha de una infraestructura completa de sistemas informáticos desde cero. El proyecto engloba las áreas principales del ciclo ASIX: administración de servidores Linux, virtualización y contenedores, bases de datos, redes y seguridad, despliegue de aplicaciones y automatizaciones.

### 1.2 Requisitos técnicos identificados

Antes de elegir ninguna tecnología, se identificaron los requisitos técnicos que debía cumplir la infraestructura:

| Requisito | Categoría ASIX | Descripción |
|---|---|---|
| Servidor Linux administrado | Sistemas | VPS con Ubuntu, acceso SSH, gestión de servicios |
| Aislamiento de servicios | Virtualización | Contenedores Docker para servicios auxiliares |
| Base de datos relacional | Bases de datos | PostgreSQL con esquema, migraciones y backups |
| Proxy inverso con SSL | Redes | Nginx con Certbot y Let's Encrypt |
| Autenticación segura | Seguridad | JWT, bcrypt, CORS, RBAC |
| Comunicación en tiempo real | Redes | WebSockets para notificaciones en tiempo real |
| Automatizaciones | Sistemas | Workflows para tareas repetitivas |
| Copias de seguridad automáticas | Sistemas | Backup diario automatizado con almacenamiento remoto |
| Monitorización | Sistemas | Control de procesos, logs y seguridad activa |
| Gestión de procesos | Sistemas | Reinicio automático, zero downtime en despliegues |

### 1.3 Áreas del currículum ASIX aplicadas

El proyecto aplica directamente los módulos profesionales del ciclo ASIX:

| Módulo profesional | Aplicación en el proyecto |
|---|---|
| **Administración de sistemas operativos** | Administración Ubuntu 24.04, systemd, cron, SSH, gestión de usuarios |
| **Implantación de sistemas operativos** | Instalación y configuración nativa de PostgreSQL, Nginx, Node.js |
| **Planificación y administración de redes** | Configuración de puertos, proxy inverso, cortafuegos |
| **Servicios de red e Internet** | DNS, SSL/TLS, HTTP/HTTPS, WebSockets |
| **Seguridad y Alta Disponibilidad** | JWT, bcrypt, Monarx, Certbot, fail2ban, UFW |
| **Implantación de aplicaciones web** | Despliegue de backend Node.js, frontend React/Vite |
| **Administración de bases de datos** | PostgreSQL, esquema, migraciones, backups, optimización |
| **Sistemas de gestión empresarial** | Automatizaciones n8n, integraciones externas |

---

## 2. Estudio de tecnologías de virtualización y contenedores

### 2.1 Contexto

Uno de los requisitos del proyecto es aislar los servicios auxiliares (automatizaciones, panel de administración) del sistema operativo base para facilitar su actualización y mantenimiento. Se estudiaron las principales tecnologías de virtualización y contenedores disponibles.

### 2.2 Opciones analizadas

#### Virtualización completa — Máquinas virtuales

| Tecnología | Tipo | Descripción |
|---|---|---|
| VMware ESXi | Hipervisor tipo 1 | Hipervisor bare-metal empresarial, alto rendimiento |
| Proxmox VE | Hipervisor tipo 1 | Open source, gestión web, KVM + LXC |
| VirtualBox | Hipervisor tipo 2 | Para entornos de desarrollo local |
| KVM (Kernel-based VM) | Hipervisor tipo 1 | Integrado en el kernel Linux, alto rendimiento |

**Características de las VM:**
- Aislamiento completo del sistema operativo
- Cada VM tiene su propio kernel
- Alto consumo de recursos (RAM, disco)
- Tiempo de arranque lento (minutos)
- Ideal para servicios que requieren sistemas operativos completamente diferentes

#### Contenedores — Virtualización a nivel de SO

| Tecnología | Descripción | Ventajas | Desventajas |
|---|---|---|---|
| **Docker** | Contenedores basados en imágenes | Ligero, rápido, Docker Hub, Compose | Kernel compartido con el host |
| LXC / LXD | Contenedores Linux nativos | Integrado en el kernel, muy ligero | Menos ecosistema que Docker |
| Podman | Alternativa a Docker sin daemon | Rootless, compatible Docker | Menos maduro, menos documentación |
| containerd | Runtime de bajo nivel | Muy ligero, usado por Kubernetes | No tiene CLI propia |

#### Orquestación de contenedores

| Tecnología | Descripción | Complejidad | Adecuación al proyecto |
|---|---|---|---|
| **Docker Compose** | Orquestación de múltiples contenedores en un solo host | Baja | ✅ Perfecta para un VPS |
| Docker Swarm | Clúster de nodos Docker | Media | ❌ Excesiva para un solo servidor |
| Kubernetes (K8s) | Orquestación empresarial a escala | Muy alta | ❌ Excesiva para este proyecto |
| Nomad (HashiCorp) | Orquestación ligera | Media | ❌ Menos extendida, poco valor añadido |

### 2.3 Decisión: Docker + Docker Compose para servicios auxiliares

**Justificación técnica:**

Docker con Docker Compose es la opción óptima para el perfil del proyecto por las siguientes razones:

- **Recursos:** Los contenedores comparten el kernel del host y consumen una fracción de los recursos de una VM completa. En un VPS con 7.8GB de RAM, n8n y EasyPanel en Docker consumen menos de 500MB, mientras que dos VM equivalentes consumirían 2-4GB.
- **Velocidad de arranque:** Un contenedor Docker arranca en milisegundos, una VM en minutos.
- **Actualización:** Actualizar n8n consiste en hacer `docker pull` y `docker compose up -d`. Con una VM habría que entrar, actualizar paquetes y reiniciar.
- **Portabilidad:** El `docker-compose.yml` documenta exactamente cómo se ejecuta cada servicio, facilitando la reproducción en otro servidor si fuera necesario.
- **Aislamiento adecuado:** Para servicios de terceros (n8n, EasyPanel), el contenedor proporciona el aislamiento necesario sin la sobrecarga de una VM.

**Por qué el backend y PostgreSQL no están en Docker:**

Los servicios críticos de rendimiento (backend Node.js y PostgreSQL) se ejecutan de forma **nativa** en el sistema operativo, no en contenedores:

- PostgreSQL nativo tiene acceso directo al subsistema de E/S del kernel, eliminando la latencia de red de los contenedores.
- Experiencia previa en el proyecto: una instancia AWS EC2 con PostgreSQL en Docker sufrió pérdida de datos por volúmenes no persistentes. La instalación nativa elimina completamente este riesgo.
- El backend Node.js gestionado por PM2 tiene acceso directo al sistema de ficheros para las imágenes de entrega, sin pasar por la capa de red Docker.

---

## 3. Estudio de tecnologías de base de datos

### 3.1 Requisitos identificados

- Soporte para transacciones ACID (integridad de datos en operaciones concurrentes)
- Modelo de datos relacional (pedidos, usuarios, zonas, tarifas con relaciones complejas)
- Soporte para JSON (coordenadas de zonas geográficas)
- Capacidad de copias de seguridad automáticas (`pg_dump`)
- Rendimiento adecuado para consultas con índices sobre múltiples tablas
- Acceso concurrente desde múltiples procesos (backend APP y backend APP-IND)

### 3.2 Opciones analizadas

| Sistema | Tipo | ACID | JSON | Rendimiento I/O | Licencia |
|---|---|---|---|---|---|
| **PostgreSQL** | Relacional | ✅ Completo | ✅ Nativo (jsonb) | ✅ Excelente | Open source |
| MySQL / MariaDB | Relacional | ✅ (InnoDB) | ⚠️ Básico | ✅ Bueno | Open source |
| SQLite | Relacional embebido | ✅ | ❌ | ⚠️ Limitado en concurrencia | Open source |
| MongoDB | Documental | ⚠️ Parcial | ✅ Nativo | ✅ Bueno | SSPL |
| Redis | Clave-valor / caché | ❌ | ❌ | ✅ Muy alto | BSD |
| Firebase Realtime DB | NoSQL en la nube | ❌ | ✅ | ✅ | Propietario |

### 3.3 Comparativa detallada: PostgreSQL vs MySQL vs MongoDB

| Criterio | PostgreSQL | MySQL | MongoDB |
|---|---|---|---|
| Transacciones ACID | ✅ Completo | ✅ (InnoDB) | ⚠️ Parcial |
| Integridad referencial (FK) | ✅ | ✅ | ❌ |
| Soporte JSON avanzado | ✅ jsonb con índices | ⚠️ Básico | ✅ Nativo |
| Herencia de tablas | ✅ | ❌ | ❌ |
| Vistas materializadas | ✅ | ❌ | ❌ |
| pg_dump para backups | ✅ Nativa | ✅ mysqldump | ⚠️ mongodump |
| Extensiones (PostGIS, etc.) | ✅ Muy rico | ⚠️ Limitado | ⚠️ Limitado |
| Rendimiento en consultas complejas | ✅ Excelente | ✅ Bueno | ⚠️ Menos óptimo |
| Comunidad y documentación | ✅ Muy grande | ✅ Muy grande | ✅ Grande |

### 3.4 Decisión: PostgreSQL 16 instalación nativa

**Justificación técnica:**
- El modelo de datos de LocalXpress es inherentemente relacional: un `stop` pertenece a una `store`, tiene un `driver`, una `pricing_zone`, una `order_photo` y genera `delivery_notifications`. PostgreSQL es el motor que mejor gestiona estas relaciones con integridad referencial completa.
- El campo `coordinates` de `pricing_zones` utiliza el tipo `jsonb` de PostgreSQL, que permite índices GIN sobre datos JSON, imposible con MySQL.
- `pg_dump` genera volcados consistentes de toda la base de datos en un solo fichero comprimible, perfecto para el sistema de backup automático a Google Drive.
- La instalación nativa (fuera de Docker) maximiza el rendimiento de E/S en el disco SSD del VPS.

---

## 4. Estudio de tecnologías de backend y API

### 4.1 Requisitos identificados

- API REST para la comunicación entre frontend y servidor
- Autenticación y autorización por roles
- Comunicación bidireccional en tiempo real (WebSockets)
- Gestión de ficheros (imágenes de confirmación de entrega)
- Integración con PostgreSQL
- Gestión de procesos en producción (reinicio automático, zero downtime)

### 4.2 Entornos de ejecución del servidor

| Tecnología | Lenguaje | Modelo concurrencia | Adecuación |
|---|---|---|---|
| **Node.js** | JavaScript | Event loop (no bloqueante) | ✅ Excelente para I/O intensivo |
| Python (Django/FastAPI) | Python | Síncrono / ASGI | ✅ Bueno, pero menos natural con Socket.io |
| PHP (Laravel) | PHP | Síncrono por petición | ⚠️ Menos adecuado para WebSockets |
| Java (Spring Boot) | Java | Multi-thread | ✅ Muy robusto, pero excesivo para este proyecto |
| Go (Gin/Fiber) | Go | Goroutines | ✅ Excelente rendimiento, pero curva de aprendizaje alta |
| Ruby on Rails | Ruby | Síncrono | ⚠️ Menos rendimiento, ecosistema decreciente |

### 4.3 Gestores de procesos para Node.js en producción

| Herramienta | Reinicio automático | Zero downtime | Logs | Monitorización | Startup SO |
|---|---|---|---|---|---|
| **PM2** | ✅ | ✅ (`pm2 reload`) | ✅ con rotación | ✅ (`pm2 monit`) | ✅ (`pm2 startup`) |
| systemd | ✅ | ❌ | ✅ (journald) | ⚠️ Básico | ✅ Nativo |
| Forever | ✅ | ❌ | ⚠️ Básico | ❌ | ❌ |
| Nodemon | ❌ | ❌ | ❌ | ❌ | ❌ (solo dev) |
| Docker (restart: always) | ✅ | ⚠️ | ✅ | ⚠️ | ✅ |

### 4.4 Protocolos de comunicación en tiempo real

| Protocolo | Bidireccional | Reconexión automática | Fallback | Adecuación |
|---|---|---|---|---|
| **Socket.io** | ✅ | ✅ Nativa | ✅ Long-polling | ✅ Excelente |
| WebSocket nativo (RFC 6455) | ✅ | ❌ Manual | ❌ | ✅ Bueno pero más código |
| Server-Sent Events (SSE) | ❌ Unidireccional | ⚠️ | ✅ HTTP | ⚠️ No sirve para este caso |
| Long Polling | ✅ Simulado | ✅ | ✅ | ❌ Ineficiente |
| gRPC streaming | ✅ | ❌ | ❌ | ❌ Excesivo para navegadores |

### 4.5 Decisiones: Node.js + Express + Socket.io + PM2

**Justificación técnica:**
- Node.js utiliza un modelo de event loop no bloqueante ideal para aplicaciones con muchas conexiones concurrentes (repartidores conectados simultáneamente vía WebSocket).
- Socket.io sobre Node.js es la combinación estándar de la industria para notificaciones en tiempo real. La reconexión automática es crítica para repartidores con conectividad móvil intermitente.
- PM2 es el estándar de facto para procesos Node.js en producción: `pm2 reload` permite actualizar la aplicación sin ningún corte de servicio.
- PM2 gestiona dos procesos independientes (`:3001` y `:3002`) con logs separados y reinicio automático individual.

---

## 5. Estudio de tecnologías de proxy inverso y SSL

### 5.1 Requisitos identificados

- Terminación SSL/TLS para todo el tráfico externo
- Enrutamiento hacia múltiples backends internos (`:3001` y `:3002`)
- Servicio de archivos estáticos del frontend compilado
- Gestión automática de certificados (sin intervención manual)
- Redirección HTTP → HTTPS

### 5.2 Servidores web y proxy inverso

| Tecnología | Modelo | Rendimiento estáticos | Proxy | SSL automático | Adecuación |
|---|---|---|---|---|---|
| **Nginx** | Event-driven | ✅ Excelente | ✅ | ✅ (Certbot) | ✅ |
| Apache | Process/thread | ✅ Bueno | ✅ | ✅ (Certbot) | ✅ Bueno pero más pesado |
| Traefik | Event-driven | ⚠️ | ✅ | ✅ Automático Docker | ⚠️ Complejo sin Docker |
| Caddy | Event-driven | ✅ | ✅ | ✅ Nativo | ✅ Simple pero menos extendido |
| HAProxy | Event-driven | ❌ (solo proxy) | ✅ Excelente | ⚠️ | ⚠️ Solo balanceo |

### 5.3 Gestión de certificados SSL/TLS

| Solución | Gratuito | Renovación automática | Let's Encrypt | Complejidad |
|---|---|---|---|---|
| **Certbot** | ✅ | ✅ (cron) | ✅ | Baja |
| acme.sh | ✅ | ✅ | ✅ | Media |
| Certificado autofirmado | ✅ | ❌ Manual | ❌ | Baja |
| Certificado comercial (DigiCert, etc.) | ❌ (coste anual) | ❌ Manual | ❌ | Baja |
| Cloudflare SSL | ✅ (plan gratuito) | ✅ | ✅ | Baja |

### 5.4 Decisión: Nginx + Certbot (Let's Encrypt)

**Justificación técnica:**
- Nginx es el servidor web/proxy inverso más extendido en entornos de producción Linux, con documentación exhaustiva y soporte activo.
- La combinación Nginx + Certbot es el estándar para la obtención y renovación automática de certificados Let's Encrypt gratuitos.
- El cron configurado (`0 */12 * * *`) intenta la renovación cada 12 horas, asegurando que el certificado nunca caduca sin intervención humana.
- Nginx sirve el frontend compilado (archivos estáticos) directamente sin pasar por el proceso Node.js, reduciendo la carga del backend.

---

## 6. Estudio de tecnologías de seguridad

### 6.1 Requisitos de seguridad identificados

- Autenticación de usuarios sin almacenar contraseñas en texto plano
- Control de acceso por roles (admin, comerciante, repartidor)
- Cifrado del tráfico cliente-servidor
- Protección contra acceso no autorizado al servidor
- Monitorización activa de amenazas
- Protección contra ataques de fuerza bruta

### 6.2 Sistemas de autenticación

| Método | Stateless | Escalabilidad | Seguridad | Complejidad |
|---|---|---|---|---|
| **JWT (JSON Web Token)** | ✅ | ✅ Excelente | ✅ Buena (con secret fuerte) | Baja |
| Sesiones en servidor (express-session) | ❌ | ⚠️ Requiere Redis para escalar | ✅ Buena | Media |
| OAuth 2.0 / OpenID Connect | ✅ | ✅ | ✅ Excelente | Alta |
| API Keys estáticas | ✅ | ✅ | ⚠️ No permite expiración fácil | Muy baja |
| Basic Auth (HTTP) | ✅ | ✅ | ❌ Inseguro sin HTTPS | Muy baja |

### 6.3 Algoritmos de hash de contraseñas

| Algoritmo | Resistencia brute-force | Salt integrado | Recomendado OWASP | Velocidad |
|---|---|---|---|---|
| **bcrypt** | ✅ Alta (coste configurable) | ✅ | ✅ | Lento (por diseño) |
| Argon2 | ✅ Muy alta | ✅ | ✅ (primera opción) | Muy lento |
| scrypt | ✅ Alta | ✅ | ✅ | Lento |
| PBKDF2 | ✅ Buena | ✅ | ✅ | Configurable |
| SHA-256 (sin salt) | ❌ Muy baja | ❌ | ❌ | Muy rápido |
| MD5 | ❌ Nula | ❌ | ❌ | Muy rápido |

### 6.4 Herramientas de seguridad a nivel de sistema

| Herramienta | Función | Tipo | Implementación en el proyecto |
|---|---|---|---|
| **Monarx Agent** | Detección de malware y amenazas en tiempo real | Agente activo | ✅ Instalado y activo |
| **Certbot** | Gestión automática de certificados SSL | Renovación automática | ✅ Cron cada 12h |
| UFW (Uncomplicated Firewall) | Cortafuegos a nivel de red | Cortafuegos | 🔜 Pendiente de configurar |
| fail2ban | Protección contra fuerza bruta SSH/HTTP | IDS/IPS | 🔜 Pendiente de configurar |
| OpenSSH | Acceso remoto seguro | Protocolo | ✅ Activo en el puerto 22 |
| Helmet.js | Cabeceras HTTP de seguridad | Middleware Express | ✅ Aplicado en el backend |

### 6.5 Decisiones de seguridad aplicadas

**Justificación técnica:**
- **JWT** se ha elegido por su naturaleza *stateless*: no requiere almacenar sesiones en el servidor, lo que simplifica la arquitectura y facilita el escalado futuro. El token incluye el `role` del usuario y tiene expiración configurable.
- **bcrypt** con 10 rondas de sal es la implementación recomendada por OWASP para hash de contraseñas en Node.js. Hace computacionalmente inviable un ataque por fuerza bruta incluso si se obtuviera acceso a la base de datos.
- **Monarx** proporciona detección activa de amenazas gestionada por Hostinger, con actualizaciones automáticas semanales.
- Los puertos del backend (`:3001`, `:3002`) solo son accesibles desde `localhost`, eliminando una superficie de ataque significativa.

---

## 7. Estudio de tecnologías de automatización

### 7.1 Requisitos identificados

- Procesamiento automático de entradas de pedidos (email, WhatsApp)
- Notificaciones automáticas a los actores implicados
- Tareas programadas (backups, limpieza de imágenes, informes)
- Integración con servicios externos (SMTP, WhatsApp Business API)
- Idempotencia en el procesamiento para evitar duplicados

### 7.2 Plataformas de automatización

| Plataforma | Self-hosted | Coste | Integraciones | Control datos | Complejidad |
|---|---|---|---|---|---|
| **n8n** | ✅ | Gratuito (self-hosted) | 400+ | ✅ Total | Baja-media |
| Zapier | ❌ | Desde 20$/mes + por operación | 6.000+ | ❌ Datos en terceros | Muy baja |
| Make (Integromat) | ❌ | Desde 9$/mes + por operación | 1.500+ | ❌ Datos en terceros | Baja |
| Apache Airflow | ✅ | Gratuito | Código | ✅ Total | Muy alta |
| Cron + scripts bash | ✅ | Gratuito | Código | ✅ Total | Alta (mantenimiento) |
| Node-RED | ✅ | Gratuito | Buena | ✅ Total | Media |

### 7.3 Herramientas de programación de tareas en el sistema

| Herramienta | Ámbito | Integración BD | Persistencia | Adecuación |
|---|---|---|---|---|
| **node-cron** | Dentro del proceso Node.js | ✅ Directa | Mientras el proceso esté activo | ✅ Para tareas ligadas al backend |
| cron del sistema (crontab) | Sistema operativo | ⚠️ Via scripts | ✅ Independiente del proceso | ✅ Para backups y tareas de sistema |
| Agenda.js | Node.js + MongoDB | ✅ | ✅ (persiste en BD) | ⚠️ Requiere MongoDB |
| Bull / BullMQ | Node.js + Redis | ✅ | ✅ (persiste en Redis) | ⚠️ Requiere Redis |

### 7.4 Decisiones: n8n + node-cron + cron del sistema

**Justificación técnica:**
- **n8n** se autoaloja en el servidor en un contenedor Docker con el volumen `n8n_data` persistente. Esto garantiza que ningún dato de los pedidos o clientes salga del servidor propio, a diferencia de Zapier o Make que procesan los datos en sus servidores.
- **node-cron** se utiliza para la limpieza automática de fotografías (02:30h) porque necesita acceso directo al filesystem y a la conexión PostgreSQL del proceso backend, sin overhead adicional.
- **cron del sistema** (`/etc/cron.d/localxpress-backup`) se utiliza para el backup de PostgreSQL (03:00h) porque es una tarea de sistema operativo independiente de cualquier proceso Node.js — si el backend estuviera parado, el backup se ejecutaría igualmente.

---

## 8. Estudio de soluciones de infraestructura en el mercado

### 8.1 Opciones de alojamiento analizadas

| Opción | Tipo | Control SO | Coste | Escalabilidad | Adecuación |
|---|---|---|---|---|---|
| **VPS Hostinger** | VPS | ✅ Total (root) | Fijo y bajo | ✅ Vertical sencillo | ✅ |
| AWS EC2 | IaaS | ✅ Total | Variable (por uso) | ✅ Excelente | ⚠️ Coste impredecible |
| Google Cloud Compute | IaaS | ✅ Total | Variable | ✅ Excelente | ⚠️ Coste impredecible |
| DigitalOcean Droplet | VPS | ✅ Total | Fijo | ✅ Bueno | ✅ Alternativa válida |
| Heroku / Railway | PaaS | ❌ Parcial | Variable | ✅ | ❌ Sin control de SO |
| Servidor físico propio | On-premise | ✅ Total | Alto (hardware) | ❌ | ❌ Mantenimiento físico |
| Shared Hosting | Hosting compartido | ❌ | Muy bajo | ❌ | ❌ Sin root, sin Docker |

### 8.2 Comparativa AWS EC2 vs VPS Hostinger

Dado que el proyecto tuvo una fase previa en AWS EC2, se realiza una comparativa directa:

| Criterio | AWS EC2 | VPS Hostinger |
|---|---|---|
| Control del sistema operativo | ✅ Total | ✅ Total |
| Coste mensual previsible | ❌ Variable | ✅ Fijo |
| Persistencia de datos por defecto | ❌ Requiere EBS configurado | ✅ Disco SSD persistente |
| Complejidad de configuración de red | Alta (VPC, Security Groups, IGW) | Baja (UFW, configuración directa) |
| Tiempo de puesta en marcha | Medio | Rápido |
| Soporte técnico | ⚠️ Coste adicional | ✅ Incluido |
| Escalado automático | ✅ Auto Scaling | ❌ Manual |
| Experiencia en el proyecto | ❌ Pérdida de datos por volúmenes | ✅ Estable |

**Lección aprendida:** La instancia AWS EC2 sufrió una pérdida de datos de PostgreSQL en un contenedor Docker porque el volumen no estaba correctamente configurado como EBS persistente. El VPS Hostinger con disco SSD fijo y PostgreSQL instalado de forma nativa elimina completamente este riesgo.

### 8.3 Paneles de administración de servidores

| Herramienta | Tipo | Docker | SSL | Coste | Adecuación |
|---|---|---|---|---|---|
| **EasyPanel** | Panel moderno | ✅ Nativo | ✅ | Gratuito (self-hosted) | ✅ |
| Portainer | Gestión Docker | ✅ Nativo | ⚠️ | Gratuito | ✅ Alternativa |
| cPanel | Hosting tradicional | ❌ | ✅ | Coste elevado | ❌ |
| Plesk | Hosting tradicional | ⚠️ | ✅ | Coste elevado | ❌ |
| Webmin | Administración Unix | ❌ | ✅ | Gratuito | ⚠️ Anticuado |
| Cockpit | Administración Linux moderno | ⚠️ | ✅ | Gratuito | ✅ Alternativa |

---

## 9. Análisis comparativo y justificación de decisiones

### 9.1 Tabla resumen de decisiones tecnológicas

| Componente | Tecnología elegida | Principal alternativa | Razón principal de la elección |
|---|---|---|---|
| Sistema operativo | Ubuntu 24.04 LTS | Debian 12 | LTS con soporte hasta 2029, amplia comunidad, paquetes actuales |
| Contenedores | Docker + Docker Compose | LXC | Ecosistema, Docker Hub, Compose para orquestación sencilla |
| Base de datos | PostgreSQL 16 nativo | MySQL / MongoDB | ACID completo, jsonb, relaciones complejas, pg_dump |
| Backend | Node.js + Express | Django (Python) | Event loop para WebSockets, mismo lenguaje que frontend |
| Tiempo real | Socket.io | WebSocket nativo | Reconexión automática, rooms, fallback HTTP |
| Proxy inverso | Nginx | Apache / Caddy | Rendimiento, madurez, integración Certbot |
| SSL | Let's Encrypt + Certbot | Certificado comercial | Gratuito, renovación automática, estándar de la industria |
| Gestión de procesos | PM2 | systemd | Específico Node.js, reload sin downtime, logs integrados |
| Automatizaciones | n8n (Docker) | Zapier / scripts bash | Self-hosted, gratuito, interfaz visual, datos propios |
| Seguridad activa | Monarx Agent | ClamAV / OSSEC | Integrado con Hostinger, actualizaciones automáticas |
| Autenticación | JWT + bcrypt | Sesiones + MD5 | Stateless, escalable, bcrypt recomendado por OWASP |
| Alojamiento | VPS Hostinger | AWS EC2 | Coste fijo, disco persistente, control total, experiencia previa |

### 9.2 Arquitectura resultante y justificación global

La combinación de **servicios nativos** (PostgreSQL, Nginx, Node.js/PM2) para los componentes críticos de rendimiento y **Docker** para los servicios auxiliares (n8n, EasyPanel) representa la decisión arquitectónica más importante del proyecto. Responde a un equilibrio entre:

- **Rendimiento:** Los componentes que acceden intensivamente al disco (PostgreSQL) y que gestionan muchas conexiones concurrentes (Node.js) funcionan mejor sin la capa de virtualización Docker.
- **Mantenibilidad:** Los servicios de terceros (n8n, EasyPanel) se mantienen en contenedores porque se actualizan con un solo comando sin afectar al sistema operativo base.
- **Fiabilidad:** Experiencia directa que la instalación nativa de PostgreSQL es más robusta y segura que en Docker en un VPS de producción con un solo nodo.

---

## 10. Conclusiones

### 10.1 Tecnologías seleccionadas y su relación con el currículum ASIX

| Área del currículum ASIX | Tecnologías aplicadas en el proyecto |
|---|---|
| Administración de sistemas Linux | Ubuntu 24.04, systemd, cron, SSH, gestión de servicios, PM2 |
| Virtualización y contenedores | Docker, Docker Compose, volúmenes persistentes, redes Docker |
| Bases de datos | PostgreSQL 16, esquema relacional, migraciones, índices, pg_dump |
| Servicios de red | Nginx proxy inverso, SSL/TLS, WebSockets, HTTP/HTTPS |
| Seguridad | JWT, bcrypt, Monarx, Certbot, UFW (pendiente), fail2ban (pendiente) |
| Alta disponibilidad | PM2 (reinicio automático), backups diarios, ramas Git de seguridad |
| Automatizaciones y scripting | n8n, node-cron, scripts bash, cron del sistema |

### 10.2 Viabilidad técnica

Todas las tecnologías seleccionadas son maduras, open source o de licencia gratuita, con amplia documentación y comunidad activa. El servidor actual (96GB de disco, 7.8GB de RAM, Ubuntu 24.04 LTS) tiene capacidad más que suficiente para el volumen operativo del proyecto, con un 89% de disco disponible y menos del 25% de RAM en uso.

### 10.3 Riesgos técnicos identificados

| Riesgo | Probabilidad | Impacto | Medida de mitigación |
|---|---|---|---|
| Caída del VPS | Baja | Alto | Backup diario a Google Drive, proceso de recuperación documentado |
| Expiración del certificado SSL | Muy baja | Alto | Certbot con cron cada 12h, renovación automática |
| Fallo del proceso Node.js | Baja | Alto | PM2 con reinicio automático en menos de 1 segundo |
| Corrupción de la BD | Muy baja | Crítico | pg_dump diario, PostgreSQL nativo (sin riesgo de volúmenes Docker) |
| Ataque de fuerza bruta SSH | Media | Alto | fail2ban pendiente de configurar, acceso por clave SSH recomendado |
| Desbordamiento del disco | Muy baja | Medio | Limpieza automática de fotos cada 20 días, 89% disco libre |
| Vulnerabilidad en dependencias npm | Media | Medio | Actualizaciones periódicas, `npm audit` en cada despliegue |

---

