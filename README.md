# 📘 Mapeo de Metadatos PLIC a Datos de InBio + Pipeline RAG Completo
## 🌟 Descripción
Este cuaderno de Jupyter implementa un pipeline completo para procesar datos de biodiversidad de Costa Rica (InBio), mapear sus metadatos al estándar Plinian Core 3.2, almacenar la información en una base de datos vectorial (Milvus), y construir un sistema de preguntas y respuestas (RAG) con interfaz interactiva.
## 🔍 Contenido del Notebook
1. **Mapeo de Metadatos InBio → Plinian Core**
   - Conversión de datos CSV a formato XML/JSONL
   - Mapeo de campos como `habitat`, `threat_status`, `distribution` al esquema Plinian
   - Generación de archivos estructurados
2. **Ingesta a Milvus**
   - Generación automática de pares pregunta-respuesta (QA)
   - Cálculo de embeddings con modelo multilingüe (`sentence-transformers-multilingual-e5-base`)
   - Almacenamiento en colección vectorial con filtros por reino y campo Plinian
3. **Consultas Vectoriales**
   - Búsquedas por similitud semántica
   - Ejemplos de consultas con filtros (`kingdom`, `plinian_field`)
   - Visualización de resultados con scores de relevancia
4. **Exploración de Metadatos**
   - Análisis estadístico de campos del dataset InBio
   - Conteo de registros por tipo de metadato
5. **Sistema RAG (Retrieval-Augmented Generation)**
   - Interfaz Gradio para preguntas en lenguaje natural
   - Modelo de lenguaje `Nous-Hermes-2-Mistral-7B-DPO`
   - Respuestas generativas basadas en contexto científico
## ⚙️ Dependencias
```bash
pip install pandas lxml tqdm dicttoxml pymilvus sentence-transformers
pip install gradio transformers huggingface-hub bitsandbytes accelerate
```
## 🚀 Configuración Clave
```python
# Conexión Milvus (servidor Azure)
MILVUS_HOST = "135.237.82.231"
MILVUS_PORT = "19530"
COLLECTION_NAME = "inbio_plinian_qa"
# Modelos
EMBED_MODEL = "embaas/sentence-transformers-multilingual-e5-base"
LLM_ID = "NousResearch/Nous-Hermes-2-Mistral-7B-DPO"
```
## 💾 Dataset de Entrada
- **Nombre:** `reg_especies_INBiov5.csv`
- **Estructura:**
  - `taxon_record_id`: Identificador único
  - `kingdom`: Reino taxonómico (Animalia, Plantae)
  - `description_type`: Tipo de metadato (habitat, threat, behavior, etc.)
  - `description`: Texto descriptivo
  - `default_name`: Nombre científico de la especie
## 📂 Archivos Generados
1. `plinian_records.jsonl`: Registros en formato JSON Lines
2. `out_xml/`: Directorio con XMLs en estándar Plinian Core
3. Colección Milvus con:
   - Embeddings de preguntas-respuestas
   - Metadatos taxonómicos
   - Campos para filtrado (`kingdom`, `plinian_field`, etc...)
## 🧠 Pipeline RAG
```mermaid
graph LR
A[Pregunta Usuario] --> B(Embedding Multilingüe)
B --> C[Búsqueda Milvus]
C --> D[Contexto Relevante]
D --> E[Prompt + Contexto]
E --> F[LLM Generativo]
F --> G[Respuesta]
```
## 🔑 Ejemplos de Uso
**Consulta en Milvus:**
```python
docs = colec.query(
    expr="kingdom == 'Plantae'",
    output_fields=["question", "answer"]
)
```
**Generación de Respuestas:**
```python
respuesta = rag_answer("¿Dónde habita el jaguar en Costa Rica?", k=3)
```
