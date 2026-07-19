# DOMAIN_MODEL_v2

Projeto: Deal Monitor

Versão: 1.0

Status: Documento Oficial

Dependências

- ARCHITECTURE.md
- CLEAN_ARCHITECTURE.md
- DATABASE_SCHEMA.md

---

# Objetivo

Este documento define o Modelo de Domínio oficial do Deal Monitor.

O Modelo de Domínio representa os conceitos de negócio da aplicação.

Ele é independente de:

- banco de dados;
- framework;
- API REST;
- Frontend;
- Playwright;
- PostgreSQL.

Nenhuma decisão técnica deverá alterar o domínio.

---

# Linguagem Ubíqua

Todos os desenvolvedores, documentação e código deverão utilizar exatamente os mesmos termos.

Termos oficiais:

Store

Categoria monitorada.

Connector

Implementação responsável por coletar dados de uma Store.

Catalog Product

Produto único conhecido pelo sistema.

Store Product

Representação de um produto em uma loja específica.

Price Snapshot

Preço coletado em determinado momento.

Promotion

Evento indicando oportunidade de compra.

Scraping Job

Execução de coleta.

History

Histórico de preços.

Configuration

Parâmetros configuráveis do sistema.

---

# Agregados

O sistema possui os seguintes Aggregates.

Store

Category

CatalogProduct

StoreProduct

Promotion

ScrapingJob

Settings

Nenhum Aggregate deverá acessar outro Aggregate diretamente.

Toda comunicação ocorre por Use Cases.

---

# Entidade Store

Representa um e-commerce monitorado.

Exemplos:

Amazon

Kabum

Pichau

Terabyte

Mercado Livre

---

Responsabilidades

- possuir configuração;
- possuir status;
- informar disponibilidade;
- identificar Connector.

Nunca conhece produtos.

---

Invariantes

Nome único.

Possui Connector associado.

Possui Status válido.

Não pode ser removida caso possua histórico.

---

Estados

Enabled

Disabled

Degraded

Unavailable

---

# Entidade Category

Representa um grupo monitorado.

Exemplos

Notebook

SSD

Monitor

GPU

Mouse

---

Responsabilidades

Organizar produtos.

Permitir filtros.

Controlar monitoramento.

---

Invariantes

Nome único.

Status obrigatório.

---

# Aggregate CatalogProduct

Representa um produto único.

Exemplo

Notebook Dell G15 Ryzen 7 RTX 4060.

Independente da loja.

---

Responsabilidades

Representar identidade.

Agrupar produtos equivalentes.

Manter histórico consolidado.

---

Nunca conhece:

Scraping

Connector

Browser

Scheduler

---

Identidade

UUID

---

Atributos principais

Nome

Marca

Modelo

Categoria

Imagem

Slug

---

Invariantes

Nunca existir duplicado.

Nome obrigatório.

Categoria obrigatória.

---

# Aggregate StoreProduct

Representa um produto vendido por uma loja.

Relacionamento

Store

↓

StoreProduct

↓

CatalogProduct

---

Responsabilidades

URL.

Disponibilidade.

Preço atual.

Frete.

Parcelamento.

Vendedor.

---

Cada Store pode possuir milhares de StoreProducts.

---

Invariantes

Sempre pertence a:

uma Store

e

um CatalogProduct.

---

# Aggregate PriceSnapshot

Representa uma coleta.

Nunca é atualizado.

Sempre é criado.

---

Atributos

Preço.

Data.

Disponibilidade.

Frete.

Parcelamento.

Origem.

---

É imutável.

---

Relacionamento

CatalogProduct

↓

StoreProduct

↓

PriceSnapshot

---

O histórico nunca deverá ser alterado.

# Aggregate Promotion

## Objetivo

Promotion representa uma oportunidade de compra detectada pelo sistema.

Uma Promotion nunca é criada manualmente.

Ela sempre é resultado do processamento do Promotion Engine.

---

## Responsabilidades

- representar uma oportunidade de compra;
- armazenar a condição encontrada;
- manter o estado da promoção;
- preservar a rastreabilidade até o PriceSnapshot que a originou.

---

## Não é responsabilidade

Promotion nunca:

- calcula descontos;
- consulta preços;
- executa scraping;
- envia notificações.

Essas responsabilidades pertencem a outros componentes.

---

## Relacionamentos

Promotion

↓

CatalogProduct

↓

StoreProduct

↓

PriceSnapshot

Uma Promotion sempre referencia exatamente um PriceSnapshot.

---

## Atributos principais

Id

CatalogProductId

StoreProductId

