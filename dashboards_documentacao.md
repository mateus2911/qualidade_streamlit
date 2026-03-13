# Documentação dos Dashboards — Visão Tecnologia e Sistemas
> Referência para recriação no Streamlit

---

## Estrutura Geral

O sistema possui **4 dashboards** independentes, cada um com sua própria base de dados:

| Dashboard | Arquivo CSV | Descrição |
|---|---|---|
| Qualidade | `qualidade_database.csv` | Auditorias, NPS e POPs |
| Vendas | `vendas_database_2025.csv` | Pipeline comercial e oportunidades |
| Gestão de Pessoas (GP) | `gb_database_2026.csv` | Membros, departamentos e desligamentos |
| Projetos | `projetos_database.csv` | Projetos, membros alocados e status |

---

## 1. Dashboard — Qualidade

### Fonte de Dados
**Arquivo:** `qualidade_database.csv`

**Colunas:**
| Coluna | Tipo | Descrição |
|---|---|---|
| `id_auditoria` | int | Identificador único da auditoria |
| `data_auditoria` | date | Data da auditoria (formato YYYY-MM-DD) |
| `id_projeto` | string | ID do projeto auditado (ex: P001, N/A) |
| `nome_projeto` | string | Nome do projeto auditado |
| `tipo_auditoria` | string | Tipo: `Projetos`, `NPS`, `POP` |
| `item_auditado` | string | Ex: `Backup`, `Boas Práticas`, `NPS`, `Projetos` |
| `resultado_texto` | string | `Conforme`, `Não Conforme`, `N/A` |
| `resultado_valor` | int/float | Número de não conformidades ou nota NPS |
| `observacoes` | string | Observações livres |

---

### Filtros (Sidebar)
- **Página 1:**
  - `Ano` — seleção múltipla (padrão: Todos)
  - `Departamento` — seleção múltipla (padrão: Todos)
- **Página 2:**
  - `Nome do Projeto` — seleção múltipla (padrão: Todos)
  - `Data da Auditoria` — seletor de data ou range (padrão: Todos)

---

### Página 1 — Visão Geral NPS e POPs

#### KPIs (Cards grandes)
| KPI | Cálculo | Valor exemplo |
|---|---|---|
| **NPS Médio** | Média de `resultado_valor` onde `tipo_auditoria == 'NPS'` | 9,00 |
| **Total de POPs** | Contagem ou soma de `resultado_valor` onde `tipo_auditoria == 'POP'` | 5 |

#### Gráfico 1 — Classificação dos Clientes (NPS)
- **Tipo:** Gráfico de pizza (Pie chart)
- **Dados:** Classificação por nota NPS
  - `Cliente Promotor` = NPS ≥ 9
  - `Cliente Detrator` = NPS ≤ 6
- **Legenda:** Verde = Promotor, Cinza = Detrator
- **Rótulos:** Mostrar percentual e contagem em cada fatia

#### Gráfico 2 — Saúde dos Processos (Nº de POPs por Departamento)
- **Tipo:** Gráfico de barras horizontal (Bar chart)
- **Eixo X:** Total de POPs (contagem de `resultado_valor` onde `tipo_auditoria == 'POP'`)
- **Eixo Y:** `item_auditado` (departamento ao qual o POP pertence)
- **Cores:** Cinza

#### Tabela de Detalhes
Colunas exibidas:
- `data_auditoria` → **Data da Auditoria**
- `nome_projeto` → **Nome do Projeto**
- `item_auditado` → **Item Auditado**
- `id_projeto` → **ID do Projeto**
- `tipo_auditoria` → **Tipo de Auditoria**
- `resultado_texto` → **Resultado**
- `resultado_valor` → **Não Conformidades**
- `observacoes` → **Observações**

---

### Página 2 — Conformidade e Não Conformidades

#### KPIs (Cards grandes)
| KPI | Cálculo | Valor exemplo |
|---|---|---|
| **Total de Não Conformidades** | Soma de `resultado_valor` onde `resultado_texto == 'Não Conforme'` | 1 |
| **Taxa de Conformidade** | `(contagem de resultado_texto == 'Conforme') / (contagem total onde tipo_auditoria == 'Projetos' ou 'POP')` | 0,50 |

> **Nota:** A taxa de conformidade exclui registros do tipo `NPS` do denominador.

#### Gráfico — Volume de Não Conformidades por Tipo de Auditoria
- **Tipo:** Gráfico de barras vertical (Bar chart)
- **Eixo X:** `tipo_auditoria`
- **Eixo Y:** Soma de `resultado_valor` (não conformidades)
- **Filtro:** Apenas registros onde `resultado_texto == 'Não Conforme'`
- **Cores:** Cinza

