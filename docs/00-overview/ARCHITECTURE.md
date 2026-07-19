# ARCHITECTURE

Projeto: Deal Monitor

Versão: 2.0

Status: Documento Oficial

Baseado na ADR-000-Catalog-Model

---

# Capítulo 1 - Visão Geral da Arquitetura

## Objetivo

O Deal Monitor é um sistema local de monitoramento de preços de e-commerces brasileiros baseado em arquitetura modular.

O sistema possui quatro objetivos principais:

- Monitorar produtos continuamente.
- Consolidar anúncios de diferentes lojas em um catálogo único.
- Armazenar histórico completo de preços.
- Exibir uma interface web para pesquisa, filtros e comparação.

O sistema **não realiza compras**, **não automatiza checkout** e **não depende de APIs públicas**. A coleta de dados é feita por scraping de forma responsável, respeitando limites de acesso e tratando mudanças de estrutura dos sites.

---

# Princípios Arquiteturais

Toda decisão arquitetural deve obedecer aos seguintes princípios.

## 1. Domain First

O domínio é o centro do sistema.

Nenhuma decisão de banco de dados, framework ou interface pode alterar o domínio.

---

## 2. Catalog First

O sistema é orientado por um catálogo universal.

Nunca tratar anúncios de lojas como produtos independentes.

Sempre consolidar anúncios em um CatalogProduct.

---

## 3. Connectors Independentes

Cada loja é completamente independente.

Um problema na Amazon nunca pode interromper o scraping da Kabum.

Cada connector possui:

- configuração própria;
- parser próprio;
- seletores próprios;
- normalizador próprio.

---

## 4. Camadas Bem Definidas

Cada camada possui uma única responsabilidade.

Nunca misturar:

- scraping;
- persistência;
- regra de negócio;
- interface.

---

## 5. Evolução Incremental

Toda arquitetura deve permitir adicionar:

- novas lojas;
- novas categorias;
- novos filtros;
- novos motores de scraping;
- novos algoritmos de matching.

Sem necessidade de alterar módulos existentes.

---

# Objetivos Não Funcionais

O sistema deverá priorizar:

- simplicidade;
- legibilidade;
- facilidade de manutenção;
- baixo acoplamento;
- alta coesão;
- fácil depuração;
- execução local.

---

# Tecnologias Oficiais

## Backend

Python 3.12

FastAPI

SQLAlchemy

SQLite

Playwright

BeautifulSoup

httpx

APScheduler

Pydantic

---

## Frontend

React

TypeScript

Vite

Tailwind CSS

TanStack Query

React Router

---

## Banco

SQLite

Banco único local.

Sem dependência de Docker.

Sem necessidade de PostgreSQL no MVP.

---

# Arquitetura Geral

A arquitetura é composta por nove módulos principais.

```
Frontend
        │
        ▼
REST API
        │
        ▼
Application Services
        │
        ▼
Repositories
        │
        ▼
SQLite
```

Em paralelo:

```
Scheduler
        │
        ▼
Connectors
        │
        ▼
Parser
        │
        ▼
Normalizer
        │
        ▼
Catalog Matcher
        │
        ▼
Repositories
```

Esses dois fluxos compartilham apenas o domínio e a persistência.

---

# Componentes Principais

O sistema é composto pelos seguintes módulos.

## Dashboard

Responsável pela interação com o usuário.

Permite:

- pesquisar produtos;
- aplicar filtros;
- visualizar histórico;
- comparar lojas;
- acompanhar promoções.

Nunca executa scraping.

Nunca acessa banco diretamente.

---

## API

Responsável por disponibilizar dados ao frontend.

A API apenas:

- recebe requisições;
- valida entradas;
- chama Services;
- retorna respostas.

Nunca contém regra de negócio.

---

## Services

Representam o cérebro da aplicação.

Toda lógica pertence aos Services.

Exemplos:

ProductService

PromotionService

HistoryService

SearchService

CatalogService

ConfigurationService

SchedulerService

---

## Repositories

Responsáveis exclusivamente pela persistência.

Nunca:

- calculam promoções;
- executam scraping;
- validam regras de negócio.

---

## Scheduler

Responsável pelas atualizações automáticas.

Pode iniciar:

- scraping manual;
- scraping programado;
- atualização parcial;
- atualização completa.

Não conhece HTML.

Não conhece banco.

Apenas coordena execuções.

---

## Connectors

Existe exatamente um connector para cada loja.

Exemplo

AmazonConnector

KabumConnector

PichauConnector

