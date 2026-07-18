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

---

# Capítulo 4 — Interface Adapters

---

# Objetivo

A Interface Adapters Layer é responsável por adaptar informações entre o núcleo da aplicação e sistemas externos.

Ela funciona como uma ponte entre:

- Application Layer;
- Domain Layer;
- Frameworks;
- Usuários;
- Dados externos.

---

# Responsabilidade Principal

Transformar dados.

Entrada externa:

↓

Formato interno.

Saída interna:

↓

Formato externo.

---

# Conceito

O domínio e a aplicação não devem conhecer detalhes externos.

Portanto, esta camada adapta:

- HTTP;
- JSON;
- HTML;
- objetos de banco;
- eventos;
- arquivos.

---

# Estrutura Oficial

```text
interfaces/

├── controllers/
│
├── presenters/
│
├── repositories/
│
├── mappers/
│
├── gateways/
│
└── serializers/
```

---

# Componentes Principais

A camada possui cinco grupos principais.

---

# 1. Controllers

## Objetivo

Receber requisições externas.

No Deal Monitor:

- HTTP REST;
- comandos internos;
- chamadas do frontend.

---

# Responsabilidades

Controllers devem:

- receber request;
- validar formato;
- criar DTO;
- chamar Use Case;
- retornar Response DTO.

---

# Controllers NÃO devem

Executar regras de negócio.

Consultar banco.

Executar scraping.

Calcular promoções.

Manipular entidades diretamente.

---

# Fluxo correto

```text
HTTP Request

↓

Controller

↓

Request DTO

↓

Use Case

↓

Response DTO

↓

Controller

↓

HTTP Response
```

---

# Fluxo incorreto

```text
HTTP Request

↓

Controller

↓

SQLite

↓

JSON
```

---

# 2. Presenters

## Objetivo

Transformar resultados da aplicação em formatos adequados para consumo.

---

# Exemplo

Application retorna:

```python
ProductResponse
```

Presenter transforma em:

```json
{
 "id":1,
 "name":"RTX 5070",
 "lowest_price":4699
}
```

---

# Responsabilidades

Formatar saída.

Controlar campos expostos.

Esconder detalhes internos.

---

# Não pode

Alterar regras.

Consultar dados.

Modificar entidades.

---

# 3. Repository Adapters

## Objetivo

Implementar contratos definidos pelo domínio/application.

---

# Exemplo

Domain define:

```python
CatalogRepository
```

Interface Adapter implementa:

```python
SQLiteCatalogRepository
```

---

# Responsabilidades

Converter:

Entidade

↓

Modelo persistente

e:

Modelo persistente

↓

Entidade

---

# Não pode

Aplicar regra de negócio.

Calcular promoção.

Executar scraping.

---

# 4. Mappers

## Objetivo

Converter estruturas.

---

# Exemplos

Database Model

↓

Domain Entity

---

JSON

↓

DTO

---

ParsedProduct

↓

NormalizedProduct

---

# Regra

Mapper apenas converte.

Mapper não decide.

---

# 5. Gateways

## Objetivo

Adaptar comunicação externa.

---

# Exemplos

NotificationGateway

StorageGateway

BrowserGateway

ScrapingGateway

---

# Exemplo

Application solicita:

```python
notification.send()
```

Gateway decide:

- WhatsApp;
- Email;
- arquivo;
- log.

---

# Regras de Dependência

A Interface Adapter conhece:

Application.

Domain.

Frameworks.

---

Nunca deve ser conhecida pelo Domain.

---

# Fluxo de Dados

## Entrada

```text
Usuário

↓

Controller

↓

DTO

↓

Use Case

↓

Domain
```

---

## Saída

```text
Domain

↓

Use Case

↓

Response DTO

↓

Presenter

↓

Usuário
```

---

# Persistência

Fluxo:

```text
Domain Entity

↓

Repository Interface

↓

Repository Adapter

↓

ORM

↓

SQLite
```

---

# Banco de Dados

A camada de banco nunca deve vazar detalhes.

Exemplo:

Errado:

```python
product.price_column
```

no domínio.

---

Correto:

```python
product.price
```

convertido pelo mapper.

---

# API

A API nunca deve expor entidades diretamente.

Errado:

```python
return CatalogProduct()
```

---

Correto:

```python
return CatalogProductResponse()
```

---

# Scraping

O resultado do scraping não entra diretamente no domínio.

Fluxo obrigatório:

```text
HTML

↓

Parser

↓

ParsedProduct

↓

Normalizer

↓

NormalizedProduct

↓

Application

↓

Domain
```

---

# Testabilidade

Todos os adapters devem possuir testes próprios.

Exemplos:

Controller Test

Repository Test

Mapper Test

Presenter Test

Gateway Test

---

# Regras para o Claude Code

Ao criar Interface Adapters:

Nunca colocar regra de negócio.

Nunca acessar entidades diretamente na API.

Nunca retornar modelos de banco.

Nunca colocar SQL em Controllers.

Nunca colocar scraping em Controllers.

Sempre criar DTOs.

Sempre utilizar interfaces.

Sempre criar Mappers quando houver mudança de formato.

---

# Critérios de Aceitação

A Interface Adapter Layer será considerada correta quando:

[ ] Controllers possuem apenas coordenação.

[ ] Banco está isolado através de adapters.

[ ] Entidades não vazam para API.

[ ] DTOs representam fronteiras.

[ ] Mappers possuem responsabilidade única.

[ ] Nenhuma regra de negócio está nesta camada.

Fim do Capítulo 4.

# Capítulo 5 — Framework Layer

---

# Objetivo

A Framework Layer concentra todas as tecnologias externas utilizadas pelo sistema.

Frameworks são detalhes de implementação.

Eles existem para suportar a aplicação.

Nunca para definir sua arquitetura.

A troca de qualquer framework deve exigir alterações mínimas no restante do sistema.

---

# Princípio Fundamental

Frameworks ficam na camada mais externa.

Eles dependem da aplicação.

A aplicação nunca depende deles.

---

# Frameworks Oficiais do MVP

Backend

- Python 3.13+
- FastAPI
- Uvicorn
- Pydantic

Persistência

- SQLite
- SQLAlchemy
- Alembic

Scraping

- Playwright
- BeautifulSoup4
- lxml

Agendamento

- APScheduler

Frontend

