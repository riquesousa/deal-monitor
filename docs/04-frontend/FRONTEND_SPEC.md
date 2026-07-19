# FRONTEND_SPEC

Projeto: Deal Monitor

Versão: 1.0

Status: Documento Oficial

Dependências

- ARCHITECTURE.md
- CLEAN_ARCHITECTURE.md
- PROJECT_STRUCTURE.md
- DATABASE_SCHEMA.md
- BACKEND_SPEC.md

---

# Objetivo

Este documento define toda a especificação da interface web do Deal Monitor.

O Frontend deverá ser uma SPA (Single Page Application), executada localmente no MVP, consumindo exclusivamente a API REST do Backend.

Toda lógica de negócio permanece no Backend.

O Frontend é responsável apenas por:

- apresentação;
- interação com o usuário;
- gerenciamento do estado da interface;
- consumo da API.

---

# Objetivos do MVP

O usuário deverá conseguir:

- visualizar promoções;
- pesquisar produtos;
- consultar histórico de preços;
- executar scraping manualmente;
- acompanhar execuções em andamento;
- habilitar ou desabilitar lojas;
- habilitar ou desabilitar categorias;
- alterar configurações do sistema;
- visualizar logs simplificados.

Não haverá autenticação no MVP.

---

# Stack Oficial

Linguagem

TypeScript

---

Framework

React 19

---

Build

Vite

---

Roteamento

React Router

---

Estado

TanStack Query

React Context

---

Tabela

TanStack Table

---

Formulários

React Hook Form

---

Validação

Zod

---

Estilo

TailwindCSS

---

Ícones

Lucide React

---

Gráficos

Recharts

---

Cliente HTTP

Axios

---

# Estrutura Oficial

```text
frontend/

src/

app/

components/

pages/

layouts/

hooks/

services/

contexts/

routes/

styles/

types/

utils/

assets/
```

---

# Filosofia

A interface deve priorizar:

- simplicidade;
- rapidez;
- legibilidade;
- baixo número de cliques;
- carregamento rápido.

Evitar animações excessivas.

---

# Layout Principal

Estrutura:

```text
┌────────────────────────────────────────────┐
│ Header                                     │
├──────────────┬─────────────────────────────┤
│ Sidebar      │                             │
│              │                             │
│              │ Conteúdo                    │
│              │                             │
│              │                             │
├──────────────┴─────────────────────────────┤
│ Status Bar                                 │
└────────────────────────────────────────────┘
```

---

# Header

Responsável por:

- nome da aplicação;
- versão;
- botão "Executar Scraping";
- indicador de execução;
- botão de configurações.

Altura fixa.

Sempre visível.

---

# Sidebar

Menu principal.

Itens:

Dashboard

Promoções

Produtos

Histórico

Lojas

Categorias

Execuções

Configurações

Nunca utilizar menus aninhados no MVP.

---

# Status Bar

Sempre visível.

Exibe:

- Backend conectado;
- Banco conectado;
- Scheduler ativo;
- Última sincronização;
- Quantidade de promoções.

Atualização automática.

---

# Navegação

Toda navegação será client-side.

Nunca recarregar a aplicação inteira.

---

# Rotas

```text
/

↓

Dashboard

/promotions

/products

/history

/stores

/categories

/jobs

/settings
```

---

# Layout Responsivo

Desktop

Principal alvo.

---

Tablet

Compatível.

---

Mobile

Não é prioridade para o MVP.

A interface deve permanecer utilizável, mas não será otimizada inicialmente.

---

# Tema

Modo claro inicialmente.

Arquitetura preparada para Dark Mode.

---

# Paleta

Neutra.

Priorizar contraste.

Promoções:

Verde.

Erros:

Vermelho.

Alertas:

Amarelo.

Informações:

Azul.

---

# Tipografia

Fonte padrão:

Inter

Hierarquia clara:

Título

Subtítulo

Texto

Legenda

---

# Componentes Globais

Button

Card

Badge

Modal

Dialog

Drawer

Tooltip

Input

SearchInput

NumberInput

Switch

Checkbox

Table

Pagination

Select

MultiSelect

Skeleton

Spinner

Toast

ConfirmDialog

