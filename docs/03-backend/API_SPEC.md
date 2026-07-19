# API_SPEC

Projeto: Deal Monitor

Versão: 1.0

Status: Documento Oficial

Dependências

- DOMAIN_MODEL.md
- BACKEND_SPEC.md
- FRONTEND_SPEC.md
- PROMOTION_ENGINE.md

---

# Objetivo

Este documento define a especificação oficial da API do Deal Monitor.

A API é responsável exclusivamente por expor funcionalidades da camada Application.

Ela não contém regras de negócio.

Toda regra de negócio pertence aos Use Cases.

---

# Filosofia

A API deverá seguir os princípios abaixo:

- Stateless
- RESTful
- Versionada
- Idempotente quando aplicável
- Documentada automaticamente (OpenAPI)
- Orientada a recursos

---

# Arquitetura

Fluxo oficial:

```text
Cliente

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

Cliente
```

O Controller nunca acessa diretamente:

- banco de dados;
- ORM;
- Connectors;
- Promotion Engine.

Toda comunicação ocorre por meio dos Use Cases.

---

# Organização

```text
presentation/

controllers/

requests/

responses/

middlewares/

dependencies/

routers/

exceptions/
```

---

# Versionamento

Toda API deverá ser versionada.

Formato:

```text
/api/v1
```

Mudanças incompatíveis deverão gerar uma nova versão.

Exemplo:

```text
/api/v2
```

Nunca quebrar compatibilidade da versão anterior.

---

# Recursos Principais

A API será organizada em recursos.

Recursos previstos para o MVP:

- Products
- Promotions
- Stores
- Categories
- Scraping Jobs
- Configuration
- Health

Cada recurso possuirá seu próprio Router.

---

# Casos de Uso

Os endpoints deverão refletir os Use Cases da aplicação.

Exemplos:

List Products

Get Product Details

List Promotions

Get Promotion

List Stores

Enable Store

Disable Store

Run Scraping

List Scraping Jobs

Get Configuration

Update Configuration

Health Check

Esses Use Cases serão mapeados para endpoints HTTP.

---

# Convenções

URLs:

Utilizar substantivos.

Exemplo:

```text
/products
```

Evitar verbos.

Exemplo incorreto:

```text
/getProducts
```

---

# Métodos HTTP

GET

Consultas.

POST

Criação.

PUT

Substituição completa.

PATCH

Atualização parcial.

DELETE

Remoção lógica quando aplicável.

---

# Padrão de Resposta

Todas as respostas deverão seguir estrutura consistente.

Sucesso:

```json
{
  "data": {},
  "meta": {},
  "links": {}
}
```

Erro:

```json
{
  "error": {
    "code": "",
    "message": "",
    "details": []
  }
}
```

---

# Paginação

Toda coleção deverá suportar paginação.

Parâmetros mínimos:

page

page_size

sort

order

Filtros específicos poderão ser adicionados por recurso.

---

# AI IMPLEMENTATION NOTES

Ao implementar a API, o Claude Code deverá:

- manter Controllers extremamente pequenos;
- delegar toda lógica aos Use Cases;
- utilizar DTOs específicos para Request e Response;
- nunca reutilizar entidades de domínio como modelos de entrada ou saída;
- gerar documentação OpenAPI automaticamente.

# Padrões Gerais da API

Todos os endpoints definidos neste documento deverão seguir as convenções abaixo.

Essas regras são obrigatórias para qualquer recurso exposto pela aplicação.

---

# Content-Type

Todas as requisições e respostas utilizarão:

application/json

Exceto endpoints futuros destinados à exportação de arquivos.

---

# Codificação

UTF-8 obrigatório.

---

# Timezone

Toda data deverá ser armazenada em UTC.

A API sempre retornará timestamps em ISO-8601.

Exemplo:

2026-07-19T15:30:42Z

Conversões de fuso horário pertencem exclusivamente ao Frontend.

---

# Formato de Datas

Campos de data:

createdAt

updatedAt

detectedAt

expiresAt

finishedAt

startedAt

Todos seguem ISO-8601.

---

# Convenção de Campos

JSON utilizará camelCase.

Exemplo:

priceHistory

promotionScore

storeName

categoryName

Nunca utilizar snake_case nas respostas da API.

---

# Identificadores

Todas as entidades públicas utilizarão UUID.

Nunca expor IDs internos do banco.

---

# Paginação

Estrutura oficial:

{
  "data": [],
  "meta": {
      "page": 1,
      "pageSize": 20,
      "totalItems": 1523,
      "totalPages": 77
  }
}

---

# Ordenação

Todos os endpoints de listagem deverão aceitar:

sort

order

Exemplos:

sort=price

sort=createdAt

sort=score

order=asc

order=desc

---

# Filtros

Filtros deverão ser explícitos.

Exemplos:

category

store

brand

priceMin

priceMax

available

promotionType

Nunca utilizar parâmetros genéricos cujo significado não seja evidente.

---

# Pesquisa Textual

Parâmetro oficial:

search

Exemplo:

GET /products?search=rtx+5070

A pesquisa deverá ser case insensitive.

---

# Códigos HTTP

200

Consulta realizada com sucesso.

201

Recurso criado.

202

Processamento assíncrono iniciado.

204

Operação concluída sem conteúdo.

400

Erro de validação.

401

Não autenticado.

403

Acesso negado.

404

Recurso inexistente.

409

Conflito.

422

Dados semanticamente inválidos.

429

Limite de requisições excedido.

500

Erro inesperado.

---

# Estrutura de Erros

Formato oficial:

{
  "error": {
      "code": "PRODUCT_NOT_FOUND",
      "message": "Product not found.",
      "details": []
  }
}

code

Identificador técnico.

message

Mensagem amigável.

details

Lista de erros específicos.

---

# Correlação de Requisições

Toda requisição receberá um Correlation ID.

Header:

X-Correlation-ID

Caso não seja informado pelo cliente, será gerado automaticamente.

Esse identificador deverá aparecer em todos os logs relacionados à requisição.

---

# Rate Limiting

O MVP deverá permitir configuração de limite de requisições por IP.

A política inicial poderá ser simples, mas a implementação deverá permitir evolução futura.

---

# Cache

Endpoints de consulta poderão utilizar cache quando apropriado.

O uso de cache nunca poderá comprometer a consistência das informações críticas.

A política de cache deverá ser configurável por endpoint.

---

# Compressão

Respostas deverão suportar compressão HTTP quando disponível.

---

# Observabilidade

Cada endpoint deverá registrar:

- duração da requisição;
- código HTTP;
- rota acessada;
- Correlation ID;
- quantidade de registros retornados (quando aplicável).

---

# Segurança

Mesmo no MVP, a API deverá ser preparada para futura autenticação.

A ausência de autenticação inicial não deverá gerar acoplamento que dificulte sua inclusão posteriormente.

---

# AI IMPLEMENTATION NOTES

O Claude Code deverá:

- implementar um padrão único de resposta para todos os endpoints;
- centralizar o tratamento de exceções;
- evitar repetição de validações entre Controllers;
- utilizar middleware para funcionalidades transversais como Correlation ID, logging e tratamento de erros;
- garantir consistência entre todos os recursos da API.