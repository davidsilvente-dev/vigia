# Privacidad, seguridad y cumplimiento

La videovigilancia municipal trata datos personales de alto riesgo. VIGÍA aborda el cumplimiento en la arquitectura, no solo en la documentación.

> Este documento describe medidas técnicas del producto. No sustituye a la evaluación de impacto (EIPD), al registro de actividades ni al criterio del delegado de protección de datos de cada organismo.

## Marco de referencia

- Reglamento General de Protección de Datos (RGPD) y LOPDGDD.
- Ley Orgánica 7/2021, de tratamiento de datos personales con fines policiales.
- Esquema Nacional de Seguridad (ENS).
- Reglamento Europeo de Inteligencia Artificial (Reglamento UE 2024/1689).

## Medidas por diseño

### 100 % local
Imágenes, vectores, modelos de IA, modelo de lenguaje y mapas funcionan en el servidor municipal. El uso de servicios en la nube está desactivado por esquema de configuración y solo se puede activar de forma expresa y auditada. La interfaz no carga fuentes ni librerías de CDN externos.

### Sin reconocimiento facial
El reconocimiento facial y cualquier identificación biométrica están **fuera del producto**. Las búsquedas se hacen por tipo de objeto, prenda, color, lugar y hora.

### Minimización y retención
- Imágenes y miniaturas: máximo **30 días**, con borrado automático.
- Excepción: **bloqueo legal** por diligencia, registrado y con fecha de fin.
- Resúmenes estadísticos agregados (sin imágenes ni identificadores) se conservan para análisis de tendencias.
- Los paquetes de soporte técnico se generan sin imágenes ni matrículas y con los registros enmascarados.

### Motivo obligatorio y auditoría
- Buscar una matrícula o una persona exige indicar el **motivo**.
- Cada acceso a datos personales queda en un registro de auditoría encadenado (cada entrada depende de la anterior) que permite detectar manipulaciones.
- Alertas por volumen anómalo de búsquedas por usuario.

### Control de acceso
| Rol | Puede |
|---|---|
| Operador | Ver directo, atender alertas, buscar escenas |
| Policía | Dossier de matrícula, clips, informes, normativa |
| Supervisor | Revisar incidencias, aprobar correcciones del chat, calibrar |
| Administrador técnico | Instalar y mantener el sistema **sin acceso a imágenes** |
| Auditor / DPD | Revisar accesos, retención y solicitudes de derechos (solo lectura) |

Además, permisos por cámara y por zona.

### Autenticación y sesiones
- TLS en todas las conexiones del navegador.
- Segundo factor, sesiones con caducidad absoluta, bloqueo por intentos fallidos.
- Sesión en cookie `HttpOnly`; nunca tokens en el almacenamiento del navegador ni en URLs.
- El vídeo se sirve mediante un proxy autenticado con enlaces firmados de corta duración.

### Cadena de custodia
Las evidencias (clips, fotos, informes) se registran con huella SHA-256 y firma. Cualquier alteración posterior es detectable.

### Gestión de secretos
- Ninguna credencial en el código, en ficheros de configuración, en la documentación ni en los registros.
- Almacén cifrado de secretos; la configuración solo contiene referencias.
- Escáner de secretos en cada commit, en la integración continua y al empaquetar.
- Las credenciales de las cámaras nunca se devuelven al navegador.

### Red
- Un único servicio expuesto a la red local, con TLS.
- El receptor de matrículas solo acepta conexiones de las IP de cámaras inventariadas.
- Servicios internos y de IA escuchando solo en `127.0.0.1` con token de servicio.

### Integraciones externas honestas
Cada integración externa (por ejemplo, la DGT) tiene tres estados explícitos: no configurada, configurada sin adaptador y activa. Nunca se simula una respuesta.

## Apoyo a la documentación del responsable

La descripción técnica de estas medidas (este documento y la especificación del producto) sirve de base para que cada organismo prepare con su DPD la EIPD y el registro de actividades de tratamiento.
