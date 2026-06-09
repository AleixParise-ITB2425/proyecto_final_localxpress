# Planificación y Seguimiento del Proyecto — LocalXpress

**Módulo:** Proyecto Final — CFGS Administración de Sistemas Informáticos en Red (ASIX)  
**Duración:** 6 de abril de 2026 — 20 de mayo de 2026 (6 semanas)  
**Metodología:** Scrum adaptado — 3 sprints de 2 semanas  
**Herramienta de gestión:** ProofHub (tablón Kanban + seguimiento de sprints)  
**Equipo:** 1 persona  

---

## Tabla de contenidos

1. [Alcance y objetivos técnicos](#1-alcance-y-objetivos-técnicos)
2. [Metodología de trabajo](#2-metodología-de-trabajo)
3. [Planificación de sprints y cronograma](#3-planificación-de-sprints-y-cronograma)
4. [Sprint 1 — Infraestructura y base de datos](#4-sprint-1--infraestructura-y-base-de-datos)
5. [Sprint 2 — Backend, API y aplicación](#5-sprint-2--backend-api-y-aplicación)
6. [Sprint 3 — Producción, seguridad y documentación](#6-sprint-3--producción-seguridad-y-documentación)
7. [Seguimiento diario de trabajo](#7-seguimiento-diario-de-trabajo)
8. [Recursos utilizados](#8-recursos-utilizados)
9. [Gestión de riesgos](#9-gestión-de-riesgos)
10. [Resumen de incidencias y soluciones](#10-resumen-de-incidencias-y-soluciones)

---

## 1. Alcance y objetivos técnicos

### 1.1 Descripción general

LocalXpress es una plataforma de reparto local construida completamente desde cero sobre infraestructura propia. El proyecto abarca la totalidad del stack técnico: desde el aprovisionamiento del servidor VPS hasta el despliegue en producción, pasando por la base de datos, el backend, la seguridad, las automatizaciones y el sistema de backups.

### 1.2 Objetivos técnicos del proyecto

| Objetivo | Categoría ASIX | Estado final |
|---|---|---|
| Aprovisionar y configurar servidor VPS Ubuntu 24.04 | Administración de sistemas | ✅ Completado |
| Instalar y configurar PostgreSQL nativo | Bases de datos | ✅ Completado |
| Desplegar backend Node.js con PM2 | Implantación de aplicaciones | ✅ Completado |
| Configurar Nginx como proxy inverso con SSL | Servicios de red | ✅ Completado |
| Desplegar servicios auxiliares en Docker | Virtualización | ✅ Completado |
| Implementar autenticación JWT + RBAC | Seguridad | ✅ Completado |
| Configurar backups automáticos a Google Drive | Alta disponibilidad | ✅ Completado |
| Implementar automatizaciones con n8n | Sistemas | ✅ Completado |
| Configurar monitorización con Monarx | Seguridad | ✅ Completado |
| Sistema de imágenes con compresión y limpieza automática | Administración de sistemas | ✅ Completado |

### 1.3 Fuera del alcance

- Aplicación móvil nativa para repartidores
- Integración con pasarelas de pago
- Escalado horizontal a múltiples servidores

---

## 2. Metodología de trabajo

### 2.1 Scrum adaptado a proyecto individual

Se utilizó una metodología **Scrum adaptada** a las características de un proyecto individual de ciclo formativo:

- **3 Sprints** de 2 semanas cada uno, con objetivos técnicos bien definidos.
- **Daily standup** diario personal (registro escrito) para controlar el avance y detectar bloqueos.
- **Sprint Review** al final de cada sprint para validar que los entregables funcionan correctamente antes de pasar al siguiente.
- **Sprint Retrospectiva** al final de cada sprint para identificar mejoras en el proceso.
- **Backlog** gestionado en ProofHub con tablón Kanban (columnas: To Do, Assignades Spring, En curs, Bloquejades, Pendent de Revisió, Done).

### 2.2 Herramienta de gestión: ProofHub

El seguimiento de tareas se realizó con **ProofHub**, una herramienta de gestión de proyectos que permite organizar tareas en tablones Kanban, asignarlas a sprints y controlar su estado. A continuación se muestran las capturas del tablón real del proyecto:

**Tablón Kanban — Sprint 1 (Infraestructura y base de datos):**

<img width="1694" height="890" alt="image" src="https://github.com/user-attachments/assets/1e63304f-581b-48ab-9cff-a3d249641fa6" />


> *Captura real del tablón ProofHub mostrando las tareas del Sprint 1 completadas (columna Done en verde).*

**Tablón Kanban — Sprint 2 (Backend y aplicación):**

<img width="1709" height="884" alt="image" src="https://github.com/user-attachments/assets/f1ac6907-9c60-488f-bba1-ac5757ee3b88" />

> *Captura real del tablón ProofHub mostrando las tareas del Sprint 2 completadas.*

**Tablón Kanban — Sprint 3 (Producción y documentación):**

<img width="1691" height="846" alt="image" src="https://github.com/user-attachments/assets/ee0e5044-624b-4996-8127-82c90c9db620" />


> *Captura real del tablón ProofHub mostrando las tareas del Sprint 3 completadas.*

### 2.3 Flujo de trabajo de cada tarea

```
Backlog (To Do)
      │
      ▼
Assignada al sprint
      │
      ▼
En curso (Doing)
      │
      ├──► Bloqueada (Block) ──► Resolución del bloqueo ──► En curso
      │
      ▼
Pendiente de revisión (Review)
      │   (prueba y verificación del funcionamiento)
      ▼
Finalizada (Done) ✅
```

---

## 3. Planificación de sprints y cronograma

### 3.1 Distribución de sprints

| Sprint | Período | Semanas | Enfoque principal |
|---|---|---|---|
| **Sprint 1** | 06/04/2026 — 19/04/2026 | Semanas 1-2 | Infraestructura, VPS, base de datos |
| **Sprint 2** | 20/04/2026 — 03/05/2026 | Semanas 3-4 | Backend, API REST, autenticación, frontend |
| **Sprint 3** | 04/05/2026 — 20/05/2026 | Semanas 5-6 | Producción, seguridad, backups, documentación |

### 3.2 Diagrama de Gantt

```
TAREA                                    | S1W1 | S1W2 | S2W1 | S2W2 | S3W1 | S3W2 |
                                         | 6-12/4| 13-19/4| 20-26/4| 27/4-3/5| 4-10/5| 11-20/5|
─────────────────────────────────────────────────────────────────────────────────────────────
SPRINT 1 — INFRAESTRUCTURA Y BASE DE DATOS
Definir arquitectura del sistema         | ████ |      |      |      |      |      |
Configurar servidor VPS                  | ████ |      |      |      |      |      |
Instalar entorno con Docker              | ████ |      |      |      |      |      |
Definir API (endpoints y estructura)     | ████ |      |      |      |      |      |
Diseñar base de datos                    | ████ |      |      |      |      |      |
Desplegar base de datos (PostgreSQL)     |      | ████ |      |      |      |      |
Configurar seguridad básica              |      | ████ |      |      |      |      |
Configurar almacenamiento de archivos    |      | ████ |      |      |      |      |
Documentar procesos y archivos (base)    |      | ████ |      |      |      |      |
─────────────────────────────────────────────────────────────────────────────────────────────
SPRINT 2 — BACKEND Y APLICACIÓN
Implementar autenticación (login/JWT)    |      |      | ████ |      |      |      |
Desarrollar backend (API funcional)      |      |      | ████ | ████ |      |      |
Implementar creación/gestión pedidos     |      |      | ████ |      |      |      |
Implementar sistema tiempo real (WS)     |      |      |      | ████ |      |      |
Integrar subida y gestión de fotos       |      |      |      | ████ |      |      |
Conectar frontend con backend            |      |      |      | ████ |      |      |
Automatizar entrada pedidos (n8n)        |      |      |      | ████ |      |      |
Realizar pruebas funcionales completas   |      |      |      | ████ |      |      |
Automatizar notificaciones               |      |      |      | ████ |      |      |
─────────────────────────────────────────────────────────────────────────────────────────────
SPRINT 3 — PRODUCCIÓN Y DOCUMENTACIÓN
Configurar dominio y HTTPS               |      |      |      |      | ████ |      |
Desplegar aplicación en producción       |      |      |      |      | ████ |      |
Configurar backups automáticos           |      |      |      |      | ████ |      |
Configurar monitorización y logs         |      |      |      |      | ████ |      |
Implementar seguridad avanzada           |      |      |      |      | ████ |      |
Optimizar rendimiento del sistema        |      |      |      |      |      | ████ |
Preparar sistema para alta carga         |      |      |      |      |      | ████ |
Realizar pruebas finales (uso real)      |      |      |      |      |      | ████ |
Documentar sistema y uso                 |      |      |      |      |      | ████ |
─────────────────────────────────────────────────────────────────────────────────────────────
```

---

## 4. Sprint 1 — Infraestructura y base de datos

**Período:** 06/04/2026 — 19/04/2026  
**Objetivo:** Tener el servidor VPS configurado, Docker operativo y PostgreSQL desplegado con el esquema completo.

### 4.1 Tareas del sprint

| # | Tarea | Descripción | Estado | Días |
|---|---|---|---|---|
| 1 | Definir arquitectura del sistema | Decidir cómo se estructura la app: frontend, backend, base de datos, automatizaciones y cómo se comunican | ✅ Done | 1 |
| 2 | Configurar servidor VPS | Preparar el servidor Ubuntu 24.04, acceso SSH, seguridad básica, usuarios | ✅ Done | 1 |
| 3 | Instalar entorno con Docker | Instalar Docker y Docker Compose, dejar el sistema preparado para levantar servicios fácilmente | ✅ Done | 1 |
| 4 | Definir API (endpoints y estructura) | Establecer cómo se crearán, consultarán y actualizarán los pedidos desde la app | ✅ Done | 1 |
| 5 | Diseñar base de datos | Crear el modelo de datos completo: pedidos, clientes, repartidores, estados, fotos | ✅ Done | 2 |
| 6 | Desplegar base de datos (PostgreSQL) | Tener la BD funcionando y accesible desde el backend con esquema y migraciones aplicadas | ✅ Done | 2 |
| 7 | Configurar seguridad básica | Firewall SSH seguro, configuración inicial de accesos, usuarios con permisos mínimos | ✅ Done | 1 |
| 8 | Configurar almacenamiento de archivos | Sistema para guardar fotos de entregas con compresión automática | ✅ Done | 1 |
| 9 | Documentar procesos y archivos (base) | Dejar documentados los primeros pasos del proyecto y la arquitectura inicial | ✅ Done | 1 |

**Total tareas:** 9/9 completadas ✅

### 4.2 Sprint Review — 19/04/2026

**Asistentes:** Desarrollador + Tutor del proyecto  
**Resultado:** ✅ Sprint completado satisfactoriamente

**Validaciones realizadas:**
- [x] Servidor VPS accesible por SSH con usuario `localxpress-admin`
- [x] Docker y Docker Compose instalados y funcionando (`docker ps` sin errores)
- [x] PostgreSQL instalado nativamente, servicio activo (`systemctl status postgresql`)
- [x] Base de datos `localxpress` creada con esquema completo y migraciones 001-002 aplicadas
- [x] Nginx instalado y respondiendo en puerto 80
- [x] Conexión backend → PostgreSQL verificada
- [x] Arquitectura documentada en `DOCUMENTACION_TECNICA.md`

**Impedimentos encontrados:**
- Problema con la configuración de `pg_hba.conf` al intentar conectar desde Docker — resuelto añadiendo la red `172.17.0.0/16` al fichero de configuración.

### 4.3 Sprint Retrospectiva — 19/04/2026

| Aspecto | Observación |
|---|---|
| ✅ Qué fue bien | La decisión de instalar PostgreSQL nativamente en lugar de en Docker fue correcta — mayor rendimiento y sin riesgos de pérdida de datos |
| ⚠️ Qué mejorar | Dedicar más tiempo inicial a estudiar la configuración de `pg_hba.conf` antes de intentar conectar desde contenedores |
| 💡 Acción de mejora | Para el Sprint 2, verificar la configuración de red de Docker antes de intentar integraciones entre servicios |

---

## 5. Sprint 2 — Backend, API y aplicación

**Período:** 20/04/2026 — 03/05/2026  
**Objetivo:** API REST completamente funcional con autenticación JWT, WebSockets activos y frontend conectado al backend propio.

### 5.1 Tareas del sprint

| # | Tarea | Descripción | Estado | Días |
|---|---|---|---|---|
| 1 | Implementar autenticación (login/JWT) | Sistema de acceso para admin, tienda y repartidores con tokens JWT y control de roles | ✅ Done | 2 |
| 2 | Desarrollar backend (API funcional) | Crear lógica completa para gestionar pedidos, usuarios y estados vía API REST | ✅ Done | 3 |
| 3 | Implementar creación y gestión de pedidos | Flujo completo: crear, ver, actualizar estado de pedidos desde la app | ✅ Done | 2 |
| 4 | Implementar sistema en tiempo real (WebSockets) | Actualización automática de pedidos con Socket.io sin necesidad de recargar la página | ✅ Done | 1 |
| 5 | Integrar subida y gestión de fotos | Guardar y mostrar imágenes de entregas con compresión automática via sharp | ✅ Done | 1 |
| 6 | Conectar frontend con backend | Hacer que la app actual use la nueva API propia en lugar de Supabase | ✅ Done | 2 |
| 7 | Automatizar entrada de pedidos (WhatsApp/Email con N8N) | Que los pedidos entren automáticamente al sistema desde email y WhatsApp | ✅ Done | 2 |
| 8 | Realizar pruebas funcionales completas | Validar que todo el flujo funciona sin errores: login → pedido → entrega → foto | ✅ Done | 1 |
| 9 | Automatizar notificaciones | Notificaciones automáticas a tienda y repartidor ante cambios de estado | ✅ Done | 1 |

**Total tareas:** 9/9 completadas ✅

### 5.2 Sprint Review — 03/05/2026

**Asistentes:** Desarrollador + Tutor del proyecto  
**Resultado:** ✅ Sprint completado con incidencias menores resueltas

**Validaciones realizadas:**
- [x] Login funcional para los tres roles (admin, tienda, repartidor) con JWT
- [x] CRUD completo de pedidos operativo vía API REST
- [x] WebSocket activo — notificaciones en tiempo real verificadas entre dos navegadores
- [x] Subida de foto de entrega funcional con compresión sharp (70% calidad, 1200px máx)
- [x] Frontend conectado al backend propio (Supabase eliminado)
- [x] n8n operativo con flujo de captura de pedidos por email configurado
- [x] Pruebas funcionales end-to-end completadas sin errores críticos

**Impedimentos encontrados:**
- CSP (Content Security Policy) bloqueaba las peticiones del frontend al backend — resuelto corrigiendo las directivas en la configuración de Vite.
- JWT no llegaba correctamente al backend — resuelto añadiendo interceptor Axios para inyectar el token en cada petición.
- Socket.io no reconectaba tras refrescar la página — resuelto ajustando la lógica de inicialización del cliente.

### 5.3 Sprint Retrospectiva — 03/05/2026

| Aspecto | Observación |
|---|---|
| ✅ Qué fue bien | La integración Socket.io funcionó perfectamente una vez resuelto el problema de la reconexión. La decisión de usar PM2 facilitó enormemente el reinicio del backend durante las pruebas |
| ⚠️ Qué mejorar | Los problemas de CSP y JWT consumieron más tiempo del previsto — deberían haberse configurado correctamente desde el principio |
| 💡 Acción de mejora | Para el Sprint 3, configurar todas las cabeceras de seguridad al inicio antes de hacer pruebas de integración |

---

## 6. Sprint 3 — Producción, seguridad y documentación

**Período:** 04/05/2026 — 20/05/2026  
**Objetivo:** Sistema completamente desplegado en producción con SSL, backups automáticos, monitorización activa, optimización de rendimiento y documentación completa.

### 6.1 Tareas del sprint

| # | Tarea | Descripción | Estado | Días |
|---|---|---|---|---|
| 1 | Configurar dominio y HTTPS | Acceso seguro con dominio propio y certificado Let's Encrypt via Certbot | ✅ Done | 1 |
| 2 | Desplegar aplicación en producción | Backend + frontend funcionando en el VPS con PM2 y Nginx | ✅ Done | 1 |
| 3 | Configurar backups automáticos | Sistema de backup diario de la base de datos con subida automática a Google Drive | ✅ Done | 2 |
| 4 | Configurar monitorización y logs | Control de errores y estado del sistema con PM2, logs de Nginx y Monarx Agent | ✅ Done | 1 |
| 5 | Implementar seguridad avanzada | Protección de datos, accesos y API: bcrypt, JWT, CORS, Helmet.js, Monarx | ✅ Done | 2 |
| 6 | Optimizar rendimiento del sistema | Mejorar tiempos de carga y consultas: índices PostgreSQL, compresión de imágenes, pm2-logrotate | ✅ Done | 2 |
| 7 | Preparar sistema para alta carga | Asegurar que el sistema soporta volumen real de pedidos y fotos (limpieza automática cada 20 días) | ✅ Done | 1 |
| 8 | Realizar pruebas finales (uso real) | Simular operaciones reales de LocalXpress con pedidos, entregas y fotos reales | ✅ Done | 2 |
| 9 | Documentar sistema y uso | Dejar todo explicado para mantenimiento y uso diario: README, guías, diagramas | ✅ Done | 2 |

**Total tareas:** 9/9 completadas ✅

### 6.2 Sprint Review — 20/05/2026

**Asistentes:** Desarrollador + Tutor del proyecto  
**Resultado:** ✅ Sprint completado — proyecto finalizado y listo para entrega

**Validaciones realizadas:**
- [x] SSL activo y funcionando — certificado Let's Encrypt emitido y renovación automática configurada
- [x] PM2 gestionando ambos backends (`:3001` y `:3002`) con reinicio automático
- [x] Backup automático ejecutado y verificado en Google Drive (`/var/log/localxpress-backup.log`)
- [x] Monarx Agent activo y funcionando
- [x] Índices de rendimiento aplicados en PostgreSQL (migración 002)
- [x] Compresión de imágenes activa: fotos de ~2MB se reducen a ~100KB
- [x] Limpieza automática de fotos cada 20 días verificada con `dry_run=true`
- [x] Pruebas con pedidos reales completadas sin errores
- [x] Documentación completa generada y subida a GitHub

**Impedimentos encontrados:**
- Contraseña del superusuario PostgreSQL perdida tras cambios en `pg_hba.conf` — resuelta mediante reset por peer auth temporal.
- Puerto 5432 de PostgreSQL expuesto a internet identificado como riesgo de seguridad — pendiente de cerrar con UFW.

### 6.3 Sprint Retrospectiva — 20/05/2026

| Aspecto | Observación |
|---|---|
| ✅ Qué fue bien | El sistema de backup automático funciona perfectamente desde el primer día de configuración. La documentación en GitHub organizada por carpetas facilita enormemente la lectura |
| ⚠️ Qué mejorar | La seguridad a nivel de red (UFW, fail2ban) debería haberse configurado antes del despliegue en producción, no al final |
| 💡 Acción de mejora | En futuros proyectos, el hardening de seguridad de red debe ser parte del Sprint 1, no del Sprint 3 |

---

## 7. Seguimiento diario de trabajo

### Semana 1 — 06/04 al 12/04/2026 (Sprint 1)

| Día | Tareas realizadas | Tiempo | Incidencias |
|---|---|---|---|
| Lun 06/04 | Definición de arquitectura del sistema, elección de tecnologías, creación del repositorio en GitHub | 4h | — |
| Mar 07/04 | Contratación y configuración inicial del VPS Hostinger, acceso SSH, actualización del sistema | 3h | — |
| Mié 08/04 | Instalación de Docker y Docker Compose, verificación del entorno | 2h | — |
| Jue 09/04 | Diseño del esquema de base de datos (tablas, relaciones, índices), creación del fichero schema.sql | 4h | — |
| Vie 10/04 | Definición completa de los endpoints de la API REST, documentación de la estructura | 3h | — |
| Lun 12/04 | Instalación nativa de PostgreSQL 16, configuración de usuarios y permisos, primer esquema aplicado | 4h | Problema con pg_hba.conf al conectar desde Docker — resuelto añadiendo red 172.17.0.0/16 |

### Semana 2 — 13/04 al 19/04/2026 (Sprint 1)

| Día | Tareas realizadas | Tiempo | Incidencias |
|---|---|---|---|
| Lun 13/04 | Aplicación de migraciones SQL (001, 002), verificación de índices de rendimiento | 3h | — |
| Mar 14/04 | Instalación y configuración de Nginx como proxy inverso | 2h | — |
| Mié 15/04 | Configuración de seguridad básica SSH, desactivación de root login, usuario `localxpress-admin` | 2h | — |
| Jue 16/04 | Configuración del sistema de almacenamiento de fotos, estructura de directorios `uploads/proofs/` | 2h | — |
| Vie 17/04 | Despliegue de n8n en Docker con volumen persistente `n8n_data` | 3h | — |
| Lun 19/04 | Sprint Review 1 — Validación de todos los entregables, documentación de la arquitectura inicial | 3h | — |

### Semana 3 — 20/04 al 26/04/2026 (Sprint 2)

| Día | Tareas realizadas | Tiempo | Incidencias |
|---|---|---|---|
| Lun 20/04 | Desarrollo del sistema de autenticación JWT: login, generación de token, middleware de verificación | 4h | — |
| Mar 21/04 | Implementación de RBAC: middleware checkRole para admin, tienda y repartidor | 3h | — |
| Mié 22/04 | Desarrollo de endpoints CRUD completos para pedidos (stops) | 4h | — |
| Jue 23/04 | Implementación de Socket.io para notificaciones en tiempo real | 3h | Socket.io no reconectaba tras refrescar — resuelto ajustando inicialización del cliente |
| Vie 24/04 | Integración de sharp para compresión automática de imágenes al subir | 2h | — |
| Lun 26/04 | Configuración de PM2 para gestión de los dos procesos backend en producción | 2h | — |

### Semana 4 — 27/04 al 03/05/2026 (Sprint 2)

| Día | Tareas realizadas | Tiempo | Incidencias |
|---|---|---|---|
| Lun 27/04 | Conexión del frontend React al nuevo backend (sustitución de Supabase por Axios) | 4h | CSP bloqueaba peticiones — resuelto corrigiendo directivas en vite.config.js |
| Mar 28/04 | Corrección del interceptor Axios para inyección automática del JWT en cada petición | 3h | JWT no llegaba al backend — resuelto con interceptor Axios |
| Mié 29/04 | Configuración de flujos n8n: captura de pedidos por email con idempotencia | 4h | — |
| Jue 30/04 | Aplicación de migraciones 007 (notificaciones entrega) y 008 (idempotencia email) | 2h | — |
| Vie 01/05 | Pruebas funcionales end-to-end: login → crear pedido → asignar → entregar → foto | 3h | — |
| Lun 03/05 | Sprint Review 2 — Validación de API, WebSockets, autenticación y frontend conectado | 3h | — |

### Semana 5 — 04/05 al 10/05/2026 (Sprint 3)

| Día | Tareas realizadas | Tiempo | Incidencias |
|---|---|---|---|
| Lun 04/05 | Obtención certificado SSL Let's Encrypt con Certbot, configuración HTTPS en Nginx | 2h | — |
| Mar 05/05 | Despliegue final en producción: PM2 startup, pm2 save, verificación de ambos backends | 3h | — |
| Mié 06/05 | Desarrollo del script `/usr/local/bin/backup-localxpress.sh` con pg_dump y subida a Google Drive | 4h | — |
| Jue 07/05 | Configuración del cron job de backup diario (`/etc/cron.d/localxpress-backup`) y verificación del log | 2h | — |
| Vie 08/05 | Instalación y configuración de Monarx Agent, verificación de monitorización activa | 1h | — |
| Lun 10/05 | Implementación de seguridad avanzada: Helmet.js, CORS estricto, rate limiting en endpoints críticos | 3h | — |

### Semana 6 — 11/05 al 20/05/2026 (Sprint 3)

| Día | Tareas realizadas | Tiempo | Incidencias |
|---|---|---|---|
| Lun 11/05 | Aplicación de índices de rendimiento PostgreSQL, análisis con EXPLAIN ANALYZE | 3h | — |
| Mar 12/05 | Configuración de limpieza automática de fotos cada 20 días con node-cron | 2h | — |
| Mié 13/05 | Pruebas de carga: simulación de 50 pedidos concurrentes, verificación de tiempos de respuesta | 3h | Contraseña PostgreSQL perdida — resuelta mediante reset por peer auth temporal |
| Jue 14/05 | Pruebas finales de uso real: pedidos reales, fotos de entrega reales, backups verificados | 4h | — |
| Vie 15/05 | Creación de ramas Git de seguridad antes de cambios críticos en producción | 1h | — |
| Lun 18/05 | Generación de documentación técnica completa: README, diagramas, guías de despliegue | 4h | — |
| Mar 19/05 | Documentación de seguridad, operación y mantenimiento | 3h | — |
| Mié 20/05 | Sprint Review 3 final — Validación completa del sistema y entrega del proyecto | 3h | — |

---

## 8. Recursos utilizados

### 8.1 Hardware

| Recurso | Especificaciones | Uso en el proyecto |
|---|---|---|
| VPS Hostinger | Ubuntu 24.04 LTS, 96GB SSD, 7.8GB RAM | Servidor de producción — todos los servicios |
| Ordenador de desarrollo | Windows 11, VSCode, Git | Desarrollo y despliegue del código |

### 8.2 Software y herramientas

| Herramienta | Versión | Uso |
|---|---|---|
| Ubuntu Server | 24.04.4 LTS | Sistema operativo del servidor |
| PostgreSQL | 16 | Base de datos relacional |
| Node.js | 20 LTS | Entorno de ejecución del backend |
| PM2 | Latest | Gestión de procesos Node.js en producción |
| Nginx | Latest | Proxy inverso y servidor web |
| Certbot | Latest | Gestión de certificados SSL |
| Docker | Latest | Contenedores para servicios auxiliares |
| Docker Compose | v2 | Orquestación de contenedores |
| n8n | Stable | Automatizaciones y workflows |
| EasyPanel | Latest | Panel de administración del servidor |
| Monarx Agent | Latest | Monitorización de seguridad |
| Git | Latest | Control de versiones |
| GitHub | — | Repositorio remoto y colaboración |
| ProofHub | — | Gestión de tareas y sprints |
| VS Code | Latest | Entorno de desarrollo |
| React + Vite | 18 / 5 | Framework frontend |
| sharp | Latest | Compresión de imágenes |
| Socket.io | 4.x | WebSockets tiempo real |

### 8.3 Estimación de tiempo por sprint

| Sprint | Horas estimadas | Horas reales | Diferencia |
|---|---|---|---|
| Sprint 1 | 30h | 32h | +2h (problema pg_hba.conf) |
| Sprint 2 | 35h | 40h | +5h (problemas CSP, JWT, Socket.io) |
| Sprint 3 | 30h | 33h | +3h (problema contraseña PostgreSQL) |
| **Total** | **95h** | **105h** | **+10h** |

---

## 9. Gestión de riesgos

### 9.1 Riesgos identificados antes del proyecto

| ID | Riesgo | Probabilidad | Impacto | Plan de mitigación |
|---|---|---|---|---|
| R01 | Pérdida de datos en la base de datos | Baja | Crítico | Backup diario automático a Google Drive con pg_dump |
| R02 | Caída del servidor VPS | Baja | Alto | Proceso de recuperación documentado, backup diario |
| R03 | Expiración del certificado SSL | Muy baja | Alto | Certbot con renovación automática cada 12h |
| R04 | Fallo del proceso Node.js | Baja | Alto | PM2 con reinicio automático en menos de 1 segundo |
| R05 | Ataque de fuerza bruta al servidor | Media | Alto | fail2ban (pendiente), SSH por clave, Monarx activo |
| R06 | Incompatibilidad entre servicios Docker y PostgreSQL nativo | Media | Medio | Configurar pg_hba.conf con red Docker (172.17.0.0/16) |
| R07 | Desbordamiento del disco por imágenes | Baja | Medio | Limpieza automática de fotos cada 20 días |
| R08 | Problemas de CORS entre frontend y backend | Alta | Medio | Configurar CORS en Express desde el inicio |
| R09 | Falta de tiempo para documentación | Media | Medio | Reservar la semana final completa para documentación |

### 9.2 Riesgos materializados durante el proyecto

| ID | Riesgo | Cuándo | Impacto real | Solución aplicada |
|---|---|---|---|---|
| R06 | Problema conexión Docker → PostgreSQL | Sprint 1, semana 1 | Bajo — 2h de trabajo extra | Añadida red `172.17.0.0/16` a `pg_hba.conf` |
| R08 | CORS bloqueando frontend → backend | Sprint 2, semana 3 | Medio — 4h de trabajo extra | Configuración explícita de CORS en Express + corrección CSP en Vite |
| — | JWT no llegaba al backend | Sprint 2, semana 4 | Bajo — 2h de trabajo extra | Interceptor Axios para inyección automática del token |
| — | Socket.io sin reconexión tras refresh | Sprint 2, semana 3 | Bajo — 1h de trabajo extra | Ajuste lógica inicialización cliente Socket.io |
| — | Contraseña PostgreSQL perdida | Sprint 3, semana 5 | Bajo — 1h de trabajo extra | Reset via peer auth temporal, nueva contraseña establecida |

---

## 10. Resumen de incidencias y soluciones

| # | Incidencia | Sprint | Horas invertidas | Solución |
|---|---|---|---|---|
| INC-001 | `pg_hba.conf` no permitía conexión desde contenedores Docker | Sprint 1 | 2h | Añadida regla `host all all 172.17.0.0/16 md5` |
| INC-002 | CSP de Vite bloqueaba peticiones al backend en producción | Sprint 2 | 3h | Corrección directivas Content-Security-Policy en `vite.config.js` |
| INC-003 | JWT no incluido en peticiones Axios | Sprint 2 | 1h | Interceptor Axios: `Authorization: Bearer <token>` en cada petición |
| INC-004 | Socket.io no reconectaba tras refresco de página | Sprint 2 | 1h | Lógica de inicialización ajustada para esperar disponibilidad del token |
| INC-005 | Contraseña superusuario PostgreSQL perdida tras cambios en `pg_hba.conf` | Sprint 3 | 1h | Reset via cambio temporal a peer auth + `ALTER USER postgres PASSWORD` |

**Total horas en incidencias:** 8h sobre 105h totales (7.6% del tiempo total del proyecto)
