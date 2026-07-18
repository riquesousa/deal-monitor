# CLEAN_ARCHITECTURE

Projeto: Deal Monitor

Versão: 2.0

Status: Documento Oficial

Dependência:

ARCHITECTURE.md

ADR-000

---

# Objetivo

Este documento define como os princípios da Clean Architecture serão aplicados no Deal Monitor.

A arquitetura deverá permanecer independente de frameworks, banco de dados, mecanismos de scraping e interface gráfica.

Toda decisão técnica deve preservar o domínio da aplicação.

---

# O que é Clean Architecture

O objetivo da Clean Architecture é garantir que o núcleo da aplicação permaneça independente de detalhes externos.

No Deal Monitor, isso significa que:

- trocar SQLite por PostgreSQL não altera o domínio;
- trocar React por Vue não altera o domínio;
- trocar Playwright por Selenium não altera o domínio;
- trocar FastAPI por Flask não altera o domínio.

O domínio representa o negócio.

Todo o restante representa tecnologia.

---

# Objetivos

Separar responsabilidades.

Reduzir acoplamento.

Aumentar testabilidade.

Facilitar manutenção.

Permitir evolução incremental.

Facilitar implementação pelo Claude Code.

---

# Estrutura Oficial

```text
                  Frameworks

                       │

               Interface Adapters

                       │

               Application Layer

                       │

                  Domain Layer
```

O fluxo de dependências ocorre sempre em direção ao centro.

Nunca no sentido contrário.

---

# Camadas Oficiais

O sistema possui quatro camadas principais.

Domain

Application

Interface Adapters

Frameworks

---

# Domain Layer

## Objetivo

Representar o negócio.

É a única camada que conhece as regras fundamentais do sistema.

---

## Componentes

CatalogProduct

StoreProduct

Store

Category

Promotion

PriceHistory

Value Objects

Enums

Domain Services

---

## Responsabilidades

Representar conceitos.

Garantir consistência.

Definir identidade.

Aplicar invariantes.

---

## Não conhece

SQLite.

FastAPI.

React.

Playwright.

BeautifulSoup.

SQLAlchemy.

HTTP.

JSON.

HTML.

CSS.

JavaScript.

---

## Regra Fundamental

O Domain nunca importa código de outras camadas.

Fim do capítulo 1.

---

# Capítulo 2 — Domain Layer

---

# Objetivo

O Domain Layer representa o núcleo do sistema.

Todas as regras de negócio fundamentais pertencem exclusivamente a esta camada.

Ela deve permanecer totalmente independente de qualquer tecnologia utilizada pelo projeto.

O domínio deve poder ser reutilizado em qualquer aplicação sem modificações.

---

# Princípio Fundamental

O domínio representa o negócio.

Não representa:

- banco de dados;
- API;
- frontend;
- scraping;
- framework;
- navegador;
- HTML.

Toda tecnologia é considerada um detalhe de implementação.

---

# Responsabilidades

O Domain Layer é responsável por:

- representar entidades do negócio;
- garantir consistência dos dados;
- definir regras invariantes;
- representar relacionamentos;
- encapsular comportamentos essenciais;
- proteger o estado interno das entidades.

---

# Não é responsabilidade do domínio

O domínio nunca deve:

- acessar banco de dados;
- executar consultas SQL;
- fazer requisições HTTP;
- interpretar HTML;
- consumir APIs externas;
- renderizar interface;
- acessar arquivos;
- utilizar Playwright;
- utilizar FastAPI;
- utilizar React.

---

# Estrutura Oficial

```text
domain/

├── entities/
│
├── value_objects/
│
├── enums/
│
├── services/
│
├── exceptions/
│
├── specifications/
│
└── interfaces/
```

---

# Entidades

As entidades representam conceitos centrais do negócio.

Entidades oficiais do MVP:

- CatalogProduct
- StoreProduct
- Store
- Category
- PriceHistory
- Promotion
- ScrapingJob

Cada entidade possui identidade própria.

---

# CatalogProduct

Representa um produto universal.

Nunca representa um anúncio.

Nunca representa uma loja.

Nunca representa um preço.

Exemplo:

```
AMD Ryzen 7 9700X
```

Independentemente da loja onde é vendido.

---

# StoreProduct

Representa um anúncio específico de uma loja.

Cada StoreProduct pertence exatamente a:

- um CatalogProduct;
- uma Store.

Exemplos:

Amazon → Ryzen 7 9700X

Kabum → Ryzen 7 9700X

Terabyte → Ryzen 7 9700X

São anúncios diferentes para o mesmo produto.

---

# Store

Representa um e-commerce monitorado.

Exemplos:

Amazon

Kabum

Pichau

Terabyte

Mercado Livre

A Store contém apenas informações institucionais da loja.

Nunca contém lógica de scraping.

