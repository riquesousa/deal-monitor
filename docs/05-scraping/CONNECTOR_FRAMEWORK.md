# CONNECTOR_FRAMEWORK

Projeto: Deal Monitor

Versão: 1.0

Status: Documento Oficial

Dependências

- ARCHITECTURE.md
- CLEAN_ARCHITECTURE.md
- PROJECT_STRUCTURE.md
- SCRAPING_ENGINE.md

---

# Objetivo

Este documento define o framework oficial utilizado pelos Connectors do Deal Monitor.

Todos os e-commerces deverão implementar exatamente o mesmo contrato.

O objetivo é permitir que novas lojas sejam adicionadas sem alterar a arquitetura existente.

Nenhum Connector poderá implementar comportamentos fora deste documento.

---

# Conceito

Cada loja será representada por um Connector.

Exemplos:

AmazonConnector

KabumConnector

PichauConnector

TerabyteConnector

MercadoLivreConnector

CasasBahiaConnector

MagazineLuizaConnector

Todos seguem exatamente a mesma estrutura.

---

# Arquitetura

```text
Connector

↓

Browser

↓

Page

↓

Parser

↓

Extractor

↓

Normalizer

↓

Validator

↓

Products
```

O Connector nunca conhece:

- banco de dados;
- API REST;
- repositórios;
- Promotion Engine;
- Scheduler.

Sua única responsabilidade é coletar dados.

---

# Estrutura Oficial

```text
connectors/

base/

amazon/

kabum/

pichau/

terabyte/

mercadolivre/
```

---

# Base

A pasta `base/` contém toda a infraestrutura compartilhada.

Estrutura:

```text
base/

base_connector.py

connector_protocol.py

connector_result.py

connector_context.py

connector_config.py

connector_exception.py
```

Nenhum Connector poderá duplicar código existente em `base/`.

---

# Contrato Oficial

Todo Connector deverá implementar os seguintes métodos.

---

initialize()

Responsável por:

- carregar configuração;
- validar parâmetros;
- preparar Browser.

Não realiza scraping.

---

health_check()

Responsável por verificar:

- conectividade;
- disponibilidade da loja;
- validade do seletor principal.

Não persiste dados.

---

fetch()

Responsável por:

- navegar até a página;
- carregar conteúdo.

Não interpreta HTML.

---

parse()

Transforma HTML bruto em estrutura navegável.

Não extrai dados.

---

extract()

Extrai:

- título;
- preço;
- URL;
- imagem;
- disponibilidade;
- vendedor;
- frete;
- parcelamento.

Nenhuma normalização ocorre nesta etapa.

---

normalize()

Padroniza:

- preços;
- nomes;
- URLs;
- categorias;
- moedas;
- marcas.

---

validate()

Valida:

- preço;
- título;
- URL;
- categoria.

Produtos inválidos são descartados.

---

close()

Libera:

- Browser;
- Pages;
- Context;
- recursos temporários.

Sempre deverá ser executado.

Mesmo em caso de erro.

---

# Fluxo Oficial

```text
initialize()

↓

health_check()

↓

fetch()

↓

parse()

↓

extract()

↓

normalize()

↓

validate()

↓

return ConnectorResult

↓

close()
```

Nenhuma etapa poderá ser omitida.

---

# ConnectorResult

Todo Connector retorna exatamente um objeto.

Estrutura lógica:

```text
ConnectorResult

status

products

statistics

errors

warnings

duration
```

Nunca retornar listas diretamente.

---

# ConnectorContext

Cada execução receberá um contexto.

Informações disponíveis:

- Store;
- Categoria;
- Timeout;
- Configuração;
- Limite de páginas;
- Request ID;
- Job ID.

Nenhum Connector deverá acessar configurações globais diretamente.

---

# ConnectorConfig

Cada Connector possui configuração própria.

Exemplos:

URL base

User-Agent

Delay

Retry

Timeout

Paginação

Scroll

Headers

Cookies opcionais

Tudo deve ser parametrizado.

---

# Produto Extraído

