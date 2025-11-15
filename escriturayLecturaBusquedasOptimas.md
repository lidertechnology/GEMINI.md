🏛️ Convención Maestra de Datos (Normalización y Presentación)
Esta es la estrategia única y obligatoria para la gestión de datos de texto en Lidertech, diseñada para garantizar la máxima eficiencia en la búsqueda (eliminando la dependencia de Algolia) y la consistencia absoluta en la interfaz de usuario.

1. Escritura (Normalización Universal)
Todos los datos de texto (strings) destinados a la búsqueda, filtrado o comparación (como nombres, categorías, emails, etiquetas, etc.) deben guardarse en Firestore siempre en minúsculas (lowercase).

Esta normalización se aplica obligatoriamente en la Convención de Formularios, justo antes de enviar los datos al WriteService, utilizando el método .toLowerCase() nativo de JavaScript.

2. Lectura (Formateo en Presentación)
Los datos normalizados (guardados en minúsculas) deben formatearse únicamente en la capa de presentación (el template HTML) para ser visualmente legibles por el usuario.

Esta transformación se aplica exclusivamente usando los Pipes nativos de Angular (importados de CommonModule), como | titlecase o | uppercase.
