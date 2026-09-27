# LINDDUN (privacidad)

Usar **solo** si hay PII, cuentas, RGPD, salud, o el usuario lo pide. Mismos DFD que STRIDE; otras categorías.

| | Categoría | Pregunta | Mitigación típica |
|--|-----------|----------|-------------------|
| **L** | Linking | ¿Se puede unir actividad anónima a una persona? | Separar identificadores, no reutilizar IDs públicos |
| **I** | Identifying | ¿El dato identifica a alguien (directa o indirectamente)? | Minimizar campos; seudonimizar analítica |
| **N** | Non-repudiation | ¿El sistema guarda pruebas que el usuario no puede impugnar de forma justa? | Retención limitada; propósito claro |
| **D** | Detecting | ¿Se puede observar que alguien usó el servicio? | Tráfico metadata, logs de acceso |
| **D** | Data disclosure | ¿PII sale a logs, terceros, backups, soporte? | Redacción en logs; DPAs; cifrado |
| **U** | Unawareness | ¿El usuario entiende qué se recoge y para qué? | Consentimiento / aviso real, no solo checkbox |
| **N** | Non-compliance | ¿Viola retención, base legal, derechos ARCO/RGPD? | Políticas de borrado, export, plazos |

IDs `P-01`, `P-02`… No duplicar una amenaza STRIDE **I** que ya cubra la misma fuga: enlazar (`ver T-0x`) o dejar solo LINDDUN si el daño es de privacidad, no de seguridad.