TerabyteConnector

MercadoLivreConnector

Cada connector conhece apenas uma loja.

Nunca reutilizar seletores entre lojas.

---

## Parser

Converte HTML bruto em objetos estruturados.

Nunca persiste dados.

Nunca calcula promoções.

Nunca cria CatalogProducts.

---

## Normalizer

Padroniza informações vindas das lojas.

Exemplo

"R$4.999"

↓

4999.00

Outro exemplo

"Processador AMD Ryzen™ 7 9700X"

↓

"amd ryzen 7 9700x"

Toda padronização ocorre aqui.

---

## Catalog Matcher

Novo componente introduzido pela ADR-000.

Responsável por responder:

"Este anúncio pertence a qual produto do catálogo?"

Nenhum outro componente pode tomar essa decisão.

---

## Promotion Engine

Responsável por detectar promoções.

Não utiliza informações da loja como verdade absoluta.

Toda promoção é recalculada.

Exemplos:

- menor preço histórico;
- queda percentual;
- preço abaixo da média.

---

# Fluxo Geral do Sistema

```
Usuário

↓

Frontend

↓

API

↓

Services

↓

Repositories

↓

SQLite
```

Atualizações seguem outro fluxo.

```
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

Banco de Dados
```

Todos os componentes possuem responsabilidades independentes.

Fim do Capítulo 1.

# Capítulo 2 — Camadas Arquiteturais

---

# Objetivo

Este capítulo define todas as camadas do sistema.

Cada camada possui uma única responsabilidade.

Nenhuma camada pode executar responsabilidades pertencentes a outra.

Toda implementação deverá respeitar estas regras.

---

# Visão Geral

A arquitetura do Deal Monitor é organizada em sete camadas.

```text

Presentation

↓

API

↓

Application

↓

Domain

↓

Infrastructure

↓

Persistence

↓

External Systems

```

A comunicação sempre acontece de cima para baixo.

Nunca no sentido contrário.

---

# 1. Presentation Layer

## Objetivo

Responsável pela experiência do usuário.

Implementada em React.

Nunca contém regra de negócio.

---

## Componentes

Pages

Components

Layouts

Hooks

Routes

Contexts

Assets

---

## Responsabilidades

Renderizar dados.

Enviar requisições.

Receber respostas.

Atualizar interface.

Aplicar filtros visuais.

Exibir gráficos.

Exibir tabelas.

---

## Não pode

Executar scraping.

Consultar SQLite.

Calcular promoções.

Calcular estatísticas.

Executar SQL.

Conhecer HTML das lojas.

Conhecer Playwright.

---

# Dependências Permitidas

Presentation

↓

API

Apenas.

---

# 2. API Layer

## Objetivo

Expor endpoints REST.

---

## Componentes

Controllers

Routers

Schemas

Middlewares

Validators

---

## Responsabilidades

Receber requisição.

Validar parâmetros.

Converter DTOs.

Invocar Services.

Retornar resposta.

---

## Não pode

Executar scraping.

Consultar banco diretamente.

Criar SQL.

Aplicar regras de promoção.

Criar Product.

Criar PriceHistory.

---

# Dependências

API

↓

Application

Nunca diretamente para Repository.

---

# 3. Application Layer

## Objetivo

Orquestrar casos de uso.

Esta é a camada onde vivem os Services.

---

## Exemplos

SearchService

HistoryService

PromotionService

CatalogService

StoreService

CategoryService

ConfigurationService

SchedulerService

---

## Responsabilidades

Executar casos de uso.

Orquestrar chamadas.

Aplicar regras.

Controlar transações.

Controlar fluxo.

---

## Não pode

Conhecer HTML.

Conhecer CSS.

Conhecer React.

Conhecer Playwright.

Conhecer SQL.

---

# Dependências

Application

↓

Domain

↓

Repositories

Nunca diretamente para SQLite.

---

# 4. Domain Layer

## Objetivo

Representar o negócio.

É a camada mais importante.

---

## Componentes

CatalogProduct

StoreProduct

Category

Store

PriceHistory

Promotion

ScrapingJob

Value Objects

Enums

Domain Services

---

## Responsabilidades

Representar conceitos.

Garantir consistência.

Definir invariantes.

Definir identidade.

---

## Não pode

Executar HTTP.

Executar scraping.

Persistir dados.

Conhecer SQLite.

Conhecer React.

Conhecer FastAPI.

---

# Dependências

Nenhuma.

O domínio é independente.

---

# 5. Infrastructure Layer

## Objetivo

