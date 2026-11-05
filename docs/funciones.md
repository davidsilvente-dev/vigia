# Funciones, módulo a módulo

Cada módulo se activa por instalación. Un municipio puede empezar con directo y matrículas y añadir el resto después. Los módulos sensibles (búsqueda de personas, dossier de matrícula) vienen desactivados por defecto en una instalación nueva.

---

## 1. Directo y muro de cámaras

- Muro de cámaras en el navegador y en el móvil, organizado por grabador e incluyendo las cámaras caídas (para que se vean, no para esconderlas).
- Instantáneas perezosas: solo se piden las de las cámaras que están en pantalla.
- Vídeo en directo bajo demanda a través de un proxy autenticado, con enlaces firmados que caducan a los 60 s. El navegador nunca habla directamente con una cámara.
- Clips de grabación: descarga directa del grabador y empaquetado sin recodificar. **1,5 s** de media (antes 21 s recodificando).
- Multi-fabricante: Hikvision (ISAPI), Bosch, Uniview y equipos ONVIF. Cada fabricante tiene su controlador en un único servicio que es el único autorizado a hablar con el hardware.

## 2. Lectura de matrículas (LPR)

Cinco fuentes de lectura, todas escritas por una única función:

| Fuente | Qué es |
|---|---|
| `push` | La cámara LPR envía la lectura al servidor |
| `alert_ocr` | El grabador avisa de un evento de matrícula sin el texto; VIGÍA toma la imagen y la lee |
| `live_ocr` | Lectura continua del directo de cualquier cámara que vea placas |
| `recording_lpr` | Revisión de la grabación de cámaras de carril |
| `recording_general` | Revisión de la grabación de cámaras panorámicas |

**Cómo se lee una matrícula**

1. Un detector localiza la placa (nunca se hace OCR sobre el plano completo: así no se leen rótulos ni textos sobreimpresos).
2. Tres lectores de dos familias distintas («linajes») leen el recorte ampliado.
3. Consenso por linaje: solo cuenta como confirmación que coincidan motores **independientes**; dos lecturas del mismo motor no son dos votos.
4. Validación de formato español (actual y antiguo, con provincias reales).
5. Voto multi-fotograma por vehículo: la lectura final sale de varios fotogramas, y las «sombras» (variantes a ≤ 2 caracteres con menos votos) se descartan.

Resultado medido: **97,8 %** de acierto en grabación sobre verdad comprobada a mano y **0 falsos positivos** por textos sobreimpresos. En directo, **224–280 matrículas por hora** en la instalación de referencia.

## 3. Tracking y dossier de matrícula

- Todos los avistamientos de una matrícula: foto, hora, cámara y ubicación vigente de la cámara en ese momento.
- Recorrido reconstruido **por calles reales** con un motor de rutas (OSRM) que corre en el propio servidor.
- Vehículos acompañantes: matrículas que aparecen repetidamente cerca en tiempo y lugar.
- **La consulta exige motivo** y queda en el registro de auditoría con usuario, hora y motivo.
- Integración con la DGT preparada con tres estados explícitos (`not_configured`, `configured_no_adapter`, `active`). Sin convenio activo, el sistema lo dice; nunca simula una respuesta.

## 4. «¿Qué quieres ver?» — búsqueda visual

- Cada fotograma indexado y cada objeto detectado (persona, vehículo) se convierte en un vector con un modelo imagen-texto (SigLIP 2) que entiende consultas en español.
- La consulta se interpreta (prenda, color, tipo de vehículo, momento, lugar) y se combina la similitud del vector con filtros exactos.
- El **color se mide** sobre los píxeles del objeto (prenda o carrocería), no se adivina. Si el color medido no casa con la consulta, el resultado se marca como no verificado.
- Si una consulta no es fiable (por ejemplo «grúa», que el detector confunde con camiones con pluma), la respuesta lo dice: «0 resultados verificados».
- Medido en noviembre de 2026 sobre consultas nunca usadas para ajustar: **96 %** de precisión; prenda + color **96,5 %**; coche por color **100 %**.

## 5. Fichas por objeto

- Cada persona y vehículo detectado tiene una ficha: tipo, prendas (segmentación de ropa), color medido en una paleta de 10 colores.
- **Control de realidad**: cada recorte se compara con lo que suele confundirse (árbol, sombra, farola, señal, estatua, contenedor). Por debajo del umbral, la ficha no cuenta.
- Filtro de «objetos fijos»: una caja casi idéntica durante horas (una escultura con forma humana, un aparcabicis) sale del índice.
- **95,7 %** de las «personas» indexadas son personas reales.

