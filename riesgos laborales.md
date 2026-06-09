# Estudio de Riesgos Laborales — LocalXpress

---

## Tabla de contenidos

1. [Introducción y objeto del documento](#1-introducción-y-objeto-del-documento)
2. [Descripción del puesto de trabajo](#2-descripción-del-puesto-de-trabajo)
3. [Marco legal aplicable](#3-marco-legal-aplicable)
4. [Identificación de riesgos laborales](#4-identificación-de-riesgos-laborales)
5. [Evaluación de riesgos](#5-evaluación-de-riesgos)
6. [Medidas preventivas aplicadas](#6-medidas-preventivas-aplicadas)
7. [Riesgos específicos del trabajo con servidores y CPD](#7-riesgos-específicos-del-trabajo-con-servidores-y-cpd)
8. [Riesgos psicosociales](#8-riesgos-psicosociales)
9. [Ergonomía y condiciones del puesto](#9-ergonomía-y-condiciones-del-puesto)
10. [Plan de actuación ante emergencias](#10-plan-de-actuación-ante-emergencias)
11. [Formación e información en prevención](#11-formación-e-información-en-prevención)
12. [Conclusiones](#12-conclusiones)

---

## 1. Introducción y objeto del documento

### 1.1 Objeto

El presente documento tiene como objeto identificar, evaluar y establecer medidas preventivas para los riesgos laborales asociados al desarrollo y mantenimiento del proyecto LocalXpress, ejecutado desde un puesto de trabajo de técnico en sistemas informáticos en red (ASIX).

El estudio cubre las actividades realizadas durante el desarrollo del proyecto:

- Trabajo continuado frente a pantalla (PVD — Pantalla de Visualización de Datos)
- Administración remota de servidores mediante SSH
- Intervención puntual en equipos informáticos (hardware, cableado)
- Trabajo en entornos de centro de proceso de datos (CPD) o sala de servidores cuando aplica
- Gestión de sistemas eléctricos asociados a la infraestructura informática

### 1.2 Alcance

Este plan de prevención aplica a:

- El técnico desarrollador y administrador de sistemas del proyecto
- El entorno físico de trabajo (puesto de trabajo con ordenador)
- Los accesos remotos a la infraestructura del servidor VPS
- Las intervenciones físicas ocasionales en equipos o instalaciones

### 1.3 Metodología de evaluación

Para la evaluación de riesgos se ha utilizado el método del **Instituto Nacional de Seguridad y Salud en el Trabajo (INSST)**, que combina:

- **Probabilidad** de que el daño ocurra (Baja / Media / Alta)
- **Severidad** del daño si ocurre (Ligeramente dañino / Dañino / Extremadamente dañino)
- **Nivel de riesgo** resultante (Trivial / Tolerable / Moderado / Importante / Intolerable)

---

## 2. Descripción del puesto de trabajo

### 2.1 Denominación del puesto

**Técnico en Administración de Sistemas Informáticos en Red (ASIX)**

### 2.2 Tareas principales del puesto

| Tarea | Frecuencia | Duración estimada |
|---|---|---|
| Trabajo frente a pantalla (desarrollo, administración) | Diaria | 6-8 horas |
| Administración remota de servidores (SSH) | Diaria | 2-4 horas |
| Gestión y configuración de servicios (Docker, PM2, PostgreSQL) | Diaria | 2-3 horas |
| Lectura y análisis de logs y documentación técnica | Diaria | 1-2 horas |
| Intervención física en equipos (conexión de cables, hardware) | Ocasional | 30-60 min |
| Trabajo en sala de servidores o CPD | Ocasional | 1-2 horas |
| Videoconferencias y reuniones técnicas | Semanal | 1-2 horas |

### 2.3 Equipos y herramientas utilizados

| Equipo / Herramienta | Uso |
|---|---|
| Ordenador de sobremesa o portátil | Desarrollo y administración |
| Monitor(es) | Pantalla de visualización de datos |
| Teclado y ratón | Entrada de datos y navegación |
| Auriculares / headset | Videoconferencias y reuniones |
| Servidor VPS remoto | Infraestructura del proyecto |
| Conexión a Internet (SSH, HTTPS) | Acceso remoto al servidor |
| Herramientas de mano (destornilladores, bridas) | Intervenciones físicas ocasionales |

### 2.4 Entorno de trabajo

El trabajo se desarrolla principalmente en:

- **Oficina / aula técnica:** puesto de trabajo fijo con mesa, silla y equipo informático
- **Acceso remoto:** desde el puesto habitual mediante SSH al servidor VPS
- **Sala de servidores / CPD (ocasional):** cuando se requiere intervención física en equipos

---

## 3. Marco legal aplicable

El presente estudio se elabora en cumplimiento de la normativa vigente en materia de prevención de riesgos laborales:

| Normativa | Contenido relevante |
|---|---|
| **Ley 31/1995 de Prevención de Riesgos Laborales (LPRL)** | Marco general de obligaciones del empresario y derechos del trabajador en materia de seguridad y salud |
| **RD 486/1997 — Lugares de trabajo** | Condiciones mínimas de seguridad y salud en los lugares de trabajo: temperatura, iluminación, ventilación, espacios |
| **RD 488/1997 — Pantallas de visualización de datos (PVD)** | Disposiciones mínimas de seguridad para trabajadores que usan PVD de forma habitual: distancia a pantalla, pausas, reconocimiento médico |
| **RD 485/1997 — Señalización de seguridad** | Señalización de riesgos, vías de evacuación y equipos de emergencia |
| **RD 614/2001 — Riesgo eléctrico** | Disposiciones mínimas para la protección de los trabajadores frente al riesgo eléctrico |
| **RD 374/2001 — Agentes químicos** | Aplicable en la medida en que se utilizan productos de limpieza de equipos |
| **NTP 232 — Pantallas de visualización** | Nota técnica del INSST sobre condiciones ergonómicas en puestos con PVD |
| **NTP 601 — Evaluación de las condiciones de trabajo** | Metodología del INSST para la evaluación de riesgos |
| **ISO 9241 — Ergonomía en sistemas de trabajo** | Norma internacional sobre ergonomía en el uso de pantallas y equipos informáticos |

---

## 4. Identificación de riesgos laborales

Se han identificado los siguientes grupos de riesgos asociados al puesto de trabajo de técnico ASIX:

### 4.1 Riesgos físicos

| ID | Riesgo | Actividad asociada |
|---|---|---|
| RF-01 | Fatiga visual por uso prolongado de PVD | Trabajo continuado frente a pantalla |
| RF-02 | Trastornos musculoesqueléticos (TME) por postura inadecuada | Trabajo sentado durante largas jornadas |
| RF-03 | Fatiga cervical y lumbar | Posición incorrecta del cuello y espalda |
| RF-04 | Tendinitis / síndrome del túnel carpiano | Uso intensivo del teclado y ratón |
| RF-05 | Contacto eléctrico directo o indirecto | Manipulación de equipos enchufados, intervención en CPD |
| RF-06 | Caídas al mismo nivel | Cables en el suelo, suelos resbaladizos en CPD |
| RF-07 | Golpes con equipos o mobiliario | Entorno reducido en sala de servidores |
| RF-08 | Quemaduras por componentes calientes | Manipulación de servidores en funcionamiento |
| RF-09 | Exposición a ruido | Ventiladores y sistemas de refrigeración en CPD |
| RF-10 | Exposición a campos electromagnéticos | Proximidad a equipos de red y servidores |

### 4.2 Riesgos ambientales

| ID | Riesgo | Actividad asociada |
|---|---|---|
| RA-01 | Temperatura inadecuada (exceso de frío en CPD) | Trabajo en sala de servidores con climatización intensa |
| RA-02 | Iluminación insuficiente o con deslumbramientos | Trabajo frente a pantalla con iluminación incorrecta |
| RA-03 | Polvo y partículas en equipos | Limpieza y mantenimiento de hardware |
| RA-04 | Incendio en instalaciones eléctricas | Sobrecarga eléctrica en sala de servidores |

### 4.3 Riesgos psicosociales

| ID | Riesgo | Actividad asociada |
|---|---|---|
| RP-01 | Estrés tecnológico (technostress) | Gestión de incidencias urgentes, sistemas caídos en producción |
| RP-02 | Fatiga mental por concentración prolongada | Depuración de errores complejos, análisis de logs |
| RP-03 | Aislamiento social | Trabajo en remoto o en solitario durante largas jornadas |
| RP-04 | Presión por disponibilidad continua | Guardia de sistemas, alertas de producción fuera del horario laboral |
| RP-05 | Síndrome de burnout | Acumulación de carga de trabajo técnica sin descanso adecuado |

### 4.4 Riesgos de seguridad informática con impacto laboral

| ID | Riesgo | Actividad asociada |
|---|---|---|
| RI-01 | Acceso no autorizado al servidor por credenciales débiles | Gestión de accesos SSH y contraseñas |
| RI-02 | Pérdida de datos por fallo de backup | Administración del sistema de backups |
| RI-03 | Exposición de datos sensibles por configuración incorrecta | Gestión de ficheros .env y variables de entorno |
| RI-04 | Responsabilidad legal por brecha de seguridad | Administración de datos de usuarios y pedidos |

---

## 5. Evaluación de riesgos

### 5.1 Matriz de evaluación

Metodología INSST:

| Severidad \ Probabilidad | Baja | Media | Alta |
|---|---|---|---|
| **Ligeramente dañino** | Trivial (T) | Tolerable (To) | Moderado (M) |
| **Dañino** | Tolerable (To) | Moderado (M) | Importante (I) |
| **Extremadamente dañino** | Moderado (M) | Importante (I) | Intolerable (In) |

### 5.2 Evaluación detallada de riesgos identificados

| ID | Riesgo | Probabilidad | Severidad | Nivel de riesgo | Acción requerida |
|---|---|---|---|---|---|
| RF-01 | Fatiga visual por PVD | Alta | Dañino | **Importante** | Acción correctora inmediata |
| RF-02 | Trastornos musculoesqueléticos | Alta | Dañino | **Importante** | Acción correctora inmediata |
| RF-03 | Fatiga cervical y lumbar | Alta | Dañino | **Importante** | Acción correctora inmediata |
| RF-04 | Tendinitis / síndrome túnel carpiano | Media | Dañino | **Moderado** | Acción correctora necesaria |
| RF-05 | Contacto eléctrico | Baja | Extremadamente dañino | **Moderado** | Acción correctora necesaria |
| RF-06 | Caídas al mismo nivel | Baja | Dañino | **Tolerable** | Mantener bajo control |
| RF-07 | Golpes con equipos | Baja | Ligeramente dañino | **Trivial** | No requiere acción específica |
| RF-08 | Quemaduras por componentes | Baja | Dañino | **Tolerable** | Mantener bajo control |
| RF-09 | Exposición a ruido en CPD | Media | Ligeramente dañino | **Tolerable** | Mantener bajo control |
| RF-10 | Campos electromagnéticos | Baja | Ligeramente dañino | **Trivial** | No requiere acción específica |
| RA-01 | Temperatura baja en CPD | Media | Dañino | **Moderado** | Acción correctora necesaria |
| RA-02 | Iluminación inadecuada | Media | Dañino | **Moderado** | Acción correctora necesaria |
| RA-03 | Polvo en equipos | Baja | Ligeramente dañino | **Trivial** | No requiere acción específica |
| RA-04 | Incendio eléctrico en CPD | Baja | Extremadamente dañino | **Moderado** | Acción correctora necesaria |
| RP-01 | Estrés tecnológico | Alta | Dañino | **Importante** | Acción correctora inmediata |
| RP-02 | Fatiga mental | Alta | Dañino | **Importante** | Acción correctora inmediata |
| RP-03 | Aislamiento social | Media | Dañino | **Moderado** | Acción correctora necesaria |
| RP-04 | Presión por disponibilidad | Media | Dañino | **Moderado** | Acción correctora necesaria |
| RP-05 | Síndrome de burnout | Media | Extremadamente dañino | **Importante** | Acción correctora inmediata |
| RI-01 | Acceso no autorizado al servidor | Media | Extremadamente dañino | **Importante** | Acción correctora inmediata |
| RI-02 | Pérdida de datos | Baja | Extremadamente dañino | **Moderado** | Acción correctora necesaria |
| RI-03 | Exposición de datos sensibles | Media | Extremadamente dañino | **Importante** | Acción correctora inmediata |
| RI-04 | Responsabilidad legal por brecha | Baja | Extremadamente dañino | **Moderado** | Acción correctora necesaria |

---

## 6. Medidas preventivas aplicadas

### 6.1 Medidas para riesgos físicos de PVD (RF-01, RF-02, RF-03, RF-04)

Estas medidas se aplican de acuerdo con el **RD 488/1997** sobre pantallas de visualización de datos:

**Pantalla y equipos:**
- La pantalla debe estar situada a una distancia mínima de 50-70 cm de los ojos
- La parte superior de la pantalla debe estar al nivel de los ojos o ligeramente por debajo
- Usar monitor de al menos 24 pulgadas con resolución Full HD o superior
- Activar el modo de reducción de luz azul (night mode) especialmente en horario nocturno
- Ajustar el brillo de la pantalla al nivel del entorno — nunca más brillante que el entorno circundante
- Usar protector de pantalla mate para evitar reflejos

**Teclado y ratón:**
- El teclado debe estar en una posición que permita tener los codos a 90° y las muñecas rectas
- Usar un ratón ergonómico adaptado al tamaño de la mano
- Considerar el uso de un teclado ergonómico dividido si hay síntomas de tensión en las muñecas
- Alternar entre teclado y ratón para reducir la carga repetitiva

**Pausas obligatorias (RD 488/1997):**

| Jornada | Pausa recomendada | Frecuencia |
|---|---|---|
| Jornada continua | 10-15 minutos | Cada 50-60 minutos de trabajo con PVD |
| Pausa visual | Mirar a un punto a más de 6 metros durante 20 segundos | Cada 20 minutos (regla 20-20-20) |
| Micropausa activa | Levantarse, estirar cuello, hombros y muñecas | Cada 45-60 minutos |

**Regla 20-20-20 para la fatiga visual:**
> Cada 20 minutos, mirar un objeto a 20 pies (6 metros) de distancia durante 20 segundos. Esto relaja los músculos ciliares del ojo y reduce la fatiga visual acumulada.

### 6.2 Medidas ergonómicas del puesto (RF-02, RF-03)

**Silla de trabajo:**
- Altura regulable de forma que los pies reposen planos en el suelo
- Respaldo con soporte lumbar ajustable
- Reposabrazos a la altura de los codos (90°)
- Profundidad del asiento que permita apoyar completamente los muslos

**Mesa de trabajo:**
- Altura entre 70-75 cm para trabajo sentado estándar
- Superficie suficiente para colocar monitor, teclado y ratón sin obstáculos
- Espacio libre bajo la mesa para mover las piernas libremente

**Postura correcta recomendada:**

```
        ✓ Pantalla a nivel de ojos o ligeramente por debajo
        ✓ Cuello recto, sin inclinar hacia adelante
        ✓ Hombros relajados, sin elevarlos
        ✓ Codos a 90°, pegados al cuerpo
        ✓ Muñecas rectas al teclear
        ✓ Espalda apoyada en el respaldo
        ✓ Pies planos en el suelo o en reposapiés
        ✓ Rodillas a 90°, no cruzar las piernas
```

### 6.3 Medidas para riesgo eléctrico (RF-05)

Aplicando el **RD 614/2001** sobre riesgo eléctrico:

- **Nunca** manipular equipos enchufados a la red eléctrica
- Apagar y desenchufar siempre antes de abrir carcasas de equipos
- Verificar el estado del cableado antes de cada intervención (sin pelados, roturas ni deterioros)
- Usar regletas con protección ante sobrecargas (no conectar demasiados equipos en la misma regleta)
- No trabajar con humedad en las manos cerca de equipos eléctricos
- En intervenciones en CPD, seguir el protocolo de **Bloqueo/Enclavamiento (LOTO — Lock Out Tag Out)**:
  - Identificar la fuente de energía
  - Desconectar la fuente
  - Bloquear con candado y etiquetar
  - Verificar que no hay energía residual antes de intervenir

### 6.4 Medidas para trabajo en CPD / sala de servidores (RF-06, RF-07, RF-08, RF-09, RA-01, RA-04)

- **Ropa adecuada:** llevar ropa de abrigo cuando se trabaja en CPD (temperatura habitual 18-22°C)
- **Calzado:** usar calzado cerrado y antideslizante, nunca sandalias en sala de servidores
- **Cables:** mantener los cables ordenados y sujetos con bridas — nunca en el suelo sin marcar
- **Protección auditiva:** usar tapones o auriculares de protección si el nivel de ruido supera los 80 dB (ventiladores de alta velocidad)
- **Extintores:** verificar que hay extintores de CO₂ (para equipos eléctricos) cerca de la entrada al CPD. Nunca usar extintores de agua en presencia de equipos eléctricos
- **Señalización:** respetar la señalización de riesgo eléctrico, acceso restringido y vías de evacuación
- **Trabajo en altura:** si se requiere acceder a servidores en racks altos, usar escaleras homologadas — nunca sillas ni cajas

### 6.5 Medidas para iluminación (RA-02)

Según el **RD 486/1997** y la norma **UNE-EN 12464-1**:

| Zona | Nivel mínimo recomendado |
|---|---|
| Puesto de trabajo con PVD | 300-500 lux |
| Sala de reuniones | 300-500 lux |
| CPD / sala de servidores | 200-300 lux |

- La iluminación no debe generar reflejos en la pantalla — situar el monitor perpendicular a las ventanas, nunca de frente ni de espaldas
- Usar iluminación indirecta o difusa, evitar fuentes de luz directa sobre la pantalla
- Regular las persianas o cortinas para controlar la luz natural
- Sustituir las lámparas fundidas de forma inmediata

---

## 7. Riesgos específicos del trabajo con servidores y CPD

### 7.1 Administración remota de servidores (SSH)

El trabajo con servidores en producción mediante SSH introduce riesgos técnicos con consecuencias laborales directas:

| Riesgo técnico | Consecuencia laboral | Medida preventiva |
|---|---|---|
| Ejecución de comandos destructivos por error (`rm -rf`, `DROP TABLE`) | Pérdida de datos, responsabilidad profesional, horas extra de recuperación | Siempre hacer backup antes de operaciones críticas. Crear rama Git de seguridad antes de cambios en producción |
| Acceso con credenciales de root sin necesidad | Superficie de ataque ampliada, mayor daño potencial ante error | Usar usuario con sudo (`localxpress-admin`) para tareas cotidianas. Root solo cuando sea imprescindible |
| Trabajar directamente en producción sin entorno de pruebas | Interrupción del servicio, impacto en usuarios reales | Probar cambios en entorno de desarrollo antes de aplicar en producción |
| Sesión SSH sin timeout | Sesión abierta y expuesta si se abandona el terminal | Configurar `ClientAliveInterval` y `ClientAliveCountMax` en SSH |
| Contraseñas débiles o reutilizadas | Acceso no autorizado al servidor | Usar autenticación por clave SSH (ed25519). Contraseñas largas y únicas |

### 7.2 Gestión de datos personales (RGPD)

El sistema LocalXpress gestiona datos de carácter personal (nombres de clientes, direcciones de entrega, imágenes de entregas). El técnico administrador tiene acceso directo a estos datos, lo que implica responsabilidades legales adicionales:

| Obligación | Medida aplicada |
|---|---|
| Minimización de datos | Solo se almacenan los datos estrictamente necesarios para la entrega |
| Acceso controlado | JWT con roles — cada usuario solo accede a sus propios datos |
| Seguridad de los datos | Cifrado en tránsito (HTTPS/TLS), contraseñas con bcrypt, ficheros .env no versionados |
| Derecho de supresión | Las imágenes de entrega se eliminan automáticamente a los 20 días |
| Integridad y confidencialidad | Backups cifrados, acceso SSH restringido, Monarx activo |

> El incumplimiento del RGPD puede conllevar sanciones de hasta **20 millones de euros** o el **4% de la facturación global** de la empresa. El administrador de sistemas tiene responsabilidad directa en la implementación técnica de las medidas de seguridad.

### 7.3 Protocolo de intervención en sala de servidores

Antes de cualquier intervención física en equipos:

```
1. PLANIFICACIÓN
   └── Informar al responsable de la intervención prevista
   └── Programar la intervención en horario de menor impacto
   └── Documentar qué se va a hacer y los pasos a seguir

2. PREPARACIÓN
   └── Hacer backup completo del sistema antes de intervenir
   └── Verificar que hay una persona de apoyo disponible
   └── Preparar los equipos de protección individual (EPI) necesarios

3. INTERVENCIÓN
   └── Aplicar protocolo LOTO si hay riesgo eléctrico
   └── Trabajar con calzado adecuado y ropa de abrigo en CPD
   └── Mantener el orden y los cables organizados
   └── Documentar cada paso realizado

4. VERIFICACIÓN POST-INTERVENCIÓN
   └── Comprobar que todos los servicios volvieron a funcionar correctamente
   └── Documentar el resultado de la intervención
   └── Informar al responsable del resultado
```

---

## 8. Riesgos psicosociales

### 8.1 Estrés tecnológico (Technostress)

El trabajo en administración de sistemas está asociado a situaciones de alta presión: sistemas caídos en producción, pérdidas de datos, incidencias de seguridad. Estas situaciones generan estrés agudo que, si es recurrente, puede derivar en problemas de salud.

**Factores de riesgo identificados en el proyecto:**
- Gestión de incidencias en producción (backend caído, PostgreSQL inaccesible)
- Presión por restaurar el servicio lo antes posible
- Trabajo solitario sin apoyo inmediato ante problemas técnicos complejos
- Aprendizaje continuo obligatorio ante tecnologías en constante evolución

**Medidas preventivas:**
- Documentar todos los procedimientos de recuperación ante fallos **antes** de que ocurran (no improvisar bajo presión)
- Mantener un **runbook** (libro de procedimientos) actualizado con los pasos exactos para cada escenario de fallo
- Establecer límites claros de horario — las incidencias no urgentes se atienden en horario laboral
- Practicar simulacros de recuperación en entorno de pruebas para ganar seguridad

### 8.2 Fatiga mental

La administración de sistemas implica tareas de alta concentración: análisis de logs, depuración de errores, lectura de configuraciones complejas. La fatiga mental acumulada reduce la capacidad de concentración y aumenta el riesgo de cometer errores graves.

**Medidas preventivas:**
- Alternar tareas de alta concentración con tareas administrativas más rutinarias
- Respetar las pausas activas cada 50-60 minutos
- No abordar cambios críticos en producción al final de la jornada cuando la fatiga es mayor
- Usar listas de verificación (checklists) para operaciones críticas — no confiar en la memoria

### 8.3 Presión por disponibilidad continua

La administración de sistemas puede generar la expectativa de disponibilidad 24/7. Esto tiene un impacto directo en la conciliación y la salud mental.

**Medidas preventivas:**
- Establecer y comunicar claramente el horario de disponibilidad
- Configurar alertas automáticas (monitoring) que solo notifiquen por incidencias críticas fuera del horario laboral
- Documentar procedimientos básicos para que otras personas puedan gestionar incidencias simples
- El sistema de backup automático y PM2 con reinicio automático reducen la necesidad de intervención manual urgente

---

## 9. Ergonomía y condiciones del puesto

### 9.1 Checklist de evaluación ergonómica del puesto

| Elemento | Condición correcta | ¿Cumple? |
|---|---|---|
| Altura de la silla | Pies planos en el suelo, rodillas a 90° | ✅ / ❌ |
| Altura de la mesa | Codos a 90° al teclear | ✅ / ❌ |
| Posición del monitor | A nivel de ojos, a 50-70 cm de distancia | ✅ / ❌ |
| Iluminación | 300-500 lux, sin reflejos en pantalla | ✅ / ❌ |
| Temperatura | 21-23°C en puesto de trabajo | ✅ / ❌ |
| Humedad relativa | 45-65% | ✅ / ❌ |
| Ruido ambiental | Por debajo de 55 dB en tareas de concentración | ✅ / ❌ |
| Espacio libre bajo la mesa | Suficiente para mover las piernas libremente | ✅ / ❌ |
| Cables ordenados | Sin cables en el suelo sin proteger | ✅ / ❌ |
| Reposabrazos | A la altura de los codos | ✅ / ❌ |

### 9.2 Condiciones ambientales según RD 486/1997

| Parámetro | Valor recomendado | Norma |
|---|---|---|
| Temperatura (trabajo sedentario) | 17-27°C | RD 486/1997 |
| Temperatura (trabajo ligero) | 14-25°C | RD 486/1997 |
| Humedad relativa | 30-70% | RD 486/1997 |
| Velocidad del aire | < 0.25 m/s (trabajo sedentario) | RD 486/1997 |
| Iluminación mínima (oficina) | 300 lux | RD 486/1997 |
| Nivel de ruido (concentración alta) | < 55 dB | NTP 503 |

### 9.3 Ejercicios preventivos recomendados

Para prevenir los TME más comunes en el puesto de trabajo de técnico informático:

**Cuello (cada 60 minutos):**
- Inclinar lentamente la cabeza hacia la derecha durante 10 segundos, repetir hacia la izquierda
- Girar suavemente la cabeza hacia cada lado hasta el límite cómodo

**Hombros (cada 60 minutos):**
- Elevar los hombros hacia las orejas, mantener 5 segundos, soltar
- Rotar los hombros hacia adelante y hacia atrás en círculos

**Muñecas y manos (cada 30 minutos):**
- Cerrar el puño con fuerza, abrir los dedos completamente — repetir 10 veces
- Rotar las muñecas en círculos en ambos sentidos
- Estirar la muñeca hacia atrás con la otra mano, mantener 10 segundos

**Espalda (cada 90 minutos):**
- Levantarse y caminar al menos 2 minutos
- Estirar los brazos hacia arriba mientras se está de pie

---

## 10. Plan de actuación ante emergencias

### 10.1 Emergencias en el puesto de trabajo

**Ante un incendio:**
1. Mantener la calma — activar la alarma de incendios
2. Llamar al 112
3. Evacuar por las vías de evacuación señalizadas — nunca usar el ascensor
4. No recoger pertenencias — la seguridad es prioritaria
5. Reunirse en el punto de encuentro establecido
6. No volver al edificio hasta que lo autoricen los bomberos

**Ante un accidente eléctrico:**
1. No tocar a la víctima si aún está en contacto con la corriente
2. Cortar el suministro eléctrico desde el cuadro general si es posible
3. Llamar al 112 inmediatamente
4. Aplicar primeros auxilios solo si la víctima ya no está en contacto con la corriente
5. No mover a la víctima salvo que haya peligro inmediato

**Ante un accidente con lesión:**
1. Llamar al 112
2. No mover al accidentado si hay sospecha de lesión en la columna
3. Mantener a la víctima tranquila y abrigada hasta la llegada de los servicios de emergencia
4. Avisar al responsable de prevención

### 10.2 Emergencias en sistemas informáticos

**Ante una caída del servidor en producción:**
1. Acceder al servidor por SSH para evaluar el estado
2. Consultar el runbook de recuperación (ver `OPERACION_MANTENIMIENTO.md`)
3. Si no hay acceso SSH, contactar con el soporte de Hostinger
4. Notificar al responsable técnico del proyecto
5. Documentar la incidencia en el registro de incidencias

**Ante una brecha de seguridad detectada:**
1. Aislar el sistema afectado inmediatamente (si es posible, sin cortar el servicio)
2. Cambiar todas las contraseñas de acceso (SSH, PostgreSQL, JWT_SECRET, n8n)
3. Revocar todos los tokens JWT activos (cambiar JWT_SECRET y reiniciar el backend)
4. Analizar los logs de acceso para determinar el alcance
5. Notificar a los afectados si se han comprometido datos personales (obligación RGPD — 72 horas)
6. Documentar el incidente y las medidas tomadas

### 10.3 Teléfonos de emergencia

| Servicio | Teléfono |
|---|---|
| Emergencias generales | 112 |
| Policía Nacional | 091 |
| Bomberos | 080 (Barcelona) |
| Ambulancias / Urgencias médicas | 061 |
| Soporte técnico Hostinger | Panel de control Hostinger |

---

## 11. Formación e información en prevención

### 11.1 Formación mínima recomendada para el técnico ASIX

| Formación | Contenido | Periodicidad |
|---|---|---|
| Prevención de riesgos laborales básica | Marco legal, riesgos generales, primeros auxilios básicos | Inicial + revisión cada 2 años |
| Ergonomía en puestos con PVD | Ajuste del puesto, pausas, ejercicios preventivos | Inicial |
| Riesgo eléctrico (operaciones en proximidad) | RD 614/2001, LOTO, EPI específicos | Antes de intervenciones en CPD |
| Seguridad informática y RGPD | Gestión de datos personales, responsabilidades legales | Anual |
| Actuación ante emergencias | Evacuación, primeros auxilios, uso de extintores | Inicial + simulacro anual |

### 11.2 Información que debe recibir el trabajador

Según el **artículo 18 de la LPRL**, el trabajador tiene derecho a recibir información sobre:

- Los riesgos para su seguridad y salud en el trabajo
- Las medidas y actividades de protección y prevención aplicables
- Las medidas de emergencia adoptadas
- Los resultados de la evaluación de riesgos

### 11.3 Vigilancia de la salud

El **artículo 22 de la LPRL** establece el derecho del trabajador a la vigilancia periódica de su estado de salud. Para el puesto de técnico con PVD, se recomienda:

- **Reconocimiento médico inicial** al incorporarse al puesto
- **Revisión visual** cada 2 años (o antes si aparecen síntomas)
- **Evaluación musculoesquelética** ante síntomas de TME
- **Evaluación psicosocial** si se detectan indicadores de estrés o burnout

---

## 12. Conclusiones

### 12.1 Resumen de riesgos principales

Los riesgos más significativos identificados para el puesto de técnico ASIX en el proyecto LocalXpress son:

| Riesgo | Nivel | Prioridad de actuación |
|---|---|---|
| Fatiga visual por PVD | Importante | 🔴 Inmediata |
| Trastornos musculoesqueléticos | Importante | 🔴 Inmediata |
| Estrés tecnológico | Importante | 🔴 Inmediata |
| Fatiga mental | Importante | 🔴 Inmediata |
| Burnout | Importante | 🔴 Inmediata |
| Acceso no autorizado al servidor | Importante | 🔴 Inmediata |
| Exposición de datos sensibles | Importante | 🔴 Inmediata |
| Contacto eléctrico | Moderado | 🟡 Necesaria |
| Temperatura inadecuada en CPD | Moderado | 🟡 Necesaria |
| Incendio eléctrico en CPD | Moderado | 🟡 Necesaria |

### 12.2 Medidas prioritarias implementadas en el proyecto

| Medida | Implementación en LocalXpress |
|---|---|
| Documentación de procedimientos de emergencia | `OPERACION_MANTENIMIENTO.md` con runbooks completos |
| Backups automáticos para reducir el estrés ante fallos | Cron diario a las 3:00h → Google Drive |
| Reinicio automático de procesos | PM2 con restart automático |
| Acceso al servidor con usuario no root | Usuario `localxpress-admin` con sudo |
| Autenticación por clave SSH | Clave ed25519 recomendada |
| Cifrado de datos en tránsito | HTTPS/TLS con Let's Encrypt |
| Control de acceso a datos sensibles | JWT + RBAC + ficheros .env no versionados |
| Monitorización activa de seguridad | Monarx Agent instalado y activo |

### 12.3 Mejoras pendientes de implementar

| Mejora | Riesgo que mitiga | Prioridad |
|---|---|---|
| Configurar fail2ban para protección SSH | RI-01 — Acceso no autorizado | 🔴 Alta |
| Configurar UFW para cerrar puertos no necesarios | RI-01 — Exposición de servicios | 🔴 Alta |
| Establecer política de descansos obligatorios documentada | RF-01, RF-02, RP-01 | 🟡 Media |
| Habilitar autenticación por clave SSH (deshabilitar contraseña) | RI-01 | 🔴 Alta |
| Restringir PostgreSQL a localhost | RI-03 — Exposición de datos | 🔴 Alta |

