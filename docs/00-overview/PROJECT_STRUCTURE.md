# PROJECT_STRUCTURE.md

Projeto: Deal Monitor

Versão: 1.0

Status: Documento Oficial

Dependências:

ARCHITECTURE.md

CLEAN_ARCHITECTURE.md

---

# Objetivo

Este documento define a estrutura oficial do repositório do Deal Monitor.

Toda implementação deverá respeitar exatamente esta organização.

Não é permitido criar novos diretórios sem aprovação arquitetural (ADR).

---

# Princípios

A estrutura deve:

- facilitar localização de código;
- reduzir acoplamento;
- separar domínio de infraestrutura;
- facilitar testes;
- permitir crescimento do projeto;
- facilitar navegação pelo Claude Code.

---

# Estrutura Geral

```text
deal-monitor/

├── backend/
├── frontend/
├── docs/
├── scripts/
├── docker/
├── .github/
├── tests/
├── tools/
├── .env.example
├── docker-compose.yml
├── Makefile
├── README.md
└── LICENSE
```

---

# Responsabilidade

backend/

Código Python.

Nunca conter React.

---

frontend/

Código React.

Nunca conter Python.

---

docs/

Toda documentação oficial.

Nunca conter código executável.

---

scripts/

Scripts auxiliares.

Migrações.

Importações.

Utilidades.

---

docker/

Dockerfiles.

Compose.

Infraestrutura local.

---

tests/

Testes compartilhados.

Fixtures.

Recursos auxiliares.

---

tools/

Ferramentas de desenvolvimento.

Geradores.

Validadores.

Linters.

---

# Backend

```text
backend/

├── app/
├── bootstrap/
├── infrastructure/
├── framework/
├── shared/
├── tests/
├── alembic/
├── pyproject.toml
└── README.md
```

---

# app/

Representa o núcleo da aplicação.

Nunca conter código específico de framework.

---

# Estrutura do app

```text
app/

├── domain/
├── application/
├── interfaces/
└── common/
```

---

# domain/

Representa o negócio.

Estrutura:

```text
domain/

├── entities/
├── value_objects/
├── enums/
├── services/
├── specifications/
├── exceptions/
└── interfaces/
```

---

# application/

```text
application/

├── use_cases/
├── dto/
├── commands/
├── queries/
├── services/
├── ports/
├── validators/
└── mappers/
```

---

# interfaces/

```text
interfaces/

├── controllers/
├── presenters/
├── mappers/
└── serializers/
```

---

# common/

Código compartilhado.

Somente componentes realmente reutilizáveis.

Nunca colocar lógica de negócio.

---

# bootstrap/

Inicialização da aplicação.

Estrutura:

```text
bootstrap/

├── startup.py
├── container.py
├── registry.py
├── dependencies.py
└── lifecycle.py
```

---

# infrastructure/

```text
infrastructure/

├── persistence/
├── scraping/
├── notifications/
├── configuration/
├── storage/
├── scheduler/
└── monitoring/
```

---

# persistence/

```text
persistence/

├── sqlite/
├── models/
├── mappers/
└── migrations/
```

---

# scraping/

```text
scraping/

├── connectors/
├── parsers/
├── normalizers/
├── validators/
├── browser/
└── pipelines/
```

---

# monitoring/

Logs.

Métricas.

Tracing.

Health checks.

---

# framework/

Responsável por integrar tecnologias externas.

```text
framework/

├── api/
├── database/
├── scheduler/
├── configuration/
└── logging/
```

---

# shared/

Código compartilhado entre infraestrutura e framework.

Evitar crescimento excessivo.

---

# Frontend

O frontend possui responsabilidade exclusiva pela interface do usuário.

Nunca implementa regras de negócio.

Nunca acessa banco de dados.

Toda comunicação ocorre através da API.

---

Estrutura oficial

frontend/

├── src/
│
├── public/
│
├── tests/
│
├── package.json
│
├── vite.config.ts
│
└── README.md

---

# Estrutura do src

src/

├── app/
├── components/
├── pages/
├── layouts/
├── routes/
├── services/
├── hooks/
├── contexts/
├── assets/
├── styles/
├── utils/
├── types/
└── tests/

---

# app/

Responsável por:

- inicialização;
- providers;
- configuração global;
- bootstrap do React.

---

# components/

Componentes reutilizáveis.

Exemplos:

Button

Card

DataGrid

SearchBar

PriceCard

PromotionBadge

CategorySelector

StoreSelector

Modal

Dialog

---

# pages/

Cada página representa uma rota principal.

Exemplos:

Dashboard

Products

Promotions

History

Settings

Stores

Categories

Scheduler

Logs

---

# layouts/

Layouts compartilhados.