Conversar com o mundo externo.

---

## Componentes

Connectors

Fetchers

Playwright

HTTP Client

Parser

Normalizer

Catalog Matcher

Scheduler

Logger

Configuration Loader

---

## Responsabilidades

Buscar dados.

Converter HTML.

Executar navegador.

Consumir sites.

Carregar arquivos.

Gerenciar scraping.

---

## Não pode

Executar SQL.

Criar Controllers.

Renderizar páginas.

---

# Dependências

Infrastructure

↓

Domain

Nunca para Frontend.

---

# 6. Persistence Layer

## Objetivo

Persistir informações.

---

## Componentes

Repositories

SQLite

SQLAlchemy

Session

Engine

Migrations

---

## Responsabilidades

Salvar.

Atualizar.

Consultar.

Excluir (quando permitido).

---

## Não pode

Executar scraping.

Calcular descontos.

Criar promoções.

Normalizar títulos.

Executar lógica.

---

# Dependências

Persistence

↓

SQLite

---

# 7. External Systems

## Objetivo

Representar sistemas externos.

---

## Exemplos

Amazon

Kabum

Pichau

Terabyte

Mercado Livre

Arquivos YAML

Sistema Operacional

---

## Características

Não controlamos estes sistemas.

Podem mudar.

Podem falhar.

Podem bloquear scraping.

Devem ser tratados como não confiáveis.

---

# Fluxo Oficial

```text

Presentation

↓

API

↓

Application

↓

Domain

↓

Persistence

```

Em paralelo

```text

Scheduler

↓

Fetch

↓

Parse

↓

Normalize

↓

Catalog Matcher

↓

Persistence

```

---

# Fluxo Proibido

Nunca permitir

Presentation

↓

SQLite

---

Nunca permitir

Presentation

↓

Playwright

---

Nunca permitir

Controller

↓

Repository

---

Nunca permitir

Connector

↓

Controller

---

Nunca permitir

Repository

↓

Service

---

Nunca permitir

Domain

↓

SQLite

---

Nunca permitir

Parser

↓

Repository

---

Nunca permitir

Normalizer

↓

SQLite

---

# Regras de Comunicação

Cada camada conversa apenas com sua camada imediatamente inferior.

Exemplo

Controller

↓

Service

↓

Repository

Correto.

---

Exemplo

Controller

↓

Repository

Errado.

---

Exemplo

Parser

↓

Catalog Matcher

↓

Repository

Correto.

---

Exemplo

Parser

↓

SQLite

Errado.

---

# Regras de Dependência

Camadas superiores conhecem inferiores.

Camadas inferiores nunca conhecem superiores.

---

Exemplo

Frontend conhece API.

API não conhece Frontend.

---

Service conhece Repository.

Repository nunca conhece Service.

---

Catalog Matcher conhece Domain.

Domain nunca conhece Catalog Matcher.

---

# Objetivo Final

Toda nova funcionalidade deverá responder:

Em qual camada ela pertence?

Caso a resposta não seja clara, a implementação deve ser interrompida até que a arquitetura seja revisada.

Fim do Capítulo 2.

# Capítulo 3 — Arquitetura do Pipeline de Scraping

---

# Objetivo

O Pipeline de Scraping é responsável por transformar páginas HTML de diferentes e-commerces em informações estruturadas e persistidas no banco de dados.

Cada etapa possui uma responsabilidade única.

Nenhuma etapa deve conhecer detalhes internos das demais.

---

# Visão Geral

O pipeline oficial do sistema é:

Fetch

↓

Parse

↓

Normalize

↓

Match

↓

Persist

↓

Analyze

Cada etapa recebe um objeto de entrada e produz um objeto de saída.

Nunca compartilhar estado global.

Nunca reutilizar objetos mutáveis entre etapas.

---

# Fluxo Completo

```text
Scheduler

↓

Connector

↓

Fetcher

↓

Raw HTML

↓

Parser

↓

Parsed Products

↓

Normalizer

↓

Normalized Products

↓

Catalog Matcher

↓

CatalogProduct + StoreProduct

↓

Persistence Adapter

↓

SQLite

↓

Promotion Engine

↓

Dashboard
```

---

# Estágio 1 — Fetch

## Objetivo

Obter o conteúdo da página da loja.

Nenhuma interpretação deve acontecer aqui.

---

## Entrada

Store

Categoria

URL

Configuração

---

## Saída

RawPage

---

## Responsabilidades

Realizar requisição HTTP ou navegação Playwright.