- React
- Vite
- TypeScript
- Tailwind CSS

Testes

- Pytest
- Playwright Test

Utilidades

- httpx
- structlog
- python-dotenv

---

# Organização

```text
framework/

├── api/
│
├── database/
│
├── scraping/
│
├── scheduler/
│
├── logging/
│
├── configuration/
│
└── frontend/
```

---

# API Framework

FastAPI será responsável apenas por:

- inicializar servidor;
- registrar rotas;
- configurar middlewares;
- configurar documentação OpenAPI;
- realizar validações básicas.

---

# FastAPI NÃO deve

Executar scraping.

Calcular promoções.

Consultar SQLite diretamente.

Interpretar HTML.

Executar regras de negócio.

---

# Banco de Dados

SQLite será utilizado como banco oficial do MVP.

Responsabilidades:

- persistência;
- consultas;
- índices;
- integridade referencial.

SQLite nunca será acessado diretamente pelos Controllers.

Toda comunicação ocorrerá através dos Repositories.

---

# SQLAlchemy

Responsável apenas por:

- mapear entidades persistentes;
- criar sessões;
- executar transações;
- abstrair SQL.

Nunca utilizar SQLAlchemy dentro do Domain Layer.

---

# Alembic

Toda alteração estrutural do banco deverá ocorrer através de migrations.

É proibido alterar tabelas manualmente durante o desenvolvimento.

Cada mudança deve possuir uma migration correspondente.

---

# Playwright

Playwright será o navegador oficial do projeto.

Responsabilidades:

- abrir páginas;
- aguardar carregamento;
- executar scroll;
- capturar HTML.

Playwright nunca interpreta dados.

Após capturar o HTML, sua responsabilidade termina.

---

# BeautifulSoup

Responsável por transformar HTML em estrutura navegável.

Nunca executa requisições.

Nunca abre navegador.

Nunca realiza persistência.

---

# APScheduler

Responsável por iniciar tarefas automáticas.

Exemplos:

Atualização completa.

Atualização incremental.

Limpeza de cache.

Reprocessamento.

O Scheduler nunca implementa lógica de negócio.

Ele apenas dispara Use Cases.

---

# React

Responsável exclusivamente pela interface do usuário.

Funções:

- renderizar páginas;
- exibir tabelas;
- exibir gráficos;
- permitir filtros;
- enviar requisições para API.

---

# React NÃO deve

Conhecer SQLite.

Conhecer Playwright.

Executar scraping.

Implementar regras de promoção.

Conhecer SQL.

---

# Tailwind CSS

Responsável apenas pela camada visual.

Nunca utilizar Tailwind para controlar lógica da aplicação.

---

# Configuração

Toda configuração deverá ser externa.

Exemplos:

.env

config.yaml

stores.yaml

categories.yaml

Nunca armazenar configurações sensíveis no código-fonte.

---

# Logging

Será utilizado logging estruturado.

Todos os logs deverão conter:

timestamp

nível

componente

execução

loja

categoria

mensagem

Logs devem ser consistentes em toda a aplicação.

---

# Dependências Permitidas

Framework

↓

Application

↓

Domain

Frameworks podem conhecer a aplicação.

A aplicação nunca conhece frameworks específicos.

---

# Atualização de Frameworks

A substituição de qualquer framework deve impactar apenas esta camada.

Exemplos

FastAPI

↓

Flask

Impacto esperado:

API Framework apenas.

---

SQLite

↓

PostgreSQL

Impacto esperado:

Persistence Adapter.

---

Playwright

↓

Selenium

Impacto esperado:

Fetcher.

---

React

↓

Vue

Impacto esperado:

Frontend.

---

# Regras para o Claude Code

Ao utilizar frameworks:

Nunca mover regras de negócio para o framework.

Nunca criar dependência do Domain para FastAPI.

Nunca importar SQLAlchemy no Domain.

Nunca utilizar Playwright fora dos Connectors.

Nunca utilizar React para processar regras de negócio.

Sempre isolar código específico de framework.

---

# Critérios de Aceitação

A Framework Layer será considerada correta quando:

[ ] Todos os frameworks estiverem isolados.

[ ] O domínio permanecer independente.

[ ] Frameworks puderem ser substituídos.

[ ] Nenhuma regra de negócio depender de tecnologia.

[ ] Configurações estiverem externas ao código.

Fim do Capítulo 5.

---

# Capítulo 6 — Dependency Rule

---

# Objetivo

Este capítulo define as regras oficiais de dependência entre todos os módulos do sistema.

Essas regras são obrigatórias.

Nenhum componente pode violá-las.

Caso exista necessidade de quebrar uma regra, uma nova ADR (Architecture Decision Record) deverá ser criada antes da implementação.

---

# Princípio Fundamental

As dependências sempre apontam para o centro da arquitetura.

Nunca para fora.

Representação:

```text
Presentation
      │
      ▼
Interface Adapters
      │
      ▼
Application
      │
      ▼
Domain
```

A direção da dependência nunca deve ser invertida.

---

# Regra de Ouro

O Domain não conhece nenhuma outra camada.

Ele representa apenas o negócio.

Toda tecnologia depende dele.

Ele nunca depende de tecnologia.

---

# Dependências Permitidas

## Domain

Pode depender apenas de:

- Standard Library do Python
- Tipos internos do próprio domínio
- Value Objects
- Enums
- Interfaces definidas no domínio

---

## Application

Pode depender de:

- Domain
- Ports (Interfaces)
- DTOs
- Commands
- Queries

Nunca depende de implementações concretas.

---

## Interface Adapters

Pode depender de:

- Application
- Domain
- Framework Layer

É responsável por adaptar dados entre essas camadas.

---

## Framework Layer

Pode depender de qualquer camada interna.

Ela representa a borda da aplicação.

---

# Dependências Proibidas

## Domain

Nunca importar:

FastAPI

SQLAlchemy

Playwright

BeautifulSoup

React

Tailwind

SQLite

httpx

requests

APScheduler

JSON específico de APIs

Modelos ORM

---

## Application

Nunca importar:

FastAPI

Playwright

BeautifulSoup

SQLAlchemy Models

React

Componentes visuais

---

## Interface Adapters

Nunca implementar regra de negócio.

Nunca modificar entidades.

Nunca calcular promoções.

Nunca executar matching.

---

## Framework

Nunca conter regra de negócio.

