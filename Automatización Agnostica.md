**versión agnóstica del playbook** —es decir, **aplicable a cualquier plataforma SOAR** (no solo Splunk SOAR). Esta versión usa una lógica modular que puede ser adaptar fácilmente en herramientas como:

* Splunk SOAR (Phantom)
* Cortex XSOAR (Palo Alto)
* IBM SOAR
* Microsoft Sentinel (Logic Apps)
* FortiSOAR
* TheHive/Cortex

---

## 🔁 **Playbook agnóstico – Detección y respuesta ante LOLBIN con descarga remota**

---

### 🔷 **Nombre:**

`Detección de LOLBIN con descarga de carga útil (T1105)`

### 🎯 **Objetivo:**

Detectar el uso de utilitarios del sistema (LOLBINs) como `certutil.exe`, `powershell.exe`, `curl.exe`, etc., usados para descargar archivos desde Internet, y activar una respuesta automatizada.

---

## 🔄 **Flujo del playbook**

### 1. **Inicio – Ingesta de evento**

* Fuente: EDR, SIEM (ej. Splunk, Sentinel), Sysmon, Wazuh, etc.
* Evento clave: `Process Creation` (ej. Event ID 4688)

---

### 2. **Condición inicial – Detección de LOLBIN con URL**

* Si el `process_name` está en la lista:

  * `certutil.exe`
  * `powershell.exe`
  * `curl.exe`
  * `wget.exe`
  * `bitsadmin.exe`
  * `mshta.exe`
* Y el `command_line` contiene `http`, `https` o `ftp`

➡️ **Continuar a la respuesta**
❌ Si no, **terminar ejecución**

---

### 3. **Extracción de indicadores**

* Extraer de `command_line`:

  * URL (utilizar regex o parser)
  * Posible archivo descargado
* Extraer:

  * Nombre de usuario
  * Hostname / IP del host
  * Hash del archivo (si existe)

---

### 4. **Análisis de reputación**

* Consultar reputación de:

  * URL en VirusTotal, URLScan, ThreatCloud
  * Hash en VirusTotal, Hybrid Analysis
* Marcar como malicioso si:

  * Score > umbral (por ejemplo, 3/70 en VirusTotal)
  * Coincidencia con IOC conocidos

---

### 5. **Respuesta automatizada**

#### 🖥️ Aislamiento del host:

* Con EDR (CrowdStrike, Defender, SentinelOne, etc.)

#### 🔒 Suspensión del usuario:

* Via Active Directory, Azure AD u otra solución IAM

#### 🔔 Notificación:

* A SOC vía correo, Slack, Teams, SIEM dashboard
* Incluir:

  * Proceso y comando
  * URL usada
  * Usuario afectado
  * Hash del archivo

#### 🎫 Ticket automático:

* Abrir incidente en sistema ITSM (ServiceNow, Jira, RTIR)

---

### 6. **Registro y documentación**

* Guardar artefactos:

  * Evento original
  * Indicadores extraídos
  * Resultado de reputación
  * Acciones tomadas
* Opcional: generar un PDF o entrada en Wiki interna

---

### 🔚 **Fin del playbook**

---

## 🧩 **Resumen de herramientas que puedes integrar**

| Función               | Herramientas posibles                        |
| --------------------- | -------------------------------------------- |
| Ingesta de logs       | Splunk, Sentinel, Wazuh, Elastic SIEM        |
| Reputación de IOCs    | VirusTotal, URLScan, IBM X-Force, AlienVault |
| Aislamiento de host   | CrowdStrike, Defender ATP, SentinelOne       |
| Suspensión de cuentas | Active Directory, Okta, Azure AD             |
| Notificaciones        | Email, Slack, Teams, Discord                 |
| Ticketing             | ServiceNow, Jira, RTIR                       |

---

