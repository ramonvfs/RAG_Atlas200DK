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
|---|---:|---:|:---:|
| ATCToolInstructions | 37.719 | 160.478 | OK |
| ApplicationDevelopmentGuide (MindStudio) | 25.311 | 108.117 | OK |
| Atlas 200 AI Accelerator Module App Software Dev Guide (Models 3000) | 246.233 | 1.060.979 | OK |
| EnvironmentDeploymentGuide | 30.161 | 117.544 | OK |
| Huawei Atlas 200 DK Technical White Paper (Model 3000) | 6.228 | 26.385 | OK |
| MindStudio Installation Guide (Ubuntu x86) | 19.606 | 88.690 | OK |
| TBE Custom Operator Development Guide (CLI) | 19.123 | 84.728 | OK |
| TensorFlow Network Model Porting and Training Guide | 76.327 | 353.217 | OK |
| UserGuide 2020 | 20.524 | 79.970 | OK |
| UserGuide 2021 | 29.957 | 117.392 | OK |
| **Total** | **511.189** | **2.197.500** | |

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

### Fase 1 -- Ingestão de Dados e Chunking (atual)

- Extração de PDF para Markdown com parsing layout-aware
- Pipeline de pós-processamento (remoção de cabeçalhos/rodapés, reconexão de hífens, normalização de unidades)
- Estatísticas e validação do corpus
- Comparação de estratégias de chunking: splitting recursivo vs. baseado em cabeçalhos vs. atômico com preservação de tabelas
- Diagnósticos de qualidade dos chunks (distribuição de tokens, auditoria de integridade de tabelas, legibilidade autônoma)
- Seleção da configuração final de chunking e exportação dos payloads validados (JSON)

### Fase 2 -- Indexacao e Baseline de Retrieval

- Avaliação de modelos de embedding (candidatos: OpenAI `text-embedding-3-small`, alternativas open-source)
- Configuração do vector store (FAISS ou ChromaDB)
- Baseline de retrieval denso com abordagem de query única
- Avaliação de retrieval: precision\@k, recall\@k, MRR sobre um conjunto de perguntas curado manualmente
- Comparação com retrieval esparso (BM25) e retrieval híbrido (fusão denso + esparso)

### Fase 3 -- Experimentos com Metodos Pre-Query (contribuição principal da pesquisa)

- **Step-Back Prompting**: implementar cadeia de abstração, avaliar ganho de recall em perguntas conceituais (ex.: "Como a arquitetura DaVinci lida com operacoes matriciais?")
- **Multi-Query / RAG-Fusion**: decomposição de query com LLM, retrieval paralelo, re-ranqueamento por Reciprocal Rank Fusion, medir ganhos de diversidade e cobertura
- **RAG com Memoria Conversacional**: memória por janela deslizante e por sumarização, avaliar sequências de perguntas multi-turno com resolução de correferências
- **Estratégias combinadas**: testar combinações em pares e triplas (ex.: Step-Back + RAG-Fusion, Memoria + Multi-Query)
- Dashboard de comparação lado a lado: acurácia, taxa de alucinação, relevância de retrieval, latência

### Fase 4 -- Geração e Avaliação End-to-End

- Engenharia de prompts para geração domain-specific (terminologia Ascend, especificações de operadores)
- Seleção e ajuste de parâmetros do LLM (temperature, top-p, max tokens)
- Avaliação end-to-end: corretude das respostas, fidelidade ao contexto recuperado, acurácia de citaçõees
- Estudos de ablação isolando a contribuição de cada método pre-query vs. outros componentes do pipeline

### Fase 5 -- Documentação e Resultados

- Relatório final de benchmark com comparações quantitativas entre todos os métodos pre-query

## 6. Conceitos-Chave do Dominio Ascend

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

(Pendente)
