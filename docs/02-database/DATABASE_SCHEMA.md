# DATABASE_SCHEMA

Projeto: Deal Monitor

Versão: 1.0

Status: Documento Oficial

Dependências:

- ARCHITECTURE.md
- CLEAN_ARCHITECTURE.md
- PROJECT_STRUCTURE.md

---

# Objetivo

Este documento define o modelo de dados oficial do Deal Monitor.

Toda persistência deverá seguir este esquema.

Nenhuma tabela poderá ser criada fora deste documento sem uma ADR correspondente.

---

# Objetivos do Banco

O banco de dados deve permitir:

- armazenar produtos monitorados;
- armazenar lojas;
- armazenar categorias;
- armazenar histórico de preços;
- armazenar promoções detectadas;
- armazenar execuções de scraping;
- armazenar configurações da aplicação;
- permitir evolução futura sem grandes refatorações.

---

# Banco de Dados

SGBD oficial:

PostgreSQL

ORM:

SQLAlchemy 2.x

Migrações:

Alembic

Timezone:

UTC

Charset:

UTF-8

---

# Convenções Gerais

Todas as tabelas seguem as seguintes regras:

- chave primária UUID;
- nomes em snake_case;
- timestamps em UTC;
- created_at obrigatório;
- updated_at obrigatório;
- índices explícitos;
- foreign keys obrigatórias quando aplicável.

---

# Entidades Principais

O MVP será composto pelas seguintes entidades:

- Store
- Category
- CatalogProduct
- ProductPrice
- Promotion
- ScrapingJob
- ApplicationSetting

---

# Relacionamentos

Store

↓

CatalogProduct

↓

ProductPrice

↓

Promotion

Category

↓

CatalogProduct

ScrapingJob

↓

Store

ApplicationSetting

(independente)

---

# Store

Representa um e-commerce monitorado.

Exemplos:

Amazon

Kabum

Pichau

Terabyte

Mercado Livre

---

Campos

id

UUID

PK

---

name

VARCHAR(100)

Obrigatório

Único

---

slug

VARCHAR(50)

Obrigatório

Único

Exemplo:

amazon

kabum

pichau

---

base_url

VARCHAR(500)

Obrigatório

---

enabled

BOOLEAN

Default TRUE

---

created_at

TIMESTAMP

---

updated_at

TIMESTAMP

---

Índices

slug

enabled

---

Relacionamentos

1 Store

↓

N CatalogProduct

---

# Category

Representa uma categoria monitorada.

Exemplos

Notebook

SSD

Processador

Monitor

Memória RAM

Mouse

Teclado

---

Campos

id

UUID

PK

---

name

VARCHAR(100)

Obrigatório

Único

---

slug

VARCHAR(80)

Obrigatório

Único

---

enabled

BOOLEAN

Default TRUE

---

created_at

TIMESTAMP

---

updated_at

TIMESTAMP

---

Relacionamentos

1 Category

↓

N CatalogProduct

---

# CatalogProduct

Representa um produto canônico.

Não pertence a uma loja específica.

Exemplo:

Notebook Dell G15 Ryzen 7 RTX 4060

---

Campos

id

UUID

PK

---

category_id

FK

Obrigatório

---

normalized_name

VARCHAR(300)

Obrigatório

Indexado

---

display_name

VARCHAR(300)

Obrigatório

---

brand

VARCHAR(100)

Opcional

---

model

VARCHAR(150)

Opcional

---

image_url

TEXT

Opcional

---

created_at

TIMESTAMP

---

updated_at

TIMESTAMP

---

Índices

normalized_name

category_id

brand

---

Relacionamentos

1 CatalogProduct

↓

N ProductPrice

---

# ProductPrice

Representa um preço encontrado em uma loja para um produto do catálogo.

Todo histórico de preços será armazenado.

Nenhum registro será atualizado.

Sempre será criado um novo registro.

---

Relacionamentos

1 CatalogProduct

↓

N ProductPrice

---

1 Store

↓

N ProductPrice

---

Campos

id

UUID

PK

---

catalog_product_id

UUID

FK

Obrigatório

---

store_id

UUID

FK

Obrigatório

---

store_product_id

VARCHAR(255)

Opcional

Identificador utilizado pela própria loja quando disponível.

---

product_url

TEXT

Obrigatório

---

title

TEXT

Obrigatório

Título exatamente como encontrado na loja.

---

price

NUMERIC(12,2)

Obrigatório

Sempre armazenado em BRL.

---

list_price

NUMERIC(12,2)

Opcional

Preço original antes do desconto.

---

discount_percentage

NUMERIC(5,2)

Opcional

Calculado durante a normalização.

---

currency

VARCHAR(10)

Default:

BRL

---

availability

VARCHAR(30)

Obrigatório

Valores permitidos:

IN_STOCK

OUT_OF_STOCK

PRE_ORDER

UNKNOWN

---

seller

VARCHAR(150)

Opcional

Marketplace responsável pela venda.

---

shipping_price

NUMERIC(12,2)

Opcional

---

captured_at

TIMESTAMP

Obrigatório

Momento da coleta.

---

created_at

TIMESTAMP

Obrigatório

---

Índices

catalog_product_id

store_id

captured_at DESC

price

availability

---

Restrições

price > 0

captured_at obrigatório

product_url obrigatório

---

# Promotion

Representa uma promoção identificada pelo sistema.

Promoções não substituem o histórico de preços.

São apenas eventos derivados da análise.

