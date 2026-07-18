# BACKEND_SPEC.md

Projeto: Deal Monitor

Versão: 1.0

Status: Documento Oficial

Dependências

- ARCHITECTURE.md
- CLEAN_ARCHITECTURE.md
- PROJECT_STRUCTURE.md
- DATABASE_SCHEMA.md
- SCRAPING_ENGINE.md

---

# Objetivo

Este documento especifica toda a camada Backend do Deal Monitor.

Ele define:

- responsabilidades;
- módulos;
- casos de uso;
- serviços;
- fluxo interno;
- organização da API;
- integração entre componentes.

A implementação deverá seguir rigorosamente este documento.

---

# Objetivos do Backend

O Backend deverá ser responsável por:

- gerenciar o catálogo;
- controlar lojas;
- controlar categorias;
- executar scraping;
- registrar histórico;
- detectar promoções;
- disponibilizar API REST;
- fornecer dados ao frontend;
- registrar logs;
- expor métricas.

O Backend nunca será responsável por renderizar interface.

---

# Tecnologias Oficiais

Linguagem

Python 3.13+

---

Framework Web

FastAPI

---

Servidor ASGI

Uvicorn

---

ORM

SQLAlchemy 2.x

---

Migrações

Alembic

---

Validação

Pydantic v2

---

Scraping

Playwright

---

Banco

PostgreSQL

---

Scheduler

APScheduler

---

Arquitetura

Clean Architecture

---

# Estrutura Geral

```text
backend/

src/

deal_monitor/

├── domain/
├── application/
├── presentation/
├── infrastructure/
├── bootstrap/
└── shared/
```

---

# Responsabilidades

Domain

Regras de negócio.

---

Application

Casos de uso.

---

Presentation

API REST.

Controllers.

DTOs externos.

---

Infrastructure

Persistência.

Scraping.

Browser.

Scheduler.

Notificações.

---

Bootstrap

Inicialização.

Container.

Configuração.

---

Shared

Código reutilizável.

---

# Organização dos Casos de Uso

```text
application/

use_cases/

catalog/

stores/

categories/

scraping/

promotions/

history/

settings/

scheduler/
```

Cada Use Case deverá possuir um arquivo próprio.

---

# Casos de Uso

## Catálogo

CreateCatalogProductUseCase

UpdateCatalogProductUseCase

FindCatalogProductUseCase

SearchCatalogUseCase

DeleteCatalogProductUseCase

---

## Lojas

CreateStoreUseCase

EnableStoreUseCase

DisableStoreUseCase

ListStoresUseCase

UpdateStoreUseCase

---

## Categorias

CreateCategoryUseCase

EnableCategoryUseCase

DisableCategoryUseCase

ListCategoriesUseCase

UpdateCategoryUseCase

---

## Scraping

RunScrapingUseCase

RunStoreScrapingUseCase

RunCategoryScrapingUseCase

RetryScrapingUseCase

CancelScrapingUseCase

ListScrapingJobsUseCase

---

## Promoções

DetectPromotionUseCase

ListPromotionsUseCase

DismissPromotionUseCase

GetPromotionUseCase

---

## Histórico

GetPriceHistoryUseCase

GetLowestPriceUseCase

GetAveragePriceUseCase

ComparePricesUseCase

---

## Configuração

LoadConfigurationUseCase

UpdateConfigurationUseCase

ReloadConfigurationUseCase

---

# Fluxo Principal

Frontend

↓

REST Controller

↓

Request DTO

↓

Use Case

↓

Domain

↓

Repository

↓

Database

↓

Response DTO

↓

Frontend

Nenhuma camada poderá ser ignorada.

---

# Scheduler

O Scheduler será responsável apenas por iniciar casos de uso.

Fluxo

Scheduler

↓

RunScrapingUseCase

↓

Fim

O Scheduler nunca executa scraping diretamente.

---

# Serviços Compartilhados

Somente quando realmente necessário.

Exemplos

PromotionCalculator

CatalogMatcher

PriceNormalizer

HistoryAnalyzer

Todos pertencem à Application Layer.

---

# Regras

Não criar Services genéricos.

Não criar Managers.

Não criar Helpers para lógica de negócio.

Toda regra deverá estar em:

Domain

ou

Application.

---

# DTOs

Existem três categorias.

Request DTO

↓

Application DTO

↓

Response DTO

Nunca reutilizar DTOs entre camadas diferentes.

---

# Tratamento de Exceções

Toda exceção deverá ser classificada.

Categorias

ValidationException

BusinessException

