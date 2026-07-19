# PROMOTION_ENGINE

Projeto: Deal Monitor

Versão: 1.0

Status: Documento Oficial

Dependências

- DOMAIN_MODEL.md
- DATABASE_SCHEMA.md
- BACKEND_SPEC.md

---

# Objetivo

O Promotion Engine é responsável por transformar dados brutos coletados pelos Connectors em oportunidades de compra relevantes.

Seu objetivo não é apenas identificar reduções de preço, mas determinar se uma oferta possui valor real para o usuário.

O Engine não envia notificações, não executa scraping e não persiste dados diretamente.

Ele apenas analisa informações e produz decisões de negócio.

---

# Entradas

O Promotion Engine recebe:

- CatalogProduct;
- StoreProduct;
- PriceSnapshot recém-criado;
- Histórico de preços do produto;
- Configurações vigentes.

---

# Saídas

O resultado do processamento será um dos seguintes estados:

- PromotionDetected
- PriceUpdated
- Ignored
- InvalidData

Apenas o estado `PromotionDetected` gera uma entidade `Promotion`.

---

# Pipeline de Processamento

Fluxo oficial:

```text
Novo PriceSnapshot
        │
        ▼
Validação dos dados
        │
        ▼
Normalização
        │
        ▼
Análise histórica
        │
        ▼
Cálculo de métricas
        │
        ▼
Aplicação das regras
        │
        ▼
Geração da decisão
```

Cada etapa possui responsabilidade única e não deve acumular funções de outra etapa.

---

# Etapa 1 — Validação

Objetivo:

Garantir que o PriceSnapshot possa ser utilizado.

Verificações mínimas:

- preço maior que zero;
- URL válida;
- Store habilitada;
- Category habilitada;
- produto associado ao CatalogProduct.

Falhas nesta etapa encerram o processamento.

---

# Etapa 2 — Normalização

Antes de qualquer cálculo, todos os dados deverão estar em formato padronizado.

Exemplos:

Preço:

```text
R$ 5.499,90
```

↓

```text
5499.90
```

Título:

```text
Notebook Dell G15 Ryzen™ 7
```

↓

```text
Notebook Dell G15 Ryzen 7
```

A normalização deve ocorrer apenas uma vez por processamento.

---

# Etapa 3 — Recuperação do Histórico

O Engine consulta o histórico do produto para obter:

- último preço registrado;
- menor preço histórico;
- maior preço histórico;
- preço médio;
- quantidade de coletas.

Esses dados serão utilizados pelas regras de decisão.

---

# Etapa 4 — Cálculo de Métricas

Métricas mínimas:

- variação absoluta;
- variação percentual;
- diferença para o menor preço;
- diferença para o preço médio;
- tempo desde a última alteração;
- quantidade de alterações recentes.

Essas métricas não representam regras, apenas dados derivados.

---

# Etapa 5 — Regras de Decisão

As regras são avaliadas em ordem.

A primeira regra aplicável encerra o processamento.

Exemplo de ordem:

1. Dados inválidos
2. Produto indisponível
3. Preço inalterado
4. Redução insuficiente
5. Promoção detectada

Essa ordem deverá ser configurável em futuras versões, mas permanece fixa no MVP.

---

# AI IMPLEMENTATION NOTES

O Claude Code deverá implementar o Promotion Engine como uma cadeia de processamento composta por etapas independentes.

Cada etapa deve possuir uma única responsabilidade e retornar um resultado explícito para a etapa seguinte.

Evitar funções monolíticas que concentrem toda a lógica de decisão.

Não acoplar o Engine ao banco de dados, aos Connectors ou ao Scheduler.

# Algoritmo de Avaliação

Após a normalização e recuperação do histórico, o Promotion Engine deverá calcular indicadores utilizados pelas regras de decisão.

Esses indicadores não representam decisões finais; eles apenas descrevem o estado atual do produto.

---

# Indicadores Calculados

Para cada PriceSnapshot deverão ser calculados, no mínimo:

- Preço atual
- Último preço conhecido
- Menor preço histórico
- Maior preço histórico
- Preço médio
- Mediana histórica
- Quantidade de registros históricos
- Variação absoluta
- Variação percentual
- Dias desde a última alteração
- Frequência de alterações nos últimos 30 dias

Todos os indicadores deverão ser calculados antes da aplicação de qualquer regra.

---

# Fórmulas

## Variação Absoluta

```text
preço_anterior - preço_atual
```

---

## Variação Percentual

```text
((preço_anterior - preço_atual) / preço_anterior) * 100
```

---

## Distância para o Menor Preço Histórico

```text
preço_atual - menor_preço_histórico
```

