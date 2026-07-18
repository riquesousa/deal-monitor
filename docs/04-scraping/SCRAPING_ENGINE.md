# SCRAPING_ENGINE

Projeto: Deal Monitor

Versão: 1.0

Status: Documento Oficial

Dependências

- ARCHITECTURE.md
- CLEAN_ARCHITECTURE.md
- PROJECT_STRUCTURE.md
- DATABASE_SCHEMA.md

---

# Objetivo

Este documento define toda a arquitetura responsável pela coleta de dados dos e-commerces.

O mecanismo de scraping deve ser:

- desacoplado;
- extensível;
- resiliente;
- observável;
- facilmente testável.

A inclusão de uma nova loja deve exigir apenas a implementação de um novo Connector.

Nenhuma alteração na Application Layer deverá ser necessária.

---

# Objetivos Funcionais

O mecanismo deverá ser capaz de:

- navegar pelos e-commerces;
- selecionar categorias;
- localizar produtos;
- extrair informações;
- normalizar dados;
- identificar produtos já conhecidos;
- persistir histórico de preços;
- detectar promoções;
- registrar métricas;
- registrar erros.

---

# Arquitetura Geral

Fluxo oficial

```text
Scheduler

↓

RunScrapingUseCase

↓

Connector

↓

Browser

↓

Parser

↓

Extractor

↓

Normalizer

↓

Validator

↓

Matcher

↓

Persistence

↓

Promotion Engine

↓

Logs + Métricas
```

---

# Estrutura Oficial

```text
scraping/

├── connectors/
├── browser/
├── parsers/
├── extractors/
├── normalizers/
├── validators/
├── matchers/
├── pipelines/
├── selectors/
├── resources/
└── exceptions/
```

---

# Responsabilidades

Connectors

Conhecem cada loja.

Nunca persistem dados.

Nunca acessam banco.

---

Browser

Abstrai Playwright.

Responsável por:

- abrir navegador;
- criar páginas;
- navegar;
- capturar HTML;
- controlar timeout.

Nunca interpreta conteúdo.

---

Parsers

Transformam HTML bruto em estrutura navegável.

Não executam regras de negócio.

---

Extractors

Extraem dados específicos.

Exemplos:

Título

Preço

Imagem

URL

Disponibilidade

Vendedor

Parcelamento

Frete

---

Normalizers

Padronizam dados para o formato interno.

Exemplos:

Remoção de espaços extras.

Conversão monetária.

Padronização de marcas.

Normalização de nomes.

Padronização de URLs.

---

Validators

Validam consistência dos dados.

Exemplos:

Preço válido.

Título presente.

URL válida.

Imagem válida.

Categoria conhecida.

---

Matchers

Responsáveis por localizar o CatalogProduct correspondente.

Nunca executam scraping.

Nunca acessam HTML.

---

Pipelines

Orquestram todo o fluxo.

Nunca conhecem detalhes específicos das lojas.

---

# Connector Framework

Cada loja implementará exatamente o mesmo contrato.

Estrutura

```text
Connector

↓

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

Return Products
```

---

Todos os Connectors devem implementar:

initialize()

fetch()

parse()

extract()

normalize()

validate()

health_check()

close()

---

Nenhum Connector poderá acessar diretamente:

Banco

Repositories

Promotion Engine

Frontend

API

---

# Browser Layer

Implementação oficial:

Playwright

Modo padrão:

Headless

Configuração:

Chromium

Timeout padrão:

30 segundos

Retry:

3 tentativas

---

Fluxo

```text
Browser

↓

New Context

↓

New Page

↓

Navigate

↓

Wait

↓

Capture

↓

Close Page
```

Browser permanece aberto.

Pages são descartadas após cada execução.

---

# Browser Context

Cada execução utiliza:

Novo Context.

Novos Cookies.

Novo Cache.

Novo Storage.

Evitar compartilhamento entre execuções.

---

# Timeouts

Navigation Timeout

30 segundos

Selector Timeout

15 segundos

Retry Delay

2 segundos

Máximo de tentativas

3

---

# Rate Limit

Cada Connector define seu próprio intervalo mínimo entre requisições.

Nunca utilizar valores fixos globais.

---

# User-Agent

Cada Connector poderá possuir User-Agent próprio.

Permitir rotação futura.

---

# Selectors

Todos os seletores devem permanecer isolados.

