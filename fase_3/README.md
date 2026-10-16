# Fase 3: Implementación del Sistema RAG

## Descripción

En esta fase se implementa un sistema de Retrieval Augmented Generation (RAG)
para mejorar la atención al cliente de EcoMarket.

El sistema utiliza información interna de la empresa como contexto antes de
generar una respuesta.

## Componentes utilizados

- Python
- Google Colab
- LangChain
- Sentence Transformers
- ChromaDB
- Hugging Face Transformers
- Qwen2.5 Instruct

## Modelo de embeddings

Se utiliza el modelo:

`sentence-transformers/paraphrase-multilingual-MiniLM-L12-v2`

Este modelo genera embeddings multilingües y permite realizar búsquedas
semánticas sobre los documentos de EcoMarket.

## Base de datos vectorial

Se utiliza ChromaDB para almacenar los embeddings generados a partir de los
fragmentos de los documentos.

## Base de conocimiento

La base de conocimiento se encuentra en la carpeta `data` y contiene:

- `politica_devoluciones.md`
- `faq.json`
- `productos.csv`
- `envios.md`

## Flujo del sistema

Documentos EcoMarket  
↓  
Carga de documentos  
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
Respuesta

## Chunking

Se utiliza `RecursiveCharacterTextSplitter` con:

- `chunk_size = 500`
- `chunk_overlap = 80`

El objetivo es conservar suficiente contexto dentro de cada fragmento y reducir
la pérdida de información entre chunks consecutivos.

## Retriever

El retriever se configura para recuperar los tres fragmentos con mayor similitud
semántica para cada consulta.

## Modelo generativo

Para la generación de respuestas se utiliza un modelo Qwen2.5 Instruct.

Durante las pruebas se observó que la capacidad del modelo generativo afecta
directamente la calidad de la respuesta, incluso cuando el mecanismo de retrieval
recupera correctamente la información.

## Mejora del prompt

En las primeras pruebas el modelo interpretó incorrectamente algunos fragmentos
recuperados.

Por esta razón se ajustaron las instrucciones del prompt para:

- utilizar exclusivamente la evidencia recuperada;
- no contradecir el contexto;
- evitar inventar información;
- responder de forma breve y clara;
- no combinar reglas correspondientes a productos diferentes;
- indicar cuando no existe información suficiente.

## Pruebas realizadas

Se realizaron consultas relacionadas con:

- devolución de alimentos;
- devolución de productos específicos;
- pedidos retrasados;
- medios de pago;
- información no disponible en la base de conocimiento.

Por ejemplo, ante una consulta sobre tiendas físicas en Bogotá, información que
no se encuentra en la base de conocimiento, el sistema responde:

> No dispongo de información suficiente en la base de conocimiento de EcoMarket
> para responder esta solicitud.

## Notebook

La implementación completa se encuentra en:

`ecomarket_rag.ipynb`

El notebook fue desarrollado y probado en Google Colab.

## Ejecución

1. Abrir `ecomarket_rag.ipynb` en Google Colab.
2. Ejecutar la celda de instalación de dependencias.
3. Cargar los archivos contenidos en la carpeta `data`.
4. Ejecutar las celdas en orden.
5. Ejecutar las pruebas del sistema RAG.

## Limitaciones

- La base de conocimiento utilizada es pequeña.
- Los datos utilizados son simulados.
- No se implementó re-ranking.
- No se utilizan APIs ni bases de datos empresariales en tiempo real.
- Los parámetros de chunking fueron definidos experimentalmente.
- La calidad final depende tanto del retrieval como de la capacidad del modelo generativo.

## Conclusión

La implementación demuestra el funcionamiento de una arquitectura RAG aplicada
al caso de atención al cliente de EcoMarket.

El sistema recupera información relevante mediante embeddings y ChromaDB,
incorpora esos fragmentos al contexto y utiliza un modelo generativo para producir
la respuesta final.

Las pruebas evidenciaron que el retrieval puede recuperar correctamente la
información, pero la calidad de la respuesta también depende del modelo
generativo y de la estructura del prompt.