---

# Category

Representa categorias monitoradas.

Exemplos:

Processadores

Placas de Vídeo

Memórias

SSD

Notebook

Monitor

Gabinete

Mouse

Teclado

---

# PriceHistory

Representa um snapshot histórico.

Cada alteração gera um novo registro.

Nunca existe atualização de registros históricos.

A entidade é imutável.

---

# Promotion

Representa uma oportunidade detectada.

A Promotion sempre pertence a um StoreProduct.

Nunca ao CatalogProduct.

---

# ScrapingJob

Representa uma execução do pipeline.

Permite:

- auditoria;
- monitoramento;
- métricas;
- recuperação de falhas.

---

# Value Objects

Value Objects não possuem identidade.

São definidos apenas por seus valores.

Exemplos:

Money

Percentage

NormalizedTitle

ProductCode

PriceVariation

ExecutionStatistics

RetryPolicy

---

# Regras

Value Objects:

- são imutáveis;
- não possuem ID;
- podem ser compartilhados;
- devem validar seus próprios valores.

---

# Domain Services

Existem regras que não pertencem a uma única entidade.

Essas regras ficam em Domain Services.

Exemplos:

CatalogMatchingService

PromotionCalculationService

PriceVariationService

SimilarityService

NormalizationRules

---

# Quando utilizar um Domain Service

Sempre que:

- duas ou mais entidades participarem da regra;
- a lógica não pertencer naturalmente a apenas uma entidade;
- houver necessidade de reutilização.

---

# Specifications

Specifications representam regras reutilizáveis.

Exemplos:

IsPromotionCandidate

HasHistoricalPrice

BelongsToCategory

IsAvailable

PriceBelowAverage

---

# Exceptions

O domínio define suas próprias exceções.

Exemplos:

InvalidPriceException

InvalidCategoryException

CatalogMatchException

InvalidPromotionException

ProductAlreadyExistsException

---

# Interfaces

O domínio nunca conhece implementações.

Ele define contratos.

Exemplos:

CatalogRepository

StoreRepository

PriceHistoryRepository

PromotionRepository

SchedulerRepository

As implementações ficam fora do domínio.

---

# Regras de Dependência

O domínio nunca importa:

FastAPI

SQLAlchemy

Playwright

Requests

BeautifulSoup

SQLite

React

Tailwind

HTTPX

JSON

---

# Regras de Modelagem

As entidades devem:

- proteger seus estados internos;
- evitar atributos públicos mutáveis;
- manter consistência após qualquer operação;
- impedir estados inválidos.

---

# Comunicação

O Domain Layer não conversa diretamente com:

Infrastructure

Frameworks

Interface Adapters

Ele apenas recebe chamadas da Application Layer.

---

# Benefícios

Esta separação permite:

- testes extremamente rápidos;
- domínio independente;
- substituição de frameworks;
- evolução da infraestrutura sem impacto no negócio;
- reutilização do núcleo da aplicação.

---

# Regras para o Claude Code

Ao implementar qualquer classe do domínio:

- Não importar frameworks.
- Não criar SQL.
- Não criar endpoints.
- Não utilizar decorators do FastAPI.
- Não acessar arquivos.
- Não utilizar Playwright.
- Não utilizar BeautifulSoup.
- Não utilizar APIs externas.
- Não realizar logging.
- Não utilizar variáveis globais.

Cada entidade deve representar apenas conceitos do negócio.

---

# Critérios de Aceitação

O Domain Layer será considerado correto quando:

[ ] Não possuir dependência de frameworks.

[ ] Representar corretamente o negócio.

[ ] Ser totalmente testável em isolamento.

[ ] Permitir reutilização em outro projeto.

[ ] Permanecer independente da infraestrutura.

Fim do Capítulo 2.

---

# Capítulo 3 — Application Layer

---

# Objetivo

A Application Layer implementa os casos de uso do sistema.

Ela coordena entidades do domínio, serviços de domínio e repositórios para executar funcionalidades completas da aplicação.

Esta camada representa a orquestração do sistema.

Ela não representa regras fundamentais do negócio.

---

# Responsabilidade Principal

Receber uma solicitação.

↓

Executar um caso de uso.

↓

Coordenar Domain.

↓

Persistir alterações.

↓

Retornar resultado.

A Application Layer nunca implementa detalhes de infraestrutura.

---

# Estrutura Oficial

application/

├── use_cases/
│
├── services/
│
├── dto/
│
├── commands/
│
├── queries/
│
├── mappers/
│
├── validators/
│
└── interfaces/

---

# Separação entre Domain e Application

Domain responde:

"O que é um produto?"

Application responde:

"Como pesquisar produtos?"

---

Domain responde:

"O que é uma promoção?"

Application responde:

"Como atualizar promoções?"

---

Domain responde:

"Como representar um histórico?"