Nunca conhecer entidades internas além do necessário para adaptação.

---

# Dependências entre Pacotes

Fluxo permitido:

```text
Presentation
    │
    ▼
Controllers
    │
    ▼
Application
    │
    ▼
Domain
    │
    ▼
Ports
    │
    ▼
Infrastructure
```

Fluxo proibido:

```text
Controller
      │
      ▼
SQLite
```

---

Outro exemplo proibido:

```text
React
     │
     ▼
SQLAlchemy
```

---

Outro exemplo proibido:

```text
Repository
      │
      ▼
Promotion Engine
```

---

# Inversão de Dependência

Sempre que um componente precisar acessar infraestrutura, utilizar uma interface.

Exemplo:

Correto

```text
Application

↓

CatalogRepository (Interface)

↓

SQLiteCatalogRepository
```

Errado

```text
Application

↓

SQLiteCatalogRepository
```

---

# Ports

Toda comunicação com infraestrutura deve ocorrer através de Ports.

Exemplos:

CatalogRepository

StoreRepository

NotificationGateway

BrowserGateway

ConfigurationProvider

HistoryRepository

PromotionRepository

---

# Adapters

Cada Port deve possuir pelo menos um Adapter.

Exemplo:

Port

↓

NotificationGateway

↓

Adapters

EmailNotificationAdapter

LogNotificationAdapter

FutureWhatsAppAdapter

---

# Regra para Banco de Dados

A Application nunca conhece SQL.

Ela apenas conhece contratos.

Exemplo:

```python
catalog_repository.save(product)
```

Nunca:

```python
INSERT INTO catalog_product ...
```

---

# Regra para Scraping

O Domain nunca conhece Playwright.

A Application nunca conhece seletores CSS.

Somente o Connector conhece detalhes da página.

---

# Regra para Configuração

O domínio nunca acessa:

.env

config.yaml

stores.yaml

Toda configuração entra pela camada de Framework e é convertida em objetos internos antes de chegar à Application.

---

# Regra para Logging

O Domain não registra logs.

Caso seja necessário registrar informações, a responsabilidade pertence às camadas externas.

O domínio apenas lança exceções quando necessário.

---

# Comunicação Oficial

Fluxo de entrada:

```text
Usuário

↓

Frontend

↓

Controller

↓

Use Case

↓

Domain

↓

Repository (Port)

↓

Repository Adapter

↓

SQLite
```

Fluxo de saída:

```text
SQLite

↓

Repository Adapter

↓

Repository (Port)

↓

Use Case

↓

Presenter

↓

Frontend

↓

Usuário
```

---

# Checklist de Dependências

Antes de criar uma nova classe, verificar:

[ ] Esta classe depende apenas de camadas permitidas?

[ ] Existe alguma dependência concreta que deveria ser uma interface?

[ ] O Domain permaneceu independente?

[ ] Há alguma referência direta a framework fora da Framework Layer?

[ ] Alguma regra de negócio foi colocada em um Adapter?

[ ] Algum Controller está acessando persistência diretamente?

Se qualquer resposta for "sim" para uma violação, a implementação deve ser revisada.

---

# Regras para o Claude Code

Ao gerar código:

- Nunca importar implementações concretas na Application Layer.
- Sempre depender de interfaces.
- Nunca adicionar novos atalhos entre camadas.
- Nunca permitir que Controllers conheçam Repositories concretos.
- Nunca utilizar entidades do domínio como modelos HTTP.
- Nunca utilizar modelos ORM fora da camada de persistência.
- Sempre preservar a direção oficial das dependências.

---

# Critérios de Aceitação

A Dependency Rule será considerada atendida quando:

[ ] Todas as dependências apontarem para o centro da arquitetura.

[ ] O Domain permanecer completamente isolado.

[ ] Toda infraestrutura for acessada através de Ports.

[ ] Todos os Adapters implementarem contratos definidos pelas camadas internas.

[ ] Não existirem dependências circulares.

Fim do Capítulo 6.

---

# Capítulo 7 — Ports & Adapters

---

# Objetivo

Este capítulo define como o sistema se comunica com recursos externos sem acoplar o núcleo da aplicação à infraestrutura.

O padrão adotado é Ports & Adapters (Arquitetura Hexagonal).

Todo acesso a banco de dados, scraping, notificações, arquivos ou configurações deve ocorrer através de contratos (Ports), implementados por Adapters.

---

# Princípio Fundamental

A Application Layer depende apenas de interfaces.

Nunca de implementações.

Fluxo oficial:

```text
Application

↓

Port

↓

Adapter

↓

Framework
```

---

# O que é um Port

Um Port representa um contrato.

Ele descreve:

- quais operações são permitidas;
- quais dados entram;
- quais dados saem.

Um Port nunca contém implementação.

---

# O que é um Adapter

Um Adapter implementa um Port.

Ele conhece detalhes técnicos como:

- SQLite;
- Playwright;
- sistema de arquivos;
- variáveis de ambiente;
- HTTP;
- bibliotecas externas.

---

# Organização Oficial

```text
application/
└── ports/
    ├── repositories/
    ├── gateways/
    ├── connectors/
    └── providers/

infrastructure/
└── adapters/
    ├── persistence/
    ├── notifications/
    ├── scraping/
    ├── configuration/
    └── storage/
```

---

# Repository Ports

Representam operações de persistência.

Exemplos:

CatalogRepository

StoreRepository

PriceHistoryRepository

PromotionRepository

ScrapingJobRepository

Esses contratos são utilizados pela Application Layer.

Nunca conhecem SQLite.

---

# Gateway Ports

Representam serviços externos.

Exemplos:

NotificationGateway

BrowserGateway

StorageGateway

ImageGateway

FutureAIProvider

---

# Connector Ports

Representam conectores de e-commerce.

Exemplos:

AmazonConnector

KabumConnector

PichauConnector

TerabyteConnector

MercadoLivreConnector

Todos implementam o mesmo contrato base.

---

# Provider Ports

Responsáveis por fornecer recursos internos ao sistema.

Exemplos:

ClockProvider

ConfigurationProvider

UUIDProvider

HashProvider

Esses Ports aumentam a testabilidade e eliminam dependências diretas da biblioteca padrão.

---

# Fluxo Oficial

```text
Use Case

↓

CatalogRepository

↓

SQLiteCatalogRepository

↓

SQLite
```