---

## Distância para a Média

```text
preço_atual - preço_médio
```

---

# Score de Promoção

O Score representa a qualidade da oportunidade encontrada.

Seu objetivo é permitir ordenação e priorização das promoções.

Faixa oficial:

0 a 100

Onde:

- 0 representa nenhuma relevância;
- 100 representa uma oportunidade excepcional.

---

# Componentes do Score

O Score será composto pelos seguintes fatores:

- Desconto percentual
- Proximidade do menor preço histórico
- Estabilidade histórica do preço
- Disponibilidade do produto
- Qualidade dos dados coletados

Cada componente possui um peso específico.

---

# Pesos Iniciais (MVP)

| Critério | Peso |
|----------|------:|
| Desconto percentual | 40% |
| Menor preço histórico | 30% |
| Estabilidade do preço | 15% |
| Disponibilidade | 10% |
| Qualidade dos dados | 5% |

Os pesos deverão ser parametrizáveis em versões futuras.

---

# Critério 1 — Desconto Percentual

Quanto maior o desconto em relação ao último preço conhecido, maior a pontuação.

Exemplo:

- 2% → baixa pontuação
- 10% → média pontuação
- 25% → alta pontuação

O algoritmo deverá utilizar uma escala contínua, evitando faixas rígidas sempre que possível.

---

# Critério 2 — Menor Preço Histórico

Caso o preço atual seja igual ao menor preço já registrado, a pontuação deste critério deverá ser máxima.

Quanto mais distante do menor preço histórico, menor será a contribuição para o Score.

---

# Critério 3 — Estabilidade

Produtos com preço historicamente estável recebem maior confiança.

Produtos cujo preço oscila diariamente terão menor peso, reduzindo falsos positivos.

A estabilidade deverá considerar:

- número de alterações;
- amplitude das variações;
- frequência das mudanças.

---

# Critério 4 — Disponibilidade

Promoções de produtos indisponíveis não deverão gerar alta pontuação.

Caso o produto esteja indisponível:

- o Score deverá ser reduzido significativamente;
- a geração de Promotion poderá ser bloqueada, conforme configuração.

---

# Critério 5 — Qualidade dos Dados

Serão considerados:

- título válido;
- URL válida;
- preço consistente;
- imagem disponível (quando aplicável);
- categoria identificada.

Quanto maior a qualidade dos dados coletados, maior a confiança na promoção.

---

# Regras para Evitar Falsas Promoções

O Engine deverá ignorar automaticamente situações como:

- redução inferior ao limite configurado;
- alteração causada apenas por arredondamento;
- preço inválido;
- produto indisponível;
- erro evidente de extração;
- histórico insuficiente (quando exigido pela configuração).

Essas verificações ocorrem antes da criação da Promotion.

---

# Menor Preço Histórico

Sempre que um novo PriceSnapshot for recebido:

1. Comparar com o menor preço registrado.
2. Atualizar o histórico apenas se houver novo menor preço.
3. Registrar esse evento para futuras análises.

Esse indicador será utilizado tanto no cálculo do Score quanto na interface do usuário.

---

# Oscilações Pequenas

Para evitar ruído, o sistema deverá ignorar pequenas variações.

Exemplo de configuração inicial:

- diferença absoluta inferior a R$ 1,00; ou
- diferença percentual inferior a 0,5%.

Esses valores deverão ser configuráveis.

---

# AI IMPLEMENTATION NOTES

Ao implementar esta etapa, o Claude Code deverá:

- calcular todos os indicadores antes das regras de decisão;
- manter as fórmulas centralizadas em componentes reutilizáveis;
- evitar duplicação de cálculos entre diferentes partes do sistema;
- garantir que o cálculo do Score seja determinístico para uma mesma entrada;
- isolar a configuração de pesos para permitir futuras alterações sem modificar a lógica principal.

# Arquitetura do Engine

O Promotion Engine atua exclusivamente como um orquestrador.

Toda lógica de decisão deverá ser encapsulada em Rules independentes.

Fluxo oficial:

PriceSnapshot

↓

PromotionEngine

↓

Rule Chain

↓

PromotionDecision

↓

Promotion (opcional)

O Engine nunca implementa regras diretamente.

---

# Rule Chain

Cada Rule possui exatamente uma responsabilidade.

Pipeline inicial:

PriceChangedRule

↓

MinimumDiscountRule

↓

MinimumHistoryRule

↓

AvailabilityRule

↓

HistoricalLowRule

↓

ScoreCalculationRule

↓

PromotionCreationRule

Cada Rule recebe um PromotionContext.

Cada Rule devolve um PromotionContext atualizado.