#### Tabela de Detalhes
Colunas exibidas:
- `data_auditoria` → **Data da Auditoria**
- `nome_projeto` → **Nome do Projeto**
- `tipo_auditoria` → **Tipo de Auditoria**
- `item_auditado` → **Item Auditado**
- `resultado_texto` → **Resultado**
- `resultado_valor` → **Não Conformidades**
- `observacoes` → **Observações**

---

## 2. Dashboard — Vendas

### Fonte de Dados
**Arquivo:** `vendas_database_2025.csv`

**Colunas:**
| Coluna | Tipo | Descrição |
|---|---|---|
| `ano` | int | Ano da oportunidade |
| `data_criacao` | date | Data de criação da oportunidade |
| `nome_cliente` | string | Nome do cliente |
| `nome_projeto` | string | Nome do projeto/oportunidade |
| `tipo_projeto` | string | `Website`, `Sistema Web`, `Outros` |
| `status` | string | `Briefing`, `Orçamento`, `Contrato`, `Aprovado` |
| `data_proposta_enviada` | date | Data do envio da proposta |
| `data_fechamento` | date | Data do fechamento |

**Colunas calculadas necessárias:**
- `dias_para_fechar` = `data_fechamento - data_proposta_enviada` (em dias)
- `mes` = mês extraído de `data_proposta_enviada` ou `data_criacao`

---

### Filtros (Sidebar)
- `Ano de Criação` — seleção (padrão: 2025)
- `Clientes` — seleção múltipla (padrão: Todos)
- `Tipo do Projeto` — seleção múltipla (padrão: Todos)

---

### KPIs (Cards grandes)
| KPI | Cálculo | Valor exemplo |
|---|---|---|
| **Total Oportunidades** | Contagem total de linhas | 7 |
| **Projetos Aprovados** | Contagem onde `status == 'Aprovado'` | 2 |
| **Taxa de Conversão** | `Projetos Aprovados / Total Oportunidades * 100` | 100,0% |
| **Tempo Médio de Vendas** | Média de `dias_para_fechar` (apenas registros com valor) | 13,0 dias |

---

### Gráfico 1 — Total Oportunidades por Status
- **Tipo:** Gráfico de barras horizontal (Bar chart)
- **Eixo Y:** `status` (ordem: Briefing → Orçamento → Contrato → Aprovado)
- **Eixo X:** Contagem de oportunidades por status
- **Rótulos:** Mostrar valor e percentual do total na barra
- **Cores:** Cinza escuro

#### Dados exemplo:
| Status | Contagem |
|---|---|
| Briefing | 1 |
| Orçamento | 3 |
| Contrato | 1 |
| Aprovado | 2 |

---

### Gráfico 2 — Total Oportunidades por Tipo de Projeto
- **Tipo:** Gráfico de pizza (Pie chart)
- **Dados:** Contagem de oportunidades agrupadas por `tipo_projeto`
- **Rótulos:** Mostrar percentual e contagem
- **Legenda:** Website (preto/escuro), Sistema Web (cinza médio), Outros (cinza claro)

#### Dados exemplo:
| Tipo | Contagem | % |
|---|---|---|
| Website | 4 | 57,14% |
| Sistema Web | 2 | 28,57% |
| Outros | 1 | 14,29% |

---

### Gráfico 3 — Total Oportunidades por Mês
- **Tipo:** Gráfico de linha (Line chart)
- **Eixo X:** Mês (extraído da data)
- **Eixo Y:** Contagem de oportunidades no mês
- **Estilo:** Linha com marcadores nos pontos
- **Cores:** Cinza escuro

#### Dados exemplo:
| Mês | Total |
|---|---|
| agosto | 2 |
| setembro | 1 |
| outubro | 3 |

---

### Tabela de Detalhes
Colunas exibidas:
- `nome_projeto` → **Nome do Projeto**
- `tipo_projeto` → **Tipo do Projeto**
- `status` → **Status**
- `dias_para_fechar` → **Dias Para Fechar**
- `ano` → **Ano**
- `mes` → **Mês**
- `dia` → **Dia**

---

## 3. Dashboard — Gestão de Pessoas (GP)

### Fonte de Dados
**Arquivo:** `gb_database_2026.csv`

