# VIGÍA para administraciones públicas

Un ayuntamiento no compra solo funciones: compra la tranquilidad de que el sistema cumple. Este documento resume qué exige cada norma al producto y cómo lo resuelve VIGÍA.

> VIGÍA aporta medios técnicos. La base jurídica, la categorización y la decisión final corresponden al responsable del tratamiento y a su delegado de protección de datos.

Estados: **Disponible** (funciona hoy) · **Preparado** (se activa al configurarlo) · **Hoja de ruta** (siguiente desarrollo).

## Matriz de cumplimiento

| Norma | Qué exige al sistema | Cómo lo cubre VIGÍA | Estado |
|---|---|---|---|
| **RGPD** | Minimización, plazo, seguridad (art. 32), privacidad por defecto (art. 25), EIPD por observación sistemática de zona pública (art. 35.3.c) | Tope de 30 días en el esquema; módulos sensibles apagados por defecto y bloqueados sin EIPD; auditoría; cifrado | Disponible |
| **LOPDGDD, art. 22** | Supresión en un mes salvo para acreditar hechos; cartel informativo | Borrado automático y bloqueo legal por diligencia | Disponible |
| | | Generador del cartel | Hoja de ruta |
| **LO 7/2021** | Fines policiales: registro de consultas y comunicaciones | Auditoría con motivo y nº de diligencia en búsquedas de matrícula o persona; rol de Policía | Disponible |
| **LO 4/1997 y RD 596/1999** | Autorización previa de cámaras policiales fijas en vía pública | Propósito declarado por cámara | Disponible |
| | | Campo de autorización por cámara que la excluya de usos policiales si falta | Hoja de ruta |
| **ENS (RD 311/2022)** | Medidas según categoría; auditoría periódica | Diseño para categoría MEDIA | Disponible |
| | | Declaración de aplicabilidad medida a medida (Anexo II) | Hoja de ruta |
| **Reglamento Europeo de IA** | Prohibida la identificación biométrica remota en tiempo real con fines policiales (salvo excepciones); transparencia; formación | Ningún modelo de rostro (lo comprueba un test); seguimiento y dossier tratados por precaución como alto riesgo: registros, supervisión humana y nada automático; el chat indica que el texto lo redacta una IA | Disponible |
| **Guía de videovigilancia de la AEPD** | Cartel, supresión en un mes salvo incidente, EIPD | Referencia del diseño de conservación | Disponible |

## Derechos de las personas — Hoja de ruta

- Registro de cada solicitud con fecha límite a un mes y avisos a 7 y 2 días.
- Acceso: búsqueda asistida por fecha, hora y cámara, con terceros enmascarados al exportar.
- Supresión salvo bloqueo legal o limitación por LO 7/2021, con el motivo de denegación registrado.
- Las tablas de solicitudes y bloqueos legales ya existen; los paneles del auditor-DPD llegan en la fase de producto.

## Documentación que se mantiene sola — Hoja de ruta

- Borradores del registro de actividades, del anexo técnico de la EIPD y del cartel, generados desde la configuración y el inventario real.
- Marcados como desactualizados cuando cambia el inventario.
- Ya disponible: sin EIPD registrada, la configuración no permite activar la búsqueda de personas ni los atributos sensibles.

## Roles

| Rol | Puede |
|---|---|
| Operador | Ver directo, atender alertas, buscar escenas |
| Policía | Dossier de matrícula, clips, informes, normativa, consultas DGT |
| Supervisor | Revisar incidencias, aprobar correcciones del chat, calibrar, bloqueos legales |
| Administrador técnico | Instalar y mantener el sistema **sin acceso a imágenes** |
| Auditor / DPD | Revisar accesos, retención y solicitudes (solo lectura) |

## Exportable a cualquier organismo

- Los datos de cada cliente (nombre, ordenanzas, correos, red, coordenadas) viven en su configuración, nunca en el código. Una prueba automática arranca el sistema con la plantilla vacía y busca restos de cualquier cliente.
- Marca blanca: logotipo, colores y textos de cada organismo.
- Asistente de primer arranque e importación del inventario de cámaras.
- Una instalación por organismo (no es un servicio compartido en la nube). Para mancomunidades o Diputaciones: una instalación por municipio, con conexión a Red SARA centralizada si procede.

## Contratación

- Licencia, implantación y acompañamiento mensual, separados y medibles.
- Apoyo con la memoria justificativa y el pliego técnico.
- Instalación en un equipo municipal: sin datos en manos de terceros ni cuotas en la nube.