Estrutura

```text
selectors/

amazon.py

kabum.py

pichau.py

terabyte.py

mercadolivre.py
```

Nenhum selector poderá ser compartilhado entre lojas.

---

# HTML

Nunca persistir HTML bruto no banco.

Caso necessário para depuração:

Salvar apenas temporariamente.

---

# Pipeline de Execução

Todo scraping deverá seguir obrigatoriamente o pipeline abaixo.

```text
Scheduler

↓

Selecionar Loja

↓

Selecionar Categoria

↓

Inicializar Connector

↓

Inicializar Browser

↓

Abrir Página

↓

Coletar Produtos

↓

Extrair Dados

↓

Normalizar

↓

Validar

↓

Localizar Produto no Catálogo

↓

Persistir Histórico

↓

Calcular Promoções

↓

Registrar Métricas

↓

Encerrar Execução
```

Nenhuma etapa poderá ser ignorada.

---

# Estados da Execução

Toda execução de scraping deverá passar pelos seguintes estados:

```text
PENDING

↓

INITIALIZING

↓

FETCHING

↓

PARSING

↓

EXTRACTING

↓

NORMALIZING

↓

VALIDATING

↓

MATCHING

↓

PERSISTING

↓

PROMOTION_ANALYSIS

↓

FINISHED
```

Em caso de erro:

```text
FAILED
```

Em caso de cancelamento:

```text
CANCELLED
```

Todos os estados deverão ser registrados em log.

---

# Paginação

Cada Connector deverá informar como funciona sua paginação.

Tipos suportados:

- Query Parameter
- Offset
- Cursor
- Botão "Próxima Página"
- Scroll Infinito

Cada loja define apenas uma estratégia principal.

---

# Estratégia de Paginação

Contrato esperado:

```text
Página 1

↓

Coletar Produtos

↓

Existe próxima página?

↓

SIM

↓

Ir para próxima página

↓

Repetir

↓

NÃO

↓

Finalizar
```

---

# Scroll Infinito

Quando uma loja utilizar carregamento infinito:

Fluxo:

```text
Carregar Página

↓

Scroll

↓

Aguardar novos elementos

↓

Comparar quantidade

↓

Mudou?

↓

SIM

↓

Continuar

↓

NÃO

↓

Encerrar
```

Evitar loops infinitos.

Definir número máximo de tentativas.

Valor padrão:

20 scrolls.

---

# Lazy Loading

Sempre aguardar carregamento completo das imagens e preços antes da extração.

Nunca depender apenas do evento `load`.

Priorizar:

- presença do elemento;
- conteúdo renderizado;
- estabilidade do DOM.

---

# Retry Inteligente

Falhas temporárias devem utilizar retry automático.

Situações elegíveis:

- timeout;
- erro de navegação;
- conexão perdida;
- elemento ainda não disponível.

Nunca utilizar retry para:

- seletor inexistente;
- erro de programação;
- HTML incompatível.

---

# Política de Retry

Primeira tentativa

↓

Falhou

↓

Aguardar 2 segundos

↓

Nova tentativa

↓

Falhou

↓

Aguardar 5 segundos

↓

Nova tentativa

↓

Falhou

↓

Registrar erro

↓

Finalizar execução

Máximo:

3 tentativas.

---

# Tratamento de CAPTCHA

Caso seja detectado CAPTCHA:

Registrar evento.

Interromper coleta.

Marcar Connector como indisponível.

Nunca tentar resolver CAPTCHA automaticamente.

Nunca utilizar serviços externos de bypass.

---

# Detecção de Bloqueios

Cada Connector deverá identificar:

- HTTP 403
- HTTP 429
- Página vazia
- Página de bloqueio
- CAPTCHA
- Redirecionamentos inesperados

Quando detectado:

Encerrar execução imediatamente.

---

# Deduplicação

Um mesmo anúncio poderá aparecer mais de uma vez durante o scraping.

Critérios de deduplicação:

1. URL normalizada

2. Identificador da loja (quando disponível)

3. Título normalizado

Persistir apenas um registro por produto encontrado em uma mesma execução.

---

# Extração de Dados

Cada produto deverá fornecer, sempre que possível:

- título;
- preço;
- preço original;
- URL;
- imagem;
- disponibilidade;
- vendedor;
- categoria;
- marca;
- parcelamento;
- frete.