PriceSnapshotId

PromotionType

Score

Status

CreatedAt

ExpiresAt (opcional)

---

## Estados

Detected

Active

Dismissed

Expired

Invalid

---

## Invariantes

Uma Promotion:

- sempre possui um Product válido;
- sempre possui um Snapshot válido;
- nunca existe sem origem conhecida;
- nunca altera dados históricos.

---

# Aggregate ScrapingJob

## Objetivo

Representa uma execução de scraping.

Toda coleta realizada pelo sistema gera exatamente um ScrapingJob.

---

## Responsabilidades

- registrar início e término;
- armazenar estatísticas;
- controlar estado da execução.

---

## Relacionamentos

Store

↓

Category

↓

ScrapingJob

---

## Estados

Pending

Running

Completed

Failed

Cancelled

Timeout

---

## Atributos

Id

StoreId

CategoryId

StartedAt

FinishedAt

Status

Duration

ProductsFound

PromotionsDetected

ErrorMessage

---

## Invariantes

Um Job:

- possui apenas um estado ativo;
- não pode retornar para Running após Completed;
- FinishedAt sempre é maior que StartedAt.

---

# Aggregate Configuration

## Objetivo

Representa as configurações persistidas da aplicação.

---

## Responsabilidades

- armazenar parâmetros;
- disponibilizar configurações ao sistema.

---

## Exemplos

Intervalo do Scheduler

Timeout

Concorrência

Headless

Retry

User-Agent

Log Level

---

## Regras

Todas as configurações devem possuir:

- valor padrão;
- validação;
- descrição.

Nenhuma configuração pode ser lida diretamente pelos Use Cases.

Sempre utilizar ConfigurationProvider.

---

# Relacionamentos Gerais

Store

↓

StoreProduct

↓

CatalogProduct

↓

PriceSnapshot

↓

Promotion

Category

↓

CatalogProduct

ScrapingJob

↓

Store

↓

Category

Configuration

↓

Application

---

# Value Objects

O domínio utiliza Value Objects para representar conceitos imutáveis.

Exemplos:

Money

Percentage

Url

ProductTitle

Brand

CategoryName

StoreName

Duration

RetryPolicy

ExecutionStatus

Todos os Value Objects:

- são imutáveis;
- validam seus próprios dados;
- implementam igualdade por valor.

---

# Regras Gerais do Domínio

- O domínio não conhece banco de dados.
- O domínio não conhece FastAPI.
- O domínio não conhece Playwright.
- O domínio não conhece PostgreSQL.
- O domínio não conhece React.

O domínio representa exclusivamente regras de negócio.

---

# AI IMPLEMENTATION NOTES

Ao implementar o domínio, o Claude Code deverá seguir obrigatoriamente as seguintes regras:

- Nunca utilizar entidades como modelos de banco de dados (ORM).
- Nunca reutilizar DTOs da API como entidades de domínio.
- Não adicionar métodos de infraestrutura às entidades.
- Manter entidades focadas em comportamento e invariantes.
- Toda alteração em uma entidade deve preservar suas regras de negócio.
- Sempre criar testes unitários para cada Aggregate Root.

Fim da Parte 2.

# Diagrama Conceitual do Domínio

O modelo abaixo representa os relacionamentos lógicos entre os Aggregates.

```text
                         +----------------+
                         |   Category     |
                         +----------------+
                                 |
                                 |
                                 |
                         +----------------+
                         | CatalogProduct |
                         +----------------+
                                 |
                     1           |           N
                                 |
                         +----------------+
                         | StoreProduct   |
                         +----------------+
                           /            \
                          /              \
                         /                \
                        /                  \
          +-------------------+      +------------------+
          | PriceSnapshot     |      | Store            |
          +-------------------+      +------------------+
                     |
                     |
                     |
             +----------------+
             | Promotion      |
             +----------------+

Store
    |
    |
    +----------------------+
                           |
                     ScrapingJob

Configuration
       |
       |
       +----> Application
```

O diagrama representa dependências conceituais, não dependências de implementação.

---

# Ownership dos Aggregates

Cada Aggregate é responsável exclusivamente pelos seus próprios invariantes.

Nenhum Aggregate altera diretamente outro Aggregate.

Exemplo:

CatalogProduct

↓

não altera

↓

Promotion

Caso seja necessário modificar múltiplos Aggregates, essa responsabilidade pertence ao Use Case da camada Application.

---

# Regras de Consistência

O domínio deve manter as seguintes garantias.

## Store

- Nome único.
- Connector obrigatório.
- Status válido.
- Não pode existir sem configuração.

