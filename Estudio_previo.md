# Estudi Previ — LocalXpress


## Taula de continguts

1. [Identificació de necessitats tècniques del projecte](#1-identificació-de-necessitats-tècniques-del-projecte)
2. [Estudi de tecnologies de virtualització i contenidors](#2-estudi-de-tecnologies-de-virtualització-i-contenidors)
3. [Estudi de tecnologies de base de dades](#3-estudi-de-tecnologies-de-base-de-dades)
4. [Estudi de tecnologies de backend i API](#4-estudi-de-tecnologies-de-backend-i-api)
5. [Estudi de tecnologies de proxy invers i SSL](#5-estudi-de-tecnologies-de-proxy-invers-i-ssl)
6. [Estudi de tecnologies de seguretat](#6-estudi-de-tecnologies-de-seguretat)
7. [Estudi de tecnologies d'automatització](#7-estudi-de-tecnologies-dautomatització)
8. [Estudi de solucions d'infraestructura al mercat](#8-estudi-de-solucions-dinfrastructura-al-mercat)
9. [Anàlisi comparativa i justificació de decisions](#9-anàlisi-comparativa-i-justificació-de-decisions)
10. [Conclusions](#10-conclusions)

---

## 1. Identificació de necessitats tècniques del projecte

### 1.1 Descripció del projecte

LocalXpress és una plataforma de repartiment local que requereix la posada en marxa d'una infraestructura completa de sistemes informàtics des de zero. El projecte engloba les àrees principals del cicle ASIX: administració de servidors Linux, virtualització i contenidors, bases de dades, xarxes i seguretat, desplegament d'aplicacions i automatitzacions.

### 1.2 Requisits tècnics identificats

Abans de triar cap tecnologia, es van identificar els requisits tècnics que havia de complir la infraestructura:

| Requisit | Categoria ASIX | Descripció |
|---|---|---|
| Servidor Linux administrat | Sistemes | VPS amb Ubuntu, accés SSH, gestió de serveis |
| Aïllament de serveis | Virtualització | Contenidors Docker per a serveis auxiliars |
| Base de dades relacional | Bases de dades | PostgreSQL amb esquema, migracions i backups |
| Proxy invers amb SSL | Xarxes | Nginx amb Certbot i Let's Encrypt |
| Autenticació segura | Seguretat | JWT, bcrypt, CORS, RBAC |
| Comunicació en temps real | Xarxes | WebSockets per a notificacions en temps real |
| Automatitzacions | Sistemes | Workflows per a tasques repetitives |
| Còpies de seguretat automàtiques | Sistemes | Backup diari automatitzat amb emmagatzematge remot |
| Monitorització | Sistemes | Control de processos, logs i seguretat activa |
| Gestió de processos | Sistemes | Reinici automàtic, zero downtime en desplegaments |

### 1.3 Àrees del currículum ASIX aplicades

El projecte aplica directament els mòduls professionals del cicle ASIX:

| Mòdul professional | Aplicació en el projecte |
|---|---|
| **Administració de sistemes operatius** | Administració Ubuntu 24.04, systemd, cron, SSH, gestió d'usuaris |
| **Implantació de sistemes operatius** | Instal·lació i configuració nativa de PostgreSQL, Nginx, Node.js |
| **Planificació i administració de xarxes** | Configuració de ports, proxy invers, VPN, tallafocs |
| **Serveis de xarxa i Internet** | DNS, SSL/TLS, HTTP/HTTPS, WebSockets |
| **Seguretat i Alta Disponibilitat** | JWT, bcrypt, Monarx, Certbot, fail2ban, UFW |
| **Implantació d'aplicacions web** | Desplegament de backend Node.js, frontend React/Vite |
| **Administració de bases de dades** | PostgreSQL, esquema, migracions, backups, optimització |
| **Sistemes de gestió empresarial** | Automatitzacions n8n, integracions externes |

---

## 2. Estudi de tecnologies de virtualització i contenidors

### 2.1 Context

Un dels requisits del projecte és aïllar els serveis auxiliars (automatitzacions, panel d'administració) del sistema operatiu base per facilitar-ne l'actualització i manteniment. Es van estudiar les principals tecnologies de virtualització i contenidors disponibles.

### 2.2 Opcions analitzades

#### Virtualització completa — Màquines virtuals

| Tecnologia | Tipus | Descripció |
|---|---|---|
| VMware ESXi | Hipervisor tipus 1 | Hipervisor bare-metal empresarial, alt rendiment |
| Proxmox VE | Hipervisor tipus 1 | Open source, gestió web, KVM + LXC |
| VirtualBox | Hipervisor tipus 2 | Per a entorns de desenvolupament local |
| KVM (Kernel-based VM) | Hipervisor tipus 1 | Integrat al kernel Linux, alt rendiment |

**Característiques de les VM:**
- Aïllament complet del sistema operatiu
- Cada VM té el seu propi kernel
- Alt consum de recursos (RAM, disc)
- Temps d'arrencada lent (minuts)
- Ideal per a serveis que requereixen sistemes operatius completament diferents

#### Contenidors — Virtualització a nivell de SO

| Tecnologia | Descripció | Avantatges | Desavantatges |
|---|---|---|---|
| **Docker** | Contenidors basats en imatges | Lleuger, ràpid, Docker Hub, Compose | Kernel compartit amb l'host |
| LXC / LXD | Contenidors Linux natius | Integrat al kernel, molt lleuger | Menys ecosistema que Docker |
| Podman | Alternativa a Docker sense daemon | Rootless, compatible Docker | Menys madur, menys documentació |
| containerd | Runtime de baix nivell | Molt lleuger, usat per Kubernetes | No té CLI pròpia |

#### Orquestació de contenidors

| Tecnologia | Descripció | Complexitat | Adequació al projecte |
|---|---|---|---|
| **Docker Compose** | Orquestració de múltiples contenidors en un sol host | Baixa | ✅ Perfecta per a un VPS |
| Docker Swarm | Clúster de nodes Docker | Mitjana | ❌ Excessiva per a un sol servidor |
| Kubernetes (K8s) | Orquestació empresarial a escala | Molt alta | ❌ Excessiva per a aquest projecte |
| Nomad (HashiCorp) | Orquestació lleugera | Mitjana | ❌ Menys estesa, poc valor afegit |

### 2.3 Decisió: Docker + Docker Compose per a serveis auxiliars

**Justificació tècnica:**

Docker amb Docker Compose és l'opció òptima per al perfil del projecte per les raons següents:

- **Recursos:** Els contenidors comparteixen el kernel de l'host i consumeixen una fracció dels recursos d'una VM completa. En un VPS amb 7.8GB de RAM, n8n i EasyPanel en Docker consumeixen menys de 500MB, mentre que dues VM equivalents consumirien 2-4GB.
- **Velocitat d'arrencada:** Un contenidor Docker arrenca en mil·lisegons, una VM en minuts.
- **Actualització:** Actualitzar n8n consisteix a fer `docker pull` i `docker compose up -d`. Amb una VM caldria entrar, actualitzar paquets i reiniciar.
- **Portabilitat:** El `docker-compose.yml` documenta exactament com s'executa cada servei, facilitant la reproducció en un altre servidor si cal.
- **Aïllament adequat:** Per a serveis de tercers (n8n, EasyPanel), el contenidor proporciona l'aïllament necessari sense la sobrecàrrega d'una VM.

**Per què el backend i PostgreSQL no estan en Docker:**

Els serveis crítics de rendiment (backend Node.js i PostgreSQL) s'executen de forma **nativa** al sistema operatiu, no en contenidors:

- PostgreSQL natiu té accés directe al subsistema d'E/S del kernel, eliminant la latència de xarxa dels contenidors.
- Experiència prèvia al projecte: una instància AWS EC2 amb PostgreSQL en Docker va patir pèrdua de dades per volums no persistents. La instal·lació nativa elimina completament aquest risc.
- El backend Node.js gestionat per PM2 té accés directe al sistema de fitxers per a les imatges d'entrega, sense passar per la capa de xarxa Docker.

---

## 3. Estudi de tecnologies de base de dades

### 3.1 Requisits identificats

- Suport per a transaccions ACID (integritat de dades en operacions concurrents)
- Model de dades relacional (pedides, usuaris, zones, tarifes amb relacions complexes)
- Suport per a JSON (coordenades de zones geogràfiques)
- Capacitat de còpies de seguretat automàtiques (`pg_dump`)
- Rendiment adequat per a consultes amb índexs sobre múltiples taules
- Accés concurrent des de múltiples processos (backend APP i backend APP-IND)

### 3.2 Opcions analitzades

| Sistema | Tipus | ACID | JSON | Rendiment I/O | Llicència |
|---|---|---|---|---|---|
| **PostgreSQL** | Relacional | ✅ Complet | ✅ Nadiu (jsonb) | ✅ Excel·lent | Open source |
| MySQL / MariaDB | Relacional | ✅ (InnoDB) | ⚠️ Bàsic | ✅ Bo | Open source |
| SQLite | Relacional embegut | ✅ | ❌ | ⚠️ Limitat en concurrència | Open source |
| MongoDB | Documental | ⚠️ Parcial | ✅ Natiu | ✅ Bo | SSPL |
| Redis | Clau-valor / caché | ❌ | ❌ | ✅ Molt alt | BSD |
| Firebase Realtime DB | NoSQL al núvol | ❌ | ✅ | ✅ | Propietari |

### 3.3 Comparativa detallada: PostgreSQL vs MySQL vs MongoDB

| Criteri | PostgreSQL | MySQL | MongoDB |
|---|---|---|---|
| Transaccions ACID | ✅ Complet | ✅ (InnoDB) | ⚠️ Parcial |
| Integritat referencial (FK) | ✅ | ✅ | ❌ |
| Suport JSON avançat | ✅ jsonb amb índexs | ⚠️ Bàsic | ✅ Natiu |
| Herència de taules | ✅ | ❌ | ❌ |
| Vistes materialitzades | ✅ | ❌ | ❌ |
| pg_dump per a backups | ✅ Nativa | ✅ mysqldump | ⚠️ mongodump |
| Extensions (PostGIS, etc.) | ✅ Molt ric | ⚠️ Limitat | ⚠️ Limitat |
| Rendiment en consultes complexes | ✅ Excel·lent | ✅ Bo | ⚠️ Menys òptim |
| Comunitat i documentació | ✅ Molt gran | ✅ Molt gran | ✅ Gran |

### 3.4 Decisió: PostgreSQL 16 instal·lació nativa

**Justificació tècnica:**
- El model de dades de LocalXpress és inherentment relacional: un `stop` pertany a una `store`, té un `driver`, una `pricing_zone`, una `order_photo` i genera `delivery_notifications`. PostgreSQL és el motor que millor gestiona aquestes relacions amb integritat referencial completa.
- El camp `coordinates` de `pricing_zones` utilitza el tipus `jsonb` de PostgreSQL, que permet índexs GIN sobre dades JSON, impossible amb MySQL.
- `pg_dump` genera volcats consistents de tota la base de dades en un sol fitxer compressible, perfecte per al sistema de backup automàtic al Google Drive.
- La instal·lació nativa (fora de Docker) maximitza el rendiment d'E/S al disc SSD del VPS.

---

## 4. Estudi de tecnologies de backend i API

### 4.1 Requisits identificats

- API REST per a la comunicació entre frontend i servidor
- Autenticació i autorització per rols
- Comunicació bidireccional en temps real (WebSockets)
- Gestió de fitxers (imatges de confirmació d'entrega)
- Integració amb PostgreSQL
- Gestió de processos en producció (reinici automàtic, zero downtime)

### 4.2 Entorns d'execució del servidor

| Tecnologia | Llenguatge | Model concurrència | Adequació |
|---|---|---|---|
| **Node.js** | JavaScript | Event loop (no bloquejant) | ✅ Excel·lent per a I/O intensiu |
| Python (Django/FastAPI) | Python | Síncron / ASGI | ✅ Bo, però menys natural amb Socket.io |
| PHP (Laravel) | PHP | Síncron per petició | ⚠️ Menys adequat per a WebSockets |
| Java (Spring Boot) | Java | Multi-thread | ✅ Molt robust, però excessiu per a aquest projecte |
| Go (Gin/Fiber) | Go | Goroutines | ✅ Excel·lent rendiment, però corba d'aprenentatge alta |
| Ruby on Rails | Ruby | Síncron | ⚠️ Menys rendiment, ecosistema decreixent |

### 4.3 Gestors de processos per a Node.js en producció

| Eina | Reinici automàtic | Zero downtime | Logs | Monitorització | Startup SO |
|---|---|---|---|---|---|
| **PM2** | ✅ | ✅ (`pm2 reload`) | ✅ amb rotació | ✅ (`pm2 monit`) | ✅ (`pm2 startup`) |
| systemd | ✅ | ❌ | ✅ (journald) | ⚠️ Bàsic | ✅ Natiu |
| Forever | ✅ | ❌ | ⚠️ Bàsic | ❌ | ❌ |
| Nodemon | ❌ | ❌ | ❌ | ❌ | ❌ (només dev) |
| Docker (restart: always) | ✅ | ⚠️ | ✅ | ⚠️ | ✅ |

### 4.4 Protocols de comunicació en temps real

| Protocol | Bidireccional | Reconnexió automàtica | Fallback | Adequació |
|---|---|---|---|---|
| **Socket.io** | ✅ | ✅ Nativa | ✅ Long-polling | ✅ Excel·lent |
| WebSocket natiu (RFC 6455) | ✅ | ❌ Manual | ❌ | ✅ Bo però més codi |
| Server-Sent Events (SSE) | ❌ Unidireccional | ⚠️ | ✅ HTTP | ⚠️ No serveix per a aquest cas |
| Long Polling | ✅ Simulat | ✅ | ✅ | ❌ Ineficient |
| gRPC streaming | ✅ | ❌ | ❌ | ❌ Excessiu per a navegadors |

### 4.5 Decisions: Node.js + Express + Socket.io + PM2

**Justificació tècnica:**
- Node.js utilitza un model d'event loop no bloquejant ideal per a aplicacions amb moltes connexions concurrents (repartidors connectats simultàniament via WebSocket).
- Socket.io sobre Node.js és la combinació estàndard de la indústria per a notificacions en temps real. La reconnexió automàtica és crítica per a repartidors amb connectivitat mòbil intermitent.
- PM2 és l'estàndard de facto per a processos Node.js en producció: `pm2 reload` permet actualitzar l'aplicació sense cap tall de servei.
- PM2 gestiona dos processos independents (`:3001` i `:3002`) amb logs separats i reinici automàtic individual.

---

## 5. Estudi de tecnologies de proxy invers i SSL

### 5.1 Requisits identificats

- Terminació SSL/TLS per a tot el trànsit extern
- Enrutament cap a múltiples backends interns (`:3001` i `:3002`)
- Servei d'arxius estàtics del frontend compilat
- Gestió automàtica de certificats (sense intervenció manual)
- Redirecció HTTP → HTTPS

### 5.2 Servidors web i proxy invers

| Tecnologia | Model | Rendiment estàtics | Proxy | SSL automàtic | Adequació |
|---|---|---|---|---|---|
| **Nginx** | Event-driven | ✅ Excel·lent | ✅ | ✅ (Certbot) | ✅ |
| Apache | Process/thread | ✅ Bo | ✅ | ✅ (Certbot) | ✅ Bo però més pesat |
| Traefik | Event-driven | ⚠️ | ✅ | ✅ Automàtic Docker | ⚠️ Complex sense Docker |
| Caddy | Event-driven | ✅ | ✅ | ✅ Natiu | ✅ Simple però menys estès |
| HAProxy | Event-driven | ❌ (només proxy) | ✅ Excel·lent | ⚠️ | ⚠️ Només balanceig |

### 5.3 Gestió de certificats SSL/TLS

| Solució | Gratuït | Renovació automàtica | Let's Encrypt | Complexitat |
|---|---|---|---|---|
| **Certbot** | ✅ | ✅ (cron) | ✅ | Baixa |
| acme.sh | ✅ | ✅ | ✅ | Mitjana |
| Certificat autosignat | ✅ | ❌ Manual | ❌ | Baixa |
| Certificat comercial (DigiCert, etc.) | ❌ (cost anual) | ❌ Manual | ❌ | Baixa |
| Cloudflare SSL | ✅ (pla gratuït) | ✅ | ✅ | Baixa |

### 5.4 Decisió: Nginx + Certbot (Let's Encrypt)

**Justificació tècnica:**
- Nginx és el servidor web/proxy invers més estès en entorns de producció Linux, amb documentació exhaustiva i suport actiu.
- La combinació Nginx + Certbot és l'estàndard per a l'obtenció i renovació automàtica de certificats Let's Encrypt gratuïts.
- El cron configurat (`0 */12 * * *`) intenta la renovació cada 12 hores, assegurant que el certificat mai caduca sense intervenció humana.
- Nginx serveix el frontend compilat (arxius estàtics) directament sense passar pel procés Node.js, reduint la càrrega del backend.

---

## 6. Estudi de tecnologies de seguretat

### 6.1 Requisits de seguretat identificats

- Autenticació d'usuaris sense emmagatzemar contrasenyes en text pla
- Control d'accés per rols (admin, comerciant, repartidor)
- Xifratge del trànsit client-servidor
- Protecció contra accés no autoritzat al servidor
- Monitorització activa d'amenaces
- Protecció contra atacs de força bruta

### 6.2 Sistemes d'autenticació

| Mètode | Stateless | Escala | Seguretat | Complexitat |
|---|---|---|---|---|
| **JWT (JSON Web Token)** | ✅ | ✅ Excel·lent | ✅ Bo (amb secret fort) | Baixa |
| Sessions en servidor (express-session) | ❌ | ⚠️ Requereix Redis per escalar | ✅ Bo | Mitjana |
| OAuth 2.0 / OpenID Connect | ✅ | ✅ | ✅ Excel·lent | Alta |
| API Keys estàtiques | ✅ | ✅ | ⚠️ No permet expiració fàcil | Molt baixa |
| Basic Auth (HTTP) | ✅ | ✅ | ❌ Insegur sense HTTPS | Molt baixa |

### 6.3 Algorismes de hash de contrasenyes

| Algorisme | Resistència brute-force | Salt integrat | Recomanat OWASP | Velocitat |
|---|---|---|---|---|
| **bcrypt** | ✅ Alt (cost configurable) | ✅ | ✅ | Lent (per disseny) |
| Argon2 | ✅ Molt alt | ✅ | ✅ (primera opció) | Molt lent |
| scrypt | ✅ Alt | ✅ | ✅ | Lent |
| PBKDF2 | ✅ Bo | ✅ | ✅ | Configurable |
| SHA-256 (sense salt) | ❌ Molt baix | ❌ | ❌ | Molt ràpid |
| MD5 | ❌ Nul | ❌ | ❌ | Molt ràpid |

### 6.4 Eines de seguretat a nivell de sistema

| Eina | Funció | Tipus | Implementació al projecte |
|---|---|---|---|
| **Monarx Agent** | Detecció de malware i amenaces en temps real | Agent actiu | ✅ Instal·lat i actiu |
| **Certbot** | Gestió automàtica de certificats SSL | Renovació automàtica | ✅ Cron cada 12h |
| UFW (Uncomplicated Firewall) | Tallafocs a nivell de xarxa | Tallafocs | 🔜 Pendent de configurar |
| fail2ban | Protecció contra força bruta SSH/HTTP | IDS/IPS | 🔜 Pendent de configurar |
| OpenSSH | Accés remot segur | Protocol | ✅ Actiu al port 22 |
| Helmet.js | Capçaleres HTTP de seguretat | Middleware Express | ✅ Aplicat al backend |

### 6.5 Decisions de seguretat aplicades

**Justificació tècnica:**
- **JWT** s'ha triat per la seva naturalesa *stateless*: no requereix emmagatzemar sessions al servidor, cosa que simplifica l'arquitectura i facilita l'escalat futur. El token inclou el `role` de l'usuari i té expiració configurable.
- **bcrypt** amb 10 rondes de sal és la implementació recomanada per OWASP per a hash de contrasenyes en Node.js. Fa computacionalment inviable un atac per força bruta fins i tot si s'obtingués accés a la base de dades.
- **Monarx** proporciona detecció activa d'amenaces gestionada per Hostinger, amb actualitzacions automàtiques setmanals.
- Els ports del backend (`:3001`, `:3002`) i PostgreSQL només haurien d'estar accessibles des de `localhost`, eliminant una superfície d'atac significativa.

---

## 7. Estudi de tecnologies d'automatització

### 7.1 Requisits identificats

- Processament automàtic d'entrades de comandes (email, WhatsApp)
- Notificacions automàtiques als actors implicats
- Tasques programades (backups, neteja d'imatges, informes)
- Integració amb serveis externs (SMTP, WhatsApp Business API)
- Idempotència en el processament per evitar duplicats

### 7.2 Plataformes d'automatització

| Plataforma | Self-hosted | Cost | Integracions | Control dades | Complexitat |
|---|---|---|---|---|---|
| **n8n** | ✅ | Gratuït (self-hosted) | 400+ | ✅ Total | Baixa-mitjana |
| Zapier | ❌ | Des de 20$/mes + per operació | 6.000+ | ❌ Dades a tercers | Molt baixa |
| Make (Integromat) | ❌ | Des de 9$/mes + per operació | 1.500+ | ❌ Dades a tercers | Baixa |
| Apache Airflow | ✅ | Gratuït | Codi | ✅ Total | Molt alta |
| Cron + scripts bash | ✅ | Gratuït | Codi | ✅ Total | Alta (manteniment) |
| Node-RED | ✅ | Gratuït | Bona | ✅ Total | Mitjana |

### 7.3 Eines de programació de tasques al sistema

| Eina | Àmbit | Integració BD | Persistència | Adequació |
|---|---|---|---|---|
| **node-cron** | Dins del procés Node.js | ✅ Directa | Mentre el procés estigui actiu | ✅ Per a tasques lligades al backend |
| cron del sistema (crontab) | Sistema operatiu | ⚠️ Via scripts | ✅ Independent del procés | ✅ Per a backups i tasques de sistema |
| Agenda.js | Node.js + MongoDB | ✅ | ✅ (persisteix a BD) | ⚠️ Requereix MongoDB |
| Bull / BullMQ | Node.js + Redis | ✅ | ✅ (persisteix a Redis) | ⚠️ Requereix Redis |

### 7.4 Decisions: n8n + node-cron + cron del sistema

**Justificació tècnica:**
- **n8n** s'autoallotja al servidor en un contenidor Docker amb el volum `n8n_data` persistent. Això garanteix que cap dada de les comandes o clients surti del servidor propi, a diferència de Zapier o Make que processen les dades als seus servidors.
- **node-cron** s'utilitza per a la neteja automàtica de fotografies (02:30h) perquè necessita accés directe al filesystem i a la connexió PostgreSQL del procés backend, sense overhead addicional.
- **cron del sistema** (`/etc/cron.d/localxpress-backup`) s'utilitza per al backup de PostgreSQL (03:00h) perquè és una tasca de sistema operatiu independent de qualsevol procés Node.js — si el backend estigués aturat, el backup s'executaria igualment.

---

## 8. Estudi de solucions d'infraestructura al mercat

### 8.1 Opcions d'allotjament analitzades

| Opció | Tipus | Control SO | Cost | Escalabilitat | Adequació |
|---|---|---|---|---|---|
| **VPS Hostinger** | VPS | ✅ Total (root) | Fixe i baix | ✅ Vertical senzill | ✅ |
| AWS EC2 | IaaS | ✅ Total | Variable (per ús) | ✅ Excel·lent | ⚠️ Cost impredictible |
| Google Cloud Compute | IaaS | ✅ Total | Variable | ✅ Excel·lent | ⚠️ Cost impredictible |
| DigitalOcean Droplet | VPS | ✅ Total | Fixe | ✅ Bo | ✅ Alternativa vàlida |
| Heroku / Railway | PaaS | ❌ Parcial | Variable | ✅ | ❌ Sense control de SO |
| Servidor físic propi | On-premise | ✅ Total | Alt (hardware) | ❌ | ❌ Manteniment físic |
| Shared Hosting | Hosting compartit | ❌ | Molt baix | ❌ | ❌ Sense root, sense Docker |

### 8.2 Comparativa AWS EC2 vs VPS Hostinger

Donat que el projecte va tenir una fase prèvia a AWS EC2, es fa una comparativa directa:

| Criteri | AWS EC2 | VPS Hostinger |
|---|---|---|
| Control del sistema operatiu | ✅ Total | ✅ Total |
| Cost mensual previsible | ❌ Variable | ✅ Fix |
| Persistència de dades per defecte | ❌ Requereix EBS configurat | ✅ Disc SSD persistent |
| Complexitat de configuració de xarxa | Alta (VPC, Security Groups, IGW) | Baixa (UFW, configuració directa) |
| Temps de posada en marxa | Mitjà | Ràpid |
| Suport tècnic | ⚠️ Cost addicional | ✅ Inclòs |
| Escalat automàtic | ✅ Auto Scaling | ❌ Manual |
| Experiència al projecte | ❌ Pèrdua de dades per volums | ✅ Estable |

**Lliçó apresa:** La instància AWS EC2 va patir una pèrdua de dades de PostgreSQL en un contenidor Docker perquè el volum no estava correctament configurat com a EBS persistent. Hostinger VPS amb disc SSD fix i PostgreSQL instal·lat nativament elimina completament aquest risc.

### 8.3 Panels d'administració de servidors

| Eina | Tipus | Docker | SSL | Cost | Adequació |
|---|---|---|---|---|---|
| **EasyPanel** | Panel modern | ✅ Natiu | ✅ | Gratuït (self-hosted) | ✅ |
| Portainer | Gestió Docker | ✅ Natiu | ⚠️ | Gratuït | ✅ Alternativa |
| cPanel | Hosting tradicional | ❌ | ✅ | Cost elevat | ❌ |
| Plesk | Hosting tradicional | ⚠️ | ✅ | Cost elevat | ❌ |
| Webmin | Administració Unix | ❌ | ✅ | Gratuït | ⚠️ Antiquat |
| Cockpit | Administració Linux modern | ⚠️ | ✅ | Gratuït | ✅ Alternativa |

---

## 9. Anàlisi comparativa i justificació de decisions

### 9.1 Taula resum de decisions tecnològiques

| Component | Tecnologia triada | Principal alternativa | Raó principal de l'elecció |
|---|---|---|---|
| Sistema operatiu | Ubuntu 24.04 LTS | Debian 12 | LTS amb suport fins 2029, ampla comunitat, paquets actuals |
| Contenidors | Docker + Docker Compose | LXC | Ecosistema, Docker Hub, Compose per a orquestació senzilla |
| Base de dades | PostgreSQL 16 natiu | MySQL / MongoDB | ACID complet, jsonb, relacions complexes, pg_dump |
| Backend | Node.js + Express | Django (Python) | Event loop per a WebSockets, mateix llenguatge que frontend |
| Temps real | Socket.io | WebSocket natiu | Reconnexió automàtica, rooms, fallback HTTP |
| Proxy invers | Nginx | Apache / Caddy | Rendiment, maduresa, integració Certbot |
| SSL | Let's Encrypt + Certbot | Certificat comercial | Gratuït, renovació automàtica, estàndard de la indústria |
| Gestió de processos | PM2 | systemd | Específic Node.js, reload sense downtime, logs integrats |
| Automatitzacions | n8n (Docker) | Zapier / scripts bash | Self-hosted, gratuït, interfície visual, dades pròpies |
| Seguretat activa | Monarx Agent | ClamAV / OSSEC | Integrat amb Hostinger, actualitzacions automàtiques |
| Autenticació | JWT + bcrypt | Sessions + MD5 | Stateless, escalable, bcrypt recomanat per OWASP |
| Allotjament | VPS Hostinger | AWS EC2 | Cost fix, disc persistent, control total, experiència prèvia |

### 9.2 Arquitectura resultant i justificació global

La combinació de **serveis natius** (PostgreSQL, Nginx, Node.js/PM2) per als components crítics de rendiment i **Docker** per als serveis auxiliars (n8n, EasyPanel) representa la decisió arquitectònica més important del projecte. Respon a un equilibri entre:

- **Rendiment:** Els components que accedeixen intensivament al disc (PostgreSQL) i que gestionen moltes connexions concurrents (Node.js) funcionen millor sense la capa de virtualització Docker.
- **Mantenibilitat:** Els serveis de tercers (n8n, EasyPanel) es mantenen en contenidors perquè s'actualitzen amb una sola comanda sense afectar el sistema operatiu base.
- **Fiabilitat:** Experiència directa que la instal·lació nativa de PostgreSQL és més robusta i segura que en Docker en un VPS de producció amb un sol node.

---

## 10. Conclusions

### 10.1 Tecnologies seleccionades i la seva relació amb el currículum ASIX

| Àrea del currículum ASIX | Tecnologies aplicades al projecte |
|---|---|
| Administració de sistemes Linux | Ubuntu 24.04, systemd, cron, SSH, gestió de serveis, pm2 |
| Virtualització i contenidors | Docker, Docker Compose, volums persistents, xarxes Docker |
| Bases de dades | PostgreSQL 16, esquema relacional, migracions, índexs, pg_dump |
| Serveis de xarxa | Nginx proxy invers, SSL/TLS, WebSockets, HTTP/HTTPS |
| Seguretat | JWT, bcrypt, Monarx, Certbot, UFW (pendent), fail2ban (pendent) |
| Alta disponibilitat | PM2 (reinici automàtic), backups diaris, rames Git de seguretat |
| Automatitzacions i scripting | n8n, node-cron, scripts bash, cron del sistema |

### 10.2 Viabilitat tècnica

Totes les tecnologies seleccionades són madures, open source o de llicència gratuïta, amb àmplia documentació i comunitat activa. El servidor actual (96GB de disc, 7.8GB de RAM, Ubuntu 24.04 LTS) té capacitat sobrada per al volum operatiu del projecte, amb un 89% de disc disponible i menys del 25% de RAM en ús.

### 10.3 Riscos tècnics identificats

| Risc | Probabilitat | Impacte | Mesura de mitigació |
|---|---|---|---|
| Caiguda del VPS | Baixa | Alt | Backup diari a Google Drive, procés de recuperació documentat |
| Expiració del certificat SSL | Molt baixa | Alt | Certbot amb cron cada 12h, renovació automàtica |
| Fallada del procés Node.js | Baixa | Alt | PM2 amb reinici automàtic en menys de 1 segon |
| Corrupció de la BD | Molt baixa | Crític | pg_dump diari, PostgreSQL natiu (sense risc de volums Docker) |
| Atac de força bruta SSH | Mitjana | Alt | fail2ban pendent de configurar, accés per clau SSH recomanat |
| Desbordament del disc | Molt baixa | Mitjà | Neteja automàtica de fotos cada 20 dies, 89% disc lliure |
| Vulnerabilitat en dependències npm | Mitjana | Mitjà | Actualitzacions periòdiques, `npm audit` en cada desplegament |

---

*Document elaborat com a part del Projecte Final del CFGS Administració de Sistemes Informàtics en Xarxa (ASIX).*  
*Maig 2026.*