Campos indisponíveis permanecem nulos.

Nunca gerar valores fictícios.

---

# Normalização

Após extração:

Executar:

- remoção de espaços duplicados;
- remoção de caracteres invisíveis;
- padronização Unicode;
- normalização de moeda;
- padronização de URLs;
- remoção de parâmetros de rastreamento.

Exemplos:

utm_source

utm_medium

utm_campaign

session_id

tracking_id

---

# Normalização de Preços

Aceitar formatos:

R$ 5.499,90

5499,90

5499.90

5499

Resultado final:

5499.90

Tipo decimal.

Nunca utilizar float.

---

# Normalização de Produtos

Exemplo:

Antes

Notebook Dell G15 Ryzen™ 7 16GB RAM RTX4060 SSD 512GB

Depois

Notebook Dell G15 Ryzen 7 16GB RTX 4060 SSD 512GB

O objetivo é facilitar o Matching.

---

# Matching

Após normalização:

Fluxo:

```text
Produto

↓

Busca por URL conhecida

↓

Encontrou?

↓

SIM

↓

Associar

↓

NÃO

↓

Buscar por Nome Normalizado

↓

Encontrou?

↓

SIM

↓

Associar

↓

NÃO

↓

Criar novo CatalogProduct
```

Nunca utilizar IA para Matching no MVP.

---

# Persistência

Persistir na seguinte ordem:

CatalogProduct

↓

ProductPrice

↓

Promotion (quando existir)

↓

ScrapingJob

Caso qualquer etapa falhe:

Registrar erro.

Preservar histórico já salvo.

---

# Idempotência

Executar duas vezes o mesmo scraping não deve gerar inconsistências.

O histórico de preços sempre será preservado.

O catálogo não deverá criar produtos duplicados.

---

# Concorrência

Cada Store poderá executar apenas um ScrapingJob simultaneamente.

Lojas diferentes poderão executar em paralelo.

Categorias da mesma loja poderão ser executadas em paralelo futuramente.

No MVP:

Concorrência máxima configurável.

Valor padrão:

2 Jobs simultâneos.

---

# Cancelamento

Toda execução deverá permitir cancelamento seguro.

Fluxo:

Solicitação

↓

Finalizar página atual

↓

Persistir progresso

↓

Fechar Browser

↓

Registrar CANCELLED

↓

Encerrar

---

# Observabilidade

Toda execução deverá gerar informações suficientes para diagnóstico.

A coleta de métricas não deve impactar significativamente o desempenho.

---

# Logging

Registrar obrigatoriamente:

- início da execução;
- loja;
- categoria;
- quantidade de páginas processadas;
- quantidade de produtos encontrados;
- quantidade de produtos persistidos;
- promoções detectadas;
- tempo total da execução;
- erros encontrados;
- motivo do encerramento.

Nunca registrar:

- cookies;
- tokens;
- credenciais;
- dados sensíveis.

---

# Estrutura de Logs

Cada execução deverá possuir um identificador único.

Formato recomendado:

```text
SCRAPING_JOB_ID
```

Todos os logs gerados durante a execução deverão conter esse identificador.

---

# Métricas

Registrar no mínimo:

- scraping_jobs_started;
- scraping_jobs_completed;
- scraping_jobs_failed;
- scraping_execution_time_ms;
- products_found_total;
- products_processed_total;
- products_persisted_total;
- promotions_detected_total;
- connector_failures_total;
- retry_attempts_total.

---

# Health Check

Todo Connector deverá implementar:

```text
health_check()
```

Objetivos:

- validar conectividade;
- validar seletor principal;
- validar carregamento da página;
- validar estrutura mínima.

Resultado esperado:

```text
HEALTHY

DEGRADED

UNAVAILABLE
```

---

# Estratégia de Configuração

Cada Connector deverá possuir configuração própria.

Estrutura:

```text
config/connectors/

amazon.yaml

kabum.yaml

pichau.yaml

terabyte.yaml

mercadolivre.yaml
```

---

# Configurações por Loja

Cada arquivo poderá definir:

- URL base;
- categorias suportadas;
- timeout;
- User-Agent;
- delay entre páginas;
- número máximo de páginas;
- política de retry;
- estratégia de paginação;
- estratégia de scroll;
- limites de concorrência.

Nenhuma configuração específica deverá ficar fixa no código.

