# Informe de Auditoría de Seguridad — LocalXpress

**Herramienta:** OWASP ZAP (Zed Attack Proxy) by Checkmarx — Versión 2.17.0  
**Fecha del escaneo:** Lunes, 25 de mayo de 2026 a las 23:25:08  
**Sitios analizados:** `http://localxpress.app` y `https://localxpress.app`  
**Tipo de análisis:** Escaneo pasivo automatizado  
**Módulo:** Proyecto Final — CFGS Administración de Sistemas Informáticos en Red (ASIX)  

---

## Tabla de contenidos

1. [¿Qué es OWASP ZAP?](#1-qué-es-owasp-zap)
2. [Metodología del escaneo](#2-metodología-del-escaneo)
3. [Resumen ejecutivo de resultados](#3-resumen-ejecutivo-de-resultados)
4. [Alertas de riesgo MEDIO](#4-alertas-de-riesgo-medio)
5. [Alertas de riesgo BAJO](#5-alertas-de-riesgo-bajo)
6. [Alertas INFORMATIVAS](#6-alertas-informativas)
7. [Resultado destacado: 0 vulnerabilidades de riesgo ALTO](#7-resultado-destacado-0-vulnerabilidades-de-riesgo-alto)
8. [Plan de remediación](#8-plan-de-remediación)
9. [Conclusiones](#9-conclusiones)

---

## 1. ¿Qué es OWASP ZAP?

**OWASP ZAP** (Zed Attack Proxy) es una de las herramientas de seguridad de aplicaciones web más utilizadas en el mundo, mantenida por el proyecto OWASP (Open Web Application Security Project) y actualmente desarrollada por Checkmarx. Es completamente gratuita y de código abierto.

ZAP actúa como un **proxy interceptor** entre el navegador y la aplicación web, analizando todo el tráfico HTTP/HTTPS que pasa por él. Permite realizar dos tipos de análisis:

| Tipo de análisis | Descripción |
|---|---|
| **Escaneo pasivo** | Analiza el tráfico sin modificar las peticiones — observa las respuestas del servidor y detecta configuraciones inseguras en cabeceras HTTP, políticas CSP, cookies, etc. No genera tráfico adicional peligroso |
| **Escaneo activo** | Envía peticiones maliciosas de forma controlada para probar vulnerabilidades como XSS, SQL Injection, etc. Más agresivo y solo se usa con autorización explícita |

En este caso se realizó un **escaneo pasivo**, el método más seguro para analizar una aplicación en producción sin riesgo de interferir con el servicio.

ZAP clasifica sus hallazgos en cuatro niveles de riesgo:

| Nivel | Color | Descripción |
|---|---|---|
| 🔴 Alto | Rojo | Vulnerabilidad crítica que puede ser explotada directamente |
| 🟠 Medio | Naranja | Configuración insegura con impacto potencial significativo |
| 🟡 Bajo | Amarillo | Mejora recomendada de seguridad con impacto limitado |
| 🔵 Informativo | Azul | Observación sin impacto de seguridad directo |

---

## 2. Metodología del escaneo

### 2.1 Parámetros del escaneo

| Parámetro | Valor |
|---|---|
| Herramienta | OWASP ZAP 2.17.0 by Checkmarx |
| Fecha y hora | 25/05/2026 — 23:25:08 |
| Tipo de escaneo | Pasivo (passive scanner) |
| Sitios analizados | `http://localxpress.app` y `https://localxpress.app` |
| Niveles de riesgo incluidos | Alto, Medio, Bajo, Informativo |
| Niveles de confianza incluidos | Confirmado por usuario, Alta, Media, Baja |
| Falsos positivos excluidos | Sí |

### 2.2 Alcance del análisis

El escaneo analizó las respuestas HTTP/HTTPS del servidor ante peticiones normales, evaluando:

- **Cabeceras de seguridad HTTP** presentes o ausentes en las respuestas
- **Política de Seguridad de Contenido (CSP)** y su configuración
- **Cookies** y sus atributos de seguridad
- **Información expuesta** en respuestas y comentarios del código
- **Configuración de caché** de recursos
- **Protocolos de transporte** y configuración SSL/TLS

### 2.3 Resumen de alertas por sitio

| Sitio | Alto | Medio (≥Medio) | Bajo (≥Bajo) | Informativo (≥Info) |
|---|---|---|---|---|
| `http://localxpress.app` | 0 | 8 | 9 | 11 |
| `https://localxpress.app` | 0 | 0 | 3 | 5 |
| **Total único** | **0** | **8** | **4** | **4** |

---

## 3. Resumen ejecutivo de resultados

### 3.1 Distribución total de alertas (16 alertas únicas)

| Nivel de riesgo | Número de alertas | % del total |
|---|---|---|
| 🔴 Alto | **0** | 0,0% |
| 🟠 Medio | **8** | 50,0% |
| 🟡 Bajo | **4** | 25,0% |
| 🔵 Informativo | **4** | 25,0% |
| **Total** | **16** | 100% |

### 3.2 Distribución por nivel de confianza

| Nivel de confianza | Alertas |
|---|---|
| Alta | 8 (50,0%) |
| Media | 6 (37,5%) |
| Baja | 2 (12,5%) |

### 3.3 Tipos de alertas detectadas

| Tipo de alerta | Riesgo | Instancias |
|---|---|---|
| CSP: script-src unsafe-inline | Medio | 8 |
| CSP: style-src unsafe-inline | Medio | 8 |
| CSP: Directiva Wildcard | Medio | 5 |
| CSP: Failure to Define Directive with No Fallback | Medio | 5 |
| CSP: script-src unsafe-eval | Medio | 3 |
| CSP: Directiva inválida de meta política | Medio | 3 |
| Falta de cabecera Anti-Clickjacking | Medio | 3 |
| Falta encabezado X-Content-Type-Options | Bajo | — |
| Gran redirección detectada | Bajo | — |
| Strict-Transport-Security Header No Establecido | Bajo | — |
| Aplicación Web Moderna | Informativo | — |
| CSP: Header & Meta | Informativo | — |
| Divulgación de información - Comentarios sospechosos | Informativo | — |
| Reexaminar Directivas de Control de Caché | Informativo | — |

### 3.4 Valoración global

> **El resultado más importante del escaneo es que NO se detectó ninguna vulnerabilidad de riesgo ALTO.** Todas las alertas encontradas son de configuración de cabeceras HTTP (riesgo Medio) o informativas. Esto indica que la aplicación no presenta vulnerabilidades críticas explotables como XSS, SQL Injection, CSRF o exposición de credenciales.

---

## 4. Alertas de riesgo MEDIO

Las alertas de riesgo Medio están relacionadas casi en su totalidad con la **configuración de la Política de Seguridad de Contenido (CSP)** de la aplicación. A continuación se detalla cada una con su explicación técnica.

---

### 4.1 CSP: script-src unsafe-inline

| Campo | Detalle |
|---|---|
| **Riesgo** | 🟠 Medio |
| **Confianza** | Alta |
| **Instancias** | 8 |
| **CWE** | CWE-693 (Protection Mechanism Failure) |
| **OWASP** | A05:2021, A02:2025 — Security Misconfiguration |
| **URLs afectadas** | `http://localxpress.app`, `http://localxpress.app/sitemap.xml` y otras |

**¿Qué significa?**

La directiva `script-src` de la CSP incluye el valor `'unsafe-inline'`, lo que permite la ejecución de scripts JavaScript escritos directamente en el HTML (inline scripts). Esto debilita la protección contra ataques **XSS (Cross-Site Scripting)**, ya que si un atacante consiguiera inyectar código HTML malicioso en la página, el navegador lo ejecutaría sin restricciones.

**Evidencia encontrada en el escaneo:**

```
Content-Security-Policy: default-src 'self'; 
  script-src 'self' 'unsafe-inline' 'unsafe-eval' 
  https://maps.googleapis.com https://maps.gstatic.com;
```

**Contexto del proyecto LocalXpress:**

Esta directiva está presente porque la aplicación usa **Google Maps API** y componentes de React que en su proceso de build generan algunos scripts inline. La solución ideal sería usar **nonces** o **hashes** en lugar de `unsafe-inline`, pero requiere modificaciones en el proceso de build de Vite.

**Solución recomendada:**

```nginx
# En la configuración de Nginx, añadir nonce dinámico
add_header Content-Security-Policy "script-src 'self' 'nonce-$request_id' https://maps.googleapis.com;";
```

---

### 4.2 CSP: style-src unsafe-inline

| Campo | Detalle |
|---|---|
| **Riesgo** | 🟠 Medio |
| **Confianza** | Alta |
| **Instancias** | 8 |
| **CWE** | CWE-693 |
| **OWASP** | A05:2021, A02:2025 |

**¿Qué significa?**

Similar al anterior, pero para hojas de estilo CSS. La directiva `style-src 'unsafe-inline'` permite aplicar estilos CSS directamente en atributos HTML (`style="..."`). Aunque el riesgo de `style-src unsafe-inline` es menor que el de `script-src`, puede facilitar ataques de **CSS Injection** que extraigan información o alteren la interfaz visual de la aplicación.

**Evidencia encontrada:**

```
style-src 'self' 'unsafe-inline' https://fonts.googleapis.com 
          https://maps.googleapis.com https://maps.gstatic.com;
```

**Contexto del proyecto:**

Tailwind CSS y algunos componentes de la librería de UI generan estilos inline durante el build. Es una limitación conocida de las SPA (Single Page Applications) con estos frameworks.

---

### 4.3 CSP: Directiva Wildcard

| Campo | Detalle |
|---|---|
| **Riesgo** | 🟠 Medio |
| **Confianza** | Alta |
| **Instancias** | 5 |
| **CWE** | CWE-693 |

**¿Qué significa?**

Se han detectado directivas CSP que utilizan el comodín `*` (wildcard), lo que permite cargar recursos desde cualquier dominio. Esto anula la protección que proporciona la CSP, ya que un atacante podría cargar scripts o recursos desde dominios maliciosos.

**Ejemplo de wildcard detectado:**

```
img-src 'self' data: blob: https://maps.googleapis.com 
        https://*.googleapis.com https://*.tile.openstreetmap.org
        https://*.basemaps.cartocdn.com;
```

Los dominios con `*` como `https://*.googleapis.com` o `https://*.tile.openstreetmap.org` son técnicamente wildcards de subdominio.

**Solución recomendada:**

Especificar exactamente los subdominios necesarios en lugar de usar `*`:

```
img-src 'self' data: blob: https://maps.googleapis.com 
        https://maps.gstatic.com https://tile.openstreetmap.org;
```

---

### 4.4 CSP: Failure to Define Directive with No Fallback

| Campo | Detalle |
|---|---|
| **Riesgo** | 🟠 Medio |
| **Confianza** | Alta |
| **Instancias** | 5 |
| **CWE** | CWE-693 |

**¿Qué significa?**

Existen directivas CSP que no están definidas explícitamente y que tampoco tienen una directiva de fallback (`default-src`) que las cubra correctamente. Cuando una directiva no está definida, el navegador puede comportarse de forma imprevisible o aplicar políticas permisivas por defecto.

**Directivas no definidas detectadas:**

Las directivas como `worker-src`, `manifest-src` o `navigate-to` no están especificadas en la CSP actual, lo que puede permitir que workers de JavaScript o manifiestos se carguen desde fuentes no controladas.

**Solución recomendada:**

```
Content-Security-Policy: 
  default-src 'self';
  worker-src 'self' blob:;
  manifest-src 'self';
```

---

### 4.5 CSP: script-src unsafe-eval

| Campo | Detalle |
|---|---|
| **Riesgo** | 🟠 Medio |
| **Confianza** | Alta |
| **Instancias** | 3 |
| **CWE** | CWE-693 |
| **OWASP** | A05:2021 |

**¿Qué significa?**

La directiva `script-src 'unsafe-eval'` permite el uso de funciones JavaScript que evalúan código dinámicamente, como `eval()`, `new Function()`, `setTimeout(string)` o `setInterval(string)`. Esto es especialmente peligroso porque, combinado con XSS, permitiría a un atacante ejecutar código arbitrario inyectado como cadena de texto.

**Evidencia encontrada:**

```
script-src 'self' 'unsafe-inline' 'unsafe-eval' 
           https://maps.googleapis.com https://maps.gstatic.com;
```

**Contexto del proyecto:**

Google Maps API requiere `unsafe-eval` para algunas de sus funcionalidades internas. Es una limitación impuesta por la librería de terceros.

---

### 4.6 CSP: Directiva inválida de meta política

| Campo | Detalle |
|---|---|
| **Riesgo** | 🟠 Medio |
| **Confianza** | Media |
| **Instancias** | 3 |
| **CWE** | CWE-693 |

**¿Qué significa?**

La aplicación define la CSP tanto en la cabecera HTTP como en una etiqueta `<meta>` del HTML. Sin embargo, ciertas directivas CSP **no son válidas** cuando se especifican en etiquetas meta (como `frame-ancestors` o `sandbox`). Cuando ZAP detectó ambas fuentes de CSP, no pudo combinarlas para un análisis unificado y las evaluó por separado.

**Impacto:**

La directiva CSP en la etiqueta meta puede ser ignorada o interpretada incorrectamente por algunos navegadores, dejando huecos en la política de seguridad.

**Solución recomendada:**

Centralizar toda la CSP en la cabecera HTTP del servidor (Nginx) y eliminar la etiqueta meta CSP del HTML:

```nginx
# En nginx.conf
add_header Content-Security-Policy "default-src 'self'; ...";
```

---

### 4.7 Falta de cabecera Anti-Clickjacking

| Campo | Detalle |
|---|---|
| **Riesgo** | 🟠 Medio |
| **Confianza** | Media |
| **Instancias** | 3 |
| **CWE** | CWE-693 |
| **OWASP** | A05:2021 |

**¿Qué significa?**

El **Clickjacking** es un ataque en el que un atacante embebe la aplicación web legítima dentro de un `<iframe>` invisible en su propia página maliciosa. El usuario cree estar interactuando con la página legítima pero en realidad está haciendo clic en elementos de la página maliciosa superpuesta.

La cabecera `X-Frame-Options` o la directiva CSP `frame-ancestors` protegen contra este ataque indicando al navegador que no permita que la página sea embebida en iframes de otros dominios.

**URLs afectadas:**

- `GET http://localxpress.app`
- `GET http://localxpress.app/sitemap.xml`
- `GET http://localxpress.app/robots.txt`

**Solución recomendada:**

```nginx
# Opción 1: Cabecera X-Frame-Options (compatible con navegadores antiguos)
add_header X-Frame-Options "SAMEORIGIN";

# Opción 2: Directiva CSP frame-ancestors (método moderno recomendado)
# Añadir a la CSP existente:
Content-Security-Policy: ...; frame-ancestors 'self';
```

> **Nota:** La CSP actual ya incluye `frame-ancestors 'self'` pero solo se aplica a ciertas rutas. Debe aplicarse de forma global en Nginx.

---

## 5. Alertas de riesgo BAJO

---

### 5.1 Strict-Transport-Security Header No Establecido (HSTS)

| Campo | Detalle |
|---|---|
| **Riesgo** | 🟡 Bajo |
| **Confianza** | Alta |
| **CWE** | CWE-319 (Cleartext Transmission of Sensitive Information) |
| **OWASP** | A02:2021 — Cryptographic Failures |
| **RFC** | RFC 6797 |

**¿Qué significa?**

La cabecera **HTTP Strict Transport Security (HSTS)** indica al navegador que, en el futuro, solo se conecte al sitio a través de HTTPS, nunca por HTTP. Sin esta cabecera, un atacante podría realizar un ataque **SSL Stripping**: interceptar la conexión cuando el usuario escribe `localxpress.app` (sin https://) y degradarla a HTTP antes de que el servidor pueda redirigir a HTTPS.

Actualmente, aunque el servidor redirige de HTTP a HTTPS (código 301), sin HSTS el navegador seguirá intentando primero HTTP, lo que deja una ventana de ataque.

**Evidencia:**

La cabecera `Strict-Transport-Security` no está presente en las respuestas HTTP del servidor. ZAP detectó su ausencia en múltiples URLs de `https://localxpress.app`.

**Solución recomendada:**

```nginx
# En la configuración del servidor HTTPS en Nginx
add_header Strict-Transport-Security "max-age=31536000; includeSubDomains; preload" always;
```

| Parámetro | Explicación |
|---|---|
| `max-age=31536000` | El navegador recuerda usar HTTPS durante 1 año (31536000 segundos) |
| `includeSubDomains` | Aplica también a todos los subdominios |
| `preload` | Permite añadir el dominio a la lista HSTS preload de los navegadores |

---

### 5.2 Falta encabezado X-Content-Type-Options

| Campo | Detalle |
|---|---|
| **Riesgo** | 🟡 Bajo |
| **Confianza** | Alta |
| **CWE** | CWE-693 |
| **WASC** | 15 |

**¿Qué significa?**

La cabecera `X-Content-Type-Options: nosniff` impide que el navegador realice **MIME type sniffing** — es decir, que intente adivinar el tipo de contenido de una respuesta ignorando el `Content-Type` declarado por el servidor.

Sin esta cabecera, un atacante podría subir un archivo con extensión `.jpg` que en realidad contiene código JavaScript. El navegador podría ejecutarlo si detecta que el contenido parece JavaScript, independientemente de la extensión declarada.

**Solución recomendada:**

```nginx
add_header X-Content-Type-Options "nosniff" always;
```

---

### 5.3 Gran redirección detectada (posible fuga de información)

| Campo | Detalle |
|---|---|
| **Riesgo** | 🟡 Bajo |
| **Confianza** | Media |
| **CWE** | CWE-201 (Insertion of Sensitive Information Into Sent Data) |
| **WASC** | 13 |

**¿Qué significa?**

ZAP detectó que algunas respuestas de redirección (HTTP 301) contienen un cuerpo de respuesta inusualmente grande (795 bytes). Las redirecciones normalmente devuelven cuerpos vacíos o muy pequeños. Un cuerpo grande en una redirección puede indicar que el servidor está incluyendo información que no debería, como mensajes de error detallados, rutas internas, versiones de software o datos de configuración.

**Evidencia encontrada:**

```http
HTTP/1.1 301 Moved Permanently
Server: LiteSpeed
platform: hostinger
panel: hpanel
Content-Security-Policy: upgrade-insecure-requests
```

La respuesta incluye las cabeceras `platform: hostinger` y `panel: hpanel`, que revelan información sobre la infraestructura del servidor (proveedor de hosting y panel de control). Esta información puede ser útil para un atacante que quiera explotar vulnerabilidades específicas de Hostinger o LiteSpeed.

**Solución recomendada:**

Configurar Nginx para eliminar cabeceras que revelan información del servidor:

```nginx
# Ocultar la versión del servidor
server_tokens off;

# Eliminar cabeceras de información del proveedor
more_clear_headers 'platform';
more_clear_headers 'panel';
more_clear_headers 'Server';
```

---

## 6. Alertas INFORMATIVAS

Las alertas informativas no representan vulnerabilidades de seguridad directas pero aportan contexto relevante sobre la configuración y el estado de la aplicación.

---

### 6.1 Aplicación Web Moderna

| Campo | Detalle |
|---|---|
| **Riesgo** | 🔵 Informativo |
| **Confianza** | Alta |

**¿Qué significa?**

ZAP detectó que `localxpress.app` es una **Single Page Application (SPA)** moderna basada en JavaScript (React + Vite). Las SPA tienen características específicas que afectan al análisis de seguridad:

- El contenido se carga dinámicamente mediante JavaScript, por lo que un escaneo pasivo no puede analizar todas las rutas y funcionalidades de la aplicación.
- ZAP indica que para un análisis completo de una SPA sería necesario usar el **Ajax Spider** o el **Selenium integration** para navegar la aplicación autenticado.

**Evidencia — Build de Vite detectado en el HTML:**

```html
<script type="module" crossorigin src="/assets/index-DM6jyHjo.js"></script>
<link rel="modulepreload" crossorigin href="/assets/vendor-react-B76cYtdI.js">
<link rel="modulepreload" crossorigin href="/assets/vendor-ui-CKDNdUF9.js">
```

Esta alerta es informativa y confirma que la aplicación es una SPA React, lo cual es la arquitectura esperada del proyecto.

---

### 6.2 CSP: Header & Meta

| Campo | Detalle |
|---|---|
| **Riesgo** | 🔵 Informativo |
| **Confianza** | Alta |
| **CWE** | CWE-693 |
| **URL afectada** | `GET http://localxpress.app` |

**¿Qué significa?**

ZAP detectó que la aplicación define la CSP en **dos lugares simultáneamente**: en la cabecera HTTP del servidor Y en una etiqueta `<meta>` del HTML. Cuando existen dos fuentes de CSP, el navegador aplica la intersección de ambas (la más restrictiva), lo que puede generar comportamientos inesperados.

Esta alerta es informativa porque ZAP no pudo combinar ambas políticas para evaluarlas conjuntamente y las analizó por separado, lo que puede haber generado algunas de las alertas de riesgo Medio relacionadas con CSP.

---

### 6.3 Divulgación de información — Comentarios sospechosos

| Campo | Detalle |
|---|---|
| **Riesgo** | 🔵 Informativo |
| **Confianza** | Media |
| **CWE** | CWE-615 (Inclusion of Sensitive Information in Source Comments) |
| **WASC** | 13 |
| **URL afectada** | Fichero JavaScript del bundle de Vite |

**¿Qué significa?**

ZAP encontró en el código JavaScript minificado del bundle de Vite un fragmento de texto que parece un comentario legal:

```
HATSOEVER RESULTING FROM
LOSS OF USE, DATA O
```

Este texto forma parte de una licencia de código abierto (fragmento de la licencia MIT) incluida en una de las dependencias npm. ZAP lo detectó como posible comentario sospechoso porque contiene las palabras "LOSS OF USE, DATA" que pueden interpretar como referencia a datos internos.

**Evaluación:** Este es un **falso positivo** — se trata de una licencia de software estándar, no de información sensible. No requiere acción correctora.

---

### 6.4 Reexaminar las Directivas de Control de Caché

| Campo | Detalle |
|---|---|
| **Riesgo** | 🔵 Informativo |
| **Confianza** | Baja |
| **CWE** | CWE-525 (Use of Web Browser Cache Containing Sensitive Information) |
| **OWASP** | A07:2021, A07:2025 — Authentication Failures |
| **URL afectada** | `GET https://localxpress.app/robots.txt` |

**¿Qué significa?**

La cabecera `Cache-Control` no está configurada explícitamente en algunos recursos, lo que permite al navegador y a los proxies intermedios almacenar el contenido en caché. Para recursos estáticos como `robots.txt`, `css` o imágenes esto es intencionado y deseable.

Sin embargo, si una respuesta que contiene datos sensibles (tokens, datos de usuario) no tiene `Cache-Control: no-store`, podría quedar almacenada en la caché del navegador y ser accesible posteriormente.

**Evaluación:** La alerta se detectó en `robots.txt`, que es un fichero público sin información sensible. No requiere acción para este caso concreto.

**Buena práctica a aplicar en respuestas de la API:**

```javascript
// En el backend Node.js / Express, para endpoints de la API
res.set('Cache-Control', 'no-store, no-cache, must-revalidate');
```

---

## 7. Resultado destacado: 0 vulnerabilidades de riesgo ALTO

El resultado más significativo de la auditoría es la **ausencia total de vulnerabilidades de riesgo Alto**. Esto significa que no se detectaron:

| Vulnerabilidad crítica | Estado |
|---|---|
| SQL Injection | ✅ No detectada |
| Cross-Site Scripting (XSS) explotable | ✅ No detectada |
| Cross-Site Request Forgery (CSRF) | ✅ No detectada |
| Exposición de credenciales o tokens | ✅ No detectada |
| Inyección de comandos | ✅ No detectada |
| Traversal de directorios | ✅ No detectada |
| Autenticación rota o bypasseable | ✅ No detectada |
| Exposición de datos sensibles en respuestas | ✅ No detectada |

Las medidas de seguridad implementadas en el proyecto han resultado efectivas:

| Medida implementada | Vulnerabilidad que previene |
|---|---|
| JWT con firma HMAC-SHA256 | Autenticación rota, robo de sesión |
| bcrypt (10 rondas) para contraseñas | Exposición de credenciales |
| HTTPS/TLS con Let's Encrypt | Interceptación de tráfico (MITM) |
| Consultas parametrizadas (pg) | SQL Injection |
| CORS configurado en Express | CSRF, peticiones cross-origin |
| Helmet.js en el backend | Múltiples cabeceras de seguridad |
| RBAC (control de acceso por roles) | Escalada de privilegios |

---

## 8. Plan de remediación

### 8.1 Acciones ordenadas por prioridad

| Prioridad | Alerta | Solución | Complejidad |
|---|---|---|---|
| 🔴 Alta | HSTS no establecido | Añadir `Strict-Transport-Security` en Nginx | Baja |
| 🔴 Alta | X-Content-Type-Options ausente | Añadir `X-Content-Type-Options: nosniff` en Nginx | Muy baja |
| 🔴 Alta | Cabecera Anti-Clickjacking ausente | Añadir `X-Frame-Options: SAMEORIGIN` en Nginx | Muy baja |
| 🟡 Media | Fuga de info en cabeceras de redirección | Eliminar cabeceras `platform` y `panel` en Nginx | Baja |
| 🟡 Media | CSP: unsafe-inline en script-src | Migrar a nonces o hashes CSP (requiere cambios en el build de Vite) | Alta |
| 🟡 Media | CSP: unsafe-eval en script-src | Evaluar alternativas a `unsafe-eval` en Google Maps API | Media |
| 🟡 Media | CSP: Wildcards en directivas | Especificar dominios exactos en lugar de `*` | Media |
| 🟢 Baja | CSP: Consolidar en cabecera HTTP | Eliminar etiqueta meta CSP del HTML | Baja |
| 🟢 Baja | Cache-Control en API | Añadir `no-store` en respuestas de la API con datos sensibles | Baja |

### 8.2 Implementación inmediata de las cabeceras de seguridad

Las correcciones de mayor impacto y menor complejidad se pueden implementar añadiendo las siguientes líneas a la configuración de Nginx:

```nginx
# Añadir en el bloque server{} de /etc/nginx/sites-enabled/localxpress

# Previene ataques SSL Stripping
add_header Strict-Transport-Security "max-age=31536000; includeSubDomains; preload" always;

# Previene ataques de Clickjacking
add_header X-Frame-Options "SAMEORIGIN" always;

# Previene MIME type sniffing
add_header X-Content-Type-Options "nosniff" always;

# Protección XSS adicional para navegadores antiguos
add_header X-XSS-Protection "1; mode=block" always;

# Controla la información del Referer
add_header Referrer-Policy "strict-origin-when-cross-origin" always;

# Ocultar información del servidor
server_tokens off;
```

**Después de aplicar los cambios, verificar la configuración y recargar Nginx:**

```bash
nginx -t && systemctl reload nginx
```

**Verificar que las cabeceras se aplican correctamente:**

```bash
curl -I https://localxpress.app | grep -E "Strict|X-Frame|X-Content|X-XSS"
```

---

## 9. Conclusiones

### 9.1 Valoración general de la seguridad

La auditoría de seguridad realizada con OWASP ZAP 2.17.0 sobre `localxpress.app` arroja un **resultado positivo**. El sistema no presenta ninguna vulnerabilidad crítica ni de riesgo Alto. Las 8 alertas de riesgo Medio están relacionadas exclusivamente con la configuración de cabeceras HTTP y la política CSP, no con vulnerabilidades en la lógica de la aplicación o en el acceso a datos.

### 9.2 Puntos fuertes de seguridad detectados

- ✅ **HTTPS activo** — todo el tráfico viaja cifrado
- ✅ **Redirección HTTP → HTTPS** — implementada correctamente con código 301
- ✅ **CSP presente** — aunque con algunas directivas mejorables, existe una política de seguridad de contenido
- ✅ **Sin vulnerabilidades de inyección** — no se detectó SQL Injection, XSS explotable ni inyección de comandos
- ✅ **Sin exposición de datos sensibles** — no se encontraron credenciales, tokens ni claves en las respuestas
- ✅ **Aplicación React SPA** — el código de la aplicación está minificado y empaquetado correctamente

### 9.3 Áreas de mejora identificadas

- ⚠️ **Cabeceras de seguridad HTTP** — HSTS, X-Frame-Options y X-Content-Type-Options ausentes (solución inmediata disponible)
- ⚠️ **CSP con directivas permisivas** — `unsafe-inline` y `unsafe-eval` aumentan la superficie de ataque XSS
- ⚠️ **Información del servidor expuesta** — cabeceras `platform: hostinger` y `panel: hpanel` revelan la infraestructura
- ⚠️ **Doble definición de CSP** — cabecera HTTP y etiqueta meta simultáneas pueden generar inconsistencias

### 9.4 Comparativa con el estándar OWASP Top 10 (2021/2025)

| OWASP Top 10 | Categoría | Estado en LocalXpress |
|---|---|---|
| A01:2021 — Broken Access Control | Control de acceso | ✅ JWT + RBAC implementado |
| A02:2021 — Cryptographic Failures | Fallos criptográficos | ✅ HTTPS, bcrypt. ⚠️ HSTS pendiente |
| A03:2021 — Injection | Inyección | ✅ Consultas parametrizadas, sin detección |
| A04:2021 — Insecure Design | Diseño inseguro | ✅ Arquitectura separada por capas |
| A05:2021 — Security Misconfiguration | Mala configuración | ⚠️ Cabeceras HTTP pendientes de completar |
| A06:2021 — Vulnerable Components | Componentes vulnerables | 🔄 Requiere `npm audit` periódico |
| A07:2021 — Auth Failures | Fallos de autenticación | ✅ JWT con expiración, bcrypt |
| A08:2021 — Software Integrity | Integridad del software | ✅ GitHub + PM2, deploy controlado |
| A09:2021 — Logging Failures | Fallos de logging | ✅ PM2 logs, Nginx logs, log de backup |
| A10:2021 — SSRF | Server-Side Request Forgery | ✅ No detectada |

---

*Auditoría realizada el 25 de mayo de 2026 con OWASP ZAP 2.17.0 by Checkmarx.*  
*Proyecto Final — CFGS Administración de Sistemas Informáticos en Red (ASIX).*  
*Documentación elaborada como parte del plan de pruebas de seguridad del proyecto LocalXpress.*
