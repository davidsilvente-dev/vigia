# Lecciones aprendidas: de la v1 a la v2

La primera versión de VIGÍA demostró que las funciones tenían valor real. También demostró que un sistema hecho a base de añadir piezas no se puede operar ni exportar. La v2 se reescribió desde cero y cada regla de diseño nace de un fallo medido.

| Lo que pasó en la v1 | Cifra | Regla en la v2 |
|---|---|---|
| El OCR devolvía «ocupado» y el sistema lo guardaba como «sin matrícula» | **84 %** de las lecturas perdidas | Contratos con tres estados: ocupado, vacío y fallo. Ocupado se reintenta siempre |
| Fotogramas con detección fallida guardados como «sin objetos» | **12,7 %** del índice sin etiquetas | Estado de detección por fotograma; los fallidos se reprocesan |
| Indicador de salud en verde con el modelo de detección caído por falta de memoria de GPU | **4,5 h** sin detectar | Salud funcional: cada servicio demuestra que funciona con una muestra patrón |
| Servicio de visión caído sin relanzar | **31 h** sin leer matrículas | Supervisor con canarios de dominio (matrículas por hora por fuente) |
| Reiniciar la visión reiniciaba todo el sistema | **7 reinicios al día** | Un proceso por rol; el receptor de matrículas no depende de nadie |
| Dos procesos de indexado saturaron un grabador | lecturas a **0 por hora** | Un único acceso al hardware con presupuesto global de sesiones |
| El escáner de grabación solo revisaba una parte del vídeo | **≈ 5 %** del vídeo leído | Una conexión continua por cámara que alimenta a todos los consumidores |
| Miniaturas guardadas dentro de la base de datos | BD de **24 GB**, copias de **10,9 GB** | Imágenes en disco con catálogo; la base de datos solo guarda metadatos |
| Estado crítico (duplicados, cursores, «caída desde») solo en memoria | se perdía en cada reinicio | Todo el estado que importa, en PostgreSQL |
| ~170 variables de configuración sin validar | días con el chat en modo plantilla por un valor mal escrito | Configuración tipada; si no valida, no arranca |
| Credenciales en copias de seguridad y en el historial | **~230** ficheros afectados | Almacén cifrado, referencias en la configuración y escáner de secretos en cada commit |
| Hilos efímeros en los servicios de IA | fuga de memoria de **~4 GB/h** | Hilos de inferencia fijos y reciclado por umbral |
| Recodificar el vídeo para cada clip | **21 s** por clip | Descarga directa del grabador sin recodificar: **1,5 s** |
| El modelo de lenguaje elegía herramientas | fallos de enrutado con un modelo pequeño | Enrutado determinista medido (33/33); el modelo solo redacta |
| Numeración de GPU distinta según la herramienta | servicios en la GPU equivocada | Asignación por UUID de GPU y presupuesto comprobado al arrancar |

## Principios que salen de aquí

1. **Nada se pierde en silencio.**
2. **La salud se demuestra trabajando**, no respondiendo «estoy vivo».
3. **Un dueño por responsabilidad**: un único escritor de matrículas, un único acceso al hardware, una única fuente de configuración.
4. **Lo medido manda**: ningún umbral sin banco de verdad.
5. **Probar en un entorno aislado**, nunca contra producción.
