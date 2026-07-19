# DOMAIN_MODEL.md

Projeto: Deal Monitor

Versão: 1.0

Status: Documento Oficial

Autor: Software Architecture

---

# 1. Objetivo

Este documento define oficialmente o modelo de domínio do Deal Monitor.

O domínio representa os conceitos do negócio.

O domínio não representa tabelas.

O domínio não representa telas.

O domínio não representa APIs.

O domínio representa apenas conceitos do problema que estamos resolvendo.

Toda implementação deverá partir deste documento.

---

# 2. O que é o Domínio

O Deal Monitor é uma plataforma responsável por monitorar continuamente preços de produtos em diversos e-commerces.

Seu objetivo não é vender produtos.

Seu objetivo não é realizar compras.

Seu objetivo é produzir informações confiáveis sobre preços.

Todo o restante do sistema existe para atender esse objetivo.

---

# 3. Missão do Sistema

O sistema deverá responder, de forma rápida e confiável, perguntas como:

• Qual o menor preço atual deste produto?

• Em qual loja está mais barato?

• Esse desconto é realmente bom?

• Qual foi o menor preço dos últimos meses?

• Esse preço acabou de cair?

• Esse produto voltou ao estoque?

Toda regra de negócio existe para responder essas perguntas.

---

# 4. Linguagem Ubíqua (Ubiquitous Language)

Toda equipe deverá utilizar exatamente estes termos.

Nunca criar sinônimos.

Nunca utilizar nomes diferentes para o mesmo conceito.

---

Produto

Representa um item físico monitorado.

Exemplo

RTX 5070

Ryzen 9700X

Samsung Odyssey G5

Nunca representa um anúncio.

Nunca representa um preço.

Nunca representa um registro do scraping.

---

Loja

Representa um e-commerce.

Exemplo

Amazon

Kabum

Pichau

Terabyte

Mercado Livre

---

Categoria

Agrupa produtos semelhantes.

Exemplo

Notebook

SSD

Processador

GPU

Headset

Mouse

---

Histórico

Representa um snapshot do preço.

Nunca deve ser atualizado.

Sempre representa um instante específico.

---

Promoção

Representa uma oportunidade detectada pelo sistema.

Não depende da propaganda da loja.

Depende das regras de negócio.

---

Scraping

Processo responsável por coletar dados das lojas.

---

Connector

Módulo responsável por conversar com apenas uma loja.

Nunca conversa com outra loja.

---

Parser

Transforma HTML em dados estruturados.

---

Normalizer

Transforma dados específicos da loja em objetos padronizados.

---

Dashboard

Interface utilizada pelo usuário.

Nunca possui regra de negócio.

---

Scheduler

Processo responsável pelas atualizações automáticas.

---

# 5. Glossário Oficial

Preço Atual

Último preço conhecido.

---

Preço Original

Preço informado pela loja antes do desconto.

Pode não existir.

---

Menor Preço

Menor preço registrado no histórico.

Nunca depende da loja.

---

Desconto

Diferença percentual entre dois preços.

Sempre calculado pelo sistema.

Nunca confiar no percentual informado pela loja.

---

Disponibilidade

Indica se o produto pode ser comprado.

Não representa quantidade em estoque.

---

Atualização

Processo completo de scraping.

---

Execução

Uma rodada do scheduler.

---

Snapshot

Fotografia do estado do produto em determinado momento.

---

Monitoramento

Acompanhamento contínuo dos preços.

---

# 6. Limites do Domínio

O sistema NÃO faz:

Pagamento

Carrinho

Checkout

Login

Marketplace

Venda

Frete

Cupom

Programa de afiliados

Esses conceitos não pertencem ao domínio.

Nunca deverão aparecer no código.

---

# 7. Objetivos de Negócio

Todo desenvolvimento deve contribuir para pelo menos um destes objetivos.

Objetivo 1

Detectar mudanças de preço.

---

Objetivo 2

Armazenar histórico.

---

Objetivo 3

Facilitar pesquisa.

---

Objetivo 4

Encontrar promoções reais.

---

Objetivo 5

Permitir comparação entre lojas.

---

Objetivo 6

Permitir expansão para novas lojas.

---

# 8. Características do Domínio

O domínio possui algumas características importantes.

Mudança frequente de preços.

Grande quantidade de consultas.

Poucas escritas.

Muitos dados históricos.

Poucas regras complexas.

Muito scraping.

Poucas alterações estruturais.

Essas características influenciam toda arquitetura.

---

# 9. Princípios do Domínio

Princípio 1

Produto é independente da loja.

O mesmo produto pode existir em várias lojas.

---

Princípio 2

Preço nunca pertence ao Produto.

Preço pertence ao Histórico.

Produto possui apenas o preço mais recente.