Aguardar carregamento da página.

Executar scroll quando necessário.

Capturar HTML final.

Registrar tempo da operação.

---

## Não pode

Extrair preços.

Extrair produtos.

Interpretar HTML.

Criar objetos de domínio.

Salvar dados.

---

## Interface Esperada

```python
fetch(category) -> RawPage
```

---

# Estágio 2 — Parse

## Objetivo

Converter HTML em objetos estruturados.

---

## Entrada

RawPage

---

## Saída

ParsedProduct[]

---

## Responsabilidades

Localizar cards de produtos.

Extrair:

- título;
- preço;
- URL;
- imagem;
- disponibilidade;
- identificadores.

Nenhuma limpeza ou padronização ocorre nesta etapa.

---

## Não pode

Calcular desconto.

Normalizar texto.

Persistir dados.

Comparar produtos.

---

## Interface

```python
parse(raw_page) -> list[ParsedProduct]
```

---

# Estágio 3 — Normalize

## Objetivo

Padronizar os dados vindos de diferentes lojas.

---

## Entrada

ParsedProduct

---

## Saída

NormalizedProduct

---

## Exemplos

### Preço

```
R$ 4.999,90
```

↓

```
4999.90
```

---

### Nome

```
Processador AMD Ryzen™ 7 9700X OEM
```

↓

```
amd ryzen 7 9700x
```

---

### Disponibilidade

```
Comprar
```

↓

```
IN_STOCK
```

---

## Responsabilidades

Remover caracteres especiais.

Padronizar moeda.

Padronizar fabricante.

Normalizar espaços.

Gerar título normalizado.

Extrair modelo.

Extrair capacidade quando aplicável.

---

## Não pode

Criar Product.

Consultar banco.

Calcular promoções.

---

# Estágio 4 — Match

## Objetivo

Associar anúncios ao catálogo.

Este estágio é exclusivo do Catalog Matcher.

---

## Entrada

NormalizedProduct

---

## Saída

CatalogMatch

---

## Processo

Verificar EAN.

↓

Verificar fabricante.

↓

Verificar modelo.

↓

Verificar capacidade.

↓

Comparar título.

↓

Calcular score.

↓

Associar.

↓

Ou criar CatalogProduct.

---

## Resultado

Sempre retorna exatamente um CatalogProduct.

Nunca retorna múltiplos.

---

# Estágio 5 — Persist

## Objetivo

Salvar alterações.

---

## Entrada

CatalogMatch

---

## Responsabilidades

Criar StoreProduct quando necessário.

Criar PriceHistory.

Atualizar last_seen.

Atualizar disponibilidade.

Registrar execução.

---

## Não pode

Executar scraping.

Comparar títulos.

Normalizar texto.

---

# Estágio 6 — Analyze

## Objetivo

Gerar inteligência sobre os dados.

---

## Responsabilidades

Calcular:

Menor preço.

Maior preço.

Preço médio.

Promoções.

Mudança percentual.

Tempo desde última alteração.

Quantidade de lojas.

---

## Não altera histórico.

---

# Objetos Transferidos

Cada etapa trabalha com objetos específicos.

RawPage

↓

ParsedProduct

↓

NormalizedProduct

↓

CatalogMatch

↓

PersistedProduct

↓

PromotionResult

Nunca reutilizar o mesmo objeto em etapas diferentes.

---

# Tratamento de Erros

Cada etapa trata apenas seus próprios erros.

Exemplo

Fetcher

↓

Timeout

Retry

↓

Falhou

↓

Retorna erro.

Nunca interrompe Scheduler.

---

Parser

↓

HTML alterado

↓

Erro de Parser

↓

Registrar log.

↓

Continuar próxima página.

---

Normalizer

↓

Preço inválido

↓

Ignorar produto.

↓

Registrar motivo.

---

Catalog Matcher

↓

Nenhuma correspondência

↓

Criar novo CatalogProduct.

Nunca lançar erro.

---

Persistence

↓

Erro SQLite

↓

Rollback

↓

Registrar log.

↓

Continuar execução quando possível.

---

# Paralelismo

Cada loja executa independentemente.

Amazon

||

Kabum

||

Terabyte

||

Pichau

Nunca compartilhar estado entre threads.

Cada execução possui contexto próprio.

---

# Estratégia de Recuperação

Caso uma loja falhe:

Registrar erro.

Finalizar connector.

Continuar demais lojas.

Nunca cancelar toda a atualização.

---

# Objetivo Arquitetural

