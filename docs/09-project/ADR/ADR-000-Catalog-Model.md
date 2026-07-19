# Catalog-Centric Domain Model

Status: ACCEPTED

Data: 18/07/2026

Autor: Software Architecture Team

Projeto: Deal Monitor

Versão: 1.0

---

# Objetivo

Este documento registra oficialmente a decisão arquitetural mais importante do projeto Deal Monitor.

A partir desta decisão, o sistema deixa de ser orientado por anúncios encontrados em lojas e passa a ser orientado por um catálogo universal de produtos.

Esta decisão substitui qualquer modelagem anterior baseada apenas na entidade Product.

---

# Contexto

Durante a definição da arquitetura inicial observou-se que diferentes lojas anunciam exatamente o mesmo produto utilizando:

• nomes diferentes

• URLs diferentes

• imagens diferentes

• códigos internos diferentes

• vendedores diferentes

• descrições diferentes

Apesar disso, para o usuário continua sendo exatamente o mesmo produto.

Exemplo

Amazon

Ryzen 7 9700X Processador AMD AM5

Kabum

Processador AMD Ryzen™ 7 9700X OEM

Terabyte

AMD Ryzen 9700X Socket AM5

Todos representam o mesmo produto.

Modelar cada anúncio como um Product produziria duplicação de dados e impediria comparações corretas entre lojas.

---

# Problema

A modelagem anterior possuía a seguinte estrutura.

Product

↓

PriceHistory

↓

Promotion

Essa abordagem cria diversos problemas.

Problema 1

O mesmo produto passa a existir várias vezes.

Problema 2

Não existe um conceito de catálogo.

Problema 3

Comparação entre lojas torna-se extremamente complexa.

Problema 4

Históricos ficam misturados.

Problema 5

Promoções deixam de representar uma loja específica.

Problema 6

Dashboard apresenta informações duplicadas.

Problema 7

Adicionar novos marketplaces aumenta exponencialmente a duplicação.

---

# Objetivos da Nova Modelagem

Criar um catálogo único.

Separar conceito de produto do conceito de anúncio.

Permitir comparação entre lojas.

Manter histórico independente por loja.

Permitir evolução futura para IA.

Eliminar duplicação de produtos.

---

# Decisão

O domínio passa oficialmente a possuir duas entidades distintas.

CatalogProduct

Representa o produto universal.

StoreProduct

Representa o anúncio encontrado em determinada loja.

Todo desenvolvimento futuro deverá seguir obrigatoriamente este modelo.

---

# Nova Estrutura

CatalogProduct

↓

StoreProduct

↓

PriceHistory

↓

Promotion

---

# Diagrama Geral

```text
                 CatalogProduct
                        │
        ┌───────────────┼───────────────┐
        │               │               │
        ▼               ▼               ▼
 AmazonListing     KabumListing    PichauListing
        │               │               │
        ▼               ▼               ▼
 PriceHistory     PriceHistory    PriceHistory
        │               │               │
        ▼               ▼               ▼
 Promotion        Promotion       Promotion
```

---

# Definição Oficial das Entidades

## CatalogProduct

Representa um produto do catálogo global.

Exemplos

Ryzen 7 9700X

RTX 5070

Samsung Odyssey G5

SSD KC3000 2TB

Ele nunca representa um anúncio.

Ele nunca representa uma URL.

Ele nunca representa um preço.

Ele nunca representa uma promoção.

---

## StoreProduct

Representa o mesmo produto dentro de uma loja.

Exemplo

CatalogProduct

Ryzen 7 9700X

↓

Amazon

↓

URL Amazon

↓

Imagem Amazon

↓

Disponibilidade Amazon

↓

Histórico Amazon

Outro exemplo

CatalogProduct

Ryzen 7 9700X

↓

Kabum

↓

URL Kabum

↓

Imagem Kabum

↓

Histórico Kabum

---

# Responsabilidades

CatalogProduct

Responsável por representar a identidade do produto.

StoreProduct

Responsável por representar um anúncio específico.

PriceHistory

Responsável por registrar snapshots de preço.

Promotion

Responsável por representar oportunidades detectadas.

---

# Introdução do Catalog Matcher

A arquitetura passa oficialmente a possuir um novo componente.

Catalog Matcher.

Este componente possui responsabilidade exclusiva.

Receber um StoreProduct.

Determinar se ele pertence a um CatalogProduct existente.

Caso pertença:

Associar.

Caso contrário:

Criar um novo CatalogProduct.

Nenhum outro componente poderá executar esta decisão.

---

# Estratégia do MVP

No MVP o Catalog Matcher utilizará regras determinísticas.

Ordem de comparação.

1

EAN

2

Manufacturer Code

3

Marca + Modelo

4

Marca + Modelo + Capacidade

5

Título Normalizado

Caso nenhuma regra seja suficiente será criado um novo CatalogProduct.

---

# Evolução Futura

O Catalog Matcher poderá futuramente utilizar:

Embeddings

LLMs

Machine Learning

Busca Vetorial

IA Generativa

Sem alterar qualquer outra parte do sistema.

Apenas o Catalog Matcher será substituído.

---

# Fluxo Oficial

Scheduler

↓

Connector

↓

Parser

↓

Normalizer

↓

StoreProduct

↓

Catalog Matcher

↓

CatalogProduct

↓

PriceHistory

↓

Promotion Engine

↓

Dashboard

---

# Benefícios

Produto único.

Histórico correto.

Comparação entre lojas.

Promoções independentes.

Escalabilidade.

Baixo acoplamento.

Alta coesão.

Separação de responsabilidades.

Preparação para IA.

---

# Impacto Arquitetural

Os seguintes documentos deverão ser atualizados.

ARCHITECTURE.md

CLEAN_ARCHITECTURE.md

PROJECT_STRUCTURE.md

DOMAIN_MODEL.md

Todos passam a considerar CatalogProduct como entidade principal.

---

# Decisões Rejeitadas

Alternativa 1

Utilizar Product diretamente.

Motivo

Duplicação.

Alternativa 2

Criar histórico por Product.

Motivo

Perda da origem do preço.

Alternativa 3

Uma tabela para cada loja.

Motivo

Escalabilidade ruim.

Alternativa 4

Comparação apenas pelo nome.

Motivo

Alta taxa de falsos positivos.

---

# Consequências

Positivas

Arquitetura preparada para crescimento.

Comparação correta entre lojas.

Código mais organizado.

Maior desacoplamento.

Preparação para IA.

Negativas

Modelagem inicial ligeiramente mais complexa.

Necessidade de Catalog Matcher.

Maior quantidade de entidades.

A equipe considera que os benefícios superam amplamente os custos.

---

# Regras Obrigatórias

Todo preço pertence a StoreProduct.

Todo histórico pertence a StoreProduct.

Todo anúncio pertence a um CatalogProduct.

Todo CatalogProduct pode possuir vários StoreProducts.

Connectors nunca criam CatalogProducts diretamente.

Catalog Matcher é o único responsável pela associação entre anúncios e catálogo.

---

# Aprovação

Esta ADR entra em vigor imediatamente.

Ela substitui oficialmente a modelagem anterior baseada apenas na entidade Product.

Todos os documentos produzidos após esta data deverão seguir obrigatoriamente esta decisão.

Fim do Documento.