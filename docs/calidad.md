# Calidad: cómo se mide y qué resultados da

**Lo medido manda.** Ningún umbral se fija «a ojo». Cada componente con IA tiene un banco de verdad etiquetado a mano y una métrica bloqueante: si un cambio la empeora, no se publica.

## Método

- **Bancos de verdad** etiquetados a mano y anonimizados (matrículas, detecciones, fichas, consultas de búsqueda, preguntas de chat y normativa).
- **Separación entreno / validación**: se ajusta con un banco y se valida con otro que nunca se usó para ajustar. Para la búsqueda visual se creó en noviembre de 2026 un banco nuevo de 41 consultas sobre datos de días distintos.
- **Evaluación automática** (`vigia eval`) en cada cambio de modelo, umbral, regla o prompt.
- **Modo sombra**: la versión nueva corre en paralelo a la anterior hasta igualar o superar sus métricas, módulo a módulo.

## Resultados en producción (noviembre de 2026)

| Ámbito | Métrica | Resultado |
|---|---|---|
| Matrículas | Acierto OCR en grabación (verdad a mano) | **97,8 %** (antes 90 %) |
| Matrículas | Falsos positivos por textos sobreimpresos | **0** |
| Matrículas | Lecturas en directo | **224–280 / h** |
| Matrículas | Lecturas perdidas por saturación del OCR | de **84 %** a **0 %** |
| Búsqueda | Precisión en los 10 primeros, banco nuevo | **92,2 % → 96,1 %** |
| Búsqueda | Prenda + color | **96,5 %** |
| Búsqueda | Persona por color de ropa sin prenda («persona de negro») | **95 %** |
| Búsqueda | Coche por color | **100 %** |
| Búsqueda | Tipo de vehículo | **94,3 %** |
| Fichas | Personas reales entre las indexadas | **95,7 %** |
| Fichas | Vehículos reales (estimado y calibrado a mano) | **≈ 97 %** |
| Detección | Fotogramas guardados sin etiquetas por fallo | de **12,7 %** a **0** |
| Vídeo | Tiempo hasta el clip | **21 s → 1,5 s** |
| Indexado | Vuelta completa a todas las cámaras | **324 s → 97 s** |
| Indexado | CPU del servicio de vectores | **3,3× menos**, vectores idénticos (coseno 1,00000) |
| Normativa | Norma correcta en primer lugar (entreno) | **87 % → 96 %** |
| Normativa | Cita correcta en la respuesta (entreno / validación) | **65 % → 88 %** / **75 % → 88 %** |
| Normativa | Respuestas con una cita inventada (entreno) | **24 → 9** |
| Chat | Batería policial bien enrutada | **33/33** |

Tiempos medianos por fotograma: captura HD 1,7 s · detección 208 ms · vectores 226 ms · filtros < 1 ms · guardado ~10 ms.

## Lo que todavía no funciona (y se dice)

| Qué | Estado | Camino |
|---|---|---|
| Marca y modelo del coche por la foto | Probado en 160 recortes: **no fiable** | Entrenar un clasificador propio con fotos etiquetadas por matrícula cuando haya acceso a la DGT |
| Búsqueda de «grúa» | El detector la confunde con camiones con pluma; responde **«aún no fiable»** | Más ejemplos etiquetados |
| Color de contenedores | Poca cobertura: los contenedores no tienen recorte propio | Recorte propio o inventario fijo por cámara |
| Normativa en lenguaje de calle | «Soplar», «porro», «rastrojos»: **38 %** de norma correcta | Banco de entreno específico, separado del de validación |
| Cámaras de radioenlace | Parte de los fotogramas se indexan a 640 px porque la HD no llega a tiempo | Decodificación continua en GPU (probado: 15× menos CPU) |