O Pipeline deve permitir substituir qualquer estágio sem alterar os demais.

Exemplos

Trocar Playwright.

Trocar BeautifulSoup.

Trocar algoritmo de Matching.

Trocar banco de dados.

Trocar mecanismo de promoção.

Tudo isso deve ocorrer sem impacto nas outras etapas.

---

# Interfaces Oficiais

Fetcher

↓

RawPage

Parser

↓

ParsedProduct

Normalizer

↓

NormalizedProduct

CatalogMatcher

↓

CatalogMatch

PersistenceAdapter

↓

PersistResult

PromotionEngine

↓

PromotionResult

Estas interfaces são consideradas contratos oficiais da arquitetura.

Nenhum componente pode quebrá-las.

Fim do Capítulo 3.

# Capítulo 4 — Runtime Architecture

---

# Objetivo

Este capítulo descreve como todos os componentes do sistema convivem durante a execução.

Enquanto os capítulos anteriores apresentam a estrutura lógica da aplicação, este capítulo descreve o comportamento do sistema em tempo de execução (Runtime).

---

# Visão Geral

Quando iniciado, o Deal Monitor passa a possuir quatro processos principais.

```text

                 Frontend (React)
                        │
                        │ REST
                        ▼
                Backend (FastAPI)
                        │
        ┌───────────────┼───────────────┐
        │               │               │
        ▼               ▼               ▼
  Scheduler       Query Engine     Configuration
        │
        ▼
 Scraping Pipeline
        │
        ▼
 SQLite Database
```

Todos compartilham apenas:

- Configuração
- Banco de Dados
- Domain Model

Nenhum outro estado deve ser compartilhado.

---

# Processo 1 — Frontend

## Responsabilidade

Interface do usuário.

Responsável exclusivamente por:

- renderização;
- filtros;
- pesquisas;
- gráficos;
- histórico;
- comparação de preços.

---

## Comunicação

Sempre através da API REST.

Nunca acessa SQLite.

Nunca acessa arquivos.

Nunca executa scraping.

---

# Processo 2 — Backend

O Backend permanece ativo durante toda a execução.

Responsável por:

- receber requisições;
- consultar banco;
- executar casos de uso;
- responder ao Frontend.

---

## Estado

Stateless.

Toda informação persistente deve estar no SQLite.

Nenhuma informação crítica permanece apenas em memória.

---

# Processo 3 — Scheduler

O Scheduler é iniciado juntamente com o Backend.

Ele executa tarefas periódicas.

Exemplos

Atualização completa.

Atualização parcial.

Atualização manual.

Reprocessamento.

Limpeza.

---

## Independência

O Scheduler nunca bloqueia a API.

Mesmo durante um scraping completo, o usuário continua utilizando normalmente o sistema.

---

# Processo 4 — Scraping Pipeline

Cada execução cria um Pipeline independente.

```text

Scheduler

↓

Pipeline

↓

Amazon

↓

Pipeline

↓

Kabum

↓

Pipeline

↓

Terabyte
```

Cada loja possui seu próprio contexto.

---

# Contexto de Execução

Cada execução do Pipeline possui um Runtime Context.

Exemplo

```text

Execution ID

Store

Category

Start Time

Configuration

Retry Counter

Statistics

Logger
```

Nenhuma execução compartilha seu contexto.

---

# Fluxo de Atualização

```text

Scheduler

↓

Seleciona lojas

↓

Seleciona categorias

↓

Cria Pipeline

↓

Executa Fetch

↓

Executa Parse

↓

Executa Normalize

↓

Executa Match

↓

Executa Persist

↓

Executa Analyze

↓

Finaliza Pipeline
```

---

# Paralelismo

No MVP o paralelismo ocorrerá apenas entre lojas.

Exemplo

```text

Amazon      ||

Kabum       ||

Pichau      ||

Terabyte
```

Cada loja possui:

- conexão própria;
- navegador próprio;
- contexto próprio;
- logs próprios.

---

Categorias dentro da mesma loja serão executadas sequencialmente.

Isso reduz risco de bloqueio.

---

# Isolamento

Cada Pipeline é completamente isolado.

Erro na Amazon

↓

Não afeta Kabum.

Erro na Kabum

↓

Não afeta Mercado Livre.

Erro na Terabyte

↓

Não interrompe Scheduler.

---

# Inicialização

Quando o sistema inicia:

```text

Carregar Configuração

↓

Criar Banco

↓

Aplicar Migrações

↓

Registrar Connectors

↓

Inicializar Scheduler

↓

Inicializar API

↓

Disponibilizar Frontend
```

