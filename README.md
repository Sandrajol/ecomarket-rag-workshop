# Taller 2 
# Sistema RAG para EcoMarket

## Inteligencia Artificial Generativa

Este repositorio contiene el desarrollo del Taller 2, enfocado en la
implementación de un sistema RAG (Retrieval Augmented Generation) para mejorar
la atención al cliente de EcoMarket.

El objetivo es extender la solución propuesta en el Taller Práctico #1,
permitiendo que el modelo consulte información interna de la empresa antes de
generar una respuesta.

De esta forma, el sistema puede responder utilizando información específica de
EcoMarket y reducir la generación de respuestas sin respaldo en la base de
conocimiento.

## Estructura del repositorio

### Fase 1: Selección de componentes RAG

En esta fase se seleccionan y justifican los principales componentes del sistema:

- modelo de embeddings;
- base de datos vectorial;
- comparación entre alternativas;
- análisis de costo, escalabilidad y soporte para español.

Archivo:

`fase_1/seleccion_componentes_rag.md`

### Fase 2: Creación de la base de conocimiento

En esta fase se define:

- qué documentos utilizará EcoMarket;
- estrategia de chunking;
- generación de embeddings;
- proceso de indexación en la base vectorial.

Archivo:

`fase_2/base_conocimiento.md`

### Fase 3: Implementación del sistema RAG

Esta fase contiene la implementación práctica del sistema.

Incluye:

- documentos de EcoMarket;
- carga y procesamiento de información;
- segmentación de documentos;
- generación de embeddings;
- almacenamiento en ChromaDB;
- configuración del retriever;
- integración con un modelo de lenguaje;
- mejora del prompt;
- pruebas del sistema.

Notebook principal:

`fase_3/ecomarket_rag.ipynb`

Documentación:

`fase_3/README.md`

## Base de conocimiento

La carpeta:

`fase_3/data/`

contiene los documentos utilizados por el sistema:

- `politica_devoluciones.md`
- `faq.json`
- `productos.csv`
- `envios.md`

## Arquitectura general

El sistema implementa el siguiente flujo:

Documentos de EcoMarket  
↓  
Chunking  
↓  
Embeddings  
↓  
ChromaDB  
↓  
Retriever  
↓  
Contexto recuperado  
↓  
Prompt  
↓  
Modelo de lenguaje  
↓  
Respuesta al cliente

## Tecnologías utilizadas

- Python
- Google Colab
- LangChain
- Sentence Transformers
- ChromaDB
- Hugging Face Transformers
- Qwen2.5 Instruct

## Modelo de embeddings

Se utiliza:

`sentence-transformers/paraphrase-multilingual-MiniLM-L12-v2`

El modelo permite generar representaciones vectoriales multilingües para realizar
búsquedas por similitud semántica.

## Base de datos vectorial

Se utiliza ChromaDB para almacenar los embeddings de los documentos y recuperar
los fragmentos más relevantes para cada consulta.

## Pruebas realizadas

El sistema fue probado con consultas relacionadas con:

- devolución de alimentos;
- devolución de productos específicos;
- pedidos retrasados;
- medios de pago;
- información no disponible en la base de conocimiento.

También se verificó que, cuando la información no existe en los documentos,
el sistema indique que no dispone de información suficiente en lugar de inventar
una respuesta.

## Ejecución

1. Abrir `fase_3/ecomarket_rag.ipynb` en Google Colab.
2. Ejecutar la instalación de dependencias.
3. Cargar en Colab los archivos contenidos en `fase_3/data/`.
4. Ejecutar las celdas en orden.
5. Revisar los resultados de las pruebas.

## Conclusión

El proyecto demuestra cómo un sistema RAG puede complementar un modelo de
lenguaje con información específica de una empresa.

La implementación permite recuperar información relevante desde una base de
conocimiento, incorporarla al contexto del modelo y generar respuestas más
fundamentadas para el usuario.

Durante las pruebas también se evidenció que la calidad de la respuesta depende
tanto del mecanismo de recuperación como de la capacidad del modelo generativo
y de la estructura del prompt.