InfrastructureException

ConfigurationException

ExternalServiceException

NotFoundException

ConflictException

UnauthorizedException

InternalException

Nunca lançar Exception diretamente.

---

# Resultado dos Casos de Uso

Todo Use Case deverá retornar:

Success

ou

Failure

Nunca retornar None como indicador de erro.

---

# Transações

Casos de uso que alteram estado deverão utilizar transação.

Fluxo

Begin

↓

Executar

↓

Commit

↓

Fim

Erro

↓

Rollback

↓

Registrar

↓

Retornar erro

---

# Idempotência

Sempre que possível:

POST

deverá evitar duplicações.

PUT

deverá ser idempotente.

DELETE

não deverá gerar erro caso o recurso já esteja inativo.

---

# Critérios Gerais

Todo código deverá:

ser testável;

ser desacoplado;

seguir Clean Architecture;

não conhecer framework;

não acessar infraestrutura diretamente.

---

# API REST

O Backend expõe uma API REST consumida exclusivamente pelo Frontend.

Características:

- JSON como formato padrão;
- UTF-8;
- Stateless;
- Versionamento por URL;
- OpenAPI automático;
- Documentação Swagger.

---

# Prefixo Oficial

```text
/api/v1
```

Todos os endpoints deverão utilizar esse prefixo.

Exemplo:

```text
/api/v1/stores
/api/v1/categories
/api/v1/products
/api/v1/promotions
```

---

# Organização dos Controllers

```text
presentation/

controllers/

health_controller.py

store_controller.py

category_controller.py

catalog_controller.py

promotion_controller.py

history_controller.py

scraping_controller.py

settings_controller.py
```

Cada Controller representa apenas um recurso.

Nunca misturar responsabilidades.

---

# Endpoints

## Health

GET

```text
/api/v1/health
```

Retorna:

- status da aplicação;
- banco;
- scheduler;
- scraping engine;
- versão da aplicação.

---

## Stores

GET

```text
/api/v1/stores
```

Lista todas as lojas.

---

POST

```text
/api/v1/stores
```

Cria uma nova loja.

---

PUT

```text
/api/v1/stores/{id}
```

Atualiza informações.

---

PATCH

```text
/api/v1/stores/{id}/enable
```

Habilita a loja.

---

PATCH

```text
/api/v1/stores/{id}/disable
```

Desabilita a loja.

---

## Categories

GET

```text
/api/v1/categories
```

POST

PUT

PATCH enable

PATCH disable

Mesmo padrão utilizado em Stores.

---

## Products

GET

```text
/api/v1/products
```

Lista produtos do catálogo.

---

GET

```text
/api/v1/products/{id}
```

Detalhes de um produto.

---

GET

```text
/api/v1/products/search
```

Pesquisa textual.

Filtros:

- nome;
- marca;
- categoria;
- loja.

---

## Price History

GET

```text
/api/v1/products/{id}/history
```

Retorna histórico completo.

---

GET

```text
/api/v1/products/{id}/lowest-price
```

Menor preço registrado.

---

GET

```text
/api/v1/products/{id}/statistics
```

Estatísticas.

---

## Promotions

GET

```text
/api/v1/promotions
```

Lista promoções.

Filtros:

categoria

loja

desconto

status

---

GET

```text
/api/v1/promotions/{id}
```

---

PATCH

```text
/api/v1/promotions/{id}/dismiss
```

Descarta promoção.

---

## Scraping

POST

```text
/api/v1/scraping/run
```

Executa scraping completo.

---

POST

```text
/api/v1/scraping/store/{storeId}
```

Executa apenas uma loja.

---

POST

```text
/api/v1/scraping/category/{categoryId}
```

Executa apenas uma categoria.

---

POST

```text
/api/v1/scraping/cancel/{jobId}
```

Cancela execução.

---

GET

```text
/api/v1/scraping/jobs
```

Lista execuções.

---

GET

```text
/api/v1/scraping/jobs/{id}
```

Detalhes.

---

## Settings

GET

```text
/api/v1/settings
```

---

PUT

```text
/api/v1/settings
```

Atualiza configurações.

---

# Paginação

Todo endpoint de listagem deverá suportar:

page

size

sort

order

Valores padrão:

page=1

size=20

order=asc

Limite máximo:

size=100

---

# Filtros

Todos os filtros deverão ser opcionais.

Nunca criar endpoints específicos para cada filtro.

Exemplo:

```text
/products

?category=Notebook

&store=Amazon

&brand=Dell

&minPrice=4000

&maxPrice=7000
```