Somente após todas estas etapas o sistema é considerado pronto.

---

# Encerramento

Ao finalizar:

```text

Parar Scheduler

↓

Finalizar Jobs

↓

Fechar Navegadores

↓

Liberar Recursos

↓

Fechar SQLite

↓

Encerrar Processo
```

Nenhum processo deve ser encerrado abruptamente.

---

# Logs

Cada componente registra logs independentes.

Frontend

↓

frontend.log

Backend

↓

backend.log

Scheduler

↓

scheduler.log

Scraping

↓

scraping.log

Errors

↓

error.log

---

# Métricas

Durante a execução serão registradas métricas como:

Tempo de execução por loja.

Quantidade de produtos encontrados.

Quantidade de novos produtos.

Quantidade de alterações de preço.

Tempo médio do Fetch.

Tempo médio do Parse.

Tempo médio do Pipeline.

Quantidade de falhas.

Quantidade de retries.

Essas métricas serão utilizadas futuramente pelo Dashboard Administrativo.

---

# Consistência

Toda atualização de preços deve obedecer às seguintes regras:

Nunca atualizar diretamente um PriceHistory.

Sempre inserir um novo registro.

Nunca excluir histórico.

Nunca sobrescrever snapshots anteriores.

---

# Recuperação

Caso o sistema seja interrompido durante um scraping:

Na próxima inicialização:

- o banco permanece consistente;
- históricos anteriores permanecem íntegros;
- jobs interrompidos são marcados como FAILED;
- um novo ciclo poderá ser iniciado normalmente.

Não haverá necessidade de recuperação manual.

---

# Regras Obrigatórias

O Frontend nunca chama Connectors.

O Scheduler nunca responde HTTP.

O Pipeline nunca acessa React.

O Backend nunca interpreta HTML.

O Parser nunca grava banco.

O Normalizer nunca conhece SQLite.

O Catalog Matcher nunca conhece Playwright.

Os Repositories nunca executam regras de negócio.

---

# Resultado Esperado

A arquitetura em Runtime deve permitir:

- atualização contínua;
- interface responsiva;
- isolamento entre lojas;
- estabilidade durante falhas;
- crescimento modular.

Fim do Capítulo 4.

# Capítulo 5 — Diagramas Arquiteturais Oficiais

---

# Objetivo

Este capítulo define os diagramas oficiais da arquitetura do Deal Monitor.

Todos os desenvolvimentos futuros deverão seguir estes diagramas.

Eles representam a visão de alto nível do sistema.

---

# Diagrama 1 — Visão Geral

```text
                        Usuário
                           │
                           ▼
                  Frontend (React)
                           │
                    REST / JSON
                           │
                           ▼
                   Backend (FastAPI)
                           │
      ┌────────────────────┼────────────────────┐
      │                    │                    │
      ▼                    ▼                    ▼
 Search Service    Catalog Service    Scheduler Service
      │                    │                    │
      └────────────────────┼────────────────────┘
                           ▼
                    Domain Services
                           │
                           ▼
                     Repositories
                           │
                           ▼
                        SQLite
```

---

# Diagrama 2 — Pipeline de Atualização

```text
Scheduler

↓

Connector

↓

Fetcher

↓

Raw HTML

↓

Parser

↓

ParsedProduct

↓

Normalizer

↓

NormalizedProduct

↓

Catalog Matcher

↓

CatalogProduct + StoreProduct

↓

Persistence

↓

PriceHistory

↓

Promotion Engine

↓

Dashboard
```

---

# Diagrama 3 — Modelo do Catálogo

```text
                 CatalogProduct
                        │
        ┌───────────────┼───────────────┐
        │               │               │
        ▼               ▼               ▼
 StoreProduct     StoreProduct     StoreProduct
 (Amazon)          (Kabum)          (Pichau)
        │               │               │
        ▼               ▼               ▼
 PriceHistory     PriceHistory     PriceHistory
        │               │               │
        ▼               ▼               ▼
 Promotion        Promotion        Promotion
```

---

# Diagrama 4 — Fluxo de Pesquisa

```text
Usuário

↓

Frontend

↓

GET /products

↓

SearchService

↓

Repositories

↓

SQLite

↓

CatalogProduct

↓

StoreProduct

↓

Resposta JSON

↓

Frontend
```

---

# Diagrama 5 — Fluxo de Atualização Manual

```text
Usuário

↓

Frontend

↓

POST /scraping/run

↓

Scheduler Service

↓

Pipeline

↓

Atualização

↓

Banco

↓

Resposta
```