---

Outro exemplo:

```text
RunScrapingUseCase

↓

ConnectorPort

↓

AmazonConnector

↓

Playwright
```

---

# Regra para Repositories

Repositories representam apenas persistência.

Eles nunca:

- calculam promoções;
- interpretam HTML;
- executam validações de negócio;
- iniciam scraping.

---

# Regra para Gateways

Gateways encapsulam integrações externas.

Exemplo:

Application:

```python
notification_gateway.send(message)
```

Implementação:

EmailNotificationAdapter

LogNotificationAdapter

FutureWhatsAppAdapter

A Application nunca conhece qual implementação está sendo utilizada.

---

# Regra para Connectors

Cada loja implementa exatamente o mesmo contrato.

Métodos mínimos:

- fetch()
- parse()
- normalize()
- validate()

Métodos opcionais:

- health_check()
- supports_category()
- rate_limit()

---

# Múltiplos Adapters

Um mesmo Port pode possuir diversas implementações.

Exemplo:

ConfigurationProvider

↓

EnvironmentConfigurationProvider

↓

YamlConfigurationProvider

↓

JsonConfigurationProvider

A escolha ocorre apenas durante a inicialização da aplicação.

---

# Benefícios

Esta abordagem permite:

- trocar banco de dados;
- trocar navegador;
- trocar mecanismo de configuração;
- trocar sistema de notificações;
- criar implementações falsas para testes.

Sem alterar a Application Layer.

---

# Testes

Todos os Ports devem possuir implementações Fake ou Mock.

Exemplo:

FakeCatalogRepository

FakeNotificationGateway

FakeClockProvider

Isso permite testes rápidos e independentes de infraestrutura.

---

# Regras para o Claude Code

Ao criar novos componentes:

- Criar primeiro o Port.
- Implementar depois o Adapter.
- Nunca utilizar um Adapter diretamente na Application Layer.
- Nunca acessar infraestrutura sem um Port correspondente.
- Sempre manter contratos pequenos e coesos.
- Evitar interfaces genéricas demais.

---

# Critérios de Aceitação

O padrão Ports & Adapters será considerado corretamente aplicado quando:

[ ] Toda infraestrutura estiver atrás de um Port.

[ ] Toda implementação concreta estiver na Infrastructure Layer.

[ ] A Application Layer conhecer apenas contratos.

[ ] Novas implementações puderem ser adicionadas sem alterar casos de uso.

[ ] Os testes puderem utilizar Fakes ou Mocks sem dependência de infraestrutura.

Fim do Capítulo 7.

---

# Capítulo 8 — Dependency Injection

---

# Objetivo

Este capítulo define como dependências serão criadas, registradas e disponibilizadas para toda a aplicação.

A Dependency Injection (DI) elimina acoplamento entre componentes.

Nenhum componente deve criar diretamente suas próprias dependências.

Toda dependência deve ser recebida externamente.

---

# Princípio Fundamental

Componentes recebem dependências.

Nunca criam dependências.

Correto:

Use Case

↓

CatalogRepository

↓

SQLiteCatalogRepository

---

Errado:

Use Case

↓

SQLiteCatalogRepository()

---

# Benefícios

A utilização de Dependency Injection permite:

- baixo acoplamento;
- maior testabilidade;
- substituição de implementações;
- criação de Mocks;
- criação de Fakes;
- manutenção simplificada.

---

# Fluxo Oficial

```text
Application Startup

↓

Dependency Container

↓

Resolve Interfaces

↓

Cria Implementações

↓

Entrega aos Use Cases

↓

Sistema Inicializado
```

---

# Estrutura Oficial

```text
bootstrap/

├── container.py
├── dependencies.py
├── registry.py
└── startup.py
```

Toda configuração de dependências pertence exclusivamente a esta estrutura.

---

# Dependency Container

O projeto possuirá um único Container de dependências.

Responsabilidades:

- registrar implementações;
- resolver interfaces;
- controlar ciclo de vida;
- fornecer instâncias.

---

# Registro

Toda interface deve possuir exatamente uma implementação padrão registrada.

Exemplo:

CatalogRepository

↓

SQLiteCatalogRepository

---

NotificationGateway

↓

LogNotificationAdapter

---

ConfigurationProvider

↓

EnvironmentConfigurationProvider

---

# Ciclo de Vida

Existem três tipos de dependência.

Singleton

Uma única instância durante toda a aplicação.

Exemplos:

ConfigurationProvider

Logger

Scheduler

---

Scoped

Uma instância por requisição.

Exemplos:

Use Cases

Services

Transactions

---

Transient

Nova instância sempre que solicitada.

Exemplos:

DTO Builders

Validators

Temporary Helpers

---

# Regra Oficial

Repositories

Scoped

---

Use Cases

Scoped

---

Gateways

Singleton

---

Configuration

Singleton

---

Logger

Singleton

---

Connectors

Scoped

---

Playwright Browser

Singleton

Browser Context

Scoped

Page

Transient

---

# Construção dos Objetos

A criação das dependências ocorre apenas durante o Startup.

Exemplo:

Startup

↓

Container

↓

Repository

↓

Service

↓

Controller

Nenhuma outra camada deve instanciar componentes manualmente.

---

# Injeção

Sempre utilizar injeção via construtor.

Exemplo conceitual:

```python
class SearchProductsUseCase:

    def __init__(
        self,
        repository: CatalogRepository
    ):
        self.repository = repository
```

Nunca utilizar atributos globais.

Nunca utilizar Service Locator.

---

# Dependências Opcionais

Quando uma funcionalidade possuir implementação opcional:

Utilizar Null Object.

Exemplo:

NotificationGateway

↓

NullNotificationGateway

Evitar verificações constantes de None.

---

# Testes

Durante testes:

Interfaces são substituídas por:

FakeCatalogRepository

FakeClockProvider

FakeNotificationGateway

FakeStorageGateway

Nenhum teste unitário deve depender da infraestrutura real.

---

# Configuração

A resolução das dependências deve ser declarativa.

Nunca criar lógica de negócio dentro do Container.

O Container apenas monta objetos.

---

# Startup

Fluxo oficial:

```text
Carregar Configuração

↓

Registrar Container

↓

Registrar Providers

↓

Registrar Repositories

↓

Registrar Gateways

↓

Registrar Connectors

↓

Criar Scheduler

↓

Criar API

↓

Aplicação pronta
```