Todos reutilizáveis.

---

# Feedback Visual

Toda ação deverá apresentar retorno.

Exemplos:

Loading

Skeleton

Toast

Erro

Sucesso

Nunca deixar o usuário sem feedback.

---

# Loading

Carregamentos inferiores a 300 ms:

Não exibir Spinner.

Acima disso:

Exibir Skeleton ou Spinner.

---

# Tratamento de Erros

Toda chamada HTTP deverá possuir:

- mensagem amigável;
- opção de tentar novamente;
- detalhes apenas em ambiente de desenvolvimento.

Nunca exibir stack trace.

---

# Consumo da API

Todo acesso ao Backend deverá ocorrer através da pasta:

```text
services/
```

Nunca chamar Axios diretamente dentro dos componentes.

---

# Organização dos Services

Exemplos:

CatalogService

PromotionService

HistoryService

StoreService

CategoryService

ScrapingService

SettingsService

Cada serviço representa um recurso da API.

---

# Organização dos Hooks

Exemplos:

useProducts()

usePromotions()

useStores()

useCategories()

useJobs()

useHistory()

Hooks encapsulam toda a comunicação com TanStack Query.

Componentes nunca acessam diretamente a API.

# Dashboard

O Dashboard será a tela inicial da aplicação.

Objetivo:

Permitir ao usuário visualizar rapidamente a situação do monitoramento sem navegar por outras páginas.

Rota:

/

---

# Layout

+------------------------------------------------------------+
| Header                                                     |
+------------------------------------------------------------+

Cards de resumo

+---------+---------+---------+---------+

| Promoções | Produtos | Lojas | Execuções |

+---------+---------+---------+---------+

------------------------------------------------------------

Promoções mais recentes

------------------------------------------------------------

Execuções recentes

------------------------------------------------------------

Status dos Connectors

------------------------------------------------------------

---

# Cards Superiores

Serão exibidos:

Quantidade de promoções ativas

Quantidade total de produtos

Quantidade de lojas habilitadas

Quantidade de execuções realizadas hoje

Cada Card deverá possuir:

ícone

valor

descrição

cor indicativa

---

# Promoções Recentes

Tabela resumida.

Colunas:

Loja

Produto

Preço

Desconto

Horário

Botão "Visualizar"

Máximo:

10 registros.

---

# Execuções Recentes

Tabela.

Colunas:

Loja

Categoria

Status

Produtos

Tempo

Última execução

---

# Status dos Connectors

Lista simples.

Exemplo:

🟢 Amazon

🟢 Kabum

🟡 Pichau

🔴 Terabyte

Cores:

Verde

Saudável

Amarelo

Degradado

Vermelho

Indisponível

---

# Atualização

Atualização automática a cada:

30 segundos.

---

# Página Promoções

Rota

/promotions

Objetivo:

Visualizar promoções detectadas.

---

Layout

Filtro Superior

↓

Tabela

↓

Paginação

---

Filtros

Pesquisa

Categoria

Loja

Desconto mínimo

Status

Período

Ordenação

Todos opcionais.

---

Tabela

Colunas:

Imagem

Produto

Loja

Preço Atual

Preço Anterior

Desconto

Score

Detectado em

Ações

---

Ações

Abrir produto

Ver histórico

Descartar promoção

---

Ordenação

Preço

Desconto

Score

Data

Nome

---

Atualização

Manual.

Botão:

Atualizar

---

# Página Produtos

Rota

/products

Objetivo:

Consultar catálogo.

---

Filtros

Pesquisa

Categoria

Marca

Loja

Preço mínimo

Preço máximo

Disponibilidade

---

Tabela

Imagem

Produto

Marca

Categoria

Menor preço

Último preço

Quantidade de lojas

Última atualização

---

Ações

Ver detalhes

Ver histórico

Abrir produto

---

# Página Detalhes do Produto

Rota

/products/{id}

---

Layout

Imagem

Nome

Marca

Categoria

Menor preço histórico

Preço atual

Lojas disponíveis

---

Gráfico

Histórico de preços.

Biblioteca:

Recharts.

---

Tabela

Histórico completo.

Colunas:

Data

Loja