---

# Versionamento de Seletores

Cada alteração de seletor deverá ser rastreável.

Estrutura sugerida:

```text
selectors/

amazon/

v1.py

v2.py

current.py
```

`current.py` referencia a versão ativa.

Isso facilita rollback quando uma loja altera sua estrutura.

---

# Estratégia para Mudanças de Layout

Quando um conector falhar por alteração no HTML:

Fluxo:

```text
Falha

↓

Registrar erro

↓

Marcar Connector como DEGRADED

↓

Gerar alerta

↓

Continuar execução dos demais Connectors
```

Uma loja indisponível nunca deve interromper o monitoramento das demais.

---

# Tratamento de Produtos Inválidos

Produtos deverão ser descartados quando:

- título inexistente;
- preço inválido;
- URL inválida;
- HTML incompleto;
- erro de normalização.

Registrar motivo do descarte.

Nunca interromper a execução inteira por causa de um único produto.

---

# Estratégia para Produtos Duplicados

Dentro da mesma execução:

Persistir apenas uma ocorrência.

Entre execuções diferentes:

Sempre persistir um novo ProductPrice quando houver nova coleta válida.

---

# Estratégia de Recuperação

Após falha inesperada:

- registrar erro;
- persistir progresso disponível;
- fechar recursos;
- atualizar ScrapingJob;
- liberar Browser;
- retornar controle ao Scheduler.

Nunca deixar Browser ou páginas abertas.

---

# Testes dos Connectors

Todo Connector deverá possuir:

- testes unitários;
- testes de integração;
- validação dos seletores;
- validação da normalização;
- validação do matching;
- validação dos cenários de erro.

Sempre que possível utilizar HTML salvo em fixtures.

Evitar depender do site real nos testes automatizados.

---

# Adicionando um Novo E-commerce

Fluxo obrigatório:

1. Criar Connector.
2. Criar arquivo de configuração.
3. Criar seletores.
4. Implementar Parser.
5. Implementar Extractor.
6. Implementar testes.
7. Registrar no Container.
8. Executar Health Check.
9. Atualizar documentação.

Nenhuma outra alteração deverá ser necessária.

---

# Compatibilidade

O framework deverá permitir futuramente:

- proxies rotativos;
- múltiplos Browsers;
- Firefox;
- WebKit;
- Playwright remoto;
- execução distribuída;
- filas de processamento;
- armazenamento em cache.

Esses recursos não fazem parte do MVP, mas a arquitetura deve permitir sua inclusão.

---

# Regras para o Claude Code

Ao implementar um Connector:

- Nunca acessar banco de dados.
- Nunca chamar Repositories.
- Nunca criar lógica de promoção.
- Nunca criar lógica de catálogo.
- Nunca conhecer regras do domínio.
- Implementar apenas a coleta e transformação dos dados.

Ao implementar um Parser:

- Apenas interpretar HTML.

Ao implementar um Extractor:

- Apenas localizar e extrair informações.

Ao implementar um Normalizer:

- Apenas padronizar dados.

Ao implementar um Validator:

- Apenas validar integridade.

Ao implementar um Pipeline:

- Apenas orquestrar o fluxo.

---

# Checklist

Antes de concluir um Connector:

[ ] Implementa o contrato oficial.

[ ] Possui Health Check.

[ ] Possui testes.

[ ] Possui configuração própria.

[ ] Possui seletores versionados.

[ ] Não acessa infraestrutura proibida.

[ ] Não contém regras de negócio.

[ ] Fecha corretamente todos os recursos.

---

# Critérios de Aceitação

O mecanismo de scraping será considerado conforme quando:

[ ] Novas lojas puderem ser adicionadas sem alterar a arquitetura.

[ ] Todo Connector seguir o mesmo contrato.

[ ] Todo histórico de preços for preservado.

[ ] O sistema suportar falhas isoladas por loja.

[ ] Todos os recursos forem liberados corretamente ao final de cada execução.

[ ] O mecanismo for observável por meio de logs e métricas.

---

# Encerramento

O SCRAPING_ENGINE.md define a arquitetura oficial do mecanismo de coleta do Deal Monitor.

Toda implementação relacionada a scraping deverá seguir este documento.

Qualquer alteração estrutural deverá ser registrada por meio de uma ADR antes da implementação.

Fim do Documento.