---

# Shutdown

Fluxo oficial:

```text
Parar Scheduler

↓

Fechar Browser

↓

Fechar Banco

↓

Liberar Recursos

↓

Encerrar Processo
```

Todos os recursos devem ser encerrados corretamente.

---

# Regras para o Claude Code

Ao implementar componentes:

Nunca utilizar:

Repository()

Playwright()

SQLite()

FastAPI()

dentro de Use Cases ou Services.

Sempre receber dependências por construtor.

Nunca criar Singletons manualmente.

Nunca utilizar variáveis globais para compartilhar dependências.

Nunca utilizar import circular para resolver dependências.

O Container é o único responsável por construir objetos.

---

# Checklist

Antes de finalizar qualquer implementação:

[ ] Existe alguma instância criada manualmente?

[ ] Toda dependência é recebida externamente?

[ ] O ciclo de vida está correto?

[ ] O componente pode ser facilmente testado?

[ ] A implementação concreta está escondida atrás de uma interface?

---

# Critérios de Aceitação

A Dependency Injection será considerada corretamente aplicada quando:

[ ] Nenhum componente criar suas próprias dependências.

[ ] Toda infraestrutura for registrada no Container.

[ ] O Startup for responsável por toda composição da aplicação.

[ ] Os testes puderem substituir qualquer implementação por um Fake ou Mock.

[ ] Não existirem dependências globais.

Fim do Capítulo 8.

---

# Capítulo 9 — Use Cases

---

# Objetivo

Este capítulo define como os casos de uso (Use Cases) devem ser projetados e implementados.

Um Use Case representa uma única ação executada pelo usuário ou pelo próprio sistema.

Toda funcionalidade da aplicação deve existir como um Use Case explícito.

---

# Princípio Fundamental

Um Use Case responde a apenas uma pergunta:

"O que o sistema deve fazer?"

Nunca:

"Como o framework funciona?"

Nunca:

"Como o banco funciona?"

---

# Responsabilidade

Um Use Case deve:

- receber uma solicitação;
- validar pré-condições da aplicação;
- coordenar entidades do domínio;
- utilizar Ports;
- persistir alterações quando necessário;
- retornar um resultado.

---

# Não é responsabilidade

Um Use Case nunca deve:

- criar telas;
- interpretar HTML;
- executar SQL;
- conhecer Playwright;
- conhecer FastAPI;
- conhecer React;
- conhecer SQLite;
- renderizar JSON.

---

# Estrutura Oficial

```text
application/

└── use_cases/

    ├── catalog/

    ├── scraping/

    ├── history/

    ├── promotion/

    ├── search/

    ├── configuration/

    └── scheduler/
```

---

# Organização

Cada arquivo contém apenas um Use Case.

Exemplo:

```text
SearchProductsUseCase.py
```

Nunca:

```text
CatalogService.py
```

com dezenas de responsabilidades.

---

# Casos de Uso do MVP

## Catálogo

CreateCatalogProductUseCase

UpdateCatalogProductUseCase

FindCatalogProductUseCase

SearchCatalogUseCase

DeleteCatalogProductUseCase (uso administrativo)

---

## Lojas

RegisterStoreUseCase

EnableStoreUseCase

DisableStoreUseCase

ListStoresUseCase

---

## Categorias

CreateCategoryUseCase

EnableCategoryUseCase

DisableCategoryUseCase

ListCategoriesUseCase

---

## Scraping

RunScrapingUseCase

RunStoreScrapingUseCase

RunCategoryScrapingUseCase

RetryFailedJobUseCase

CancelScrapingJobUseCase

---

## Histórico

GetPriceHistoryUseCase

GetLowestPriceUseCase

GetAveragePriceUseCase

ComparePriceHistoryUseCase

---

## Promoções

CalculatePromotionUseCase

ListActivePromotionsUseCase

DismissPromotionUseCase

---

## Configuração

LoadConfigurationUseCase

UpdateConfigurationUseCase

ReloadConfigurationUseCase

---

# Fluxo Oficial

```text
Controller

↓

Request DTO

↓

Use Case

↓

Domain

↓

Repository Port

↓

Repository Adapter

↓

Banco

↓

Response DTO
```

---

# Entrada

Todo Use Case recebe um único Request DTO.

Exemplo:

```python
SearchProductsRequest
```

Nunca receber múltiplos parâmetros soltos.

---

# Saída

Todo Use Case retorna um único Response DTO.

Nunca retornar:

Entidades.

ORM Models.

JSON bruto.

---

# Transações

Quando houver alteração de estado:

Início

↓

Executar operação

↓

Persistir

↓

Commit

Se ocorrer erro:

Rollback

↓

Retornar exceção apropriada

---

# Validação

Existem três níveis.

## Validação de Entrada

Formato.

Obrigatoriedade.

Tipos.

Realizada antes do Use Case.

---

## Validação da Aplicação

Permissões.

Existência de registros.

Disponibilidade.

Realizada pelo Use Case.

---

## Validação de Negócio

Regras do domínio.

Realizada pelas entidades e Domain Services.

---

# Comunicação

Um Use Case pode chamar:

- Entities;
- Domain Services;
- Repository Ports;
- Gateway Ports;
- Outros Use Cases apenas quando houver justificativa arquitetural clara.

Evitar cadeias longas de Use Cases.

---

# Retorno

Todo Response DTO deve conter apenas os dados necessários para o consumidor.

Nunca expor atributos internos da entidade.

---

# Tratamento de Erros

Cada Use Case deve:

- tratar exceções previsíveis;
- propagar exceções inesperadas para tratamento global;
- nunca ocultar erros silenciosamente.

---

# Logging

Registrar apenas eventos relevantes.

Exemplos:

- início da execução;
- conclusão;
- falha;
- tempo de execução.

Nunca registrar dados sensíveis.

---

# Idempotência

Sempre que possível, os Use Cases devem ser idempotentes.

Executar novamente a mesma operação não deve gerar efeitos inesperados.

Exemplos:

EnableCategoryUseCase

Executar duas vezes:

Resultado permanece consistente.

---

# Performance

Um Use Case deve:

- executar apenas o necessário;
- evitar consultas duplicadas;
- evitar processamento desnecessário;
- delegar responsabilidades ao domínio.

---

# Nomeação

Sempre utilizar:

Verbo + Entidade + UseCase

Exemplos:

SearchProductsUseCase

RunScrapingUseCase

CalculatePromotionUseCase

GetPriceHistoryUseCase

---

# Regras para o Claude Code

Ao criar um novo Use Case:

- Implementar apenas uma responsabilidade.
- Receber um único Request DTO.
- Retornar um único Response DTO.
- Utilizar apenas Ports para acessar infraestrutura.
- Não instanciar dependências manualmente.
- Não acessar frameworks diretamente.
- Não executar lógica de apresentação.
- Não utilizar modelos ORM.
- Manter o método principal curto e legível.

---

# Checklist

Antes de concluir um Use Case:

[ ] Possui apenas uma responsabilidade?

[ ] Recebe um único DTO?

[ ] Retorna um único DTO?

[ ] Utiliza apenas interfaces?

[ ] Não conhece infraestrutura?

[ ] Não contém regras de apresentação?

[ ] Não executa SQL?

[ ] Mantém transações consistentes?

---

# Critérios de Aceitação

Os Use Cases serão considerados corretos quando:

[ ] Representarem ações completas do sistema.

[ ] Permanecerem independentes da infraestrutura.

[ ] Utilizarem apenas contratos.

[ ] Forem facilmente testáveis.

[ ] Não apresentarem dependências desnecessárias.

Fim do Capítulo 9.

---

# Capítulo 10 — Repository Pattern

---

# Objetivo

Este capítulo define como a camada de persistência será acessada pelo restante da aplicação.

O padrão Repository tem como objetivo isolar completamente o domínio e a aplicação dos detalhes de armazenamento.

Toda persistência deverá ocorrer através de contratos (Ports) implementados por Repositories.

---

# Princípio Fundamental

Um Repository representa uma coleção de objetos do domínio.

Ele abstrai completamente a forma como os dados são armazenados.

O consumidor nunca deve conhecer:

- SQLite
- SQLAlchemy
- SQL
- índices
- tabelas
- joins

---

# Estrutura Oficial

```text
application/
└── ports/
    └── repositories/
        ├── catalog_repository.py
        ├── store_repository.py
        ├── category_repository.py
        ├── price_history_repository.py
        ├── promotion_repository.py
        └── scraping_job_repository.py

infrastructure/
└── persistence/
    ├── sqlite/
    │   ├── catalog_repository.py
    │   ├── store_repository.py
    │   ├── category_repository.py
    │   ├── price_history_repository.py
    │   ├── promotion_repository.py
    │   └── scraping_job_repository.py
    └── mappers/
```

---

# Responsabilidades

Repositories são responsáveis por:

- salvar entidades;
- atualizar entidades;
- recuperar entidades;
- excluir entidades quando permitido;
- executar consultas específicas de persistência;
- controlar paginação quando necessário.

---

# Não é responsabilidade

Repositories nunca devem:

- calcular promoções;
- executar scraping;
- interpretar HTML;
- validar regras de negócio;
- normalizar títulos;
- decidir qual produto pertence ao catálogo;
- registrar métricas;
- enviar notificações.

---

# Fluxo Oficial

```text
Use Case

↓

Repository Port

↓

Repository Adapter

↓

ORM

↓

SQLite
```

---

# Operações Básicas

Todo Repository deverá fornecer apenas operações compatíveis com sua responsabilidade.

Exemplo:

CatalogRepository

- save()
- update()
- find_by_id()
- find_by_normalized_name()
- search()
- exists()
- delete()

Evitar métodos genéricos como:

process()

handle()

execute()

run()

---

# Consultas

Consultas devem representar necessidades da aplicação.

Correto:

find_active_promotions()

find_products_by_category()

find_price_history()

Errado:

execute_sql()

run_query()

generic_search()

---

# Paginação

Toda consulta que possa crescer indefinidamente deverá oferecer paginação.

A estratégia padrão será:

- limit
- offset
- ordenação explícita

Nunca retornar grandes volumes de dados sem controle.

---

# Mapeamento

Repositories trabalham apenas com entidades do domínio.

O mapeamento entre ORM e Domain ocorre através de Mappers.

Fluxo:

```text
SQLite Model

↓

Persistence Mapper

↓

Domain Entity
```

Fluxo inverso:

```text
Domain Entity

↓

Persistence Mapper

↓

SQLite Model
```

---

# Transações

Repositories não iniciam transações.

A responsabilidade pelo controle transacional pertence à Application Layer.

Repositories apenas participam da transação em andamento.

---

# Soft Delete

No MVP:

CatalogProduct

Não utilizar Soft Delete.

Category

Utilizar campo enabled.

Store

Utilizar campo enabled.

Promotion

Utilizar status.

PriceHistory

Nunca remover registros.

ScrapingJob

Nunca remover registros automaticamente.

---

# Concorrência

Repositories devem estar preparados para múltiplas operações simultâneas.

Nunca armazenar estado interno compartilhado.

Toda operação deve ser independente.

---

# Cache

No MVP não haverá cache interno em Repositories.

Toda leitura será realizada diretamente na base de dados.

Caso cache seja necessário futuramente, deverá ser implementado através de um Adapter específico.

---

# Tratamento de Erros

Repositories devem converter erros técnicos em exceções compreensíveis pela aplicação.

Nunca propagar exceções específicas do ORM para as camadas superiores.

---

# Performance

Prioridades:

1. Correção
2. Clareza
3. Simplicidade
4. Performance

Otimizações deverão ser justificadas por medições reais.

---

# Testes

Todo Repository deverá possuir:

- testes unitários utilizando banco temporário;
- testes de integração;
- cenários de erro;
- validação de mapeamentos.

---

# Regras para o Claude Code

Ao implementar um Repository:

- Implementar apenas persistência.
- Nunca adicionar lógica de negócio.
- Nunca acessar Playwright.
- Nunca importar FastAPI.
- Nunca retornar modelos ORM.
- Sempre retornar entidades do domínio.
- Utilizar Mappers para conversão.
- Manter métodos pequenos e específicos.
- Evitar consultas genéricas.

---

# Checklist

Antes de concluir um Repository:

[ ] Toda persistência está encapsulada?

[ ] Não existe regra de negócio?

[ ] O Repository retorna entidades do domínio?

[ ] Há tratamento adequado de erros?

[ ] As consultas possuem nomes claros?