Todo produto deverá possuir, no mínimo:

- título;
- URL;
- preço;
- disponibilidade.

Campos opcionais:

- imagem;
- vendedor;
- frete;
- parcelamento;
- marca;
- modelo.

Nunca criar valores fictícios.

---

# Responsabilidades

O Connector pode:

- navegar;
- clicar;
- preencher campos;
- trocar páginas;
- fazer scroll;
- aguardar carregamentos.

O Connector nunca pode:

- persistir dados;
- calcular promoções;
- criar CatalogProduct;
- acessar banco;
- acessar Scheduler;
- acessar API.

---

# Browser

O Browser será fornecido pelo framework.

O Connector nunca instancia Playwright diretamente.

Sempre utilizar BrowserGateway.

---

# Pages

Cada Connector trabalha apenas com páginas.

Nunca compartilhar páginas entre execuções.

Uma execução.

↓

Um Context.

↓

Uma ou mais Pages.

↓

Encerramento.

---

# Tratamento de Erros

Todo erro deverá ser classificado.

Categorias:

NavigationError

TimeoutError

CaptchaError

SelectorError

ParsingError

ValidationError

UnexpectedError

Nunca lançar exceções genéricas.

---

# Retry

O Connector não controla Retry.

O Retry pertence ao framework.

O Connector apenas informa que a operação falhou.

---

# Timeouts

O Connector nunca utiliza valores fixos.

Sempre utilizar:

ConnectorConfig.timeout

---

# Logs

O Connector apenas gera eventos.

Nunca escreve diretamente em arquivos.

Eventos:

START

FETCH

PARSE

EXTRACT

VALIDATE

FINISH

ERROR

Todos serão processados pelo sistema de logging central.

# Pipeline de Execução

O Connector não executa diretamente todas as etapas.

O Framework será responsável por orquestrar o Pipeline.

Fluxo oficial:

ConnectorPipeline

↓

BrowserStrategy

↓

NavigationStrategy

↓

PaginationStrategy

↓

ExtractionStrategy

↓

NormalizationStrategy

↓

ValidationStrategy

↓

ConnectorResult

Cada Strategy possui responsabilidade única.

---

# Browser Strategy

Responsável apenas por:

- iniciar Browser;
- criar Context;
- abrir Pages;
- fechar recursos.

Implementações futuras:

PlaywrightBrowserStrategy

RemotePlaywrightStrategy

HeadlessStrategy

HeadedStrategy

O Connector não conhece nenhuma dessas implementações.

---

# Navigation Strategy

Responsável por:

- abrir URL;
- navegar entre páginas;
- clicar em botões;
- preencher pesquisas;
- aguardar carregamento.

Nunca extrair dados.

---

# Pagination Strategy

Cada loja poderá utilizar uma estratégia diferente.

Exemplos:

Query Parameter

Offset

Infinite Scroll

Cursor

Load More

Botão Próxima Página

Todas implementam o mesmo contrato.

O Connector apenas informa qual estratégia utilizar.

---

# Extraction Strategy

Responsável exclusivamente pela leitura do HTML.

Nunca realiza:

- normalização;
- validação;
- cálculo.

Entrada:

DOM

Saída:

RawProduct

---

# Normalization Strategy

Recebe RawProduct.

Retorna:

NormalizedProduct.

Exemplos de transformação:

Preço

↓

5499,90

↓

5499.90

Título

↓

Notebook Dell G15 Ryzen™ 7

↓

Notebook Dell G15 Ryzen 7

---

# Validation Strategy

Recebe:

NormalizedProduct

Retorna:

ValidProduct

ou

ValidationError

Nunca modifica os dados.

---

# Strategy Registry

O Framework deverá registrar automaticamente todas as estratégias disponíveis.

Estrutura sugerida:

```text
strategies/

browser/

navigation/

pagination/

normalization/

validation/

extraction/
```

O Connector apenas referencia a estratégia necessária.

---

# Seletores

Os seletores deverão ser isolados da lógica.

Estrutura:

