Projeto: Deal Monitor

Versão: 1.0

Status: Documento Oficial

---

# 1. Objetivo

Este documento define a estrutura oficial do repositório.

Toda implementação deverá respeitar exatamente esta organização.

Nenhuma pasta deverá ser criada sem necessidade.

Sempre priorizar organização.

---

# 2. Estrutura Geral

deal-monitor/

├── backend/

├── frontend/

├── docs/

├── tests/

├── scripts/

├── database/

├── logs/

├── config/

├── .gitignore

├── README.md

├── pyproject.toml

├── package.json

└── CLAUDE.md

Cada diretório possui responsabilidade única.

---

# 3. Backend

backend/

Representa toda aplicação Python.

Nunca colocar arquivos React aqui.

Nunca colocar arquivos SQL soltos.

Nunca colocar documentação.

---

Estrutura

backend/

app/

api/

controllers/

services/

repositories/

models/

schemas/

connectors/

scheduler/

database/

core/

config/

utils/

exceptions/

middlewares/

dependencies/

main.py

---

# 4. app/

Responsável apenas por organizar módulos internos.

Não deve conter regra de negócio.

Serve apenas como agrupador.

---

# 5. api/

Representa a camada HTTP.

Pode conter:

Routers

Versionamento

Registro de endpoints

Não pode conter:

Regra de negócio

Persistência

Playwright

---

# 6. controllers/

Responsabilidade:

Receber requisições HTTP.

Validar entrada.

Chamar Services.

Retornar respostas.

Nunca:

Consultar banco.

Executar scraping.

Aplicar regras.

Controller deve possuir poucas linhas.

---

# 7. services/

Camada mais importante.

Toda regra pertence aqui.

Exemplos:

ProductService

CategoryService

StoreService

PromotionService

SearchService

HistoryService

ConfigurationService

SchedulerService

Nunca acessar Playwright diretamente.

Nunca acessar React.

Nunca responder HTTP.

---

# 8. repositories/

Persistência.

Cada entidade possui exatamente um Repository.

Exemplo

ProductRepository

CategoryRepository

StoreRepository

HistoryRepository

Repositories nunca:

Calculam descontos.

Aplicam filtros de negócio.

Executam scraping.

---

# 9. models/

Representam tabelas.

Utilizar SQLAlchemy.

Uma classe por arquivo.

Exemplo

product.py

category.py

store.py

history.py

Nunca utilizar Models para Response.

---

# 10. schemas/

DTOs.

Utilizar Pydantic.

Separar:

Request

Response

Internal DTO

Nunca misturar.

---

Exemplo

product_request.py

product_response.py

product_filter.py

---

# 11. connectors/

Um diretório para cada loja.

backend/

connectors/

amazon/

kabum/

mercadolivre/

pichau/

terabyte/

Cada connector possui estrutura própria.

---

amazon/

connector.py

parser.py

normalizer.py

selectors.py

config.py

Nunca acessar banco.

Nunca chamar Repository.

---

# 12. scheduler/

Contém atualização automática.

Arquivos

scheduler.py

jobs.py

worker.py

Nunca conter scraping.

Somente orquestração.

---

# 13. database/

Contém configuração SQLAlchemy.

engine.py

session.py

base.py

migration.py

Não colocar Models aqui.

---

# 14. core/

Configuração global.

Logger

Settings

Constantes

Enums

Nunca colocar regra de negócio.

---

# 15. config/

Arquivos de configuração.

Categorias.

Lojas.

Scheduler.

Timeout.

User-Agent.

Seletores.

Exemplo

stores.yaml

scheduler.yaml

categories.yaml

Nunca hardcode.

---

# 16. exceptions/

Exceções customizadas.

Exemplo

ConnectorException

CategoryNotFound

StoreNotFound

TimeoutException

Nunca utilizar Exception diretamente.

---

# 17. dependencies/

Injeção de dependência.

Factories.

Providers.

Singletons.

Nunca colocar regras.

---

# 18. middlewares/

Middlewares FastAPI.

Logging.

Tempo de resposta.

Tratamento global.

Nunca lógica de negócio.

---

# 19. utils/

Apenas funções realmente genéricas.