**Colunas:**
| Coluna | Tipo | Descrição |
|---|---|---|
| `ano` | int | Ano de referência |
| `nome` | string | Nome completo do membro |
| `departamento` | string | Departamento do membro |
| `ocupacao` | string | `Assessor(a)`, `Diretor(a)`, `Desligado` |
| `cargo` | string | Cargo específico |
| `email_visao` | string | E-mail institucional |
| `email_pessoal` | string | E-mail pessoal |
| `telefone` | string | Telefone |
| `cpf` | string | CPF |
| `rg` | string | RG |
| `data_nascimento` | date | Data de nascimento |
| `matricula_ufop` | string | Matrícula UFOP |
| `github` | string | Usuário GitHub |
| `estado_civil` | string | Estado civil |
| `cadastro_bj` | string | Status cadastro BJ |
| `data_efetivacao` | date | Data de efetivação |
| `data_desligamento` | date | Data de desligamento (se houver) |
| `continuou_ano_seguinte` | string | Se continuou no ano seguinte |
| `sexo_biologico` | string | `Masculino`, `Feminino` |

**Colunas calculadas necessárias:**
- `is_desligado` = `ocupacao == 'Desligado'` ou `data_desligamento` preenchida
- `is_novo_membro` = membros com `data_efetivacao` no ano filtrado

---

### Filtros (Sidebar)
- `Ano` — seleção (padrão: 2025 — filtrar pelo campo `ano`)
- `Departamento` — seleção múltipla (padrão: Todos)
- `Cargo` — seleção múltipla (padrão: Todos)

---

### KPIs (Cards grandes)
| KPI | Cálculo | Valor exemplo |
|---|---|---|
| **Total de Membros** | Contagem de membros ativos (não desligados) no ano | 37 |
| **Novos Membros** | Contagem de membros com `data_efetivacao` no ano filtrado | 31 |
| **Desligamentos** | Contagem onde `ocupacao == 'Desligado'` ou `data_desligamento` preenchida | 0 (2025) / 4 (2026) |
| **Taxa de Retenção** | `(Total - Desligamentos) / Total * 100` | Em branco se sem dados suficientes |

---

### Gráfico 1 — Membros por Departamento
- **Tipo:** Gráfico de barras horizontal (Bar chart)
- **Eixo Y:** `departamento`
- **Eixo X:** Contagem de membros ativos por departamento
- **Rótulos:** Mostrar número ao lado de cada barra
- **Cores:** Cinza escuro
- **Ordenação:** Decrescente por contagem

#### Dados exemplo (2025):
| Departamento | Membros |
|---|---|
| Projetos | 11 |
| Gestão de Pessoas | 7 |
| Qualidade | 7 |
| Vendas | 6 |
| Marketing | 5 |
| Presidência | 1 |

---

### Gráfico 2 — Membros por Sexo Biológico
- **Tipo:** Gráfico de rosca (Donut chart)
- **Dados:** Contagem agrupada por `sexo_biologico`
- **Rótulos:** Mostrar contagem e percentual em cada fatia
- **Legenda:** Masculino (cinza escuro), Feminino (rosa/salmão)

#### Dados exemplo (2025):
| Sexo | Contagem | % |
|---|---|---|
| Masculino | 31 | 83,78% |
| Feminino | 6 | 16,22% |

---

### Bloco Informativo — Sugestão de Melhoria
- **Tipo:** Caixa de texto estático destacada
- **Título:** `Sugestão de Melhoria: "Membros por Curso"`
- **Conteúdo:** Texto informativo sobre inclusão de dados do curso de cada membro na planilha de GP
- **Assinatura:** Israel — Diretor Interino de Projetos — Gestão 2025

---

### Tabela de Membros
Colunas exibidas:
- `nome` → **nome**
- `departamento` → **departamento**
- `cargo` → **cargo**

---

## 4. Dashboard — Projetos

### Fonte de Dados
**Arquivo:** `projetos_database.csv`

**Colunas:**
| Coluna | Tipo | Descrição |
|---|---|---|
| `ano` | int | Ano do projeto |
| `nome_do_projeto` | string | Nome do projeto |
| `status_do_projeto` | string | `Em andamento`, `Concluído` |
| `tipo_do_projeto` | string | `Externo`, `Interno` |
| `nome_do_membro` | string | Nome do membro alocado |
| `departamento` | string | Departamento do membro |
| `papel_no_projeto` | string | Ex: `Desenvolvedor`, `Scrum Master`, `Product Owner` |
| `data_de_entrada` | date | Data de entrada do membro no projeto |
| `data_de_saida` | date | Data de saída do membro do projeto |

**Colunas calculadas necessárias:**
- `projeto_unico` = conjunto distinto de `nome_do_projeto` (para contagens de projetos)
- `membros_alocados` = conjunto distinto de `nome_do_membro` no ano/filtro ativo

---

### Filtros (Sidebar)
- `Ano` — seleção (padrão: 2025)
- `Status do Projeto` — seleção múltipla (padrão: Todos)
- `Nome do Projeto` — seleção múltipla (padrão: Todos)
- `Membro` — seleção múltipla (padrão: Todos)
- `Departamento` — seleção múltipla (padrão: Todos)

---

