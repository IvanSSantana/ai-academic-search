# Sistema de Análise Crítica de Discursos com Base Científica (PubMed + Agno)

Este projeto implementa um **sistema inteligente de análise crítica de discursos** relacionados a esteroides anabólicos androgênicos (AAS/EAA) e suplementos, utilizando:

* Modelos de linguagem (LLM via Ollama)
* Integração com a API científica do PubMed
* Arquitetura baseada em agentes (Agno)
* Estratégia de múltiplas buscas (query expansion)

O objetivo é **identificar desinformação, falácias e alegações sem base científica**, fundamentando a análise em evidências biomédicas.

---

# Visão Geral da Arquitetura

```
Usuário
  ↓
Agente Principal (LLM)
  ↓
Tool: pesquisa_pubmed
  ↓
Agente de Palavras-chave (LLM)
  ↓
PubMed API (ESearch + EFetch)
  ↓
Artigos científicos
  ↓
Análise crítica estruturada
```

---

# Tecnologias Utilizadas

* Python 3.10+
* requests (HTTP client)
* xml.etree.ElementTree (parsing XML)
* Agno (framework de agentes)
* Ollama (modelo LLM local – llama3.1)
* PubMed E-utilities API

---

# Integração com PubMed (E-utilities)

O sistema utiliza dois endpoints principais:

## 1. ESearch

Responsável por buscar IDs (PMIDs) dos artigos.

```
GET /esearch.fcgi
```

### Parâmetros principais:

| Parâmetro | Descrição                   |
| --------- | --------------------------- |
| db        | Base de dados (`pubmed`)    |
| term      | Query de busca              |
| retmax    | Número máximo de resultados |
| retmode   | Formato da resposta (json)  |

---

## 2. EFetch

Responsável por buscar os dados completos dos artigos.

```
GET /efetch.fcgi
```

### Retorna:

* Título
* Autores
* Data de publicação
* Resumo (abstract)
* PMID

---

## Fluxo completo

1. ESearch → retorna lista de PMIDs
2. EFetch → retorna conteúdo completo em XML
3. Parsing XML → estrutura dados em dicionários

---

# Agente de Palavras-chave (Query Intelligence)

## Objetivo

Transformar linguagem natural em **queries biomédicas otimizadas**.

---

## Estratégia

* Usa um agente LLM especializado
* Gera **até 2 palavras-chave em inglês**
* Evita repetição via contexto (`palavras_excluidas`)
* Foco em termos científicos relevantes

---

## Exemplo

Input:

```
"Uso de esteroides melhora performance?"
```

Output:

```
"anabolic steroids"
```

---

## Características importantes

* Temperatura baixa → maior consistência
* Prompt especializado (farmacologia + lógica)
* Sanitização da saída (regex)

---

# Tool: `pesquisa_pubmed`

## Função

Executa buscas científicas automatizadas e retorna resultados formatados.

---

## Estratégia principal: **Multi-query**

Para cada pergunta:

1. Gera **3 conjuntos diferentes de palavras-chave**
2. Executa 3 buscas independentes
3. Combina resultados

---

## Pipeline interno

```
Pergunta
 ↓
[Query 1] → PubMed
[Query 2] → PubMed
[Query 3] → PubMed
 ↓
Merge resultados
 ↓
Remoção de duplicatas (PMID)
 ↓
Formatação Markdown
```

---

## Saída

Cada artigo contém:

* Título
* Autores
* Data
* Resumo
* Link direto

---

# Agente Principal

## Função

Executa análise crítica baseada em evidência científica.

---

## Fluxo de decisão

1. Chama a tool `pesquisa_pubmed`
2. Analisa o discurso do usuário
3. Compara com literatura científica
4. Gera relatório estruturado

---

## Tipos de análise

✔ Classificação do discurso:

* Científico
* Comercial
* Opinativo

✔ Identificação de falácias:

* Apelo à autoridade
* Evidência anedótica
* Generalização indevida

✔ Avaliação científica:

* Evidência clínica
* Riscos conhecidos
* Consistência com literatura

---

# Regras críticas do sistema

O sistema considera automaticamente problemático quando:

* Uso de esteroides fora de contexto médico
* Promessas sem evidência científica
* Minimização de riscos

---

## Riscos considerados

* Problemas cardiovasculares
* Alterações hormonais
* Dependência

---

# Formato da Resposta

A saída do agente segue estrutura:

```
1. Classificação do discurso
2. Falácias identificadas
3. Evidências científicas (com links PubMed)
4. Conclusão crítica
5. Referências
```
---

# 🧠 Conceitos-chave do Projeto

## 🔹 RAG (Retrieval-Augmented Generation)

* Busca dados externos (PubMed)
* Usa LLM para interpretação

---

## 🔹 Query Expansion

* Múltiplas buscas para maior cobertura

---

## 🔹 Multi-Agent System

* Agente principal (análise)
* Agente secundário (palavras-chave)

---

## 🔹 Tool Calling

* Integração explícita entre LLM e funções externas

---

# 📌 Conclusão

Este projeto implementa um sistema robusto de:

* **Análise crítica automatizada**
* **Busca científica estruturada**
* **Detecção de desinformação**

Combinando:

* LLMs
* APIs científicas
* Arquitetura de agentes

O resultado é um **assistente especializado em validação científica no contexto de fitness e farmacologia**, com foco em rigor, ética e clareza.

---
