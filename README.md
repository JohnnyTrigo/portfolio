# Telecom Ops Analytics — Data & BI Portfolio

> **Senior Data Analyst | BI Lead** · Johnny Trigo  
> Pipeline completo de dados aplicado em ambiente corporativo de grande escala — da modelagem relacional ao dashboard executivo, com automação VBA, governança e gestão de processos.

[![LinkedIn](https://img.shields.io/badge/LinkedIn-johnnytrigo-0A66C2?style=flat&logo=linkedin)](https://www.linkedin.com/in/johnnytrigo/)
[![Email](https://img.shields.io/badge/Email-johnny.trigo%40outlook.com.br-EA4335?style=flat&logo=gmail)](mailto:johnny.trigo@outlook.com.br)
[![Stack](https://img.shields.io/badge/Stack-SQL%20%7C%20Power%20BI%20%7C%20VBA%20%7C%20Python-C8922A?style=flat)](#-stack-técnica)
[![Cases](https://img.shields.io/badge/Cases-10%20projetos%20reais-2D7A4F?style=flat)](#-cases-documentados)

---

## Sobre o Projeto

Portfólio com **10 cases reais** de análise de dados, automação e gestão de processos em uma operadora de telecomunicações. Cada case documenta o problema, a abordagem técnica e o resultado mensurável.

> ⚠️ **Anonimização total:** Nomes de pessoas, empresa, produtos, preços e qualquer informação sensível foram substituídos por valores fictícios. A lógica técnica, os fluxos e os resultados operacionais são reais.

**Destaques do portfólio:**
- Modo claro/escuro com persistência de preferência
- Números animados — contam do zero toda vez que a seção entra na tela ou a aba é trocada
- Dashboard interativo com 5 visões independentes
- Código SQL e VBA com syntax highlighting
- Layout responsivo com scroll horizontal nos cards de resultado

---

## Estrutura do Repositório

```
telecom-ops-analytics/
│
├── portfolio_case_sql_pbi.html    # Portfólio interativo — abrir direto no navegador
│
├── sql/
│   └── telecom_ops_analytics.sql  # Star Schema + 7 queries analíticas de produção
│
├── vba/
│   ├── modulo_impressao.bas        # ImprimirPDF · ConfiguracoesImpressao · ReordenarAbas
│   ├── modulo_validacao.bas        # VerificarErros · AtualizarFormulas
│   ├── modulo_layout.bas           # AtualizarCabecalho · AtualizarGrupoCidades · AjustarRodape
│   └── funcoes_personalizadas.bas  # UDFs: ORDENAR_CANAIS · EXTRAIRPLANO · PORTIN · TIPO_TECNOLOGIA
│
├── dados/
│   ├── NovaTel_Controle_de_Demandas.xlsx  # 2.650 registros anonimizados
│   ├── NovaTel_Tabela_de_Precos.xlsx      # 28.338 linhas · preços aleatorizados
│   └── Banco_de_Horas_anonimizado.xlsx    # 194 registros de saldo de horas
│
├── pbix/
│   └── NovaTel_Comunicacao_Integrada.pbix # Dashboard Power BI anonimizado
│
└── README.md
```

---

## Cases Documentados

| # | Case | Área | Stack Principal | Resultado |
|---|------|------|-----------------|-----------|
| 01 | **Desafio Operacional** | Diagnóstico | Análise de processo | Problema mapeado, silos identificados |
| 02 | **Star Schema & SQL** | Engenharia de Dados | T-SQL · Star Schema | 4 dimensões · 1 fato · view para Power BI |
| 03 | **7 Queries Analíticas** | Analytics Avançado | T-SQL · Window Functions | KPI · Score · Média Móvel · YoY · Z-Score |
| 04 | **Dashboard BI Operacional** | Business Intelligence | Power BI · DAX · RLS | 5 visões · KPIs em tempo real · Bookmarks |
| 05 | **Banco de Horas Analytics** | Power BI | Power BI · Excel | 194 registros · 5 Tooltip Pages · alertas |
| 06 | **Matriz de Gerenciamento** | Gestão de Projetos | SWOT · RACI · GANTTER · PUGH · 5W2H | 5 frameworks · Projeto Kaizen documentado |
| 07 | **Documentação Técnica** | Governança | Word · Governança | Manual co-autoria · 14 seções · oficial |
| 08 | **Ecossistema VBA** | Automação Avançada | VBA Expert · 3 módulos | 8 macros · 6 UDFs · Retry Pattern |
| 09 | **Projeto Kaizen** | Automação | VBA · Excel | **7h → 4 min** · -99% no tempo de execução |
| 10 | **Fluxo de Análise** | Metodologia | Processo · Checklist | 3 etapas · rastreabilidade total |

---

## Arquitetura SQL — Star Schema

```
                    ┌──────────────┐
                    │   DIM_DATA   │
                    └──────┬───────┘
                           │ 1:N
┌───────────────┐  ┌───────┴────────────┐  ┌────────────────┐
│ DIM_ANALISTA  │──│  FCT_SOLICITACOES  │──│ DIM_CATEGORIA  │
└───────────────┘  └────────┬───────────┘  └────────────────┘
        1:N                 │ 1:N                   1:N
                   ┌────────┴────────┐
                   │  DIM_PRODUTO    │
                   └─────────────────┘
```

**Tabela Fato:** `ops.fct_solicitacoes`  
**4 Dimensões:** `dim_data` · `dim_analista` · `dim_categoria` · `dim_produto`  
**View de consumo:** `ops.vw_solicitacoes_pbi` — fonte única desnormalizada para o Power BI

---

## Queries Analíticas (T-SQL)

| Query | Técnicas utilizadas | Objetivo |
|-------|---------------------|----------|
| Q1 — KPI Mensal | `GROUP BY` · `CASE WHEN` · `NULLIF` | Volume, SLA, retrabalho e lead time por período |
| Q2 — Score Analista | Score ponderado (70% SLA + 30% qualidade) | Ranking de performance com métrica composta |
| Q3 — Tendência | `AVG OVER (ROWS BETWEEN 2 PRECEDING)` | Média móvel 3M suavizando sazonalidade |
| Q4 — Crescimento YoY | `LAG(total, 12) OVER (ORDER BY ...)` | Variação ano a ano por mês |
| Q5 — Anomalias | `STDEV` · Z-Score por categoria | Detecção estatística de outliers de lead time |
| Q6 — Gargalos | Lead time vs SLA definido por categoria | Identificação de onde o processo atrasa |
| Q7 — View PBI | `CREATE OR ALTER VIEW` com JOINs múltiplos | Fonte desnormalizada pronta para DirectQuery |

---

## Medidas DAX

```dax
-- KPI principal: exclui demandas ainda abertas do denominador
% Aderência SLA =
DIVIDE(
    CALCULATE(COUNTROWS(fct_solicitacoes), fct_solicitacoes[fl_dentro_sla] = 1),
    CALCULATE(COUNTROWS(fct_solicitacoes), NOT ISBLANK(fct_solicitacoes[fl_dentro_sla])),
    0
)

-- Variação mês a mês com DATEADD na tabela de datas
Var SLA MoM =
VAR atual    = [% Aderência SLA]
VAR anterior = CALCULATE([% Aderência SLA], DATEADD(dim_data[dt_data], -1, MONTH))
RETURN DIVIDE(atual - anterior, anterior, 0)

-- Lead time: AVERAGEX + FILTER exclui demandas em aberto
Lead Time Médio =
CALCULATE(
    AVERAGEX(
        FILTER(fct_solicitacoes, NOT ISBLANK(fct_solicitacoes[nr_lead_time_horas])),
        fct_solicitacoes[nr_lead_time_horas]
    )
)

-- Score composto para ranking de analistas
Score Performance =
VAR sla  = [% Aderência SLA] * 100
VAR qual = (1 - [Taxa Retrabalho]) * 100
RETURN ROUND(sla * 0.7 + qual * 0.3, 1)

-- Tempo de ciclo médio (foco em tempo, não em valor)
Tempo Ciclo Médio =
AVERAGEX(
    FILTER(fct_solicitacoes, fct_solicitacoes[nm_status] = "Concluída"),
    fct_solicitacoes[nr_lead_time_horas]
)
```

---

## Ecossistema VBA — 3 Módulos · 8 Macros

### Módulo Impressão
| Macro | O que faz |
|-------|-----------|
| `ImprimirPDF()` | Valida limite de páginas · seleciona abas em grupo · `ExportAsFixedFormat` |
| `ConfiguracoesImpressao()` | A4 + margens zero + `PrintCommunication = False` para performance máxima |
| `ReordenarAbas()` | Move abas para posição definida em `Parâmetros PDF` |

### Módulo Validação
| Macro | O que faz |
|-------|-----------|
| `VerificarErros()` | Carrega planilha em array → varre erros de fórmula → trata células mescladas → seleciona célula com problema |
| `AtualizarFormulas()` | Leitura/escrita em massa via arrays → concatenação dinâmica por categoria de produto → soma condicional |

### Módulo Layout
| Macro | O que faz |
|-------|-----------|
| `AtualizarCabecalho()` | Rotina mestre: linhas → formas com cor dinâmica → textos → logo com Retry Pattern (10 tentativas) |
| `AtualizarGrupoCidades()` | Dicionários em memória → agrupa por Tecnologia × Região → Rich Text com cor por tipo |
| `AjustarRodape()` | Calcula altura A4 exata → insere/remove linhas para paginação → posiciona logo no rodapé |

### Funções Personalizadas (UDFs)

```vba
' Uso direto em células do Excel: =ORDENAR_CANAIS(A1)

ORDENAR_CANAIS(Texto)      ' UCase → Split(",") → Bubble Sort → Join(", ")
EXTRAIRPLANO(Texto)        ' Busca por especificidade descendente + benefícios agregados
PORTIN(celula)             ' Detecta portabilidade no texto + regra especial para Multi
TIPO_TECNOLOGIA(Texto)     ' Retorna FIBRA / CABO / ÁREA NÃO CABEADA (case-insensitive)
ACENTO(Texto)              ' Normaliza 60+ caracteres especiais para comparação de strings
PERFILMÓVEL(Texto)         ' Classifica MULTI / SINGLE pelo conteúdo do campo
PERFILINTERNET(Texto)      ' COM CELULAR / INDIVIDUAL E DEMAIS COMBINAÇÕES
APROVADOCREDITO(Texto)     ' SIM / NÃO baseado no texto de política comercial
```

---

## Resultados Operacionais

| Indicador | Resultado | Contexto |
|-----------|-----------|----------|
| ⏱ Redução de tempo | **-99%** | Processo crítico de 7h reduzido para 4 minutos com automação VBA |
| ✅ Aderência ao SLA | **96.8%** | Demandas concluídas dentro do prazo acordado (meta: 95%) |
| 🔁 Taxa de Retrabalho | **3.4%** | Abaixo do limite de alerta de 5% |
| ⚡ Lead Time Médio | **4.2h** | Da abertura ao fechamento em horas úteis |
| 📋 Volume monitorado | **627** | Solicitações processadas e rastreadas no período |
| 👥 Equipe gerenciada | **2 especialistas** | Distribuição de demandas e validação técnica de entregas |

---

## Stack Técnica

| Camada | Tecnologias |
|--------|-------------|
| **Linguagens** | SQL (T-SQL · PL/pgSQL) · VBA Expert · Python (Pandas · PySpark) |
| **BI & Visualização** | Power BI Expert · DAX Avançado · RLS · Bookmarks · Tooltip Pages |
| **Big Data** | Apache Spark · Hadoop (Hive · Impala) · IBM Cognos Analytics |
| **Cloud** | AWS (S3 · Glue) · GCP (BigQuery) |
| **Automação** | VBA · ETL/ELT · Pipelines de dados · Adobe Acrobat |
| **Governança** | DataOps · LGPD · Dicionário de dados · Auditoria · Documentação |
| **Metodologia** | Kaizen · Scrum · Kanban · SWOT · RACI · GANTTER · PUGH · 5W2H |
| **Ferramentas** | Jira · Trello · Planner · SharePoint · Microsoft Forms · Teams |

---

## Como Usar

### Abrir o portfólio
```bash
# Sem instalação, sem servidor — abra direto no navegador
open portfolio_case_sql_pbi.html

# Ou arraste o arquivo para uma aba do Chrome, Firefox ou Edge
```

### Executar o SQL
```bash
# SQL Server 2016+ (T-SQL)
sqlcmd -S localhost -d NomeDoBanco -i sql/telecom_ops_analytics.sql

# O script cria o schema, as 4 tabelas, insere os dados de exemplo
# e executa as 7 queries analíticas
```

### Importar os módulos VBA
```
1. Abra o Excel → Alt+F11 (Editor VBA)
2. Insert → Module (um para cada módulo)
3. File → Import File → selecione o .bas correspondente
4. Configure a aba "Parâmetros PDF" com os nomes das abas
5. Execute pelo painel de macros ou por um botão vinculado
```

### Conectar o Power BI
```
1. Abra NovaTel_Comunicacao_Integrada.pbix no Power BI Desktop
2. Transformar Dados → Configurações da Fonte de Dados
3. Aponte para os arquivos .xlsx da pasta /dados
4. Fechar e Aplicar — todos os visuais recarregam automaticamente
```

---

## Dados de Exemplo

| Arquivo | Registros | Conteúdo |
|---------|-----------|----------|
| `NovaTel_Controle_de_Demandas.xlsx` | 2.650 linhas | Histórico de solicitações · nomes fictícios · e-mails removidos |
| `NovaTel_Tabela_de_Precos.xlsx` | 28.338 linhas | Base de precificação · preços aleatorizados ±15% com seed fixo |
| `Banco_de_Horas_anonimizado.xlsx` | 194 registros | Saldo de horas · hierarquia 3 níveis · status Positivo/Negativo |

---

## Formação

| Grau | Curso | Instituição | Situação |
|------|-------|-------------|----------|
| Pós-graduação | Engenharia de Dados | Faculdade Anhanguera | Concluída · Set/2025 |
| Pós-graduação | Comunicação e Oratória | Faculdade Anhanguera | Cursando |
| Graduação | Gestão de Tecnologia da Informação | Centro Universitário Senac | Concluída · Dez/2021 |
| Técnico | Informática | Colégio Praxis | Concluído · Dez/2014 |

---

## Licença

Portfólio pessoal — código disponível como referência de estudo.  
Dados, nomes e documentos são completamente ficcionais.

---

<div align="center">

**Johnny Trigo** · Senior Data Analyst | BI Lead · São Paulo, SP

[linkedin.com/in/johnnytrigo](https://www.linkedin.com/in/johnnytrigo/) · [johnny.trigo@outlook.com.br](mailto:johnny.trigo@outlook.com.br)

</div>