---

Relacionamentos

1 Promotion

↓

1 CatalogProduct

---

1 Promotion

↓

1 Store

---

Campos

id

UUID

PK

---

catalog_product_id

UUID

FK

Obrigatório

---

store_id

UUID

FK

Obrigatório

---

price_history_id

UUID

FK ProductPrice

Obrigatório

---

promotion_type

VARCHAR(50)

Obrigatório

Exemplos:

LOWEST_PRICE

HISTORICAL_LOW

FLASH_SALE

PRICE_DROP

---

current_price

NUMERIC(12,2)

Obrigatório

---

previous_price

NUMERIC(12,2)

Opcional

---

discount_percentage

NUMERIC(5,2)

Obrigatório

---

score

INTEGER

Obrigatório

Escala:

0 a 100

Utilizada para ranquear promoções.

---

status

VARCHAR(30)

Obrigatório

Valores:

ACTIVE

EXPIRED

DISMISSED

---

detected_at

TIMESTAMP

Obrigatório

---

expires_at

TIMESTAMP

Opcional

---

created_at

TIMESTAMP

Obrigatório

---

Índices

catalog_product_id

store_id

status

score DESC

detected_at DESC

---

# ScrapingJob

Representa cada execução de scraping.

Toda execução deverá possuir um registro.

Mesmo em caso de falha.

---

Campos

id

UUID

PK

---

store_id

UUID

FK

Obrigatório

---

category_id

UUID

FK

Opcional

---

status

VARCHAR(30)

Obrigatório

Valores:

PENDING

RUNNING

SUCCESS

FAILED

CANCELLED

---

started_at

TIMESTAMP

Obrigatório

---

finished_at

TIMESTAMP

Opcional

---

products_found

INTEGER

Default:

0

---

products_processed

INTEGER

Default:

0

---

new_products

INTEGER

Default:

0

---

updated_prices

INTEGER

Default:

0

---

promotions_detected

INTEGER

Default:

0

---

error_message

TEXT

Opcional

---

execution_time_ms

BIGINT

Opcional

---

created_at

TIMESTAMP

Obrigatório

---

Índices

store_id

status

started_at DESC

finished_at DESC

---

# ApplicationSetting

Representa configurações persistidas.

Não utilizar arquivo para configurações mutáveis.

---

Campos

id

UUID

PK

---

key

VARCHAR(150)

Único

Obrigatório

---

value

TEXT

Obrigatório

---

description

TEXT

Opcional

---

updated_at

TIMESTAMP

Obrigatório

---

created_at

TIMESTAMP

Obrigatório

---

Exemplos

SCRAPING_INTERVAL

MAX_CONCURRENT_JOBS

DEFAULT_TIMEOUT

MAX_RETRIES

HEADLESS_BROWSER

LOG_LEVEL

ENABLE_IMAGES

ENABLE_NOTIFICATIONS

---

Relacionamentos Gerais

Store

↓

CatalogProduct

↓

ProductPrice

↓

Promotion

Category

↓

CatalogProduct

Store

↓

ScrapingJob

ApplicationSetting

(independente)

---

Regras de Integridade

Nunca excluir:

ProductPrice

Promotion

ScrapingJob

Esses registros representam histórico.

---

Soft Delete

Store

Utilizar enabled.

Category

Utilizar enabled.

CatalogProduct

Permanece ativo.

Caso deixe de existir em uma loja, continua no catálogo.

---

Histórico

Toda alteração de preço gera um novo ProductPrice.

Nunca atualizar registros antigos.

Nunca sobrescrever histórico.

---

Normalização

Um CatalogProduct representa um único produto lógico.

Diversos anúncios da mesma loja poderão apontar para ele.

Diversas lojas poderão compartilhar o mesmo CatalogProduct.

---

Concorrência

O banco deverá suportar múltiplos ScrapingJobs simultâneos.

Nenhum registro poderá depender de estado compartilhado em memória.

---

Auditoria

Todos os registros possuem:

created_at

updated_at (quando aplicável)

captured_at (quando aplicável)

detected_at (quando aplicável)

---

Performance

Criar índices apenas para consultas frequentes.

Evitar índices redundantes.

Toda consulta utilizada pelo dashboard deverá possuir índice correspondente.

---

Migrações

Toda alteração estrutural deverá ser realizada exclusivamente através do Alembic.

Nunca alterar tabelas manualmente em ambientes controlados.

---

Checklist

Antes de criar uma nova tabela:

[ ] Existe justificativa arquitetural?

[ ] Há relacionamento definido?

[ ] Os índices foram especificados?

[ ] Os tipos são adequados?

[ ] O histórico será preservado?

[ ] A tabela respeita as convenções oficiais?

---

Critérios de Aceitação

O modelo de dados será considerado correto quando:

[ ] Todas as entidades estiverem normalizadas.

[ ] Não houver duplicação de responsabilidade.

[ ] O histórico de preços for preservado.

[ ] As promoções forem derivadas do histórico.

[ ] O banco suportar crescimento sem alterações estruturais significativas.

[ ] O modelo estiver compatível com SQLAlchemy e Alembic.

---

Encerramento

O DATABASE_SCHEMA.md define o modelo oficial de persistência do Deal Monitor.

Toda implementação de entidades, modelos ORM, migrações, repositórios e consultas deverá seguir este documento.

Qualquer alteração estrutural deverá ser registrada por meio de uma ADR antes da implementação.

Fim do Documento.