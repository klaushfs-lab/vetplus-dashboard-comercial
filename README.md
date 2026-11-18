# VetPlus | Dashboard Comercial em Power BI

Dashboard de acompanhamento comercial que compara **vendas realizadas x meta** por mês, região e distribuidor, para apoiar a tomada de decisão da gestão comercial.

> ⚠️ Projeto de portfólio com **dados fictícios**.

![Visão geral do dashboard](imagens/dashboard_visao_geral.png)

---

## 🎯 Perguntas de negócio

O dashboard foi desenhado para responder, em poucos segundos:

1. **Como estamos?** Quanto vendemos e quanto falta para a meta?
2. **O problema é de agora ou vem de longe?** Como foi a evolução mês a mês?
3. **Onde está o problema?** Quais regiões estão mais distantes da meta?
4. **Com quem agir primeiro?** Quais distribuidores são mais críticos?
5. **Perdemos receita depois de vender?** Qual o peso das devoluções e cancelamentos?

---

## 💡 Principais insights

- A empresa vendeu **R$ 15,78 Mi** contra uma meta de **R$ 16,80 Mi** (**93,91%** de atingimento), um desvio de **-R$ 1,02 Mi**.
- **Somente dezembro/2025 superou a meta.** Janeiro/2026 foi o pior mês (**83%**), indicando possível **sazonalidade** pós-festas. As vendas se recuperaram ao longo do ano (agosto chegou a 99,4%), mas não voltaram à meta.
- **Nenhuma região bateu a meta.** Norte (89,8%) e Nordeste (89,7%) estão abaixo de 90%; o Sudeste lidera com 96,5%.
- **13 dos 20 distribuidores estão abaixo da meta**: o problema é generalizado, não pontual. O mais crítico é o **Vet Saúde CE (74,8%)**.
- O **Saúde Animal RJ (83,6%)** está entre os 5 piores, mesmo pertencendo à região com melhor resultado. Sem o detalhamento por distribuidor, esse caso ficaria escondido na média regional.
- Devoluções e cancelamentos somam **R$ 296 mil (1,84% da venda bruta)**.

### Recomendações

- Revisar a **meta de janeiro**, considerando a sazonalidade observada.
- Criar **campanhas comerciais no 1º trimestre** para compensar a queda pós-dezembro.
- Priorizar ações em **Norte e Nordeste** e nos **5 distribuidores mais críticos**.
- Usar os **7 distribuidores que bateram a meta** como referência de boas práticas.

---

## 🖱️ Interatividade

- Segmentações de **Mês**, **Região** e **Distribuidor**, que filtram umas às outras.
- **Filtro cruzado:** clicar em uma região, mês ou distribuidor atualiza o painel inteiro.
- O ranking de distribuidores críticos e o eixo do gráfico mensal **se recalculam** conforme o filtro.

![Dashboard filtrado pela região Nordeste](imagens/dashboard_nordeste.png)

---

## 🛠️ Técnicas utilizadas

### Tratamento de dados (Power Query)
- Remoção de colunas redundantes, padronização de nomes e tipos de dados (decimal fixo para valores monetários).
- Limpeza de espaços invisíveis e verificação de duplicidade de chaves.
- **Validação da regra de negócio:** Venda Bruta − Devoluções − Cancelamentos = Venda Líquida (diferenças encontradas eram apenas de precisão de ponto flutuante).

### Modelagem de dados
- **Modelo estrela:** tabela fato (`fVendas`) e dimensões (`dDistribuidores` e `dCalendario`).
- Tabela calendário criada em DAX, dinâmica e marcada como tabela de datas.
- Medidas centralizadas em uma tabela dedicada (`_Medidas`).

![Modelo de dados](imagens/modelo_de_dados.png)

### DAX
13 medidas, entre elas:

```dax
Distribuidores Abaixo da Meta =
COUNTROWS (
    FILTER (
        VALUES ( dDistribuidores[Distribuidor_ID] ),
        [Atingimento %] < 1
    )
)
```

```dax
Eixo Max Evolucao =
VAR MaiorValor =
    MAXX (
        VALUES ( dCalendario[Mes Ano] ),
        MAX ( [Venda Líquida R$], [Meta R$] )
    )
RETURN
    MaiorValor * 1.15
```

### Visualização
- Formatação condicional em **semáforo** (vermelho < 90%, âmbar 90% a 99%, verde ≥ 100%) aplicada de forma consistente em cartões, gráficos e tabela.
- Filtro **Top N** para os 5 distribuidores com menor atingimento.
- Linhas de referência para a meta e **eixo dinâmico** controlado por medida.
- Eixos iniciando em zero, para comparações visuais honestas.

---

## 📁 Estrutura do repositório

| Arquivo | Descrição |
|---|---|
| `VetPlus_Dashboard_Comercial.pbix` | Arquivo do Power BI |
| `dados/base_case_comercial_vetplus.xlsx` | Base de dados fictícia |
| `imagens/` | Capturas de tela do dashboard e do modelo |

### Como abrir
1. Baixe o repositório (botão **Code → Download ZIP**) e descompacte.
2. Abra o arquivo `.pbix` no [Power BI Desktop](https://www.microsoft.com/pt-br/power-platform/products/power-bi/desktop) (gratuito).
3. Se necessário, atualize o caminho da fonte em **Transformar dados → Configurações da fonte de dados**, apontando para a planilha da pasta `dados`.

---

## 👤 Autor

**Klaus Silva**
[LinkedIn](https://www.linkedin.com/in/klaushfsilva) · [GitHub](https://github.com/klaushfs-lab)
