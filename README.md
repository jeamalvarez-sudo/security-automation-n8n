# Security Automation con n8n

Flujo de automatización tipo SOAR (Security Orchestration, Automation and Response) construido con [n8n](https://n8n.io) para enriquecer y triage automático de IPs sospechosas.

## ¿Qué hace?

1. **Webhook** recibe una alerta con una IP sospechosa (simulando una fuente como Wazuh, Datadog o cualquier SIEM).
2. **HTTP Request** consulta la IP contra la API de [AbuseIPDB](https://www.abuseipdb.com/) para obtener su score de riesgo, ISP, país y reportes históricos.
3. **IF** evalúa el `abuseConfidenceScore` devuelto y decide si la alerta amerita escalar.
4. **Gmail** notifica automáticamente por correo cuando el score supera el umbral definido, incluyendo los datos clave de la IP.

```
Webhook → HTTP Request (AbuseIPDB) → If (score > umbral) → Gmail (notificación)
```

## Por qué lo hice

Este proyecto nace de dos cosas: mi experiencia previa en DevOps/SRE construyendo automatizaciones de alertas (un flujo similar con n8n + Claude API para respuesta a incidentes de on-call), y la recomendación de enfocarme en mostrar cómo la automatización e IA pueden mejorar procesos de seguridad — de cara a un proceso de selección para un rol de ciberseguridad semi-senior.

Es un primer paso deliberadamente simple: la meta es demostrar el patrón completo (recibir → enriquecer → decidir → notificar) antes de sofisticarlo.

## Stack

- **n8n** (self-hosted, local) — orquestación del flujo
- **AbuseIPDB API** — enriquecimiento de IPs
- **Gmail API (OAuth2)** — notificaciones

## Próximos pasos

- Reemplazar la lógica de umbral fijo por clasificación con un modelo de IA (Claude API), que además redacte un resumen contextual de la alerta.
- Agregar un nodo de logging a base de datos (Postgres/Elasticsearch) para mantener historial de alertas.
- Sustituir el trigger de prueba (`curl`) por una integración real con una fuente de alertas (Wazuh, Datadog, etc.).
- Enrutar según severidad: bajo riesgo → solo log; alto riesgo → notificación + ticket.

## Archivo

`security-automation-n8n.json` — exportación del workflow, importable directamente en cualquier instancia de n8n (Workflows → Import from File). No incluye credenciales; hay que configurar tu propia API key de AbuseIPDB y OAuth de Gmail al importarlo.