Preço

Disponibilidade

---

Botões

Abrir loja

Atualizar histórico

Voltar

---

# Página Histórico

Rota

/history

Objetivo:

Pesquisar alterações de preços.

---

Filtros

Produto

Categoria

Loja

Período

---

Tabela

Produto

Loja

Preço

Data

Disponibilidade

---

Permitir exportação futura.

Não implementada no MVP.

---

# Página Lojas

Rota

/stores

Objetivo:

Gerenciar Connectors.

---

Tabela

Nome

Status

Última execução

Produtos encontrados

Tempo médio

Health

---

Ações

Editar

Habilitar

Desabilitar

Executar scraping

Visualizar histórico

---

Adicionar loja

Não disponível no MVP.

As lojas serão cadastradas por configuração.

---

# Página Categorias

Rota

/categories

Objetivo:

Selecionar categorias monitoradas.

---

Tabela

Nome

Status

Quantidade de produtos

Última atualização

---

Ações

Habilitar

Desabilitar

Executar scraping

---

Categorias iniciais

Notebook

Placa de Vídeo

Processador

SSD

Memória RAM

Monitor

Mouse

Teclado

Gabinete

Fonte

---

# Página Execuções

Rota

/jobs

Objetivo:

Acompanhar ScrapingJobs.

---

Tabela

Data

Loja

Categoria

Status

Produtos

Promoções

Tempo

Erro

---

Filtros

Status

Loja

Categoria

Período

---

Ações

Visualizar detalhes

Cancelar

Executar novamente

---

Atualização

Automática

A cada:

10 segundos

---

# Página Configurações

Rota

/settings

Objetivo:

Alterar parâmetros da aplicação.

---

Sessões

Scraping

Scheduler

Sistema

Logs

---

Configurações

Timeout

Concorrência

Intervalo

Headless

Retry

Log Level

Atualização automática

---

Toda alteração deverá solicitar confirmação antes de salvar.

---

# Estados da Interface

Cada tela deverá implementar:

Loading

Empty State

Erro

Conteúdo

Recarregando

Nunca deixar telas vazias sem contexto.

---

# Empty State

Toda lista vazia deverá informar:

Nenhum registro encontrado.

Exibir botão:

Atualizar

Nunca apresentar apenas uma tabela vazia.

# Organização dos Componentes

Todo componente deverá possuir responsabilidade única.

Estrutura oficial:

components/

base/

feedback/

forms/

layout/

charts/

tables/

cards/

dialogs/

navigation/

icons/

---

# Base

Componentes reutilizáveis.

Exemplos:

Button

Input

Badge

Card

Avatar

Spinner

Skeleton

Divider

Tooltip

Chip

---

# Feedback

Toast

Alert

Banner

Progress

LoadingOverlay

ErrorMessage

EmptyState

---

# Forms

SearchInput

CurrencyInput

Checkbox

Switch

MultiSelect

DatePicker

NumberInput

TextArea

---

# Charts

PriceHistoryChart

PromotionChart

ExecutionChart

StoreHealthChart

Todos utilizando Recharts.

---

# Tables

ProductTable

PromotionTable

StoreTable

CategoryTable

JobTable

HistoryTable

---

# Cards

PromotionCard

StoreCard

StatisticsCard

ExecutionCard

HealthCard

---

# Dialogs

ConfirmDialog

DeleteDialog

ConfigurationDialog

ExecutionDetailsDialog

ProductDetailsDialog

---

# Navigation

Sidebar

Header

Breadcrumb

PageTitle

MenuItem

StatusBar

---

# Organização do Estado

O estado será dividido em:

Estado do servidor

↓

TanStack Query

Estado da interface

↓

React Context

Estado local

↓

useState

Nunca utilizar Context para armazenar dados vindos da API.

---

# React Query

Responsável por:

Cache

Refetch

Retry

Loading

Error

Invalidation

Toda comunicação com o Backend deverá passar pelo React Query.

---

# Chaves de Cache

Padrão:

products

products-detail

promotions

stores

categories

jobs

history

settings

Nunca utilizar strings aleatórias.

Centralizar todas as Query Keys.

---

# Atualização Automática

Dashboard