[ ] Existe paginação quando necessário?

[ ] O ORM permanece oculto?

---

# Critérios de Aceitação

O Repository Pattern será considerado corretamente aplicado quando:

[ ] Toda persistência ocorrer através de contratos.

[ ] O domínio permanecer desacoplado do banco.

[ ] Não houver lógica de negócio em Repositories.

[ ] O ORM estiver totalmente isolado.

[ ] Os testes puderem validar a persistência de forma independente.

Fim do Capítulo 10.

---

# Capítulo 11 — Regras Oficiais para o Claude Code

---

# Objetivo

Este capítulo define as regras obrigatórias que o Claude Code deve seguir ao gerar, modificar ou refatorar qualquer parte do projeto.

Estas regras têm prioridade sobre sugestões implícitas do modelo.

Caso exista conflito entre conhecimento interno do modelo e este documento, prevalece este documento.

---

# Objetivo Principal

Garantir que toda implementação produzida pelo Claude Code:

- siga exatamente a arquitetura definida;
- mantenha consistência entre módulos;
- evite acoplamentos;
- reduza retrabalho;
- facilite futuras evoluções.

---

# Regra 1 — Nunca inventar arquitetura

O Claude Code não deve criar:

- novas camadas;
- novos padrões;
- novas abstrações;
- novos diretórios;
- novos módulos.

Toda alteração estrutural deve existir previamente na documentação.

---

# Regra 2 — Respeitar os documentos oficiais

Sempre seguir, nesta ordem de prioridade:

1. ADRs
2. ARCHITECTURE.md
3. CLEAN_ARCHITECTURE.md
4. Documento específico da funcionalidade
5. Backlog
6. Prompt da tarefa

Nunca inverter esta prioridade.

---

# Regra 3 — Uma responsabilidade por classe

Cada classe deve possuir apenas uma responsabilidade.

Nunca criar classes "God Object".

Exemplo incorreto:

CatalogService

↓

buscar produtos

↓

calcular promoção

↓

executar scraping

↓

salvar banco

---

Cada responsabilidade deve possuir sua própria classe.

---

# Regra 4 — Não mover responsabilidades

Caso exista uma classe responsável por determinada função, reutilizá-la.

Nunca mover responsabilidades para outra camada apenas por conveniência.

---

# Regra 5 — Não duplicar código

Antes de criar uma nova implementação:

- verificar se já existe funcionalidade semelhante;
- reutilizar componentes existentes;
- extrair abstrações quando necessário.

---

# Regra 6 — Não criar atalhos

Nunca acessar diretamente:

SQLite

Playwright

FastAPI

React

Arquivos

Variáveis de ambiente

Esses recursos devem ser acessados exclusivamente pelos componentes definidos na arquitetura.

---

# Regra 7 — Nunca utilizar implementações concretas

Use Cases devem depender apenas de Ports.

Nunca utilizar:

SQLiteCatalogRepository

Correto:

CatalogRepository

---

# Regra 8 — Nunca quebrar o fluxo arquitetural

Fluxo obrigatório:

Frontend

↓

Controller

↓

Use Case

↓

Domain

↓

Repository Port

↓

Repository Adapter

↓

Framework

Nunca pular camadas.

---

# Regra 9 — Não colocar lógica onde ela não pertence

Controllers

Apenas coordenam.

Repositories

Apenas persistem.

Connectors

Apenas coletam dados.

Mappers

Apenas convertem.

Presenters

Apenas formatam.

Use Cases

Orquestram.

Domain

Implementa regras de negócio.

---

# Regra 10 — Classes pequenas

Preferência:

Até aproximadamente 300 linhas por classe.

Métodos preferencialmente com até 40 linhas.

Quando ultrapassar esses limites, avaliar refatoração.

Esses valores são diretrizes, não regras absolutas.

---

# Regra 11 — Métodos pequenos

Cada método deve representar uma única ação lógica.

Evitar métodos longos com múltiplos níveis de decisão.

Extrair funções auxiliares quando necessário.

---

# Regra 12 — Nomes explícitos

Sempre utilizar nomes que descrevam claramente a responsabilidade.

Exemplos:

SearchCatalogUseCase

RunAmazonConnector

CalculatePromotionService

Evitar nomes genéricos como:

Manager

Helper

Processor

Util

Common

Misc

---

# Regra 13 — Comentários

Comentários devem explicar:

- motivo;
- restrição;
- decisão arquitetural.

Nunca explicar código óbvio.

---

# Regra 14 — Imports

Imports devem seguir a direção oficial das dependências.

Nunca criar dependências circulares.

Nunca importar módulos externos no Domain Layer.

---

# Regra 15 — Testabilidade

Todo código produzido deve permitir:

- testes unitários;
- testes de integração;
- substituição por Mocks;
- substituição por Fakes.

---

# Regra 16 — Refatoração

Antes de modificar código existente:

- preservar contratos públicos;
- manter compatibilidade;
- evitar mudanças desnecessárias;
- atualizar documentação apenas quando houver mudança arquitetural.

---

# Regra 17 — Performance

Priorizar:

1. Correção
2. Clareza
3. Simplicidade
4. Performance

Nunca introduzir otimizações prematuras.

---

# Regra 18 — Tratamento de Erros

Capturar apenas exceções que possam ser tratadas.

Nunca ocultar erros.

Nunca utilizar blocos `except` genéricos sem necessidade.

Registrar contexto suficiente para diagnóstico.

---

# Regra 19 — Logging

Registrar apenas eventos relevantes.

Evitar excesso de logs.

Nunca registrar:

- senhas;
- tokens;
- cookies;
- dados pessoais sensíveis.

---

# Regra 20 — Evolução

Toda nova funcionalidade deve:

- reutilizar arquitetura existente;
- seguir os mesmos padrões;
- manter consistência do projeto;
- evitar criação de exceções arquiteturais.

---

# Processo obrigatório antes de gerar código

Antes de implementar qualquer tarefa, o Claude Code deve verificar:

[ ] Existe documentação para esta funcionalidade?

[ ] Há um ADR relacionado?

[ ] Existe Port correspondente?

[ ] Existe Use Case correspondente?

[ ] Existe DTO correspondente?

[ ] Existe Repository correspondente?

[ ] Existe teste planejado?

[ ] A implementação respeita a direção das dependências?

Somente após todas as verificações a implementação deve começar.

