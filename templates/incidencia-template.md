# Plantilla — Reporte de Incidencia

> Usa esta plantilla para documentar cualquier incidencia en el sistema LocalXpress.  
> Copia el bloque, rellena los campos y guarda el archivo con el formato:  
> `INCIDENCIA-YYYYMMDD-descripcion-corta.md`

---

## INCIDENCIA-YYYYMMDD — Título breve

### Información general

| Campo | Valor |
|---|---|
| **ID** | INC-YYYYMMDD-001 |
| **Fecha de detección** | DD/MM/YYYY HH:MM (UTC+1) |
| **Fecha de resolución** | DD/MM/YYYY HH:MM (UTC+1) |
| **Duración del impacto** | X horas Y minutos |
| **Severidad** | 🔴 Crítica / 🟡 Media / 🟢 Baja |
| **Reportado por** | Nombre / Sistema automático |
| **Resuelto por** | Nombre |

---

### Descripción del problema

_Describe qué pasó, qué dejó de funcionar y cómo se detectó._

---

### Impacto

_Qué servicios o usuarios se vieron afectados y en qué medida._

- [ ] Backend LocalXpress APP (:3001)
- [ ] Backend LocalXpress APP-IND (:3002)
- [ ] Base de datos PostgreSQL
- [ ] Nginx / SSL
- [ ] n8n / automatizaciones
- [ ] Sistema de backup
- [ ] Sistema de imágenes
- [ ] EasyPanel

**Usuarios afectados:** Todos / Administradores / Comercios / Repartidores / Particulares

---

### Causa raíz

_Explica qué causó el problema._

---

### Pasos de resolución

_Documenta exactamente qué se hizo para resolver el problema, en orden._

```bash
# Comandos ejecutados durante la resolución
```

1. Paso 1
2. Paso 2
3. Paso 3

---

### Verificación post-resolución

_Cómo se comprobó que el sistema volvía a funcionar correctamente._

```bash
# Comandos de verificación
pm2 status
curl http://localhost:3001/api/health
systemctl status nginx postgresql
docker ps
```

---

### Medidas preventivas

_Qué se puede hacer para evitar que vuelva a ocurrir._

- [ ] Acción 1
- [ ] Acción 2

---

### Lecciones aprendidas

_Qué ha enseñado esta incidencia al equipo._

---

## Escala de severidad

| Severidad | Criterio | Tiempo de respuesta |
|---|---|---|
| 🔴 Crítica | Sistema completamente caído, datos en riesgo, todos los usuarios afectados | Inmediato |
| 🟡 Media | Funcionalidad degradada, algunos usuarios afectados, hay workaround | < 2 horas |
| 🟢 Baja | Problema menor, impacto mínimo, no afecta operativa principal | < 24 horas |