---

# PromotionContext

O contexto acompanha todo o processamento.

Contém:

- CatalogProduct
- StoreProduct
- PriceSnapshot
- Histórico
- Métricas calculadas
- Score parcial
- Score final
- Motivos da decisão
- Status da avaliação

Nenhuma Rule consulta diretamente banco de dados.

---

# PromotionDecision

Ao término da cadeia de Rules, o Engine gera uma decisão.

Estados possíveis:

Ignored

PriceUpdated

PromotionDetected

InvalidData

Rejected

Esse objeto será consumido pela camada Application.

---

# Expiração de Promoções

Uma Promotion poderá deixar de ser considerada válida quando:

- o preço subir novamente;
- o produto ficar indisponível por período configurável;
- o tempo máximo de validade for atingido;
- a promoção for descartada manualmente.

A expiração nunca remove registros históricos.

---

# Eventos Gerados

O Engine poderá publicar os seguintes eventos:

PromotionDetected

PromotionExpired

PromotionDismissed

PromotionRejected

PromotionScoreUpdated

PriceVariationIgnored

Esses eventos poderão ser utilizados futuramente por notificações, dashboards e integrações.

---

# Estratégias de Pontuação

O cálculo do Score deverá ser desacoplado da lógica principal.

Interface conceitual:

ScoringStrategy

Implementações previstas:

DefaultScoringStrategy

HistoricalScoringStrategy

SeasonalScoringStrategy

BlackFridayScoringStrategy

No MVP, apenas a estratégia padrão será implementada.

---

# Pseudocódigo

Fluxo simplificado:

Receber PriceSnapshot

↓

Validar dados

↓

Normalizar

↓

Recuperar histórico

↓

Calcular métricas

↓

Executar Rule Chain

↓

Se PromotionDetected

Criar Promotion

Senão

Registrar decisão

↓

Publicar eventos

↓

Finalizar processamento

---

# Performance

Objetivos para o MVP:

- Processamento determinístico.
- Complexidade linear em relação ao histórico analisado.
- Reutilização de métricas calculadas.
- Nenhum cálculo repetido durante a mesma execução.

---

# Observabilidade

Cada execução deverá registrar:

- duração total;
- Rule responsável pela decisão final;
- Score calculado;
- motivo da rejeição (quando houver);
- quantidade de Rules executadas.

Esses dados facilitarão auditoria e depuração.

---

# Convenções para o Claude Code

Ao implementar o Promotion Engine:

- Nunca utilizar grandes blocos de if/else.
- Criar uma classe (ou componente) para cada Rule.
- As Rules devem ser independentes e facilmente testáveis.
- O Engine apenas coordena a execução.
- O cálculo do Score deve ser isolado em uma Strategy.
- Regras novas devem ser adicionadas sem modificar as existentes (princípio Open/Closed).

---

# AI IMPLEMENTATION NOTES

O Claude Code deverá:

- Implementar o Engine utilizando Chain of Responsibility.
- Garantir que todas as Rules sejam determinísticas.
- Evitar dependências entre Rules.
- Não permitir que uma Rule altere diretamente infraestrutura.
- Criar testes unitários independentes para cada Rule.
- Criar testes de integração para o fluxo completo do Engine.
- Utilizar nomes claros e alinhados à linguagem ubíqua definida no DOMAIN_MODEL.md.

---

# Checklist

Antes de considerar o Promotion Engine concluído:

[ ] Pipeline implementado conforme especificação.

[ ] Todas as Rules possuem responsabilidade única.

[ ] Score desacoplado em Strategy.

[ ] PromotionContext utilizado durante todo o fluxo.

[ ] Eventos publicados corretamente.

[ ] Testes unitários para cada Rule.

[ ] Testes de integração do Engine.

---

# Critérios de Aceitação

O Promotion Engine será considerado conforme quando:

[ ] Todas as decisões forem reproduzíveis para a mesma entrada.

[ ] O cálculo do Score seguir a estratégia configurada.

[ ] A inclusão de uma nova Rule não exigir alterações nas Rules existentes.

[ ] O Engine permanecer desacoplado do banco de dados, Connectors e Scheduler.

[ ] Todas as decisões puderem ser auditadas por meio dos eventos e métricas gerados.

---

# Encerramento

O PROMOTION_ENGINE.md define a especificação oficial do mecanismo de detecção e avaliação de promoções do Deal Monitor.

Ele estabelece a arquitetura, o pipeline, as regras de decisão e os critérios de pontuação utilizados pelo sistema.

Qualquer alteração estrutural deverá ser registrada por meio de uma ADR antes da implementação.

Fim do Documento.