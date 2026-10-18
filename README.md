# RAG Atlas 200 DK -- Benchmark de Otimizacao Pre-Query

## 1. Visão Geral

Este projeto implementa um sistema de Retrieval-Augmented Generation (RAG) construído
sobre a documentação técnica oficial da Huawei para o **Ascend 310 AI Processor**, o
**Atlas 200 Developer Kit (Model 3000)** e toda a stack de software associada (CANN, DDK,
toolchains ATC/OMG, MindStudio e desenvolvimento de operadores TBE).

O **objetivo principal de pesquisa** é comparar e combinar sistematicamente
**metodos de otimizacao pre-query** -- estratégias aplicadas *antes* da etapa de
retrieval -- e medir seu impacto na acurácia das respostas, na taxa de alucinação e na
relevância da recuperação em um corpus técnico denso e altamente especializado.

Os métodos em estudo são:

| Metodo | Ideia Central |
|---|---|
| **Step-Back Prompting** | Gerar uma pergunta mais abstrata e de nível mais alto antes do retrieval para melhorar o recall em queries conceituais. |
| **Multi-Query / RAG-Fusion** | Decompor ou reformular a query original em múltiplas variantes, recuperar documentos independentemente e então fundir e re-ranquear os resultados via Reciprocal Rank Fusion (RRF). |
| **RAG com Memoria Conversacional** | Manter uma janela deslizante ou memória resumida de turnos anteriores para que perguntas de acompanhamento resolvam correferências e carreguem contexto. |
| **Baseline (Query Unica)** | Retrieval denso padrão com query única sem nenhuma aumentação, utilizado como grupo de controle. |

Além dos métodos pre-query, o projeto também cobre o pipeline RAG completo -- estrategias
de chunking, seleção de modelo de embedding, indexação em vector store, ajuste de
retrieval e qualidade de geração -- para fornecer um benchmark end-to-end completo.

## 2. Por Que Este Corpus

A documentação Huawei Ascend é um teste de estresse ideal para sistemas RAG porque combina:

- **Alta especificidade de hardware** -- especificacoes em nível de pino, arquitetura do
  DaVinci AI Core, layouts de memória LPDDR4X e formatos tensoriais proprietários
  (NC1HWC0, FRACTAL_Z).
- **Tabelas densas de parâmetros** -- flags do compilador ATC, configurações de
  préprocessamento AIPP e matrizes de limites de operadores que quebram estratégias
  de chunking ingenues.
- **Sobreposição multi-framework** -- nomes de operadores idênticos (`Conv2D`,
  `BatchNorm`) aparecem tanto nas seções de Caffe quanto de TensorFlow com restrições
  diferentes, exigindo escopo preciso de seção para evitar contaminação cruzada.
- **Estrutura hierárquica profunda** -- árvores de cabeçalhos com quatro níveis onde um
  chunk isolado sobre `--precision_mode` perde o sentido sem seu caminho pai
  (`ATC Tool > Restrictions and Parameters > Precision Control`).

## 3. Dados Preliminares -- Resultados da Extração do Corpus

Os PDFs fonte em `data/raw/manuals/` foram convertidos para Markdown estruturado usando
`marker-pdf` (extração por camada de texto, sem OCR). O pós-processamento removeu
cabeçalhos/rodapés proprietários da Huawei, reconectou termos técnicos hifenizados e
normalizou a notação de unidades de hardware.

| Documento | Tokens | Caracteres | Status |
| --- | --- | --- | --- |
| ATCToolInstructions | 67.820 | 415.690 | OK |
| ApplicationDevelopmentGuide_MindStudio_ | 24.627 | 127.687 | OK |
| Atlas200AIAcceleratorModuleApplicationSoftwareDevelopmentGuide_Models3000_ | 286.523 | 1.691.982 | OK |
| EnvironmentDeploymentGuide | 29.857 | 148.346 | OK |
| Huawei_Atlas_200_DK_developer_kit_Technical_White_Paper__Model_3000__08 | 7.304 | 48.283 | OK |
| MindStudioInstallationGuideUbuntuX86 | 21.276 | 134.197 | OK |
| TBECustomOperatorDevelopmentGuide_CLI_ | 18.822 | 96.798 | OK |
| TensorFlowNetworkModelPortingandTrainingGuide | 82.818 | 537.969 | OK |
| UserGuide2020 | 20.056 | 96.481 | OK |
| UserGuide2021 | 29.347 | 143.195 | OK |
| **Total** | **588.450** | **3.440.628** |  |