Exemplo

Conversão monetária.

Datas.

Strings.

Hash.

Nunca criar:

helpers.py

utils2.py

misc.py

---

# 20. Frontend

frontend/

Aplicação React.

Nunca acessar banco.

Nunca executar scraping.

Sempre consumir REST.

---

Estrutura

frontend/

src/

assets/

components/

pages/

services/

hooks/

contexts/

types/

routes/

layouts/

styles/

utils/

---

# 21. pages/

Cada tela possui uma página.

Dashboard

Products

Categories

Stores

History

Settings

Nunca componentes reutilizáveis.

---

# 22. components/

Componentes reutilizáveis.

Exemplo

Table

Button

Card

Modal

SearchInput

ProductCard

PriceBadge

PromotionBadge

---

# 23. services/

Comunicação HTTP.

Exemplo

product_service.ts

category_service.ts

history_service.ts

Nunca colocar lógica visual.

---

# 24. hooks/

React Hooks.

Exemplo

useProducts

useHistory

useCategories

useStores

---

# 25. routes/

Rotas.

Nada além disso.

---

# 26. assets/

Imagens.

Ícones.

Logo.

---

# 27. styles/

CSS global.

Tema.

Variáveis.

Tailwind.

---

# 28. types/

Interfaces TypeScript.

Nunca duplicar DTO.

Sempre manter compatível com Backend.

---

# 29. Tests

tests/

backend/

frontend/

integration/

fixtures/

mocks/

Nunca misturar.

---

# 30. Scripts

scripts/

Inicialização.

Backup.

Importação.

Limpeza.

Nunca colocar regra de negócio.

---

# 31. Logs

logs/

Logs diários.

Nunca versionar.

Adicionar ao .gitignore.

---

# 32. Database

database/

Apenas SQLite.

Exemplo

deal_monitor.db

Backups.

Nunca código.

---

# 33. Docs

docs/

Toda documentação.

Nunca colocar código.

Nunca colocar imagens temporárias.

---

Estrutura

00-project/

01-architecture/

02-backend/

03-frontend/

04-database/

05-scraping/

06-testing/

07-guides/

08-backlog/

09-prompts/

---

# 34. Nomeação

Pastas

snake_case

Arquivos

snake_case

Classes

PascalCase

Métodos

snake_case

Constantes

UPPER_CASE

---

# 35. Como adicionar um novo Connector

Criar pasta

connectors/nova_loja/

Adicionar

connector.py

parser.py

normalizer.py

selectors.py

config.py

Registrar Connector.

Fim.

Nenhuma outra camada deve ser modificada.

---

# 36. Como adicionar uma nova tela

Criar

pages/

Criar rota.

Criar Service.

Consumir API.

Nunca acessar banco.

---

# 37. Como adicionar um novo Endpoint

Controller

↓

Service

↓

Repository

↓

Model

↓

Schema

Nunca pular camadas.

---

# 38. Como adicionar uma nova tabela

Model

Migration

Repository

DTO

Service

Endpoint

Sempre nesta ordem.

---

# 39. Arquivos Proibidos

helpers.py

utils2.py

misc.py

temp.py

novo.py

teste.py

final.py

codigo.py

Sempre utilizar nomes descritivos.

---

# 40. Checklist do Claude Code

Antes de criar um arquivo verificar:

Existe pasta correta?

Existe módulo correto?

Existe camada correta?

Já existe arquivo semelhante?

Estou quebrando alguma regra arquitetural?

Este arquivo realmente precisa existir?

---

# 41. Estrutura Esperada do Projeto Final

Ao concluir o MVP o projeto deverá possuir aproximadamente:

Backend

80 a 120 arquivos

Frontend

40 a 60 arquivos

Docs

50+ documentos

Testes

100+ testes

Connectors

5

Services

10+

Repositories

8+

Models

8+

Pages

8+

Sem arquivos órfãos.

Sem diretórios vazios.

Sem duplicação.

---

# 42. Regra Final

A estrutura do projeto é considerada parte da arquitetura.

Mover arquivos para locais incorretos caracteriza violação arquitetural.

Todo novo módulo deverá seguir este documento integralmente.

Fim do Documento.