# Fase 1: Selección de Componentes Clave del Sistema RAG

## 1. Introducción

En el Taller Práctico #1 se propuso una solución de inteligencia artificial generativa
para apoyar la atención al cliente de EcoMarket. Sin embargo, un modelo de lenguaje
de propósito general puede no tener acceso a información interna, específica y
actualizada de la empresa.

Para ampliar las capacidades del sistema se propone incorporar una arquitectura
RAG (Retrieval Augmented Generation), que permita recuperar información desde una
base de conocimiento de EcoMarket antes de generar la respuesta al usuario.

De esta manera, el sistema podrá utilizar información empresarial relevante como
políticas de devolución, información de productos, preguntas frecuentes y datos
relacionados con envíos, reduciendo el riesgo de generar respuestas sin respaldo
en los datos disponibles.

Para la implementación del sistema RAG se deben seleccionar principalmente:

1. Un modelo de embeddings.
2. Una base de datos vectorial.

---

## 2. Modelo de embeddings

### 2.1 Modelo seleccionado

Modelo propuesto:

`sentence-transformers/paraphrase-multilingual-MiniLM-L12-v2`

Este modelo permite transformar fragmentos de texto en vectores numéricos o
embeddings. Estos vectores representan características semánticas del texto y
permiten comparar una consulta del usuario con los documentos almacenados en
la base de conocimiento.

### 2.2 Justificación

#### Soporte para idioma español

Uno de los principales requisitos del sistema es trabajar con información y
consultas en español. El modelo seleccionado es multilingüe, por lo que resulta adecuado para representar textos en español y realizar búsquedas por similitud semántica.

Esto es importante para EcoMarket porque un usuario puede formular una misma
consulta de diferentes maneras.

Por ejemplo:

- "¿Puedo devolver un producto?"
- "¿Cuál es la política de devoluciones?"
- "Quiero regresar un artículo."

Aunque las frases no utilizan exactamente las mismas palabras, los embeddings
permiten identificar que están relacionadas semánticamente.

#### Costo

El modelo es de código abierto y puede utilizarse localmente mediante la librería
Sentence Transformers. Por esta razón no es necesario pagar por cada solicitud realizada al modelo, lo cual resulta conveniente para el desarrollo del prototipo.

#### Rendimiento

El modelo está diseñado para tareas de similitud semántica, por lo que puede
utilizarse para comparar la representación vectorial de la consulta de un usuario
con los fragmentos de información almacenados en la base vectorial.

#### Facilidad de integración

Puede utilizarse desde Python y es compatible con herramientas utilizadas
habitualmente para construir sistemas RAG, esto facilita su integración con frameworks como LangChain y con bases de datos vectoriales como ChromaDB.

---

## 3. Modelos open-source y modelos propietarios

Para generar embeddings también podrían utilizarse modelos propietarios disponibles
mediante APIs comerciales.

### Modelos propietarios

Ventajas:
- Infraestructura administrada por el proveedor.
- Integración sencilla mediante API.
- No requieren administrar localmente el modelo.

Desventajas:
- Pueden generar costos recurrentes por uso.
- Requieren conexión a servicios externos.
- Existe dependencia del proveedor.

### Modelos open-source

Ventajas:
- Pueden ejecutarse localmente.
- No generan costos por cada consulta.
- Permiten mayor control sobre la implementación.
- Facilitan la experimentación académica.

Desventajas:
- Requieren instalar y administrar las dependencias.
- El rendimiento depende de los recursos computacionales disponibles.

Para este taller se selecciona un modelo open-source, ya que permite implementar
el sistema RAG sin depender de servicios externos y es suficiente para demostrar
el funcionamiento de la arquitectura propuesta.

---

## 4. Base de datos vectorial

### 4.1 Base de datos seleccionada

Se propone utilizar **ChromaDB**.

Una base de datos vectorial almacena los embeddings generados a partir de los
documentos y permite realizar búsquedas basadas en similitud. Cuando un usuario realiza una consulta, esta también se convierte en un embedding. El sistema compara dicho vector con los almacenados y recupera los fragmentos más similares.

### 4.2 Justificación

#### Facilidad de uso

ChromaDB puede utilizarse directamente desde Python y ofrece integración con
frameworks como LangChain. Esto permite construir un prototipo RAG sin necesidad de desplegar infraestructura compleja.

#### Costo

ChromaDB puede ejecutarse localmente de forma gratuita, por lo que es adecuada
para el desarrollo académico del sistema de EcoMarket.

#### Escalabilidad

Para el inicio del proyecto, la base de conocimiento de EcoMarket será
relativamente pequeña. Por esta razón, ChromaDB ofrece la capacidad suficiente para almacenar y consultar los embeddings utilizados durante este taller.

#### Integración

ChromaDB puede conectarse con modelos de embeddings y con el componente de
retrieval del sistema RAG. Esto permite implementar el siguiente flujo:

Documentos → Fragmentos → Embeddings → ChromaDB → Retrieval → LLM

---

## 5. Comparación de bases de datos vectoriales

### ChromaDB

**Ventajas**
- Fácil de utilizar en proyectos Python.
- Puede ejecutarse localmente.
- Buena integración con LangChain.
- Adecuada para prototipos y proyectos académicos.

**Desventajas**
- Para escenarios de gran escala pueden requerirse alternativas con
infraestructura empresarial más robusta.

### Pinecone

**Ventajas**
- Servicio especializado en búsqueda vectorial.
- Alta escalabilidad.
- Infraestructura administrada.

**Desventajas**
- Dependencia de un proveedor externo.
- Puede implicar costos asociados al servicio.
- Para el alcance del taller añade una dependencia innecesaria.

### Weaviate

**Ventajas**
- Puede utilizarse como solución open-source.
- Permite búsquedas vectoriales y manejo de metadatos.
- Puede utilizarse en escenarios de mayor escala.

**Desventajas**
- Su configuración puede ser más compleja que ChromaDB para un prototipo
académico.

---

## 6. Arquitectura seleccionada

Para la implementación del sistema RAG de EcoMarket se seleccionan los siguientes
componentes:

- **Modelo de embeddings:** `sentence-transformers/paraphrase-multilingual-MiniLM-L12-v2`
- **Base de datos vectorial:** ChromaDB
- **Framework para la integración:** LangChain
- **Entorno de ejecución:** Google Colab

La arquitectura general será:

Documentos de EcoMarket -> Segmentación de documentos -> Modelo de embeddings -> ChromaDB ↓         ↓ LLM <- Prompt + contexto <- Información recuperada <- Consulta del cliente <- Retriever
Respuesta al cliente
       
---

## 7. Conclusión

La combinación de un modelo de embeddings multilingüe y ChromaDB permite construir
una arquitectura RAG adecuada para las necesidades iniciales de EcoMarket.

La selección prioriza tres aspectos importantes para el proyecto: capacidad de
trabajar con información en español, bajo costo y facilidad de implementación.

Además, la solución permitirá que el modelo utilice información recuperada desde
la base de conocimiento antes de responder al cliente, reduciendo la dependencia
exclusiva del conocimiento interno del LLM y proporcionando respuestas mejor
fundamentadas en la información disponible de EcoMarket.