A contagem de tokens utiliza o tokenizador `cl100k_base` (GPT-4). O corpus é dominado
pelo Atlas 200 Application Software Development Guide (~48% de todos os tokens), que
contém a referência do framework Matrix, a documentação dos pipelines OMG/OME e o
fluxo completo de desenvolvimento de operadores customizados com TE.

Os arquivos Markdown processados estão armazenados em `data/processed/markdown/`.

## 4. Estrutura do Projeto

```
RAG_Atlas200DK/
|-- data/
|   |-- raw/
|   |   |-- manuals/              # PDFs fonte (10 documentos, ~24 MB)
|   |-- processed/
|   |   |-- markdown/             # Arquivos Markdown extraidos e pos-processados
|   |-- output/                   # Exports de chunks, indices vetoriais, artefatos de experimentos
|-- data_chunking.ipynb           # Secoes A-C: extracao, laboratorio de estrategias de chunking
|-- GEMINI.md                     # Contexto do projeto e regras de codificacao
|-- README.md                     # Este arquivo
```

## 5. Roadmap

### Fase 1 -- Ingestão de Dados e Chunking

- Extração de PDF para Markdown com parsing layout-aware
- Pipeline de pós-processamento (remoção de cabeçalhos/rodapés, reconexão de hífens, normalização de unidades)
- Estatísticas e validação do corpus
- Comparação de estratégias de chunking: splitting recursivo vs. baseado em cabeçalhos vs. atômico com preservação de tabelas
- Diagnósticos de qualidade dos chunks (distribuição de tokens, auditoria de integridade de tabelas, legibilidade autônoma)
- Seleção da configuração final de chunking e exportação dos payloads validados (JSON)

### Fase 2 -- Indexação e Baseline de Retrieval

- Avaliação de modelos de embedding
- Configuração do vector store (ChromaDB)
- Baseline de retrieval denso com abordagem de query única

### Fase 3 -- Experimentos com Métodos Pre-Query (contribuição principal da pesquisa)

- **Step-Back Prompting**: implementar cadeia de abstração, avaliar ganho de recall em perguntas conceituais (ex.: "Como a arquitetura DaVinci lida com operacoes matriciais?")
- **Multi-Query**: decomposição de query com LLM, retrieval paralelo, re-ranqueamento por Reciprocal Rank Fusion, medir ganhos de diversidade e cobertura
- **RAG com Memória Conversacional**: memória por janela deslizante e por sumarização, avaliar sequências de perguntas multi-turno com resolução de correferências

### Fase 4 -- Geração e Avaliação End-to-End

- Engenharia de prompts para geração domain-specific (terminologia Ascend, especificações de operadores)
- Seleção e ajuste de parâmetros do LLM (temperature, top-p, max tokens)
- Avaliação end-to-end: corretude das respostas, fidelidade ao contexto recuperado, acurácia de citaçõees
- Estudos de ablação isolando a contribuição de cada método pre-query vs. outros componentes do pipeline

### Fase 5 -- Documentação e Resultados

- Benchmark com comparações quantitativas entre todos os métodos pre-query

## 6. Conceitos-Chave do Domínio Ascend

Para leitores não familiarizados com o ecossistema Huawei Ascend, estes são os
componentes centrais referenciados ao longo da documentação:

- **ATC / OMG** -- Ascend Tensor Compiler / Offline Model Generator; converte modelos
  Caffe/TensorFlow para arquivos `.om` (DaVinci offline model).
- **AIPP** -- Modulo de Pre-processamento de IA executado em hardware (AI Core) para
  conversao de espaco de cor, recorte, padding e normalizacao de media/variancia antes
  da inferencia do modelo.
- **Matrix / OME** -- Framework de orquestracao de processos e engine de runtime para
  carregamento e execucao de grafos de modelos na memoria do dispositivo.
- **TE / TVM** -- DSL do Tensor Engine usando primitivas Python e TVM para gerar kernels
  customizados de AI Core (`.o`) e AI CPU (`.so`).
- **NC1HWC0 / FRACTAL_Z** -- Layouts de memoria tensorial 5D e fractal especificos do
  Ascend, otimizados para as unidades de computacao matricial do AI Core.
- **DaVinci AI Core** -- Unidade fundamental de computacao do Ascend 310, contendo
  pipelines de matriz, vetor e escalar para inferencia em precisao mista.

## 7. Como Executar o Pipeline

O projeto é dividido em dois notebooks principais que podem ser executados localmente ou no Google Colab:

### 1. Extração e Particionamento (`data_chunking.ipynb`)

Converte os 10 manuais em PDF (`data/raw/manuals/`) para Markdown estruturado em `data/processed/markdown/` utilizando `docling`, aplica o pós-processamento estrutural e executa o comparativo entre as três estratégias de chunking, exportando os artefatos validados (`.parquet` e `.json`) para `data/output/`.

