# Configuración local y secrets

Convenciones acordadas para entorno de desarrollo. **Sin valores reales** — solo nombres de archivos, plantillas y gitignore.

## Archivos de config local

| Archivo / patrón | Propósito | ¿En git? |
|------------------|-----------|----------|
| {{LOCAL_CONFIG_FILES}} | Settings locales del runtime / app | **No** (gitignored) |
| {{LOCAL_CONFIG_EXAMPLE}} | Plantilla sin secrets para onboarding | **Sí** (si el equipo la usa) |

## Reglas

- Nunca commitear secrets, connection strings ni API keys.
- Los agentes **no inventan** valores — solo referencian nombres de variables acordadas.
- Si falta una variable requerida en local, fallar explícitamente en arranque (preferible a defaults peligrosos).

## Relación con `verify`

El cinturón local (`verify`) **no sustituye** validar config de deploy. Solo asegura calidad de código en el clone.