---

# Diagrama 6 — Dependências Permitidas

```text
Presentation

↓

API

↓

Application

↓

Domain

↓

Persistence
```

Infrastructure comunica apenas com Domain e Persistence.

Nunca com Presentation.

---

# Diagrama 7 — Fluxo de Persistência

```text
NormalizedProduct

↓

Catalog Matcher

↓

CatalogProduct

↓

StoreProduct

↓

PriceHistory

↓

Promotion

↓

SQLite
```

---

# Diagrama 8 — Estrutura de Execução

```text
                Scheduler

                    │

        ┌───────────┼───────────┐

        ▼           ▼           ▼

     Amazon      Kabum      Terabyte

        │           │           │

        ▼           ▼           ▼

    Pipeline    Pipeline    Pipeline

        │           │           │

        └───────────┼───────────┘

                    ▼

                 SQLite
```

Cada Pipeline é independente.

---

# Diagrama 9 — Camadas

```text
┌────────────────────────────┐
│ Presentation               │
├────────────────────────────┤
│ API                        │
├────────────────────────────┤
│ Application                │
├────────────────────────────┤
│ Domain                     │
├────────────────────────────┤
│ Infrastructure             │
├────────────────────────────┤
│ Persistence                │
├────────────────────────────┤
│ External Systems           │
└────────────────────────────┘
```

---

# Diagrama 10 — Responsabilidade dos Componentes

```text
Frontend
    │ Interface

API
    │ Endpoints

Services
    │ Casos de Uso

Repositories
    │ Persistência

Connectors
    │ Comunicação com Lojas

Fetcher
    │ Download HTML

Parser
    │ Extração

Normalizer
    │ Padronização

Catalog Matcher
    │ Associação ao Catálogo

Promotion Engine
    │ Inteligência

SQLite
    │ Persistência Permanente
```

---

# Convenções Arquiteturais

Todos os diagramas deste documento são considerados oficiais.

Qualquer novo componente deverá ser encaixado em uma dessas arquiteturas.

Caso um novo componente não possa ser representado por estes diagramas, uma nova ADR deverá ser criada antes da implementação.

---

# Resultado Esperado

Os diagramas apresentados neste capítulo servem como referência única para:

- desenvolvimento;
- revisão de código;
- documentação;
- implementação pelo Claude Code;
- onboarding de novos desenvolvedores.

Fim do Capítulo 5.


# Capítulo 6 — Restrições Arquiteturais e Checklist de Implementação

---

# Objetivo

Este capítulo estabelece as regras obrigatórias para toda implementação do projeto.

Estas regras possuem prioridade sobre qualquer decisão tomada durante o desenvolvimento.

Caso exista conflito entre uma implementação e este documento, este documento prevalece.

---

# Regra 1 — O domínio é soberano

Nenhuma tecnologia pode alterar o domínio.

Nunca adaptar entidades para facilitar:

- SQLite
- FastAPI
- React
- Playwright
- SQLAlchemy

As tecnologias devem se adaptar ao domínio.

---

# Regra 2 — Uma responsabilidade por classe

Cada classe possui apenas uma responsabilidade.

Exemplos corretos

ProductService

↓

Gerenciar produtos.

PromotionEngine

↓

Calcular promoções.

Parser

↓

Extrair HTML.

Repository

↓

Persistir dados.

---

Exemplos incorretos

Repository calculando descontos.

Controller fazendo scraping.

Parser gravando banco.

Service interpretando HTML.

---

# Regra 3 — Toda regra de negócio pertence aos Services ou Domain

Nunca colocar regra de negócio em:

Controllers.

Repositories.

Parser.

Normalizer.

React.

---

# Regra 4 — Todo acesso externo passa pela Infrastructure

Nunca acessar diretamente:

Amazon.

Kabum.

Mercado Livre.

Pichau.

Terabyte.

Todo acesso deve ocorrer através dos Connectors.

---

# Regra 5 — Nunca misturar camadas

É proibido:

Controller chamar SQLite.

Repository chamar Service.

Parser chamar Repository.

Frontend chamar banco.

Scheduler chamar React.

---

# Regra 6 — Todo histórico é imutável

PriceHistory nunca é atualizado.

Nunca utilizar UPDATE.

Sempre utilizar INSERT.

Snapshots representam fatos históricos.

---

# Regra 7 — Nunca apagar histórico

Mesmo que um produto desapareça da loja.

Mesmo que o preço esteja incorreto.