---

# Processo obrigatório após gerar código

Antes de considerar a tarefa concluída, verificar:

[ ] Nenhuma camada foi violada.

[ ] Não existem imports proibidos.

[ ] Não há lógica duplicada.

[ ] Não foram criados atalhos.

[ ] O código segue os documentos oficiais.

[ ] Todos os contratos foram respeitados.

---

# Critérios de Aceitação

O Claude Code estará em conformidade quando:

[ ] Toda implementação respeitar a arquitetura.

[ ] Não existirem dependências indevidas.

[ ] O projeto permanecer consistente.

[ ] Novas funcionalidades seguirem o mesmo padrão.

Fim do Capítulo 11.

---

# Capítulo 12 — Checklist Final de Conformidade Arquitetural

---

# Objetivo

Este checklist define os critérios mínimos que toda funcionalidade implementada deve atender para ser considerada compatível com a arquitetura oficial do projeto.

Toda Pull Request, tarefa concluída ou funcionalidade entregue deverá ser validada utilizando este checklist.

---

# 1. Estrutura do Projeto

Verificar:

[ ] O arquivo foi criado na pasta correta.

[ ] A estrutura oficial do projeto foi respeitada.

[ ] Não foram criados diretórios desnecessários.

[ ] Nenhuma camada nova foi adicionada.

[ ] A organização permanece consistente.

---

# 2. Domain Layer

Verificar:

[ ] O domínio continua independente.

[ ] Nenhum framework foi importado.

[ ] Não existem dependências externas.

[ ] As entidades representam conceitos do negócio.

[ ] Value Objects permanecem imutáveis.

[ ] Não existe acesso direto ao banco.

[ ] Não existe acesso à API.

---

# 3. Application Layer

Verificar:

[ ] Existe um Use Case para a funcionalidade.

[ ] O Use Case possui apenas uma responsabilidade.

[ ] Toda infraestrutura é acessada por Ports.

[ ] Apenas DTOs cruzam as fronteiras.

[ ] Não existem implementações concretas.

---

# 4. Interface Adapters

Verificar:

[ ] Controllers apenas coordenam.

[ ] Presenters apenas formatam.

[ ] Repositories apenas persistem.

[ ] Mappers apenas convertem.

[ ] Gateways apenas adaptam serviços externos.

---

# 5. Framework Layer

Verificar:

[ ] FastAPI permanece isolado.

[ ] SQLAlchemy permanece isolado.

[ ] SQLite permanece isolado.

[ ] Playwright permanece isolado.

[ ] React permanece isolado.

[ ] Configurações externas não vazaram para outras camadas.

---

# 6. Dependency Rule

Verificar:

[ ] Todas as dependências apontam para o centro da arquitetura.

[ ] Não existem imports proibidos.

[ ] Não existem dependências circulares.

[ ] O Domain continua desacoplado.

---

# 7. Ports & Adapters

Verificar:

[ ] Existe Port para toda infraestrutura.

[ ] Existe Adapter para cada Port.

[ ] Nenhum Adapter é utilizado diretamente pela Application.

[ ] Toda implementação concreta permanece isolada.

---

# 8. Dependency Injection

Verificar:

[ ] Nenhuma dependência foi instanciada manualmente.

[ ] Toda composição ocorre no Container.

[ ] O ciclo de vida das dependências está correto.

[ ] Não existem Singletons criados fora do bootstrap.

---

# 9. Use Cases

Verificar:

[ ] Um Use Case representa uma única ação.

[ ] Recebe um único Request DTO.

[ ] Retorna um único Response DTO.

[ ] Não conhece infraestrutura.

[ ] Não conhece frameworks.

---

# 10. Repository Pattern

Verificar:

[ ] O Repository implementa apenas persistência.

[ ] Não existe lógica de negócio.

[ ] O ORM permanece encapsulado.

[ ] As consultas possuem nomes específicos.

[ ] Há paginação quando necessária.

---

# 11. Testabilidade

Verificar:

[ ] Existem testes unitários.

[ ] Existem testes de integração quando aplicável.

[ ] Dependências podem ser substituídas por Fakes ou Mocks.

[ ] Não existem dependências ocultas.

---

# 12. Logging

Verificar:

[ ] Logs estruturados.

[ ] Sem dados sensíveis.

[ ] Eventos relevantes registrados.

[ ] Exceções preservam contexto.

---

# 13. Performance

Verificar:

[ ] Não existem consultas duplicadas.

[ ] Não existem operações desnecessárias.

[ ] Não existem loops evitáveis.

[ ] Não existem otimizações prematuras.

---

# 14. Código

Verificar:

[ ] Nomes claros.

[ ] Métodos coesos.

[ ] Classes com responsabilidade única.

[ ] Comentários apenas quando agregam valor.

[ ] Sem duplicação de código.

---

# 15. Documentação

Verificar:

[ ] A implementação segue os documentos oficiais.

[ ] Não criou exceções arquiteturais.

[ ] Toda mudança estrutural possui ADR correspondente.

[ ] O backlog permanece consistente.

---

# Processo Oficial de Validação

Antes de concluir qualquer funcionalidade:

1. Validar este checklist.
2. Executar testes.
3. Executar análise estática.
4. Corrigir inconsistências.
5. Atualizar documentação, se necessário.
6. Somente então considerar a entrega concluída.

---

# Critério de Aprovação

Uma funcionalidade será considerada aprovada quando:

- Todos os itens obrigatórios forem atendidos.
- Não houver violações arquiteturais.
- Todos os testes relevantes forem aprovados.
- Não existirem dependências indevidas.
- A documentação permanecer consistente.

---

# Evolução da Arquitetura

Toda alteração que impacte a arquitetura deverá:

- ser discutida antes da implementação;
- possuir uma ADR;
- atualizar a documentação oficial;
- preservar compatibilidade sempre que possível.

Mudanças arquiteturais nunca devem ser feitas diretamente no código sem atualização da documentação correspondente.

---

# Encerramento

O `CLEAN_ARCHITECTURE.md` estabelece as regras oficiais de organização, dependências, responsabilidades e implementação do Deal Monitor.

Ele deve ser utilizado como referência obrigatória durante todo o ciclo de vida do projeto.

Em caso de conflito entre implementações e este documento, este documento prevalece até que uma nova ADR determine o contrário.

Fim do Documento.