```text
selectors/

amazon.yaml

kabum.yaml

pichau.yaml
```

Cada arquivo conterá apenas:

- CSS Selectors;
- XPath (quando necessário);
- atributos;
- expressões auxiliares.

Nunca escrever seletores diretamente no código Python.

---

# Versionamento de Seletores

Sempre que uma loja alterar seu HTML:

Criar nova versão.

Exemplo:

```text
amazon/

v1.yaml

v2.yaml

current.yaml
```

`current.yaml` aponta para a versão ativa.

Nunca sobrescrever versões antigas.

---

# Estratégia para Mudanças de Layout

Quando um seletor deixar de funcionar:

Health Check

↓

Falha

↓

Marcar Connector como DEGRADED

↓

Registrar erro

↓

Continuar execução das demais lojas

Uma loja nunca deve interromper o monitoramento das outras.

---

# Testes Obrigatórios

Cada Connector deverá possuir:

- testes unitários;
- testes de parsing;
- testes de normalização;
- testes de validação;
- testes de paginação;
- testes de tratamento de erro.

Sempre utilizar HTML salvo localmente (fixtures) quando possível.

Evitar dependência do site real durante testes automatizados.

---

# Template Oficial para Novo Connector

Fluxo obrigatório:

1. Criar pasta da loja.
2. Criar arquivo de configuração.
3. Criar arquivo de seletores.
4. Implementar Protocol.
5. Registrar estratégias utilizadas.
6. Criar testes.
7. Executar Health Check.
8. Registrar no Registry.
9. Atualizar documentação.

Nenhuma alteração em outros Connectors deverá ser necessária.

---

# Exemplo Conceitual

AmazonConnector

↓

PaginationStrategy = InfiniteScroll

↓

NavigationStrategy = DefaultNavigation

↓

ExtractionStrategy = AmazonExtractor

↓

NormalizationStrategy = DefaultNormalizer

↓

ValidationStrategy = DefaultValidator

↓

ConnectorResult

Cada loja apenas combina estratégias.

A lógica permanece reutilizável.

---

# Compatibilidade Futura

O Framework deverá permitir futuramente:

- execução distribuída;
- múltiplos navegadores;
- proxies rotativos;
- filas de scraping;
- captura de screenshots;
- exportação de HTML bruto;
- execução em containers independentes.

Esses recursos não fazem parte do MVP, mas não deverão exigir mudanças estruturais.

---

# Convenções para o Claude Code

Ao criar um novo Connector:

1. Nunca copiar código de outro Connector.
2. Reutilizar Strategies existentes sempre que possível.
3. Criar novas Strategies apenas quando realmente necessário.
4. Nunca acessar banco de dados.
5. Nunca conhecer regras de negócio.
6. Nunca implementar lógica de promoção.
7. Nunca modificar o Pipeline.

Caso uma nova necessidade surja, avaliar primeiro a criação de uma nova Strategy antes de alterar a arquitetura existente.

---

# Checklist

Antes de concluir um Connector:

[ ] Implementa o Protocol oficial.

[ ] Utiliza apenas Strategies registradas.

[ ] Não contém seletores embutidos no código.

[ ] Possui configuração própria.

[ ] Possui testes.

[ ] Fecha corretamente Browser e Pages.

[ ] Não acessa infraestrutura proibida.

[ ] Passa no Health Check.

---

# Critérios de Aceitação

O Connector será considerado conforme quando:

[ ] Implementar integralmente o contrato oficial.

[ ] Não possuir dependências diretas do Backend.

[ ] Utilizar apenas o Pipeline definido pelo Framework.

[ ] Possuir cobertura mínima de testes definida em TESTING_GUIDELINES.md.

[ ] Permitir substituição sem impacto nos demais Connectors.

---

# Encerramento

O CONNECTOR_FRAMEWORK.md define a especificação oficial para todos os Connectors do Deal Monitor.

Nenhum novo Connector deverá ser implementado fora deste padrão.

Qualquer alteração estrutural deverá ser registrada por meio de uma ADR antes da implementação.

Fim do Documento.