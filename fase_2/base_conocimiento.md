# Fase 2: Creación de la Base de Conocimiento

## 1. Introducción

El desempeño de un sistema RAG depende directamente de la calidad de la
información que pueda recuperar. Para EcoMarket se propone construir una base de conocimiento compuesta por documentos internos relacionados con los temas más frecuentes de atención al
cliente.

El objetivo es que el sistema pueda recuperar información relevante antes de generar una respuesta, evitando depender únicamente del conocimiento general del modelo de lenguaje.

---

## 2. Documentos seleccionados

La propuesta es trabajar con cuatro tipos de documentos para EcoMarket.

### 2.1 Política de devoluciones

Archivo:`politica_devoluciones.md`

Este documento tendrá información y será especialmente importante para responder preguntas
relacionadas con cambios, devoluciones y restricciones por tipo de producto.

- Plazo permitido para realizar devoluciones
- Condiciones que debe cumplir el producto
- Productos que no pueden devolverse
- Procedimiento general para solicitar una devolución

---

### 2.2 Preguntas frecuentes

Archivo: `faq.json`

Este archivo tendrá preguntas y respuestas frecuentes relacionadas con: medios de pago, tiempos de entrega, modificaciones de pedidos, cancelaciones, seguimiento de pedidos y canales de atención. 
El formato JSON permite organizar la información de forma estructurada.

---

### 2.3 Catálogo de productos

Archivo: `productos.csv`

Este documento tendrá información básica de los productos de EcoMarket: código del producto, nombre, categoría, disponibilidad e información relacionada con devolución. El formato CSV es adecuado para información tabular y facilita representar un catálogo de productos.

---

### 2.4 Información sobre envíos

Archivo: `envios.md`

Este documento tendrá información general relacionada con: zonas de cobertura, tiempos estimados de entrega, transportadoras, seguimiento de pedidos y posibles retrasos. Este tipo de documento permitirá responder consultas relacionadas con la logística de los pedidos.

---

## 3. Estrategia de segmentación o chunking

Antes de almacenar los documentos en la base vectorial, es necesario dividirlos en pequeños fragmentos llamados chunks. Esto permite que el sistema recupere solamente la parte del documento que sea más relevante para la consulta del usuario.

### 3.1 Estrategias consideradas

#### Tamaño fijo

Consiste en dividir el texto cada cierto número de caracteres o tokens.

Ventajas:
- Es fácil de implementar.
- Produce fragmentos de tamaños similares.

Desventajas:
- Puede cortar una idea en medio de una frase o párrafo.
- Puede separar información que debería mantenerse unida.

#### División por párrafos

Consiste en utilizar cada párrafo como una unidad independiente.

Ventajas:
- Mantiene mejor el significado del texto.
- Resulta intuitiva para documentos bien estructurados.

Desventajas:
- Los párrafos pueden tener tamaños muy diferentes.
- Algunos pueden ser demasiado largos o demasiado cortos.

#### Segmentación recursiva

La segmentación recursiva intenta dividir el documento respetando primero separaciones naturales, como párrafos y frases, y utiliza divisiones más pequeñas cuando el fragmento supera el tamaño definido.

Sus ventajas es que conserva mejor el contexto, reduce la probabilidad de cortar ideas importantes y ermite controlar el tamaño máximo de los fragmentos.

---

## 4. Estrategia seleccionada

Para EcoMarket se utilizará una estrategia de segmentación recursiva y en la implementación se utilizará un divisor de texto (similar a `RecursiveCharacterTextSplitter`)

La configuración inicial que se propone es:

- `chunk_size = 500`
- `chunk_overlap = 80`

El tamaño de 500 caracteres permite obtener fragmentos suficientemente pequeños
para realizar búsquedas precisas, pero con suficiente contexto para conservar
el significado de la información.

El solapamiento de 80 caracteres permite compartir una pequeña parte de la
información entre dos fragmentos consecutivos, asi mismo reduce el riesgo de perder contexto cuando una idea se encuentra cerca del límite entre dos chunks. Estos valores podrán ajustarse posteriormente después de evaluar el comportamiento del sistema.

---

## 5. Preparación de los documentos

Los documentos pasarán por el siguiente proceso: Documentos originales -> Carga de documentos -> Normalización del contenido -> Segmentación en chunks -> Generación de embeddings -> Almacenamiento en ChromaDB

Cada fragmento conservará información básica sobre su documento de origen para
facilitar la identificación de la fuente recuperada.

---

## 6. Generación de embeddings

Cada chunk será procesado mediante el modelo `sentence-transformers/paraphrase-multilingual-MiniLM-L12-v2`, el cual convertirá cada fragmento de texto en una representación numérica. Asi (Texto: "Los productos perecederos no pueden ser devueltos."
-> Modelo de embeddings -> Vector numérico -> Ese vector representa características semánticas del texto. La consulta del usuario también será transformada en un vector mediante el mismo modelo, lo cual permite comparar ambos vectores mediante similitud semántica.

---

## 7. Indexación en ChromaDB

Después de generar los embeddings, cada fragmento será almacenado en ChromaDB. La base vectorial almacenará: contenido del chunk, embedding, metadatos del documento y fuente del fragmento.

Cuando un cliente realice una pregunta, el proceso será:

Consulta del usuario
        ↓
Generación del embedding de la consulta
        ↓
Búsqueda por similitud en ChromaDB
        ↓
Recuperación de los chunks más relevantes
        ↓
Incorporación de esos chunks al prompt
        ↓
Generación de la respuesta por el LLM

---

## 8. Importancia de la calidad de los documentos

La calidad de los documentos tiene un impacto directo sobre el sistema RAG, si la información almacenada es incorrecta, incompleta o desactualizada, el retriever puede recuperar información inadecuada y el modelo puede generar una respuesta incorrecta.

Por esta razón, la base de conocimiento debe mantenerse actualizada, estructurada, sin información contradictoria y con fuentes claramente identificables.

---

## 10. Conclusión

Para EcoMarket se propone una base de conocimiento compuesta por información de devoluciones, preguntas frecuentes, catálogo de productos e información sobre
envíos.

Los documentos serán segmentados mediante una estrategia recursiva, generando
chunks con un tamaño inicial de 500 caracteres y un solapamiento de 80.

Posteriormente, cada fragmento será convertido en un embedding utilizando el
modelo multilingüe seleccionado en la Fase 1 y será almacenado en ChromaDB.

Esta estructura permitirá que el sistema RAG recupere información relevante antes
de generar una respuesta al cliente.