Todo restante fica no histórico.

---

Princípio 3

Histórico nunca muda.

É imutável.

Caso o preço mude, cria-se um novo registro.

Nunca atualizar um histórico existente.

---

Princípio 4

Uma promoção nunca é salva manualmente.

Sempre é calculada.

---

Princípio 5

Connectors nunca conhecem regras de negócio.

Eles apenas coletam dados.

---

Princípio 6

Dashboard nunca conhece scraping.

---

Princípio 7

Todo cálculo pertence aos Services.

---

# 10. Regras Gerais

Não utilizar entidades para comunicação HTTP.

Não utilizar Models SQLAlchemy como DTO.

Não utilizar DTO para persistência.

Não misturar conceitos.

Cada camada possui sua representação.

---

# 11. Objetos do Domínio

O domínio será composto por cinco tipos de objetos.

Entities

Value Objects

Aggregates

Services

Events

Cada um será detalhado nas próximas seções.

---

# 12. Entities

Entities possuem identidade.

Mesmo que seus atributos mudem, continuam sendo o mesmo objeto.

Exemplo

Produto

Hoje

RTX 5070

Preço

R$ 4.800

Amanhã

RTX 5070

Preço

R$ 4.600

Continua sendo o mesmo Produto.

---

# 13. Value Objects

Não possuem identidade.

São definidos apenas pelos seus valores.

Exemplo

Faixa de preço.

Percentual de desconto.

Intervalo de datas.

Filtros.

Sempre imutáveis.

---

# 14. Domain Services

Representam operações do negócio.

Exemplo

Calcular Promoção.

Calcular Menor Preço.

Comparar Lojas.

Atualizar Histórico.

Não representam tabelas.

---

# 15. Domain Events

Eventos importantes.

Exemplo

Preço Alterado.

Produto Descoberto.

Produto Removido.

Nova Promoção.

Atualização Finalizada.

No MVP serão utilizados apenas internamente.

Não utilizar mensageria.

---

# 16. Objetivo da Modelagem

Toda modelagem deverá responder estas perguntas.

Quem é o objeto?

Quem é responsável?

Quem pode modificá-lo?

Quem pode consultá-lo?

Quem depende dele?

Essas perguntas deverão ser respondidas antes de criar qualquer nova entidade.

---

Fim da Parte 1.

# DOMAIN_MODEL.md

# Parte 2 — Entidades Oficiais do Domínio

---

# 17. Product

## Responsabilidade

Representa um produto monitorado pelo sistema.

O Product é a entidade central de todo o domínio.

Todo o restante do sistema existe para coletar, organizar ou exibir informações relacionadas a ele.

Um Product representa um item físico ou eletrônico vendido em diferentes lojas.

Ele **não representa**:

- um anúncio;
- um preço;
- um resultado de scraping;
- uma promoção;
- uma linha da interface.

Ele possui identidade própria e permanece existindo mesmo que seu preço, disponibilidade ou loja mudem.

---

## Exemplos

Ryzen 7 9700X

RTX 5070

SSD Kingston KC3000 2TB

Samsung Odyssey G5 34"

---

## Identidade

Todo Product deverá possuir um identificador interno único.

Esse identificador nunca muda.

Mesmo que:

- nome seja alterado;
- preço seja alterado;
- categoria seja alterada;
- loja deixe de vender o produto.

---

## Atributos Obrigatórios

- id
- name
- normalized_name
- brand
- category
- created_at
- updated_at
- active

---

## Atributos Opcionais

- model
- image_url
- official_url
- ean
- manufacturer_code
- description

---

## Responsabilidades

Product pode:

✓ possuir histórico

✓ possuir várias ofertas

✓ possuir várias lojas

✓ pertencer a uma categoria

✓ possuir marca

Product nunca pode:

✗ calcular desconto

✗ executar scraping

✗ salvar banco

✗ consultar API

✗ conhecer HTML

---

## Invariantes

Nome nunca pode ser vazio.

Categoria sempre deve existir.

Marca deve existir.

Produto pode estar inativo.

ID nunca muda.

---

## Relacionamentos

Product

↓

Category

Product

↓

PriceHistory

Product

↓

StoreProduct

Product

↓

Promotion

---

# 18. Store

## Responsabilidade

Representa um e-commerce monitorado.

Store não representa uma empresa apenas.

Ela representa toda a estratégia necessária para conversar com aquela loja.

---

## Exemplos

Amazon

Kabum

Terabyte

Pichau

Mercado Livre

---

## Atributos Obrigatórios

- id

- name

- slug

- enabled

- scraping_engine

- retry_limit

- timeout

- request_delay

---

## Responsabilidades

Store pode:

✓ ser habilitada

✓ ser desabilitada

✓ possuir Connector