30 segundos

Promoções

60 segundos

Execuções

10 segundos

Histórico

Manual

Produtos

Manual

Configurações

Manual

---

# Invalidação

Após operações de escrita:

Invalidar apenas os recursos afetados.

Nunca invalidar todo o cache da aplicação.

---

# Formulários

Todo formulário deverá utilizar:

React Hook Form

+

Zod

Fluxo:

Input

↓

Validação

↓

Submit

↓

API

↓

Feedback

---

# Mensagens de Validação

Devem ser objetivas.

Exemplo:

"Informe um valor válido."

Evitar mensagens técnicas.

---

# Feedback de Operações

Após sucesso:

Toast verde

Após erro:

Toast vermelho

Após operação demorada:

Loading Overlay

---

# Performance

Utilizar:

React.memo

useMemo

useCallback

Apenas quando existir ganho mensurável.

Evitar otimizações prematuras.

---

# Lazy Loading

Todas as páginas deverão utilizar lazy loading.

Exemplo:

Dashboard

Promoções

Produtos

Histórico

Configurações

Nunca carregar todas as páginas na inicialização.

---

# Paginação

Paginação sempre no servidor.

Nunca carregar milhares de registros no navegador.

---

# Acessibilidade

Todos os componentes deverão possuir:

- labels associados;
- foco por teclado;
- navegação por TAB;
- contraste adequado;
- atributos ARIA quando aplicável.

---

# Internacionalização

O MVP será apenas em português (pt-BR).

A arquitetura deverá permitir futura internacionalização.

Nunca escrever textos diretamente dentro dos componentes.

Centralizar mensagens em arquivos de recursos.

---

# Organização dos Tipos

Estrutura:

types/

api/

dto/

entities/

forms/

responses/

Cada domínio possuirá seus próprios tipos.

---

# Convenções para Componentes

Todo componente deverá:

- possuir Props tipadas;
- possuir responsabilidade única;
- evitar lógica de negócio;
- ser reutilizável sempre que possível.

Nunca realizar chamadas HTTP diretamente.

---

# Convenções para o Claude Code

Ao implementar uma nova tela:

1. Criar a rota.
2. Criar a página.
3. Criar os componentes necessários.
4. Criar o Service correspondente.
5. Criar os Hooks.
6. Integrar com React Query.
7. Implementar tratamento de Loading.
8. Implementar tratamento de Erro.
9. Implementar Empty State.
10. Escrever os testes.

Nunca inverter essa sequência.

---

# Testes

Toda página deverá possuir:

- testes de renderização;
- testes de interação;
- testes de estados (loading, erro, vazio);
- testes de integração com os Hooks.

Componentes reutilizáveis também deverão possuir testes unitários.

---

# Checklist

Antes de concluir uma funcionalidade:

[ ] Existe rota definida.

[ ] A página segue o layout oficial.

[ ] O componente possui responsabilidade única.

[ ] Não existe chamada HTTP direta.

[ ] React Query foi utilizado.

[ ] Existe tratamento de erro.

[ ] Existe Loading.

[ ] Existe Empty State.

[ ] Existe feedback visual.

[ ] O código está tipado.

[ ] Existem testes.

---

# Critérios de Aceitação

O Frontend será considerado conforme quando:

[ ] Todas as telas seguirem este documento.

[ ] Toda comunicação ocorrer exclusivamente pela API REST.

[ ] Não existir lógica de negócio na interface.

[ ] Todos os componentes forem reutilizáveis.

[ ] A aplicação permanecer responsiva durante operações de scraping.

[ ] O usuário receber feedback para todas as ações.

[ ] O código seguir as convenções oficiais.

---

# Encerramento

O FRONTEND_SPEC.md define a especificação oficial da interface web do Deal Monitor.

Toda implementação deverá seguir este documento em conjunto com:

- ARCHITECTURE.md
- CLEAN_ARCHITECTURE.md
- PROJECT_STRUCTURE.md
- BACKEND_SPEC.md
- DATABASE_SCHEMA.md
- API_SPEC.md (quando disponível)

Qualquer alteração estrutural deverá ser registrada por meio de uma ADR antes da implementação.

Fim do Documento.