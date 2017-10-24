# Cálculo da AUDPC em experimentos com repetição
Emerson  
10/24/2017  


## Epidemiologia comparativa

###  O experimento

No exemplo, um experimento foi conduzido em condições controladas para comparar diferentes espécies de um fungo patogênico quanto capacidade de causar maiou ou menor intensidade de doença após a inoculação em uma espigueta central da espiga. Quatro avaliações foram feitas em intervalos variados de dias após a inoculação, até o vigésimo dia. 

Os dados foram planilhados no formato "longo" no arquivo `data_audpc.xlsx`. Ao carregar o arquivo, pode-se notar quatro colunas: 

- dai: dias após a avaliação
- species: nome da espécie do fungo
- spike: número da espiga inoculada
- above: número de espiguetas acima do ponto de inoculação



```r
library(readxl)
data1 <- read_excel("data_audpc.xlsx")
data1
```

```
## # A tibble: 403 x 4
##      dai             species spike above
##    <dbl>               <chr> <dbl> <dbl>
##  1     4 F. austroamericanum     1     1
##  2     4 F. austroamericanum     2     2
##  3     4 F. austroamericanum     3     2
##  4     4 F. austroamericanum     4     2
##  5     7 F. austroamericanum     1     4
##  6     7 F. austroamericanum     2     4
##  7     7 F. austroamericanum     3     3
##  8     7 F. austroamericanum     4     3
##  9    11 F. austroamericanum     1     4
## 10    11 F. austroamericanum     2     5
## # ... with 393 more rows
```


### Cálculo da AUDPC

As espécies serão comparadas quanto à área abaixo da curva de progresso da doença (em inglês, AUDPC) considerando as espigas como repetições. Primeiramente, deve-se calcular a AUDPC para cada espiga, considerando as quatro avaliações no tempo.

Usaremos a função `aupdc` do pacote `agricolae`. O cálculo da AUDPC para todas as epigas de uma só vez será feita com o auxílio da função `do` do pacote `dplyr` e `tidy` do pacote `broom` para extrair os valores e formatar de forma elegante em um `dataframe`.


```r
library(agricolae)
library(tidyverse)
library(broom)

# pipe para deixar nosso código mais enxuto
audpc1 <- data1 %>% 
# agrupa por espécie e espiga 
  group_by(spike, species) %>% 
# roda a função audpc para número de espigas no y e dia no x
  do(tidy(audpc(.$above, .$dai))) 
# renomeia a últuma coluna para audpc
names(audpc1)[4] <- "audpc"

audpc1
```

```
## # A tibble: 22 x 4
## # Groups:   spike, species [22]
##    spike             species      names audpc
##    <dbl>               <chr>      <chr> <dbl>
##  1     1 F. austroamericanum evaluation  59.5
##  2     1      F. cortaderiae evaluation  50.0
##  3     1      F. graminearum evaluation 103.0
##  4     1      F. meridionale evaluation  21.0
##  5     2 F. austroamericanum evaluation  72.0
##  6     2      F. cortaderiae evaluation  72.0
##  7     2      F. graminearum evaluation  61.5
##  8     2      F. meridionale evaluation  39.0
##  9     3 F. austroamericanum evaluation  46.5
## 10     3      F. cortaderiae evaluation  17.5
## # ... with 12 more rows
```


### Visualização


```r
audpc1 %>% 
  ggplot(aes(species, audpc))+
  geom_boxplot(outlier.colour = NA)+
  geom_jitter(width = 0.05, size=3, shape = 1)+
  theme_light()
```

![](audpc_files/figure-html/unnamed-chunk-3-1.png)<!-- -->



```