---

## Category

- Nome único.
- Sempre habilitada ou desabilitada.
- Nunca possuir estado indefinido.

---

## CatalogProduct

- Não pode existir duplicado.
- Deve possuir Categoria.
- Deve possuir Nome.
- Deve possuir Slug único.

---

## StoreProduct

- Deve referenciar exatamente uma Store.
- Deve referenciar exatamente um CatalogProduct.
- Não pode existir URL vazia.

---

## PriceSnapshot

- Imutável.
- Nunca atualizado.
- Apenas criado.

---

## Promotion

- Sempre originada de um PriceSnapshot.
- Nunca criada manualmente.
- Nunca altera histórico.

---

## ScrapingJob

- Apenas um estado ativo.
- Nunca retornar para Running após conclusão.
- Deve registrar horário de início.

---

## Configuration

- Chaves únicas.
- Valores validados.
- Valor padrão obrigatório.

---

# Eventos de Domínio

O domínio publica eventos.

Esses eventos representam fatos de negócio.

Nunca comandos.

Eventos iniciais:

ProductDiscovered

PriceChanged

PriceSnapshotCreated

PromotionDetected

PromotionDismissed

StoreUnavailable

StoreRecovered

ScrapingStarted

ScrapingCompleted

ScrapingFailed

ConfigurationChanged

---

# Fluxo Conceitual

Store

↓

Connector

↓

StoreProduct

↓

PriceSnapshot

↓

Promotion Engine

↓

Promotion

↓

Frontend

Esse fluxo representa o caminho natural das informações.

---

# Limites do Domínio

O domínio NÃO conhece:

- SQLAlchemy
- PostgreSQL
- FastAPI
- React
- Axios
- Playwright
- TailwindCSS
- TanStack Query
- Scheduler
- Browser

Esses componentes pertencem a outras camadas da arquitetura.

---

# Evolução do Domínio

Novos Aggregates poderão ser adicionados no futuro.

Exemplos:

Coupon

Cashback

MarketplaceSeller

Notification

PriceForecast

Esses novos conceitos não devem exigir alterações estruturais nos Aggregates existentes.

---

# Convenções de Modelagem

As entidades devem:

- possuir identidade estável;
- proteger seus invariantes;
- evitar setters públicos desnecessários;
- encapsular comportamento;
- expor intenção por meio de métodos claros.

Evitar modelos anêmicos.

---

# Critérios para Novas Entidades

Uma nova entidade somente poderá ser criada quando:

- possuir identidade própria;
- possuir ciclo de vida independente;
- possuir regras de negócio relevantes.

Caso contrário, deve ser modelada como Value Object.

---

# AI IMPLEMENTATION NOTES

Ao implementar o domínio, o Claude Code deverá observar obrigatoriamente:

- Não utilizar herança entre Aggregates.
- Preferir composição.
- Utilizar dataclasses apenas para Value Objects quando apropriado.
- Não adicionar lógica de persistência.
- Não adicionar decorators do ORM nas entidades de domínio.
- Não criar métodos utilitários genéricos.
- Não utilizar entidades como DTOs.
- Sempre preservar invariantes.
- Sempre escrever testes unitários antes da integração com infraestrutura.

---

# Checklist

Antes de considerar o domínio concluído:

[ ] Todas as entidades possuem identidade definida.

[ ] Todos os Aggregates possuem invariantes documentados.

[ ] Todos os relacionamentos estão descritos.

[ ] Todos os eventos de domínio foram identificados.

[ ] Nenhuma entidade depende de infraestrutura.

[ ] Nenhuma entidade depende de framework.

[ ] O domínio representa exclusivamente conceitos de negócio.

---

# Critérios de Aceitação

O modelo de domínio será considerado conforme quando:

[ ] Todos os casos de uso puderem operar utilizando apenas os Aggregates definidos.

[ ] Nenhuma regra de negócio depender de detalhes de infraestrutura.

[ ] O domínio permanecer estável mesmo com mudanças na API, banco de dados ou interface.

[ ] A linguagem ubíqua for utilizada de maneira consistente em toda a documentação e implementação.

---

# Encerramento

O DOMAIN_MODEL.md estabelece a definição oficial dos conceitos de negócio do Deal Monitor.

Todos os documentos subsequentes (API, Promotion Engine, Scheduler e demais especificações) deverão utilizar este modelo como referência única para nomenclatura, responsabilidades e relacionamentos.

Qualquer alteração nas entidades, invariantes ou eventos deverá ser registrada por meio de uma ADR antes da implementação.

Fim do Documento.