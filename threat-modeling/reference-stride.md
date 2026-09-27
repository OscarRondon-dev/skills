# STRIDE per element

Aplicar **por elemento e interacción**, no una lluvia de ideas suelta. Priorizar con la bug bar de la skill, no con DREAD salvo que el usuario lo pida.

## Categorías

| | Amenaza | Qué rompe | Mitigación típica (diseño) |
|--|---------|-----------|----------------------------|
| **S** | Spoofing | Identidad | Auth fuerte, MFA, identidad de servicio |
| **T** | Tampering | Integridad | TLS, firmas, ORM parametrizado, integridad de artefactos |
| **R** | Repudiation | No repudio | Audit log inmutable, correlación request-id, firmas |
| **I** | Information disclosure | Confidencialidad | Cifrado, minimización, least privilege, no secretos en cliente |
| **D** | Denial of service | Disponibilidad | Rate limit, cuotas, timeouts, backpressure |
| **E** | Elevation of privilege | Autorización | AuthZ en servidor, fail-closed, separación de roles |

## Qué preguntar por tipo de elemento

| Elemento | STRIDE aplicable | Preguntas |
|----------|------------------|-----------|
| Entidad externa (usuario, SaaS) | S, R | ¿Cómo se autentica? ¿Podemos probar quién hizo qué? |
| Proceso (API, worker, SPA) | S T R I D E | Las seis. SPA no es frontera de seguridad. |
| Almacén (BD, blob, cola, cache) | T, R, I, D | ¿Quién escribe? ¿Cifrado? ¿Backup? ¿Logs de acceso? |
| Flujo de datos | T, I, D | ¿TLS? ¿Datos en claro en logs? ¿Replay? |

Recorrer **cada cruce de límite de confianza**. Las amenazas intra-límite (mismo proceso de confianza) son de menor prioridad salvo que el proceso esté expuesto.

## Bug bar (priorizar)

1. ¿Es explotable sin cuenta o con cuenta de usuario normal?
2. ¿Afecta a muchos usuarios o a secretos/PII?
3. ¿La mitigación es de diseño (ahora) o de detección (ops)?

No listar OWASP Top 10 entero “por si acaso”: solo amenazas con un elemento del DFD.

## Salida por amenaza

`ID`, categoría STRIDE, elemento del DFD, una frase de amenaza, severidad, estado, mitigación **accionable** (qué control, dónde). Sin pasos de exploit.
