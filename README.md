# Análise de componentes principais

**Contanto:[transformacao.digital\@fjp.mg.gov.br](mailto:transformacao.digital@fjp.mg.gov.br)**

### Este tutorial aborda a análise de componentes principais (ACP) em R. Exploraremos os conceitos fundamentais da ACP e demonstraremos como implementá-la em R. O tutorial inclui exemplos práticos e código fonte para ajudar os leitores a compreender e aplicar a ACP em seus próprios projetos de análise de dados.

# Introdução

A análise de componentes principais (ACP) é uma técnica estatística amplamente utilizada para redução de dimensionalidade e extração de informações relevantes de conjuntos de dados multivariados. O objetivo principal da ACP é transformar um conjunto de variáveis correlacionadas em um novo conjunto de variáveis não correlacionadas, chamadas de componentes principais. Cada componente principal é uma combinação linear das variáveis originais e é ordenado de acordo com a quantidade de variação que ele captura. Isso permite a identificação dos padrões mais significativos dos dados, facilitando a interpretação e visualização. [@manly1994]

Estes são alguns exemplos de aplicações:

Estudos de qualidade de vida: Através da ACP, é possível identificar dimensões principais que influenciam a qualidade de vida em uma determinada região, permitindo que políticas públicas sejam direcionadas de forma mais eficaz.

Análise de desigualdade social: A ACP pode ser aplicada em indicadores sociais e econômicos para compreender os principais fatores que contribuem para a desigualdade entre grupos populacionais.

Análise de dados de saúde: A ACP é útil para identificar padrões em grandes conjuntos de dados de saúde, como fatores de risco e grupos de doenças.

Estudos de mercado: A ACP é aplicada em pesquisas de mercado para identificar segmentos de clientes com características semelhantes, ajudando as empresas a direcionar suas estratégias de marketing.

Análise de indicadores econômicos: Através da ACP, é possível reduzir um grande número de indicadores econômicos em poucos componentes principais, facilitando a compreensão das principais tendências e correlações na economia.

Previsão econômica: A ACP pode ser usada para reduzir a dimensionalidade de séries temporais econômicas e melhorar a precisão das previsões de indicadores macroeconômicos.

# Como calcular ACP no R

Antes de iniciar o processo de ACP, é necessário carregar as bibliotecas necessárias.

```{r, message=FALSE, warning=FALSE, output=FALSE}

# Lista de pacotes necessários
pacotes <- c("tidyverse", "readxl", "FactoMineR", "factoextra", "gt")

# Verifica se os pacotes estão instalados e instala se necessário
install.packages(setdiff(x = pacotes,
                         y = rownames(installed.packages())))

# Carrega os pacotes
lapply(X = pacotes,
       FUN = library,
       character.only = TRUE)

```

#### Importação dos dados:

Os dados utilizados para a análise de componentes principais são apenas um exemplo, a base contém informações sobre o PIB, Número de óbitos, Despesas com saúde e número de beneficiários do bolsa família. Para calcular ACP os dados devem conter as variáveis numéricas.