### KPIs (Cards grandes)
| KPI | Cálculo | Valor exemplo |
|---|---|---|
| **Total de Projetos** | Contagem distinta de `nome_do_projeto` no ano | 10 |
| **Projetos em Andamento** | Contagem distinta de projetos onde `status_do_projeto == 'Em andamento'` | 6 |
| **Total de Membros Alocados** | Contagem distinta de `nome_do_membro` no ano/filtros | 17 |
| **% de Membros Alocados** | `Membros Alocados / Total de Membros GP * 100` | 45,95% |

> **Nota:** "% de Membros Alocados" usa o total de membros ativos da base de GP como denominador (37 em 2025).

---

### Gráfico 1 — Total de Projetos por Ano
- **Tipo:** Gráfico de barras vertical (Bar chart)
- **Eixo X:** Ano
- **Eixo Y:** Contagem distinta de projetos no ano
- **Rótulos:** Mostrar valor no topo de cada barra
- **Cores:** Cinza

---

### Gráfico 2 — Projetos por Departamento
- **Tipo:** Gráfico de barras horizontal (Bar chart)
- **Eixo Y:** `departamento` do membro (prefixo "Dep. " + nome)
- **Eixo X:** Contagem de alocações por departamento
- **Rótulos:** Mostrar número ao lado de cada barra
- **Ordenação:** Decrescente
- **Cores:** Cinza

#### Dados exemplo (2025):
| Departamento | Alocações |
|---|---|
| Dep. Projetos | 10 |
| Dep. Qualidade | 5 |
| Dep. Marketing | 3 |
| Dep. Vendas | 2 |
| Dep. Gestão de Pessoas | 1 |

---

### Gráfico 3 — Status dos Projetos
- **Tipo:** Gráfico de pizza (Pie chart)
- **Dados:** Contagem distinta de projetos por `status_do_projeto`
- **Rótulos:** Mostrar contagem e percentual
- **Legenda:** Em andamento (cinza), Concluído (verde)

#### Dados exemplo (2025):
| Status | Contagem | % |
|---|---|---|
| Em andamento | 6 | 60% |
| Concluído | 4 | 40% |

---

### Tabela de Detalhes
Colunas exibidas:
- `ano` → **Ano**
- `nome_do_projeto` → **Projeto**
- `nome_do_membro` → **Membro**
- `departamento` → **Departamento**
- `papel_no_projeto` → **Papel no Projeto**
- `status_do_projeto` → **Status do Projeto**
- `tipo_do_projeto` → **Tipo de Projeto**
- `data_de_entrada` → **Data de Entrada**

---

## Paleta de Cores e Estilo Visual

> Baseado nos dashboards originais da "Visão Tecnologia e Sistemas"

| Elemento | Cor |
|---|---|
| Fundo sidebar | Cinza médio (`#6B6B6B` aprox.) |
| Fundo principal | Branco/Off-white |
| Barras e elementos primários | Cinza escuro (`#4A4A4A`) |
| Destaque / Concluído | Verde (`#7AB03A` aprox.) |
| Texto KPI grande | Preto |
| Texto labels | Cinza escuro |
| Gráfico pizza — Promotor | Verde (`#7AB03A`) |
| Gráfico pizza — Detrator | Cinza (`#AAAAAA`) |
| Gráfico rosca — Masculino | Cinza escuro |
| Gráfico rosca — Feminino | Rosa/Salmão (`#F4A7B9` aprox.) |
| Logo Visão | Verde + Branco |

---

## Observações para Implementação no Streamlit

1. **Navegação entre dashboards:** Usar `st.sidebar` com `st.radio` ou `st.selectbox` para alternar entre as 4 páginas.
2. **Navegação entre páginas internas (Qualidade):** O dashboard de Qualidade tem 2 sub-páginas — implementar com `st.tabs`.
3. **KPIs:** Usar `st.metric` para exibir os valores grandes com rótulos.
4. **Gráficos:** Recomenda-se Plotly (`plotly.express`) para manter interatividade similar ao Power BI.
5. **Tabelas:** Usar `st.dataframe` com filtros já aplicados.
6. **Logo:** Exibir logo da Visão no topo do sidebar em todas as páginas.
7. **Filtros:** Todos os filtros devem ser reativos — ao selecionar um valor, todos os KPIs, gráficos e a tabela se atualizam.
8. **Formatação de datas:** CSV usa `YYYY-MM-DD` e `DD/MM/YYYY` — padronizar no carregamento com `pandas.to_datetime`.
9. **Valores N/A:** Campos com `N/A` no CSV devem ser tratados como `NaN` no pandas para evitar erros de cálculo.
10. **Taxa de Retenção:** Aparece como "Em branco" quando não há dados suficientes — implementar verificação antes do cálculo.