MainLayout

AdminLayout

SettingsLayout

---

# routes/

Configuração das rotas.

Nunca colocar lógica de negócio.

---

# services/

Clientes HTTP.

Exemplo:

BackendAPI

PromotionAPI

CatalogAPI

HistoryAPI

ConfigurationAPI

Nunca implementar regras.

---

# hooks/

Hooks reutilizáveis.

Exemplos:

useProducts()

usePromotions()

useHistory()

usePagination()

useFilters()

---

# contexts/

Context API.

Responsável apenas por estado global.

Exemplos:

ThemeContext

ConfigurationContext

AuthenticationContext (futuro)

---

# assets/

Imagens.

Ícones.

Fontes.

Logotipos.

---

# styles/

CSS global.

Tailwind.

Variáveis visuais.

---

# utils/

Funções auxiliares.

Nunca implementar regras de negócio.

---

# types/

Tipos TypeScript.

DTOs.

Interfaces.

Enums do frontend.

---

# Testes

Estrutura

tests/

├── unit/
├── integration/
├── e2e/
└── fixtures/

---

# Organização da Documentação

docs/

├── 01-architecture/
├── 02-backend/
├── 03-frontend/
├── 04-scraping/
├── 05-database/
├── 06-api/
├── 07-testing/
├── 08-devops/
├── 09-decisions/
├── 10-prompts/
└── assets/

---

# Organização dos ADRs

docs/

09-decisions/

├── ADR-000.md
├── ADR-001.md
├── ADR-002.md

...

Cada decisão arquitetural recebe um documento próprio.

Nunca alterar ADRs antigos.

Criar sempre uma nova ADR.

---

# Organização dos Prompts

docs/

10-prompts/

├── backlog/
├── implementation/
├── refactoring/
├── testing/
├── debugging/
└── review/

Todos os prompts utilizados pelo Claude Code permanecerão versionados.

---

# Convenções de Nome

Arquivos Python

snake_case.py

Classes

PascalCase

Funções

snake_case

Constantes

UPPER_CASE

Variáveis

snake_case

---

# Convenções React

Componentes

PascalCase.tsx

Hooks

useSomething.ts

Contexts

SomethingContext.tsx

Tipos

something.types.ts

---

# Arquivos Proibidos

Nunca criar:

helper.py

utils.py (genérico)

manager.py

common.py (genérico)

misc.py

temp.py

test2.py

new.py

final.py

copy.py

---

Cada arquivo deve possuir responsabilidade explícita.

---

# Critérios de Aceitação

A estrutura será considerada correta quando:

[ ] Cada módulo possuir responsabilidade única.

[ ] O domínio permanecer isolado.

[ ] Backend e Frontend permanecerem independentes.

[ ] Toda documentação estiver organizada.

[ ] Não existirem diretórios genéricos.

# PROJECT_STRUCTURE.md

Projeto: Deal Monitor

Versão: 1.0

Status: Documento Oficial

---

# Organização dos Testes

Todos os testes devem ficar separados do código de produção.

Nenhum arquivo de teste deve ser armazenado junto aos módulos da aplicação, exceto quando houver necessidade específica de testes locais.

Estrutura oficial:

```text
tests/

├── unit/
│
├── integration/
│
├── e2e/
│
├── fixtures/
│
├── mocks/
│
├── fakes/
│
├── builders/
│
└── resources/
```

---

# Testes Unitários

Responsáveis por validar componentes isolados.

Nunca devem depender de:

- SQLite
- PostgreSQL
- Playwright
- Internet
- APIs externas

Devem executar rapidamente.

---

# Testes de Integração

Validam integração entre módulos.

Podem utilizar:

- Banco local
- API local
- Arquivos temporários

Nunca acessar ambientes externos.

---

# Testes End-to-End

Validam o comportamento completo do sistema.

Fluxo esperado:

Frontend

↓

API

↓

Application

↓

Infrastructure

↓

Banco

---

# Fixtures

Fixtures representam dados reutilizáveis.

Exemplos:

Produtos

Categorias

Lojas

Históricos

Promoções

Configurações

Nunca utilizar dados aleatórios quando houver fixture disponível.

---

# Builders

Builders facilitam criação de objetos de teste.

Exemplos:

CatalogProductBuilder

PromotionBuilder

PriceHistoryBuilder

StoreBuilder

CategoryBuilder

---

# Mocks

Utilizados quando for necessário validar interação entre componentes.

Exemplos:

NotificationGateway

StorageGateway

ConfigurationProvider

---

# Fakes

Implementações simplificadas.

Exemplos:

FakeCatalogRepository

FakeBrowserGateway

FakeClockProvider

---

# Organização das Configurações