Mesmo que a loja saia do monitoramento.

Histórico permanece.

---

# Regra 8 — Produtos pertencem ao catálogo

Nunca criar lógica baseada apenas em StoreProduct.

Toda consulta principal deve partir de CatalogProduct.

---

# Regra 9 — Cada loja é independente

Cada Connector deve funcionar isoladamente.

Falhas em uma loja não podem interromper outra.

---

# Regra 10 — Todo componente deve ser testável

Nenhum componente deve depender de execução completa do sistema.

Cada componente deve poder ser testado isoladamente.

---

# Convenções de Código

## Idioma

Código:

Inglês.

Comentários:

Inglês.

Documentação:

Markdown.

Comunicação do projeto:

Português.

---

## Nome de Classes

Sempre:

Substantivos.

Exemplos

CatalogService

PromotionEngine

AmazonConnector

PriceRepository

---

Nunca:

Manager

Helper

Utils

Misc

Common

Generic

---

## Nome de Métodos

Sempre verbos.

Exemplos

fetch()

parse()

normalize()

match()

persist()

calculate()

find()

create()

update()

delete()

---

Nunca

processEverything()

handle()

executeAll()

doStuff()

---

# Organização de Arquivos

Cada arquivo deve conter apenas uma responsabilidade principal.

Evitar arquivos excessivamente grandes.

Referência recomendada:

- até 300 linhas: ideal;
- até 500 linhas: aceitável;
- acima de 500 linhas: revisar e considerar divisão.

---

# Dependências Permitidas

Presentation

↓

API

↓

Application

↓

Domain

↓

Persistence

Infrastructure comunica apenas através das interfaces definidas.

---

# Dependências Proibidas

Presentation → SQLite

Presentation → Playwright

Controller → Repository (sem passar pelo Service)

Repository → Service

Domain → SQLAlchemy

Domain → FastAPI

Parser → Repository

Normalizer → SQLite

Promotion Engine → Frontend

---

# Tratamento de Erros

Toda exceção deve:

- possuir mensagem clara;
- ser registrada em log;
- preservar contexto da execução;
- não expor detalhes internos ao Frontend.

---

# Logs

Todos os componentes devem gerar logs estruturados.

Cada registro deve conter, sempre que aplicável:

- timestamp;
- nível (INFO, WARNING, ERROR);
- componente;
- identificador da execução;
- loja;
- categoria;
- mensagem.

---

# Performance

Priorizar:

- simplicidade;
- legibilidade;
- manutenção.

Evitar otimizações prematuras.

Toda otimização deve ser justificada por medições reais.

---

# Extensibilidade

Adicionar uma nova loja deve exigir apenas:

1. Criar um novo Connector.
2. Criar um novo Parser.
3. Configurar a loja.

Nenhuma alteração nas demais camadas deve ser necessária.

---

# Segurança

Não armazenar credenciais no código.

Toda configuração sensível deve ser externa.

Validar todas as entradas da API.

Nunca confiar em dados provenientes do scraping.

---

# Checklist para Implementação

Antes de concluir qualquer funcionalidade, verificar:

[ ] A responsabilidade está na camada correta?

[ ] Existe apenas uma responsabilidade por classe?

[ ] O domínio permaneceu independente?

[ ] O código evita acoplamento desnecessário?

[ ] Há tratamento adequado de erros?

[ ] Os logs foram implementados?

[ ] Existem testes para o comportamento principal?

[ ] A funcionalidade respeita a ADR-000?

[ ] O histórico permanece imutável?

[ ] A implementação permite futuras extensões?

Se qualquer resposta for "não", a implementação deve ser revisada antes da conclusão.

---

# Regras para o Claude Code

Ao gerar código para este projeto, seguir obrigatoriamente:

- Respeitar a arquitetura definida neste documento.
- Não criar dependências entre camadas proibidas.
- Não mover regras de negócio para Controllers ou Repositories.
- Implementar uma responsabilidade por classe.
- Utilizar nomes claros e consistentes.
- Priorizar código simples e legível.
- Seguir os contratos definidos nos documentos de especificação.
- Em caso de dúvida arquitetural, interromper a implementação e consultar a documentação antes de criar uma solução alternativa.

---

# Encerramento

Este documento estabelece a arquitetura oficial do Deal Monitor.

Todos os documentos produzidos posteriormente devem complementar esta arquitetura, nunca contradizê-la.

Alterações arquiteturais relevantes exigem uma nova Architecture Decision Record (ADR).

Fim do Documento.