### 2. Indexação Híbrida e Benchmark Pre-Query (`notebook.ipynb`)

1. **Servidor de Modelos Locais (Ollama):** instala o Ollama e realiza o pull dos modelos de embedding e geração/avaliação:

```bash
curl -fsSL https://ollama.com/install.sh | sh
ollama serve &
ollama pull nomic-embed-text
ollama pull qwen2.5:7b

```

2. **Dependências Python:** instala os pacotes necessários para o banco vetorial, tokenização e orquestração:

```bash
pip install chromadb openai tiktoken pandas pyarrow tqdm requests langchain-text-splitters

```

3. **Execução do Benchmark:**
* Particiona os 10 manuais com a **Estratégia 3 (Atomic Table-Aware)** e filtra ruídos de sumário (ToC).

* Constrói o índice híbrido combinando busca densa no **ChromaDB** (`nomic-embed-text` com prefixos assimétricos `search_document:` e `search_query:`) e busca léxica em memória via **Okapi BM25**, fundidos por Reciprocal Rank Fusion (RRF).

* Avalia as 20 questões técnicas (`results/questions.json`) nas variantes `baseline`, `stepback`, `multiquery`, `memory` e `combined` usando **LLM-as-a-Judge** (`qwen2.5:7b`, `temperature=0`) e exporta as métricas detalhadas em `results/`.

---

## 8. Resultados

### 8.1 Comparação das Estratégias de Chunking

A avaliação em amostra diagnóstica demonstrou que estratégias convencionais fragmentam tabelas de especificação, enquanto o particionamento atômico preserva a integridade estrutural:

| Estratégia | Chunks | Min (tokens) | Max (tokens) | Média (tokens) | Mediana (tokens) | Tabelas Preservadas |
| --- | --- | --- | --- | --- | --- | --- |
| `1_recursive` | 87 | 102 | 1.011 | 837 | 925 | 0 |
| `2_markdown_header` | 121 | 2 | 1.008 | 576 | 702 | 0 |
| `3_atomic_table_aware` | 245 | 22 | 4.415 | 325 | 102 | 33 |

Aplicada aos 10 manuais completos, a **`3_atomic_table_aware`** gerou **3.762 chunks brutos** e, após a filtragem automática de 35 chunks de sumário (ToC), consolidou **3.727 chunks finais** (sendo **704 chunks de tabelas intactas**, ou **18,9%** do índice) enriquecidos com *breadcrumbs* hierárquicos (`[Doc: <doc> > <secao>]`).

### 8.2 Configuração Experimental e Hiperparâmetros

Os experimentos finais de avaliação foram executados sobre uma suíte de **20 perguntas técnicas** distribuídas em 5 categorias (`direct`, `multi`, `paraphrase`, `followup` e `none` para recusa fora do domínio) com a seguinte configuração:

```python
GEN_MODEL         = "qwen2.5:7b"        # modelo gerador
JUDGE_MODEL       = "qwen2.5:7b"        # modelo avaliador (LLM-as-a-Judge, temp=0)
EMB_MODEL         = "nomic-embed-text"  # embeddings densos + BM25 (15.015 termos)
K                 = 4                   # chunks entregues ao gerador
N_RUNS            = 1                   # execucoes por variante
SEED_BASE         = 42                  # seed para reprodutibilidade
GEN_TEMP          = 0.5                 # temperatura da resposta final
REWRITE_TEMP      = 0.3                 # temperatura das chamadas de reescrita
N_VARIANTS        = 3                   # reformulacoes no Multi-Query
RRF_C             = 15                  # constante do Reciprocal Rank Fusion
MEMORY_DISTRACTOR = True                # injecao de historico distrator na variante memory

```

### 8.3 Desempenho das Variantes Pre-Query

Comparativo entre a fase preliminar (dados sintéticos) e o benchmark final sobre o corpus real após ajuste de hiperparâmetros e adição da variante combinada:

| Variante | Acurácia (Sintético) | LLM Calls | Tempo (Sintético) | Acurácia (Corpus Real) | LLM Calls | Tempo (Corpus Real) |
| --- | --- | --- | --- | --- | --- | --- |
| **baseline** | 70% | 1,0 | 15s | 80% (16/20) | 1,0 | 54s |
| **stepback** | 75% | 2,0 | 22s | 70% (14/20) | 2,0 | 49s |
| **multiquery** | 75% | 2,0 | 43s | 75% (15/20) | 2,0 | 58s |
| **memory** | 75% | 2,0 | 24s | 75% (15/20) | 2,0 | 51s |
| **combined** | -- | -- | -- | **90% (18/20)** | 3,1 | 57s |