Application responde:

"Como consultar um histórico?"

---

# Casos de Uso

Todo comportamento do sistema deve existir como um caso de uso.

Exemplos

SearchProductsUseCase

UpdateCatalogUseCase

RunScrapingUseCase

CalculatePromotionsUseCase

CompareStoresUseCase

GetPriceHistoryUseCase

CreateCategoryUseCase

UpdateConfigurationUseCase

---

# Organização

Um caso de uso deve possuir apenas uma responsabilidade.

Exemplo

SearchProductsUseCase

Responsável apenas por pesquisar produtos.

Nunca atualizar preços.

Nunca executar scraping.

Nunca gerar promoções.

---

# Services

Services representam grupos de casos de uso relacionados.

Exemplos

CatalogService

StoreService

HistoryService

PromotionService

ConfigurationService

SchedulerService

---

# Responsabilidades dos Services

Coordenar Use Cases.

Validar fluxo.

Gerenciar transações.

Invocar Domain Services.

Consultar Repositories.

Retornar DTOs.

---

# Não pertence aos Services

SQL.

HTML.

Playwright.

BeautifulSoup.

FastAPI.

React.

Componentes visuais.

---

# Fluxo Oficial

Frontend

↓

Controller

↓

Application Service

↓

Use Case

↓

Domain

↓

Repository

↓

Banco

↓

Resposta

---

# DTOs

DTOs representam dados trafegados entre camadas.

Nunca utilizar entidades diretamente na API.

Tipos de DTO

Request DTO

Response DTO

Internal DTO

Event DTO

---

# Exemplo

SearchProductRequest

↓

SearchProductsUseCase

↓

SearchProductResponse

---

# Commands

Commands representam intenções de alteração.

Exemplos

CreateProductCommand

UpdateCategoryCommand

RunScrapingCommand

SavePromotionCommand

Commands modificam estado.

---

# Queries

Queries representam consultas.

Nunca modificam estado.

Exemplos

SearchProductsQuery

FindStoreQuery

GetHistoryQuery

ListCategoriesQuery

---

# CQRS Simplificado

No MVP adotaremos uma separação lógica entre Commands e Queries.

Sem filas.

Sem Event Sourcing.

Sem microserviços.

Apenas organização de código.

---

# Mappers

Responsáveis por converter objetos.

Exemplos

Entity

↓

Response DTO

Command

↓

Entity

ParsedProduct

↓

NormalizedProduct

Nunca colocar lógica de negócio nos Mappers.

---

# Validators

Responsáveis por validar entradas da aplicação.

Exemplos

Filtro válido.

Categoria existente.

Preço mínimo.

Preço máximo.

Datas.

Paginação.

Nunca validar regras do domínio aqui.

---

# Transações

Toda operação que altera dados deve ser transacional.

Exemplo

Criar StoreProduct

↓

Criar PriceHistory

↓

Criar Promotion

↓

Commit

Caso qualquer etapa falhe:

Rollback completo.

---

# Comunicação com Domain

Application conhece Domain.

Domain nunca conhece Application.

---

# Comunicação com Infrastructure

Application depende apenas de interfaces.

Nunca de implementações concretas.

Exemplo

CatalogRepository

✔ Correto

SQLiteCatalogRepository

✘ Incorreto

---

# Comunicação com API

Controllers apenas encaminham chamadas.

Nunca implementam lógica.

Fluxo correto

Controller

↓

Service

↓

UseCase

↓

Repository

↓

DTO

↓

Controller

↓

Resposta

---

# Comunicação com Scheduler

Scheduler nunca altera entidades diretamente.

Sempre utiliza Use Cases.

Fluxo

Scheduler

↓

RunScrapingUseCase

↓

Pipeline

↓

Persistência

---

# Tratamento de Erros

A Application Layer captura exceções técnicas.

Converte para erros compreensíveis.

Registra logs.

Propaga apenas informações necessárias.

Nunca expor stack trace ao usuário.

---

# Regras para o Claude Code

Ao implementar a Application Layer:

Nunca criar SQL.

Nunca interpretar HTML.

Nunca acessar Playwright.

Nunca importar React.

Nunca criar Components.

Nunca utilizar SQLite diretamente.

Sempre utilizar interfaces.

Sempre retornar DTOs.

Cada Use Case deve representar uma única ação do usuário ou do sistema.

---

# Critérios de Aceitação

A Application Layer será considerada correta quando:

[ ] Todos os casos de uso estiverem isolados.

[ ] Nenhum Service depender de infraestrutura concreta.

[ ] Toda comunicação ocorrer através de interfaces.

[ ] Controllers permanecerem sem regra de negócio.

[ ] DTOs forem utilizados em todas as fronteiras.

[ ] Transações forem controladas pela Application Layer.

Fim do Capítulo 3.