Disponível em: [base_dados](https://github.com/vitor-marinho-fjp/fjp_boletim/blob/master/dados/dados_acp.xlsx){.uri}

```{r}

# Importar dados 
df_acp <-read_excel("dados/dados_acp.xlsx")


```

#### Visualização da matriz de dados

Podemos usar o comando `gt()` para visualizar uma tabela estatistica do conjunto de dados. Isso ajuda a garantir que os dados foram carregados corretamente antes de prosseguir com a análise.

```{r}

df_acp %>%
  head(5) %>%
  gt()
```

#### Limpeza e seleção de variáveis

Antes de proceder com ACP vamos selecionar algumas variaveis

```{r}
dados<- df_acp %>% select(pib, numero_obitos, desp_saude, pessoas_pbf)
```

### Função de Redução de dimensionalidade

ACP é realizada usando o pacote `FactoMineR`. Nesse exemplo, o cálculo é feito com comando o `PCA`, onde "dados" é o dataframe que contém as variáveis a serem analisadas:

```{r}
acp <- PCA(dados, graph=F)

```

## **Visualização e exploração dos resultados:**

Para visualizar a qualidade das variáveis no mapa de fatores, usa-se a função `fviz_pca_var()`

O gráfico que exibe a qualidade (cos²) de cada variável em relação aos componentes principais é uma representação visual da contribuição de cada variável para a formação desses componentes. O "cos²" é a proporção da variância da variável original que é explicada pelo componente principal específico.

```{r}

fviz_pca_var(acp, col.var = "cos2",
             gradient.cols = c("#00AFBB", "#E7B800", "#FC4E07"),
             repel = TRUE # Avoid text overlapping 
             )


```

Quando realizamos a Análise de Componentes Principais (ACP), estamos buscando encontrar novas variáveis (os componentes principais) que sejam combinações lineares das variáveis originais, de modo que eles capturem a maior quantidade possível de variação dos dados. Cada componente principal é uma combinação ponderada das variáveis originais, e a quantidade de variação explicada por cada componente é medida pelos autovalores associados a eles.

O gráfico de qualidade das variáveis em relação aos componentes principais ajuda a identificar quais variáveis têm uma forte influência na definição de cada componente principal e quais têm uma influência mais fraca. Essa informação é útil para entender quais variáveis são mais importantes para explicar a estrutura dos dados e quais têm menos impacto.

### **Visualização da comunalidade explicada por cada componente:**

A função `fviz_eig` é utilizada para exibir a inércia explicada por cada componente principal e o pacote `plotly` permite deixar o gráfico interativo. Ela mostra o quanto de variação dos dados é explicada por cada componente:

```{r}
fviz_eig(acp, addlabels=TRUE, ylim = c(0,70))
```

### **Sumarização dos resultados:**

A função `facto_summarize` é usada para resumir as informações sobre as variáveis e os componentes principais obtidos a partir da ACP. No exemplo, a sumarização é feita para os dois primeiros componentes principais (axes = 1:2):

```{r}
facto_summarize(acp, "var", axes = 1:2) %>% gt()
```

Analisando as coordenadas das variáveis nos dois componentes principais:

A variável `despesas_com_saúde` tem uma forte relação positiva com o primeiro componente principal (Dim.1), mas uma relação negativa muito pequena com o segundo componente principal (Dim.2). Isso sugere que essa variável tem um papel dominante na definição do primeiro componente principal, enquanto sua influência no segundo componente é praticamente insignificante.

A variável `populacao_atendida_agua` também possui uma relação muito forte e positiva com o primeiro componente principal (Dim.1), mas uma relação negativa muito pequena com o segundo componente principal (Dim.2). Isso indica que essa variável também é um importante contribuinte para a definição do primeiro componente principal.

Assim como as duas variáveis anteriores, a "`populacao_atentida_esgoto`" tem uma relação positiva forte com o primeiro componente principal (Dim.1) e uma relação negativa pequena com o segundo componente principal (Dim.2). Isso sugere que essa variável é relevante para o primeiro componente principal.

A variável `pessoas_beneficiarias_pbf` tem uma relação positiva relativamente alta com o primeiro componente principal (Dim.1) e uma relação positiva menor com o segundo componente principal (Dim.2). Isso indica que essa variável contribui significativamente para ambos os componentes principais, mas tem uma importância maior no primeiro.

Por fim, o `PIB` tem uma relação positiva muito alta com o segundo componente principal (Dim.2) e uma relação positiva menor com o primeiro componente principal (Dim.1). Isso sugere que essa variável é essencialmente representada pelo segundo componente principal e tem uma importância menor no primeiro.

# Recursos Adicionais

<http://www.sthda.com/english/wiki/wiki.php?id_contents=7851>

### Referências