✓ possuir Parser

✓ possuir Normalizer

Store nunca:

✗ conhece Produtos

✗ calcula preços

✗ executa Scheduler

---

## Invariantes

Nome único.

Slug único.

Timeout maior que zero.

Retry maior ou igual a zero.

---

# 19. Category

## Responsabilidade

Agrupar produtos.

Nunca representa navegação.

Nunca representa menus da interface.

É um conceito de negócio.

---

## Exemplos

Notebook

GPU

Monitor

Mouse

SSD

Processador

Placa Mãe

---

## Atributos

id

name

slug

enabled

priority

---

## Responsabilidades

Agrupar produtos.

Facilitar pesquisas.

Permitir filtros.

Controlar scraping.

---

## Regras

Nome único.

Slug único.

Pode ser desabilitada.

---

# 20. StoreProduct

## Responsabilidade

Representa um Product dentro de uma Store.

Esta entidade resolve um problema importante.

O mesmo Product pode existir em cinco lojas diferentes.

Cada loja possui:

URL diferente

Preço diferente

Imagem diferente

Disponibilidade diferente

Por isso StoreProduct existe.

---

## Atributos

id

product_id

store_id

external_id

product_url

image_url

last_seen

availability

active

---

## Responsabilidades

Relacionar Produto com Loja.

Nunca calcular promoções.

Nunca armazenar histórico.

---

## Invariantes

Um Product pode possuir vários StoreProduct.

Cada StoreProduct pertence a apenas uma Store.

---

# 21. PriceHistory

## Responsabilidade

Registrar um snapshot do preço.

É a entidade mais importante depois de Product.

---

## Características

Imutável.

Nunca atualizar.

Nunca excluir.

Apenas inserir novos registros.

---

## Atributos

id

store_product_id

captured_at

current_price

original_price

discount_percent

currency

availability

scraping_job_id

---

## Regras

Sempre representa um instante específico.

Jamais representa o preço atual.

O preço atual é apenas o último histórico.

---

## Responsabilidades

Permitir gráficos.

Permitir estatísticas.

Permitir comparação.

Permitir detectar promoções.

---

# 22. Promotion

## Responsabilidade

Representa uma oportunidade identificada pelo sistema.

Nunca confiar em etiquetas da loja.

Promoção sempre é calculada.

---

## Exemplos

Menor preço dos últimos 180 dias.

Queda superior a 20%.

Preço abaixo da média histórica.

---

## Atributos

id

product_id

store_product_id

price_history_id

promotion_type

score

detected_at

active

---

## Responsabilidades

Representar eventos.

Nunca alterar histórico.

Nunca alterar preço.

---

# 23. ScrapingJob

## Responsabilidade

Representa uma execução do Scheduler.

---

## Exemplos

Atualização das 08:00.

Atualização das 12:00.

Atualização manual.

---

## Atributos

id

started_at

finished_at

status

duration

trigger

total_products

total_errors

total_new_products

total_price_changes

---

## Estados

Pending

Running

Completed

Failed

Cancelled

---

# 24. ScrapingResult

## Responsabilidade

Representa o resultado produzido por um Connector antes da persistência.

Esta entidade existe apenas durante a execução.

Nunca é salva permanentemente.

---

## Atributos

connector

store

category

page

products_found

products_processed

warnings

errors

duration

---

## Responsabilidades

Auditoria.

Logging.

Métricas.

---

# 25. Configuration

## Responsabilidade

Representa todas as configurações do sistema.

---

## Exemplos

Intervalo Scheduler.

Categorias monitoradas.

Lojas habilitadas.

Timeout.

Retry.

User-Agent.

Concorrência.

---

## Regras

Sempre carregada durante inicialização.

Nunca hardcoded.

Pode ser alterada pela interface futuramente.

---

# 26. Relacionamentos

Product

↓

1 Category

↓

N StoreProducts

↓

N PriceHistory

↓

N Promotions

---

Store

↓

N StoreProducts

↓

1 Connector

---

ScrapingJob

↓

N PriceHistory

↓

N ScrapingResults

---

# 27. Hierarquia do Domínio

Product

é o centro do domínio.

Store

define onde ele existe.

StoreProduct

liga Produto e Loja.

PriceHistory

armazena sua evolução.

Promotion

analisa o histórico.

ScrapingJob

gera novos históricos.

Configuration

controla todo o comportamento do sistema.

---

# 28. Regras Fundamentais

Nunca alterar um PriceHistory.

Nunca excluir histórico.

Nunca alterar Promotion manualmente.

Nunca permitir Product sem Category.

Nunca permitir Store sem Connector.

Nunca permitir StoreProduct sem Product.

Nunca permitir PriceHistory sem StoreProduct.

---

Fim da Parte 2.