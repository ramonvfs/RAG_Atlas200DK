# Huawei Ascend Hardware RAG Project Context

## 1. Project Mission & Objectives

This project implements a Retrieval-Augmented Generation (RAG) system tailored for technical documentation on **Huawei AI Hardware Accelerators and Software Architecture**. The primary objective is to evaluate and benchmark **pre-call optimization methods** (e.g., query augmentation, dense vs. sparse indexing, metadata filtering, hierarchical retrieval, and prompt contextualization) for accurate, hallucination-resistant LLM inference.

The system targets deep technical questions concerning:

* **Hardware Architecture:** Ascend 310 AI Processor, Atlas 200 AI Accelerator Module (Model 3000), DaVinci AI Cores, LPDDR4X memory layout, and peripheral interfaces.


* **Software Stack (CANN / DDK):** Offline Model Generator (OMG) / Ascend Tensor Compiler (ATC) workflows, AscendCL (ACLlib), Tensor Engine (TE / TVM), and AI Pre-processing (AIPP).


* **Operator Specifications:** Caffe and TensorFlow operator boundaries, custom TE operator implementation, and plug-in compilation.



---

## 2. Source Documents & Repository Structure

Raw manuals and technical specifications are stored in `data/raw/markdown`:

* `data/raw/manuals/ATCToolInstructions.pdf`: Ascend Tensor Compiler (ATC) instructions, parameter tables, operator mapping, and AIPP configuration.


* `data/raw/manuals/Atlas200AIAcceleratorModuleApplicationSoftwareDevelopmentGuide(Models3000).pdf`: DDK configuration, Matrix framework, OMG/OME pipelines, and TE custom operator development.


* `data/raw/manuals/EnvironmentDeploymentGuide.pdf`: Atlas 200 DK environment preparation, CANN Toolkit setup, cross-compilation toolchains, and hardware verification.


* `data/raw/manuals/Huawei Atlas 200 DK developer kit Technical White Paper (Model 3000) 08.pdf`: System specifications, IT21DMDA vs. IT21VDMB board hardware revisions, computing power (TOPS/TFLOPS), and electrical/pin interfaces.


* `data/raw/manuals/UserGuide2021.pdf`: Operating system setup, SD card preparation, and hardware troubleshooting.



---

## 3. Data Preprocessing Notebook: `data_chunking.ipynb`

Before building the retrieval index or LLM generation loops, the first phase focuses on data treatment and structural preservation via `data_chunking.ipynb` (designed to run in Google Colab / cloud GPU environments).

### Core Goals of `data_chunking.ipynb`

1. **Document Parsing & Extraction:**
* Convert dense PDFs in `data/raw/manuals/` into structured Markdown (`.md`) to be stored on `data/processed/manuals/`.
* Preserve deep heading hierarchies (`#`, `##`, `###`, `####`) to maintain topic scoping (e.g., distinguishing between Caffe and TensorFlow versions of an operator).


* Retain Markdown pipe tables for hardware specs (e.g., ATC parameters, pin definitions, operator input/output boundaries) without cell collapse.




2. **Chunking Strategy Laboratory:**
* **Baseline Strategy:** Recursive character splitting with configurable chunk size and overlap windows.
* **Markdown Structural Chunking:** Splitting along semantic Markdown header boundaries.
* **Table-Aware Atomic Chunking:** Keeping multi-column configuration tables and C/Python code blocks intact within single chunks.


3. **Metadata & Breadcrumb Enrichment:**
* Prepend hierarchical paths to isolated chunks (e.g., `[Document: ATCToolInstructions > Section: 2 Restrictions and Parameters > Parameter: --precision_mode]`) to prevent loss of context.


* Inject document tags: `chip_type` (Ascend 310), `framework` (Caffe, TensorFlow, MindSpore), `hardware_rev` (IT21DMDA, IT21VDMB), and `tool` (ATC, OMG, AscendCL).




4. **Diagnostics & Serialization:**
* Evaluate chunk token distribution (min, max, median, token histograms).
* Flag corrupted or sliced tables.
* Export validated chunk payloads (with metadata) to structured formats (JSON / Parquet) for downstream vector indexing and pre-call benchmarking.



---

## 4. Key Ascend Domain Concepts for Document Ingestion & Retrieval

* **ATC / OMG:** Ascend Tensor Compiler / Offline Model Generator; converts Caffe/TensorFlow models to `.om` (DaVinci offline model) files.


* **AIPP:** AI Preprocessing module executed in hardware (AI Core) for Color Space Conversion (CSC), cropping, padding, and mean/variance normalization before model execution.


* **Matrix / OME:** Process orchestration framework and runtime engine responsible for loading and executing graphs and models on device memory.


* **TE / TVM:** Tensor Engine DSL using Python and TVM primitives to generate custom AI Core (`.o`) and AI CPU (`.so`) operator kernels.


* **NC1HWC0 / FRACTAL_Z:** Huawei Ascend-specific 5D and fractal tensor memory layouts optimized for AI Core matrix computation units.

# Rules
- Every code must be in english, even docstrings;
- Do not use emojis;