---

# Ordenação

Campos permitidos:

price

discount

createdAt

updatedAt

title

brand

Qualquer outro campo deverá retornar erro de validação.

---

# Resposta de Sucesso

Formato oficial:

```json
{
  "success": true,
  "data": {},
  "metadata": {},
  "timestamp": "2026-07-18T12:00:00Z"
}
```

---

# Resposta de Erro

Formato oficial:

```json
{
  "success": false,
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Invalid request.",
    "details": []
  },
  "timestamp": "2026-07-18T12:00:00Z"
}
```

---

# Códigos HTTP

200 OK

201 Created

204 No Content

400 Bad Request

401 Unauthorized

403 Forbidden

404 Not Found

409 Conflict

422 Unprocessable Entity

500 Internal Server Error

Nunca retornar 200 para operações que falharam.

---

# Validação

Toda entrada deverá ser validada por Pydantic.

Nunca confiar em dados enviados pelo cliente.

---

# Middleware

Middleware obrigatórios:

- Request ID;
- Logging;
- Tratamento global de exceções;
- Tempo de execução;
- CORS;
- Compressão GZip.

---

# CORS

No MVP:

Permitir apenas:

```text
http://localhost:5173
```

Configuração deve ser parametrizável.

---

# Health Check

O endpoint `/health` deverá verificar:

- API ativa;
- conexão com PostgreSQL;
- Scheduler ativo;
- Playwright disponível.

Não deve executar scraping.

---

# OpenAPI

Toda rota deverá conter:

- resumo;
- descrição;
- parâmetros;
- exemplos;
- códigos de resposta;
- modelos de request e response.

A documentação Swagger deverá estar sempre sincronizada com a implementação.

---

# Observabilidade

Toda requisição deverá registrar:

- Request ID;
- endpoint;
- método HTTP;
- tempo de resposta;
- código HTTP;
- exceções (quando houver).

Nunca registrar informações sensíveis.

---

# Critérios de Aceitação

A API será considerada conforme quando:

[ ] Todos os endpoints seguirem o padrão `/api/v1`.

[ ] Todas as respostas utilizarem o envelope oficial.

[ ] Todos os endpoints possuírem documentação OpenAPI.

[ ] Todas as entradas forem validadas por Pydantic.

[ ] Todos os erros utilizarem o formato padronizado.

[ ] Os Controllers apenas coordenarem a execução dos Use Cases.

---

# Encerramento

A especificação descrita neste documento é a referência oficial para a implementação do Backend do Deal Monitor.

Nenhum endpoint deverá ser criado fora deste padrão sem atualização da documentação e registro de uma ADR correspondente.

Fim da Parte 2.

---

# Dependency Injection

Toda dependência deverá ser resolvida pelo Container da aplicação.

Nunca instanciar dependências dentro dos Use Cases.

Exemplo correto:

Controller

↓

UseCase

↓

Repository (Port)

↓

Repository (Adapter)

↓

Database

---

O Container será responsável por:

- construir objetos;
- resolver dependências;
- compartilhar instâncias quando necessário;
- inicializar serviços de infraestrutura.

---

# Repository Pattern

Todo acesso ao banco deverá ocorrer através de Repositories.

Nunca acessar SQLAlchemy diretamente fora da camada de infraestrutura.

Estrutura:

```text
application/
    ports/
        repositories/

infrastructure/
    persistence/
        repositories/
```

---

# Ports

Os Ports representam contratos.

Exemplos:

CatalogRepositoryPort

StoreRepositoryPort

CategoryRepositoryPort

ProductPriceRepositoryPort

PromotionRepositoryPort

SettingsRepositoryPort

NotificationGatewayPort

BrowserGatewayPort

ClockProviderPort

UuidProviderPort

---

Os Use Cases conhecem apenas os Ports.

Nunca conhecem implementações concretas.

---

# Adapters

Todo Port possui um Adapter correspondente.

Exemplos:

SqlAlchemyCatalogRepository

SqlAlchemyStoreRepository

PlaywrightBrowserGateway

SystemClockProvider

Uuid4Provider

---

Toda implementação concreta pertence exclusivamente à camada Infrastructure.

---

# Organização dos Repositories

Cada Repository deverá possuir responsabilidade única.

Exemplo:

CatalogRepository

Responsável apenas por operações relacionadas ao catálogo.

Nunca consultar promoções.

Nunca consultar configurações.

---

# Organização dos Workers

No MVP haverá apenas um Worker responsável pela execução do Scheduler.

Estrutura:

```text
infrastructure/

scheduler/

scheduler.py

jobs.py

worker.py
```

Responsabilidades:

- iniciar tarefas agendadas;
- controlar concorrência;
- registrar métricas;
- atualizar o status dos ScrapingJobs.

---

# Scheduler

Toda tarefa agendada deverá invocar um Use Case.

Fluxo:

Scheduler

↓

RunScrapingUseCase

↓

Fim

Nunca executar scraping diretamente.

---

# Estratégia de Cache

O MVP não utilizará cache distribuído.

Entretanto, a arquitetura deverá permitir futura integração com Redis.

Os casos de uso nunca deverão conhecer a implementação de cache.

Quando necessário, utilizar um CachePort.

---

# Performance

Princípios:

- evitar consultas N+1;
- utilizar paginação em listagens;
- carregar apenas os campos necessários;
- preferir consultas indexadas;
- utilizar transações apenas quando necessário.

Nunca otimizar prematuramente.

---

# Concorrência

O Backend deverá suportar:

- múltiplas requisições HTTP simultâneas;
- múltiplos ScrapingJobs;
- leitura concorrente do histórico de preços.

Toda operação crítica deverá ser transacional.

---

# Configuração

Todas as configurações deverão ser carregadas por um ConfigurationProvider.

Nunca acessar variáveis de ambiente diretamente nos Use Cases.

Estrutura:

```text
shared/
    configuration/
```

---

# Logging

Utilizar logging estruturado.

Campos mínimos:

- timestamp;
- level;
- request_id;
- scraping_job_id (quando aplicável);
- módulo;
- mensagem.

Nunca registrar:

- senhas;
- tokens;
- cookies;
- dados sensíveis.

---

# Tratamento Global de Exceções

Toda exceção deverá ser convertida em resposta HTTP padronizada.

Fluxo:

Exception

↓

Exception Handler

↓

Error Response DTO

↓

HTTP Response

Nunca expor stack trace ao cliente.

---

# Eventos Internos

Para o MVP, eventos internos serão síncronos.

Exemplos:

PriceCaptured

↓

PromotionDetected

↓

PromotionPersisted

No futuro, esses eventos poderão ser publicados em filas sem alteração dos Use Cases.

---

# Convenções de Implementação

Todo arquivo deverá conter apenas uma responsabilidade.

Todo Use Case deverá conter apenas uma ação.

Toda classe deverá possuir nome explícito.

Evitar abreviações.

Evitar nomes genéricos.

Nunca criar:

- Helper
- Manager
- Utils
- CommonService
- BaseManager

---

# Convenções para o Claude Code

Ao implementar um novo recurso:

1. Criar a Entidade (se necessário).
2. Criar os DTOs.
3. Criar o Port.
4. Criar o Repository.
5. Criar o Use Case.
6. Criar os testes unitários.
7. Criar o Controller.
8. Atualizar a documentação OpenAPI.
9. Registrar dependências no Container.

Nunca inverter essa ordem.

---

# Checklist

Antes de concluir uma funcionalidade:

[ ] Existe um Use Case dedicado.

[ ] O Controller apenas coordena a execução.

[ ] O Use Case conhece apenas Ports.

[ ] O Repository implementa apenas persistência.

[ ] O código possui testes.

[ ] A documentação OpenAPI foi atualizada.

[ ] O recurso foi registrado no Container.

[ ] Os logs seguem o padrão oficial.

[ ] Os erros utilizam o Error Response DTO.

---

# Critérios de Aceitação

O Backend será considerado conforme quando:

[ ] Todas as funcionalidades forem implementadas através de Use Cases.

[ ] Não existirem dependências diretas entre Domain e Infrastructure.

[ ] Todos os Controllers permanecerem finos.

[ ] Toda persistência ocorrer através de Repositories.

[ ] Toda infraestrutura for acessada através de Ports.

[ ] Todos os endpoints seguirem o padrão definido em BACKEND_SPEC.md.

[ ] A documentação OpenAPI estiver completa.

[ ] Os testes automatizados cobrirem os principais fluxos da aplicação.

---

# Encerramento

O BACKEND_SPEC.md define a especificação oficial da camada Backend do Deal Monitor.

Toda implementação deverá seguir este documento em conjunto com:

- ARCHITECTURE.md
- CLEAN_ARCHITECTURE.md
- PROJECT_STRUCTURE.md
- DATABASE_SCHEMA.md
- SCRAPING_ENGINE.md

Qualquer alteração estrutural deverá ser registrada por meio de uma ADR antes da implementação.

Fim do Documento.