## 6. Vertidos y enseres abandonados

1. La zona de contenedores de cada cámara tiene una referencia limpia.
2. Un detector de vocabulario abierto busca objetos nuevos (colchón, mueble, escombros, electrodoméstico) dentro de la zona, a resolución nativa.
3. El objeto tiene que **persistir** en comprobaciones sucesivas para descartar a alguien de paso.
4. Se descarga del grabador el clip de antes y después, anclado al momento del depósito.
5. Se busca el vehículo que se detuvo allí en esa ventana y su matrícula, si alguna cámara la leyó.
6. Se genera un informe A4 con fotos, clip, vehículo correlado y **huella SHA-256 firmada** (cadena de custodia).
7. Aviso por correo a la Policía Local con las fotos.

## 7. Incidencias de cámaras

- Vigilancia continua de cada cámara: red y, sobre todo, **imagen real** (una cámara que responde al ping pero no da vídeo también es una avería).
- Confirmación a los **20 minutos** para no avisar por microcortes.
- Reparto por causa: **sin red → mantenimiento eléctrico**; **sin señal de vídeo → informática**; tercer nivel solo a mano.
- Correo con la última imagen y botones de decisión con **enlaces firmados de un solo uso** («voy de camino», «no es lo mío → siguiente nivel», «resuelto»).
- Un «resuelto» se comprueba: si la cámara sigue sin imagen, la incidencia se reabre.
- Estadística de tiempos de respuesta y resolución por responsable.

## 8. Red e inventario

- Descubrimiento de equipos en las subredes de vídeo: cámaras, grabadores, routers y switches, con fabricante por MAC y huella del equipo.
- Inventario editable con bloqueo de campos revisados a mano.
- Cámaras «sin ubicar» señaladas para colocarlas en el mapa; ubicaciones con estado (borrador / verificada).
- Comprobación de la hora de los equipos: una grabación con la hora mal es una mala prueba.

## 9. Chat de investigación

- Preguntas en castellano llano: «¿cuántos coches pasaron ayer entre 8 y 9 por la avenida?», «enséñame la furgoneta blanca de las 10:42», «¿dónde se vio esta matrícula esta semana?».
- **Enrutado determinista**: el código decide qué consulta ejecutar; el modelo de lenguaje no elige herramientas (con un modelo pequeño eso falla). Batería policial: **33/33**.
- **El modelo solo redacta.** Las cifras, las citas y los avisos los inserta el código a partir de datos verificados.
- Guardia por perfil de cámara: si una cámara no ve personas (por ejemplo, una cámara de carril LPR), el chat no las busca allí.
- Enseñanza en directo: un supervisor puede corregir una respuesta; la corrección se convierte en caso de prueba.

## 10. Normativa con cita literal

- Legislación estatal y autonómica y ordenanzas municipales indexadas en local (búsqueda de texto completo) y actualizadas cada semana.
- Cada artículo llega al modelo con su cita ya escrita; la respuesta incluye norma y artículo exactos.
- Cabecera de contexto con las ordenanzas disponibles, para que «no consta» sea exacto.
- Medido: cita correcta **88 %** (antes 65 %), citas inventadas de 24 a 9 en el banco de entreno.

## 11. Analítica de tráfico

- Conteo de vehículos y personas por cámara y hora, velocidad estimada y mapas de calor por zona y por matrícula.
- Seguimiento de objetos en vídeo en vivo (bajo demanda) con marcas de tiempo reales.
- Resúmenes horarios agregados, sin imágenes ni datos personales, que se conservan para comparar tendencias.

## 12. Supervisor y operación

- Sondas en tres niveles por servicio: `live`, `ready` y `deep` (trabajo real sobre muestras patrón).
- Canarios de dominio: matrículas por hora por fuente, avance del indexado, respuestas del modelo de lenguaje.
- Máquina de estados `OK → DEGRADED → RESTARTING → QUARANTINE`: reinicia solo lo que falla y, si se repite más de 3 veces en 30 min, lo pone en cuarentena y avisa en lugar de entrar en bucle.
- Contrapresión: si el indexado va con retraso, baja su ritmo; la lectura de matrículas en directo **nunca** se degrada.
- Arranque desatendido tras un corte de luz, sin iniciar sesión.
- Alertas por correo y notificaciones web al móvil.
