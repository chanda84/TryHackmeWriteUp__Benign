# TryHackmeWriteUp__Benign

# 🕵️‍♂️ Investigando Actividad Sospechosa con LOLBIN – certutil.exe

## 📘 Escenario: Identificación e investigación de un host comprometido

Durante una operación de monitoreo de seguridad, un sistema IDS del cliente detectó la posible ejecución de un proceso malicioso en un equipo del departamento de Recursos Humanos. La alerta apuntaba al uso de herramientas del sistema para establecer persistencia y/o descargar cargas útiles desde Internet. Debido a limitaciones de recursos, se extrajeron e ingirieron únicamente los eventos de creación de procesos (Event ID 4688) en Splunk, usando el índice `win_eventlogs`.

### 🧭 Estructura de red del cliente

La red está segmentada lógicamente en tres áreas:

- **TI**: Jaime, Menos Mal, Katrina  
- **Recursos Humanos (RR.HH.)**: Haroon, Chris, Diana  
- **Marketing**: Campana, Amelia, Deepak

Esta segmentación ayudó a enfocar el análisis sobre la unidad de RR.HH., donde se originó la alerta.

---

## 🔍 Objetivo del análisis

Identificar qué usuario ejecutó una utilidad legítima de Windows (LOLBIN) con el fin de descargar una posible carga útil desde un sitio externo, y comprender el contexto y propósito del proceso.

---

## 🧠 Preguntas clave del análisis

1. ¿Qué proceso del sistema (LOLBIN) fue utilizado para descargar la carga útil?
2. ¿Qué usuario lo ejecutó y en qué host?
3. ¿Desde qué sitio externo se realizó la descarga?
4. ¿El comportamiento coincide con técnicas de persistencia o evasión?
5. ¿Qué acciones automáticas pueden aplicarse para prevenir futuros eventos similares?

> ❗ Las respuestas a estas preguntas están documentadas en el análisis técnico, el cual se encuentra en la carpeta del repositorio o en el documento PDF asociado.

---

## 🔧 Automatización y Detección

Para mitigar y responder ante eventos similares en el futuro, se desarrollaron e implementaron las siguientes acciones automatizadas:

### 🛡️ Detección en Splunk

- Alertas en tiempo real ante ejecuciones de `certutil.exe`, `powershell.exe`, `bitsadmin.exe`, `curl.exe`, etc., con presencia de URLs (`http`, `https`).
- Dashboards de monitoreo de uso de LOLBINs por usuario, host y frecuencia.
- Filtros de búsqueda por patrones de descarga sospechosa (MITRE ATT&CK T1105).

### 🤖 Automatización con SOAR (agnóstica)

Se diseñó un **playbook automatizado** para cualquier plataforma SOAR con los siguientes pasos:

1. Detección del proceso y análisis del comando ejecutado.
2. Extracción de indicadores (URL, hash, usuario, host).
3. Análisis de reputación en VirusTotal y otras fuentes.
4. Aislamiento automático del host comprometido (EDR).
5. Suspensión de la cuenta del usuario afectado (AD/Azure AD).
6. Notificación inmediata al SOC vía email o mensajería.
7. Creación automática de un ticket de incidente.
8. Registro y documentación de evidencia.

---

## 📁 Estructura del repositorio
.
├── README.md # Este archivo
├── analysis/ # Archivos de eventos analizados (JSON/CSV)
├── diagrams/ # Diagramas del flujo de análisis o detección
├── playbook/ # Playbook agnóstico en JSON/YAML para SOAR
├── report/ # Análisis completo en PDF (sin respuestas públicas)


---

## 📌 Referencias

- MITRE ATT&CK: [T1105 - Ingress Tool Transfer](https://attack.mitre.org/techniques/T1105/)
- Splunk Use Case Library: [LOLBIN Detection](https://lantern.splunk.com)
- VirusTotal, URLScan.io, Hybrid Analysis

---

## 👨‍💻 Autor

**Andrés Valdivieso Pinilla**  
🔗 [LinkedIn](https://www.linkedin.com/in/andres-valdivieso-pinilla)  
📁 [GitHub](https://github.com/chanda84)

---

> Este proyecto forma parte de mis ejercicios de análisis de eventos reales en entornos segmentados, enfocados en Blue Team y respuesta automatizada.