Toda configuração deverá ficar centralizada.

Estrutura:

```text
config/

├── application.yaml
├── logging.yaml
├── scraping.yaml
├── scheduler.yaml
├── categories.yaml
├── stores.yaml
└── development.yaml
```

---

# Variáveis de Ambiente

Arquivos:

```text
.env

.env.example

.env.local

.env.test
```

Nunca versionar:

.env

---

# Organização do Banco

Estrutura:

```text
database/

├── migrations/
├── seeds/
├── backups/
├── scripts/
└── README.md
```

---

# Migrations

Toda alteração estrutural deverá possuir migration.

Nunca alterar banco manualmente.

---

# Seeds

Responsáveis por popular:

Categorias

Lojas

Configurações iniciais

Dados de desenvolvimento

Nunca conter dados sensíveis.

---

# Backups

Utilizados apenas para ambientes locais.

Nunca armazenar backups dentro do repositório.

O diretório existe apenas para scripts auxiliares.

---

# Organização do Scraping

Estrutura:

```text
scraping/

├── connectors/
├── browser/
├── parsers/
├── extractors/
├── normalizers/
├── validators/
├── pipelines/
├── selectors/
└── resources/
```

---

# Connectors

Cada loja possui um Connector próprio.

Exemplos:

AmazonConnector

KabumConnector

PichauConnector

TerabyteConnector

MercadoLivreConnector

---

# Browser

Responsável exclusivamente pelo Playwright.

Nunca interpretar HTML.

---

# Parsers

Transformam HTML em estruturas navegáveis.

---

# Extractors

Extraem dados da estrutura HTML.

Exemplos:

Título

Preço

Disponibilidade

Imagem

Link

Avaliação

---

# Normalizers

Padronizam dados.

Exemplos:

Nome

Preço

Moeda

Categorias

URLs

---

# Validators

Validam integridade dos dados extraídos.

Nunca executam scraping.

---

# Pipelines

Orquestram o fluxo:

Fetch

↓

Parse

↓

Extract

↓

Normalize

↓

Validate

↓

Persist

---

# Selectors

Armazenam seletores CSS e XPath.

Cada loja possui seus próprios arquivos.

Nunca misturar seletores de lojas diferentes.

---

# Organização de Logs

Estrutura:

```text
logs/

├── application/
├── scraping/
├── scheduler/
├── api/
└── archive/
```

Em produção, preferencialmente utilizar logging estruturado e armazenamento externo.

---

# Organização de Scripts

Estrutura:

```text
scripts/

├── setup/
├── database/
├── scraping/
├── maintenance/
├── release/
└── development/
```

Scripts devem ser idempotentes sempre que possível.

---

# Organização do Docker

Estrutura:

```text
docker/

├── backend/
├── frontend/
├── database/
├── development/
└── production/
```

Cada serviço deve possuir configuração própria.

---

# Organização do GitHub

Estrutura:

```text
.github/

├── workflows/
├── ISSUE_TEMPLATE/
├── PULL_REQUEST_TEMPLATE.md
├── CODEOWNERS
└── dependabot.yml
```

---

# Workflows

Separar pipelines por responsabilidade.

Exemplos:

CI

Lint

Test

Build

Release

Docker

---

# Recursos Compartilhados

Estrutura:

```text
resources/

├── icons/
├── images/
├── logos/
├── mock-data/
└── templates/
```

Nunca armazenar arquivos temporários.

---

# Estrutura Final Esperada

O projeto deverá manter separação clara entre:

- Código de domínio
- Aplicação
- Apresentação
- Infraestrutura
- Frameworks
- Testes
- Documentação
- Scripts
- Configuração

Nenhum módulo deve assumir responsabilidades pertencentes a outro.

---

# Checklist

Antes de adicionar um novo arquivo:

[ ] A pasta correta já existe?

[ ] O arquivo possui responsabilidade única?

[ ] Existe documentação correspondente?

[ ] A nomenclatura segue o padrão oficial?

[ ] Não há duplicação de responsabilidade?

---

# Critérios de Aceitação

A estrutura do projeto será considerada correta quando:

[ ] A organização permanecer consistente.

[ ] Cada módulo possuir responsabilidade única.

[ ] O domínio permanecer isolado.

[ ] A infraestrutura permanecer desacoplada.

[ ] A documentação refletir a estrutura implementada.

[ ] Novos desenvolvedores conseguirem localizar facilmente qualquer componente.

---

# Encerramento

O PROJECT_STRUCTURE.md define a organização oficial do repositório do Deal Monitor.

Toda implementação deverá respeitar esta estrutura.

Qualquer alteração estrutural deverá ser registrada por meio de uma ADR antes de ser aplicada.

Fim do Documento.
