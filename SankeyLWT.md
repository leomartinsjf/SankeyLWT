# Sankey Plots - Flow Network 
Leonardo Martins  
22 de janeiro de 2016  

Here you can find a first a draft of Sankey Graphs for Website Flow Pages - This report was based on LWT data sent from Felipe in 2015. 

####Results: [http://rpubs.com/leomartinsjf/SankeyLWT]

####Git: [https://github.com/leomartinsjf/SankeyLWT]

####################################
#Sankey Plots from Adjacency Matrix#
####################################


```r
#If you don't have these packages installed use:
#install.packages("igraph")
#install.packages("networkD3")
#install.packages("curl")

require(igraph) #load package for social network analysis
require(networkD3) #load package for sankey network
require(curl) #load package to download csv files


#In order to change the Sankey plot configuration - please, acesses  "~/Library/Frameworks/R.framework/Versions/"your version 3.xxx"/library/networwD3/htmlwidgets/lib/sankey.js

# This current version was made using the following sankey.js "https://dl.dropboxusercontent.com/s/5386z4jwrymlubq/sankey.js"
```


#####################
#Data manipulation  #
#####################

```r
#Raw Data sent from Felipe April 2015
#https://dl.dropboxusercontent.com/s/2y9dl246ctv9y59/result-2.txt
#I had to manipulated this data on Excel in order to create a csv file with labels and another one with the squared matrix

data <- read.csv(curl("https://dl.dropboxusercontent.com/s/dz571ak1whb8lqd/grafo.csv"), sep = ";")
#Importing  bi-directional squared matrix with labels into a "data" object

matrix=as.matrix(data)
#Transforming dataframe into a "matrix" adjacency with

colnames(matrix)<-c(0:40)
#Coding matrix coluns as numeric labels 

label <- read.csv(curl("https://dl.dropboxusercontent.com/s/erdu802n7bsqk5e/label.csv"), sep=";")
#Importing original labels - page adress

Nodes <- as.data.frame(label$Label)
#Generating nodes object with orderd labels

colnames(Nodes)<- as.factor(colnames(Nodes))
#Setting column names 
```


################################################################
#Preparing First Sankey Draft - With Loops and Recursive Paths #
################################################################

```r
network1=graph_from_adjacency_matrix(matrix,mode="directed",weighted=T,diag=T)
#Generating a graph network structure - overall flow - directed with weights and diag different from zero

Links1 <- get.data.frame(network1)
#Getting a dataframe from igraph object (from, to, weight) - overall flow

Links1$logweight <- log(Links1$weight)
#Creating a transformed variable using log weight

Links1$from<-as.numeric(Links1$from)
#Converting to numeric

Links1$to<-as.numeric(Links1$to)
#Converting to numeric

#Generating first sankey plot object
#sankeyNetwork(Links= Links1, Nodes = Nodes, Source = 'from', Target = 'to', Value = 'logweight', NodeID ="label$Label", fontSize=12, nodeWidth=30)

#This is not included as we need to check the Java Scrisct template for Sankey Plot with Recursive Patterns
```

##############################################################################
#Preparing Second Sankey - No Loops and non Recursive - Forced Directed Paths #
##############################################################################

```r
network2=graph_from_adjacency_matrix(matrix,mode="undirected",weighted=T,diag=F)
#Generating a igraph network structure - directed flow without loops - undirected with weight and diag equal to zero - only the upper matrix triangle it is used 

#IMPORTANT: Hopefully this is the right one to show forward flow  - if it is not the case use the following network object
#network2=graph_from_adjacency_matrix(matrix,mode="lower",weighted=TRUE,diag=F)

Links2 <- get.data.frame(network2)
#Getting a dataframe from igraph object (from, to, weight)  - directed flow without loops

Links2$logweight <- log(Links2$weight)
#Creating a transformed variable using log weight

Links2$from<-as.numeric(Links2$from)
#Converting to numeric

Links2$to<-as.numeric(Links2$to)
#Converting to numeric

#Generating sankey plot object
sankeyNetwork(Links= Links2, Nodes = Nodes, Source = 'from', Target = 'to', Value = 'logweight', NodeID ="label$Label", fontSize=12, nodeWidth=5)
```

<!--html_preserve--><div id="htmlwidget-5313" style="width:864px;height:960px;" class="sankeyNetwork"></div>
<script type="application/json" data-for="htmlwidget-5313">{"x":{"links":{"source":[0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,1,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,3,3,3,3,3,3,3,3,3,3,3,3,3,3,3,3,3,3,4,4,4,4,5,5,5,5,5,5,5,6,6,6,6,7,7,8,8,8,8,8,8,8,8,8,8,8,8,8,8,8,8,8,8,8,8,9,10,10,10,11,11,12,12,13,13,14,15,15,15,15,15,15,15,15,15,15,15,15,15,15,15,15,15,16,16,16,16,16,16,16,16,16,16,16,16,16,16,16,16,16,16,17,17,17,17,17,17,17,17,17,17,17,17,18,18,18,18,18,18,18,18,18,18,18,18,18,19,19,19,19,19,19,19,19,19,19,19,19,19,20,20,20,20,20,20,20,20,20,20,20,20,20,20,21,21,21,21,21,21,21,21,21,21,21,22,22,22,22,22,22,22,22,22,22,22,22,22,22,23,23,23,23,23,23,23,23,23,23,23,24,24,24,24,24,24,24,24,24,25,25,25,25,25,25,25,26,26,26,26,26,26,26,26,27,27,27,27,27,27,27,27,29,29,29,29,29,29,29,29,31,32,32,32,32,32,32,33,33,33,33,33,33,34,34,34,34,35,35,36,36,36,37,37,38],"target":[2,3,5,7,8,10,13,15,16,17,18,19,20,21,22,23,24,25,26,27,29,32,33,34,35,36,37,38,40,3,8,15,19,20,22,24,25,26,27,29,32,33,34,36,37,38,40,8,15,18,19,20,21,22,23,26,27,29,32,33,34,36,37,38,40,6,7,14,15,7,8,9,13,22,34,40,7,10,14,40,34,40,14,15,17,19,20,21,22,23,24,26,27,29,32,33,34,35,36,37,38,40,22,11,34,40,12,13,13,40,34,40,40,16,18,20,21,22,23,24,26,27,29,32,33,34,36,37,38,40,17,18,19,20,21,22,23,24,25,26,27,32,33,34,36,37,38,40,19,20,21,23,24,25,26,27,33,34,38,40,20,21,22,23,24,25,26,29,32,33,34,36,40,20,21,22,23,24,25,26,27,33,34,37,38,40,21,22,23,24,25,26,29,32,33,34,36,37,38,40,22,23,24,25,26,27,29,33,34,37,40,23,24,25,26,27,29,32,33,34,35,36,37,38,40,24,25,26,27,29,33,34,36,37,38,40,25,26,27,29,32,33,34,37,40,26,27,32,33,34,38,40,27,29,32,33,34,37,38,40,29,32,33,34,36,37,38,40,32,33,34,35,36,37,38,40,40,33,34,36,37,38,40,34,35,36,37,38,40,36,37,38,40,38,40,37,38,40,38,40,40],"value":[3.80666248977032,3.80666248977032,1.38629436111989,0,2.484906649788,0.693147180559945,0,3.43398720448515,2.63905732961526,1.79175946922805,1.38629436111989,1.79175946922805,2.484906649788,1.38629436111989,3.43398720448515,2.70805020110221,2.63905732961526,2.484906649788,1.94591014905531,2.56494935746154,3.13549421592915,3.43398720448515,3.91202300542815,7.25559127425367,0,1.79175946922805,3.87120101090789,3.58351893845611,4.48863636973214,2.94443897916644,2.30258509299405,2.30258509299405,0,0,2.39789527279837,0,2.19722457733622,0,5.28826703069454,0,3.36729582998647,2.89037175789616,3.98898404656427,2.19722457733622,3.29583686600433,3.89182029811063,4.06044301054642,2.56494935746154,5.05624580534831,0.693147180559945,0,1.09861228866811,0,2.94443897916644,0.693147180559945,0.693147180559945,2.39789527279837,2.07944154167984,4.20469261939097,2.19722457733622,5.18738580584075,2.89037175789616,5.24702407216049,2.83321334405622,3.93182563272433,1.6094379124341,0,2.07944154167984,0,0,0,0,0.693147180559945,0,0.693147180559945,1.6094379124341,0.693147180559945,1.94591014905531,0,0,0,0,2.19722457733622,1.94591014905531,1.09861228866811,0.693147180559945,0,0,4.20469261939097,0.693147180559945,0,0.693147180559945,1.38629436111989,3.78418963391826,1.79175946922805,4.85203026391962,3.63758615972639,1.6094379124341,0.693147180559945,2.19722457733622,2.30258509299405,2.94443897916644,0,1.94591014905531,0,1.09861228866811,1.79175946922805,0,1.6094379124341,0,0,1.38629436111989,0.693147180559945,0.693147180559945,0,1.94591014905531,1.38629436111989,2.99573227355399,1.38629436111989,0,0,2.30258509299405,2.56494935746154,3.93182563272433,2.19722457733622,5.35185813347607,3.61091791264422,3.97029191355212,3.04452243772342,3.55534806148941,0.693147180559945,0,0.693147180559945,2.99573227355399,1.38629436111989,0.693147180559945,1.09861228866811,1.6094379124341,3.04452243772342,1.09861228866811,0,1.6094379124341,0.693147180559945,2.56494935746154,0,0,0,2.77258872223978,3.2188758248682,0,0,0.693147180559945,3.04452243772342,0,2.07944154167984,0,0.693147180559945,2.30258509299405,1.09861228866811,1.6094379124341,1.38629436111989,0,1.09861228866811,2.484906649788,2.19722457733622,0,1.38629436111989,0,0,0.693147180559945,1.79175946922805,0.693147180559945,1.79175946922805,0.693147180559945,0,3.09104245335832,0,0.693147180559945,0,1.09861228866811,0,1.09861228866811,2.07944154167984,1.09861228866811,0,2.30258509299405,1.6094379124341,0.693147180559945,1.09861228866811,0.693147180559945,1.79175946922805,0.693147180559945,1.38629436111989,0.693147180559945,0.693147180559945,2.83321334405622,0,0.693147180559945,1.94591014905531,2.77258872223978,1.38629436111989,1.6094379124341,0.693147180559945,3.09104245335832,3.17805383034795,0,0.693147180559945,0,2.39789527279837,0,2.30258509299405,2.39789527279837,2.77258872223978,0.693147180559945,0.693147180559945,2.484906649788,3.61091791264422,3.40119738166216,4.7361984483945,4.54329478227,0.693147180559945,0,2.484906649788,2.56494935746154,3.52636052461616,3.25809653802148,0.693147180559945,3.13549421592915,0,0,1.38629436111989,3.04452243772342,0,0,0.693147180559945,3.17805383034795,1.09861228866811,2.07944154167984,1.38629436111989,0,0.693147180559945,1.09861228866811,3.09104245335832,0.693147180559945,2.484906649788,2.07944154167984,0,0,0,3.13549421592915,1.09861228866811,2.19722457733622,0.693147180559945,0,2.07944154167984,0.693147180559945,2.19722457733622,0,0.693147180559945,3.66356164612965,1.94591014905531,4.76217393479776,1.38629436111989,3.25809653802148,0.693147180559945,3.43398720448515,2.484906649788,2.77258872223978,2.30258509299405,4.90527477843843,3.09104245335832,0,0,1.6094379124341,0.693147180559945,2.99573227355399,3.68887945411394,2.56494935746154,4.23410650459726,1.38629436111989,5.24702407216049,3.17805383034795,3.63758615972639,4.12713438504509,0,1.09861228866811,3.04452243772342,2.484906649788,4.15888308335967,4.91998092582813,4.39444915467244,4.70048036579242,7.21007962817079,1.94591014905531,3.58351893845611,1.94591014905531,5.03695260241363,2.56494935746154,2.94443897916644,4.90527477843843,3.40119738166216]},"nodes":{"name":["0 - entrada","1 - user_list.xhtml","2 - termos-de-uso.xhtml","3 - quem-somos-os-autores.xhtml","4 - pronto-para-parar-de-fumar-vencendo-a-fissura.xhtml","5 - pronto-para-parar-de-fumar-plano-personalizado.xhtml","6 - pronto-para-parar-de-fumar-metodos-de-parar.xhtml","7 - pronto-para-parar-de-fumar-medicamentos.xhtml","8 - pronto-para-parar-de-fumar-introducao.xhtml","9 - pronto-para-parar-de-fumar-ganho-de-peso.xhtml","10 - pronto-para-parar-de-fumar-data-para-parar.xhtml","11 - pronto-para-parar-de-fumar-como-evitar-recaidas.xhtml","12 - pronto-para-parar-de-fumar-como-evitar-recaidas-sim.xhtml","13 - pronto-para-parar-de-fumar-como-evitar-recaidas-estrategias.xhtml","14 - pronto-para-parar-de-fumar-abstinencia.xhtml","15 - programa.xhtml","16 - preparando-parar-de-fumar-substituir-prazer-cigarro.xhtml","17 - preparando-parar-de-fumar-riscos-fumo-passivo.xhtml","18 - preparando-parar-de-fumar-riscos-do-cigarro.xhtml","19 - preparando-parar-de-fumar-riscos-de-fumar.xhtml","20 - preparando-parar-de-fumar-onde-encontrar-ajuda.xhtml","21 - preparando-parar-de-fumar-medo-nao-conseguir-ganho-de-peso.xhtml","22 - preparando-parar-de-fumar-introducao.xhtml","23 - preparando-parar-de-fumar-calculadora-gastos.xhtml","24 - preparando-parar-de-fumar-beneficios-de-parar.xhtml","25 - preparando-parar-de-fumar-ansiedade-e-depressao.xhtml","26 - preparando-parar-de-fumar-abstinencia-fissura.xhtml","27 - politica-do-site.xhtml","28 - parou-de-fumar-onde-procurar-ajuda.xhtml","29 - parou-de-fumar-acompanhamento.xhtml","30 - parou-de-fumar-acompanhamento-recaidas-identificar-motivos.xhtml","31 - parou-de-fumar-acompanhamento-lapso-identificar-fatores-recaida.xhtml","32 - para-pesquisadores.xhtml","33 - login.xhtml","34 - index.xhtml","35 - escolha-uma-etapa.xhtml","36 - escolha-uma-etapa.xhtml","37 - contato.xhtml","38 - cadastrar-nova-conta.xhtml","39 - outras.xhtml","40 - saida"]},"options":{"NodeID":"label$Label","colourScale":"d3.scale.category20()","fontSize":12,"fontFamily":"serif","nodeWidth":5,"nodePadding":10}},"evals":[]}</script><!--/html_preserve-->

#############################################################################
#Time to be "lazy" - Creating functions to replicated the sankey plots above# 
#############################################################################

#1 - Function to generate a sankey plot including foward and backward flow links

```r
sankeyFT <- function(x, y) {
a <- y[(y$from %in% c(x)|y$to %in% c(x)),]
a1<-cat("#Links Forward (From) and Backward (To),\n")
sankeyNetwork(Links= a, Nodes = Nodes, Source = 'from', Target = 'to', Value = 'logweight', NodeID ="label$Label", fontSize=12, nodeWidth=30)
}
```

#2 - Function to generate a sankey plot including only foward links (From)

```r
sankeyF <- function(x, y) {
b <- y[y$from %in% c(x),]
b1<-cat("#Links From - no log transformation due to low level of observations\n")
sankeyNetwork(Links= b, Nodes = Nodes, Source = 'from', Target = 'to', Value = 'logweight', NodeID ="label$Label", fontSize=12, nodeWidth=30)
}
```

#3 - Function to generate a sankey plot including only backward links (To)

```r
sankeyT <- function(x, y) {
c <- y[y$to %in% c(x),]
c1<-cat("#Links To  - no log transformation due to low level of observations\n")
sankeyNetwork(Links= c, Nodes = Nodes, Source = 'from', Target = 'to', Value = 'logweight', NodeID ="label$Label", fontSize=12, nodeWidth=30)
}
```

##############################################################
#Organizing Plots by Interaction with Specific Section Pages #
##############################################################

########################################
##Zero "Frontpage" (1:3;15;27;32;39)#
########################################

Selecting links made by at least one node from Zero Step (Frontpages) 

########################################
###1 Model With Loops and Recursive Paths#
########################################

```r
#sankeyFT(c(1:3,15,27,32:39),Links1)
#sankeyF(c(1:3,15,27,32:39), Links1)
#sankeyT(c(1:3,15,27,32:39), Links1)

#These plots are not included as we need to check the Java Scrisct template for Sankey Plot with Recursive Patterns
```

###########################################
###2 Model Without Loops or Recursive Paths#
###########################################


```r
sankeyFT(c(1:3,15,27,32:39),Links2)
```

```
## #Links Forward (From) and Backward (To),
```

<!--html_preserve--><div id="htmlwidget-2136" style="width:864px;height:960px;" class="sankeyNetwork"></div>
<script type="application/json" data-for="htmlwidget-2136">{"x":{"links":{"source":[0,0,0,0,0,0,0,0,0,0,0,1,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,3,3,3,3,3,3,3,3,3,3,3,3,3,3,3,3,3,3,4,5,7,8,8,8,8,8,8,8,8,8,10,13,15,15,15,15,15,15,15,15,15,15,15,15,15,15,15,15,15,16,16,16,16,16,16,16,17,17,17,17,18,18,18,18,19,19,19,19,19,20,20,20,20,20,20,21,21,21,21,22,22,22,22,22,22,22,22,23,23,23,23,23,23,24,24,24,24,24,25,25,25,25,25,26,26,26,26,26,26,27,27,27,27,27,27,27,27,29,29,29,29,29,29,29,32,32,32,32,32,32,33,33,33,33,33,33,34,34,34,34,35,35,36,36,36,37,37,38],"target":[2,3,15,27,32,33,34,35,36,37,38,40,3,8,15,19,20,22,24,25,26,27,29,32,33,34,36,37,38,40,8,15,18,19,20,21,22,23,26,27,29,32,33,34,36,37,38,40,15,34,34,15,27,32,33,34,35,36,37,38,34,34,16,18,20,21,22,23,24,26,27,29,32,33,34,36,37,38,40,27,32,33,34,36,37,38,27,33,34,38,32,33,34,36,27,33,34,37,38,32,33,34,36,37,38,27,33,34,37,27,32,33,34,35,36,37,38,27,33,34,36,37,38,27,32,33,34,37,27,32,33,34,38,27,32,33,34,37,38,29,32,33,34,36,37,38,40,32,33,34,35,36,37,38,33,34,36,37,38,40,34,35,36,37,38,40,36,37,38,40,38,40,37,38,40,38,40,40],"value":[3.80666248977032,3.80666248977032,3.43398720448515,2.56494935746154,3.43398720448515,3.91202300542815,7.25559127425367,0,1.79175946922805,3.87120101090789,3.58351893845611,4.48863636973214,2.94443897916644,2.30258509299405,2.30258509299405,0,0,2.39789527279837,0,2.19722457733622,0,5.28826703069454,0,3.36729582998647,2.89037175789616,3.98898404656427,2.19722457733622,3.29583686600433,3.89182029811063,4.06044301054642,2.56494935746154,5.05624580534831,0.693147180559945,0,1.09861228866811,0,2.94443897916644,0.693147180559945,0.693147180559945,2.39789527279837,2.07944154167984,4.20469261939097,2.19722457733622,5.18738580584075,2.89037175789616,5.24702407216049,2.83321334405622,3.93182563272433,0,0.693147180559945,0,1.94591014905531,1.38629436111989,1.79175946922805,4.85203026391962,3.63758615972639,1.6094379124341,0.693147180559945,2.19722457733622,2.30258509299405,0,0,0.693147180559945,0,1.94591014905531,1.38629436111989,2.99573227355399,1.38629436111989,0,0,2.30258509299405,2.56494935746154,3.93182563272433,2.19722457733622,5.35185813347607,3.61091791264422,3.97029191355212,3.04452243772342,3.55534806148941,0,1.6094379124341,0.693147180559945,2.56494935746154,0,0,0,0,0.693147180559945,2.30258509299405,1.09861228866811,0,0.693147180559945,1.79175946922805,0.693147180559945,0,1.09861228866811,2.07944154167984,1.09861228866811,0,0.693147180559945,0.693147180559945,2.83321334405622,0,0.693147180559945,1.94591014905531,0,0,2.39789527279837,0,2.484906649788,3.40119738166216,4.7361984483945,4.54329478227,0.693147180559945,0,2.484906649788,2.56494935746154,0,1.38629436111989,3.04452243772342,0,0,0.693147180559945,1.38629436111989,0.693147180559945,1.09861228866811,3.09104245335832,0.693147180559945,0,0,0,3.13549421592915,1.09861228866811,0.693147180559945,2.07944154167984,0.693147180559945,2.19722457733622,0,0.693147180559945,1.94591014905531,4.76217393479776,1.38629436111989,3.25809653802148,0.693147180559945,3.43398720448515,2.484906649788,2.77258872223978,2.30258509299405,4.90527477843843,3.09104245335832,0,0,1.6094379124341,0.693147180559945,2.56494935746154,4.23410650459726,1.38629436111989,5.24702407216049,3.17805383034795,3.63758615972639,4.12713438504509,0,1.09861228866811,3.04452243772342,2.484906649788,4.15888308335967,4.91998092582813,4.39444915467244,4.70048036579242,7.21007962817079,1.94591014905531,3.58351893845611,1.94591014905531,5.03695260241363,2.56494935746154,2.94443897916644,4.90527477843843,3.40119738166216]},"nodes":{"name":["0 - entrada","1 - user_list.xhtml","2 - termos-de-uso.xhtml","3 - quem-somos-os-autores.xhtml","4 - pronto-para-parar-de-fumar-vencendo-a-fissura.xhtml","5 - pronto-para-parar-de-fumar-plano-personalizado.xhtml","6 - pronto-para-parar-de-fumar-metodos-de-parar.xhtml","7 - pronto-para-parar-de-fumar-medicamentos.xhtml","8 - pronto-para-parar-de-fumar-introducao.xhtml","9 - pronto-para-parar-de-fumar-ganho-de-peso.xhtml","10 - pronto-para-parar-de-fumar-data-para-parar.xhtml","11 - pronto-para-parar-de-fumar-como-evitar-recaidas.xhtml","12 - pronto-para-parar-de-fumar-como-evitar-recaidas-sim.xhtml","13 - pronto-para-parar-de-fumar-como-evitar-recaidas-estrategias.xhtml","14 - pronto-para-parar-de-fumar-abstinencia.xhtml","15 - programa.xhtml","16 - preparando-parar-de-fumar-substituir-prazer-cigarro.xhtml","17 - preparando-parar-de-fumar-riscos-fumo-passivo.xhtml","18 - preparando-parar-de-fumar-riscos-do-cigarro.xhtml","19 - preparando-parar-de-fumar-riscos-de-fumar.xhtml","20 - preparando-parar-de-fumar-onde-encontrar-ajuda.xhtml","21 - preparando-parar-de-fumar-medo-nao-conseguir-ganho-de-peso.xhtml","22 - preparando-parar-de-fumar-introducao.xhtml","23 - preparando-parar-de-fumar-calculadora-gastos.xhtml","24 - preparando-parar-de-fumar-beneficios-de-parar.xhtml","25 - preparando-parar-de-fumar-ansiedade-e-depressao.xhtml","26 - preparando-parar-de-fumar-abstinencia-fissura.xhtml","27 - politica-do-site.xhtml","28 - parou-de-fumar-onde-procurar-ajuda.xhtml","29 - parou-de-fumar-acompanhamento.xhtml","30 - parou-de-fumar-acompanhamento-recaidas-identificar-motivos.xhtml","31 - parou-de-fumar-acompanhamento-lapso-identificar-fatores-recaida.xhtml","32 - para-pesquisadores.xhtml","33 - login.xhtml","34 - index.xhtml","35 - escolha-uma-etapa.xhtml","36 - escolha-uma-etapa.xhtml","37 - contato.xhtml","38 - cadastrar-nova-conta.xhtml","39 - outras.xhtml","40 - saida"]},"options":{"NodeID":"label$Label","colourScale":"d3.scale.category20()","fontSize":12,"fontFamily":"serif","nodeWidth":30,"nodePadding":10}},"evals":[]}</script><!--/html_preserve-->

```r
sankeyF(c(1:3,15,27,32:39), Links2)
```

```
## #Links From - no log transformation due to low level of observations
```

<!--html_preserve--><div id="htmlwidget-7967" style="width:864px;height:960px;" class="sankeyNetwork"></div>
<script type="application/json" data-for="htmlwidget-7967">{"x":{"links":{"source":[1,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,3,3,3,3,3,3,3,3,3,3,3,3,3,3,3,3,3,3,15,15,15,15,15,15,15,15,15,15,15,15,15,15,15,15,15,27,27,27,27,27,27,27,27,32,32,32,32,32,32,33,33,33,33,33,33,34,34,34,34,35,35,36,36,36,37,37,38],"target":[40,3,8,15,19,20,22,24,25,26,27,29,32,33,34,36,37,38,40,8,15,18,19,20,21,22,23,26,27,29,32,33,34,36,37,38,40,16,18,20,21,22,23,24,26,27,29,32,33,34,36,37,38,40,29,32,33,34,36,37,38,40,33,34,36,37,38,40,34,35,36,37,38,40,36,37,38,40,38,40,37,38,40,38,40,40],"value":[4.48863636973214,2.94443897916644,2.30258509299405,2.30258509299405,0,0,2.39789527279837,0,2.19722457733622,0,5.28826703069454,0,3.36729582998647,2.89037175789616,3.98898404656427,2.19722457733622,3.29583686600433,3.89182029811063,4.06044301054642,2.56494935746154,5.05624580534831,0.693147180559945,0,1.09861228866811,0,2.94443897916644,0.693147180559945,0.693147180559945,2.39789527279837,2.07944154167984,4.20469261939097,2.19722457733622,5.18738580584075,2.89037175789616,5.24702407216049,2.83321334405622,3.93182563272433,0.693147180559945,0,1.94591014905531,1.38629436111989,2.99573227355399,1.38629436111989,0,0,2.30258509299405,2.56494935746154,3.93182563272433,2.19722457733622,5.35185813347607,3.61091791264422,3.97029191355212,3.04452243772342,3.55534806148941,1.94591014905531,4.76217393479776,1.38629436111989,3.25809653802148,0.693147180559945,3.43398720448515,2.484906649788,2.77258872223978,2.56494935746154,4.23410650459726,1.38629436111989,5.24702407216049,3.17805383034795,3.63758615972639,4.12713438504509,0,1.09861228866811,3.04452243772342,2.484906649788,4.15888308335967,4.91998092582813,4.39444915467244,4.70048036579242,7.21007962817079,1.94591014905531,3.58351893845611,1.94591014905531,5.03695260241363,2.56494935746154,2.94443897916644,4.90527477843843,3.40119738166216]},"nodes":{"name":["0 - entrada","1 - user_list.xhtml","2 - termos-de-uso.xhtml","3 - quem-somos-os-autores.xhtml","4 - pronto-para-parar-de-fumar-vencendo-a-fissura.xhtml","5 - pronto-para-parar-de-fumar-plano-personalizado.xhtml","6 - pronto-para-parar-de-fumar-metodos-de-parar.xhtml","7 - pronto-para-parar-de-fumar-medicamentos.xhtml","8 - pronto-para-parar-de-fumar-introducao.xhtml","9 - pronto-para-parar-de-fumar-ganho-de-peso.xhtml","10 - pronto-para-parar-de-fumar-data-para-parar.xhtml","11 - pronto-para-parar-de-fumar-como-evitar-recaidas.xhtml","12 - pronto-para-parar-de-fumar-como-evitar-recaidas-sim.xhtml","13 - pronto-para-parar-de-fumar-como-evitar-recaidas-estrategias.xhtml","14 - pronto-para-parar-de-fumar-abstinencia.xhtml","15 - programa.xhtml","16 - preparando-parar-de-fumar-substituir-prazer-cigarro.xhtml","17 - preparando-parar-de-fumar-riscos-fumo-passivo.xhtml","18 - preparando-parar-de-fumar-riscos-do-cigarro.xhtml","19 - preparando-parar-de-fumar-riscos-de-fumar.xhtml","20 - preparando-parar-de-fumar-onde-encontrar-ajuda.xhtml","21 - preparando-parar-de-fumar-medo-nao-conseguir-ganho-de-peso.xhtml","22 - preparando-parar-de-fumar-introducao.xhtml","23 - preparando-parar-de-fumar-calculadora-gastos.xhtml","24 - preparando-parar-de-fumar-beneficios-de-parar.xhtml","25 - preparando-parar-de-fumar-ansiedade-e-depressao.xhtml","26 - preparando-parar-de-fumar-abstinencia-fissura.xhtml","27 - politica-do-site.xhtml","28 - parou-de-fumar-onde-procurar-ajuda.xhtml","29 - parou-de-fumar-acompanhamento.xhtml","30 - parou-de-fumar-acompanhamento-recaidas-identificar-motivos.xhtml","31 - parou-de-fumar-acompanhamento-lapso-identificar-fatores-recaida.xhtml","32 - para-pesquisadores.xhtml","33 - login.xhtml","34 - index.xhtml","35 - escolha-uma-etapa.xhtml","36 - escolha-uma-etapa.xhtml","37 - contato.xhtml","38 - cadastrar-nova-conta.xhtml","39 - outras.xhtml","40 - saida"]},"options":{"NodeID":"label$Label","colourScale":"d3.scale.category20()","fontSize":12,"fontFamily":"serif","nodeWidth":30,"nodePadding":10}},"evals":[]}</script><!--/html_preserve-->

```r
sankeyT(c(1:3,15,27,32:39), Links2)
```

```
## #Links To  - no log transformation due to low level of observations
```

<!--html_preserve--><div id="htmlwidget-5048" style="width:864px;height:960px;" class="sankeyNetwork"></div>
<script type="application/json" data-for="htmlwidget-5048">{"x":{"links":{"source":[0,0,0,0,0,0,0,0,0,0,0,2,2,2,2,2,2,2,2,2,3,3,3,3,3,3,3,3,4,5,7,8,8,8,8,8,8,8,8,8,10,13,15,15,15,15,15,15,15,16,16,16,16,16,16,16,17,17,17,17,18,18,18,18,19,19,19,19,19,20,20,20,20,20,20,21,21,21,21,22,22,22,22,22,22,22,22,23,23,23,23,23,23,24,24,24,24,24,25,25,25,25,25,26,26,26,26,26,26,27,27,27,27,27,27,29,29,29,29,29,29,29,32,32,32,32,32,33,33,33,33,33,34,34,34,35,36,36,37],"target":[2,3,15,27,32,33,34,35,36,37,38,3,15,27,32,33,34,36,37,38,15,27,32,33,34,36,37,38,15,34,34,15,27,32,33,34,35,36,37,38,34,34,27,32,33,34,36,37,38,27,32,33,34,36,37,38,27,33,34,38,32,33,34,36,27,33,34,37,38,32,33,34,36,37,38,27,33,34,37,27,32,33,34,35,36,37,38,27,33,34,36,37,38,27,32,33,34,37,27,32,33,34,38,27,32,33,34,37,38,32,33,34,36,37,38,32,33,34,35,36,37,38,33,34,36,37,38,34,35,36,37,38,36,37,38,38,37,38,38],"value":[3.80666248977032,3.80666248977032,3.43398720448515,2.56494935746154,3.43398720448515,3.91202300542815,7.25559127425367,0,1.79175946922805,3.87120101090789,3.58351893845611,2.94443897916644,2.30258509299405,5.28826703069454,3.36729582998647,2.89037175789616,3.98898404656427,2.19722457733622,3.29583686600433,3.89182029811063,5.05624580534831,2.39789527279837,4.20469261939097,2.19722457733622,5.18738580584075,2.89037175789616,5.24702407216049,2.83321334405622,0,0.693147180559945,0,1.94591014905531,1.38629436111989,1.79175946922805,4.85203026391962,3.63758615972639,1.6094379124341,0.693147180559945,2.19722457733622,2.30258509299405,0,0,2.30258509299405,3.93182563272433,2.19722457733622,5.35185813347607,3.61091791264422,3.97029191355212,3.04452243772342,0,1.6094379124341,0.693147180559945,2.56494935746154,0,0,0,0,0.693147180559945,2.30258509299405,1.09861228866811,0,0.693147180559945,1.79175946922805,0.693147180559945,0,1.09861228866811,2.07944154167984,1.09861228866811,0,0.693147180559945,0.693147180559945,2.83321334405622,0,0.693147180559945,1.94591014905531,0,0,2.39789527279837,0,2.484906649788,3.40119738166216,4.7361984483945,4.54329478227,0.693147180559945,0,2.484906649788,2.56494935746154,0,1.38629436111989,3.04452243772342,0,0,0.693147180559945,1.38629436111989,0.693147180559945,1.09861228866811,3.09104245335832,0.693147180559945,0,0,0,3.13549421592915,1.09861228866811,0.693147180559945,2.07944154167984,0.693147180559945,2.19722457733622,0,0.693147180559945,4.76217393479776,1.38629436111989,3.25809653802148,0.693147180559945,3.43398720448515,2.484906649788,2.30258509299405,4.90527477843843,3.09104245335832,0,0,1.6094379124341,0.693147180559945,2.56494935746154,4.23410650459726,1.38629436111989,5.24702407216049,3.17805383034795,4.12713438504509,0,1.09861228866811,3.04452243772342,2.484906649788,4.91998092582813,4.39444915467244,4.70048036579242,1.94591014905531,1.94591014905531,5.03695260241363,2.94443897916644]},"nodes":{"name":["0 - entrada","1 - user_list.xhtml","2 - termos-de-uso.xhtml","3 - quem-somos-os-autores.xhtml","4 - pronto-para-parar-de-fumar-vencendo-a-fissura.xhtml","5 - pronto-para-parar-de-fumar-plano-personalizado.xhtml","6 - pronto-para-parar-de-fumar-metodos-de-parar.xhtml","7 - pronto-para-parar-de-fumar-medicamentos.xhtml","8 - pronto-para-parar-de-fumar-introducao.xhtml","9 - pronto-para-parar-de-fumar-ganho-de-peso.xhtml","10 - pronto-para-parar-de-fumar-data-para-parar.xhtml","11 - pronto-para-parar-de-fumar-como-evitar-recaidas.xhtml","12 - pronto-para-parar-de-fumar-como-evitar-recaidas-sim.xhtml","13 - pronto-para-parar-de-fumar-como-evitar-recaidas-estrategias.xhtml","14 - pronto-para-parar-de-fumar-abstinencia.xhtml","15 - programa.xhtml","16 - preparando-parar-de-fumar-substituir-prazer-cigarro.xhtml","17 - preparando-parar-de-fumar-riscos-fumo-passivo.xhtml","18 - preparando-parar-de-fumar-riscos-do-cigarro.xhtml","19 - preparando-parar-de-fumar-riscos-de-fumar.xhtml","20 - preparando-parar-de-fumar-onde-encontrar-ajuda.xhtml","21 - preparando-parar-de-fumar-medo-nao-conseguir-ganho-de-peso.xhtml","22 - preparando-parar-de-fumar-introducao.xhtml","23 - preparando-parar-de-fumar-calculadora-gastos.xhtml","24 - preparando-parar-de-fumar-beneficios-de-parar.xhtml","25 - preparando-parar-de-fumar-ansiedade-e-depressao.xhtml","26 - preparando-parar-de-fumar-abstinencia-fissura.xhtml","27 - politica-do-site.xhtml","28 - parou-de-fumar-onde-procurar-ajuda.xhtml","29 - parou-de-fumar-acompanhamento.xhtml","30 - parou-de-fumar-acompanhamento-recaidas-identificar-motivos.xhtml","31 - parou-de-fumar-acompanhamento-lapso-identificar-fatores-recaida.xhtml","32 - para-pesquisadores.xhtml","33 - login.xhtml","34 - index.xhtml","35 - escolha-uma-etapa.xhtml","36 - escolha-uma-etapa.xhtml","37 - contato.xhtml","38 - cadastrar-nova-conta.xhtml","39 - outras.xhtml","40 - saida"]},"options":{"NodeID":"label$Label","colourScale":"d3.scale.category20()","fontSize":12,"fontFamily":"serif","nodeWidth":30,"nodePadding":10}},"evals":[]}</script><!--/html_preserve-->

#####################################################
##First Step "Preparando-parar-de-fumar" (V16 to V26)#
#####################################################

Selecting links made by at least one node from First Step 

########################################
###1 Model With Loops and Recursive Paths#
########################################


```r
#sankeyFT(c(16:26),Links1)
#sankeyF(c(16:26), Links1)
#sankeyT(c(16:26), Links1)

#These plots are not included as we need to check the Java Scrisct template for Sankey Plot with Recursive Patterns
```
###########################################
###2 Model Without Loops or Recursive Paths#
###########################################


```r
sankeyFT(c(16:26),Links2)
```

```
## #Links Forward (From) and Backward (To),
```

<!--html_preserve--><div id="htmlwidget-8989" style="width:864px;height:960px;" class="sankeyNetwork"></div>
<script type="application/json" data-for="htmlwidget-8989">{"x":{"links":{"source":[0,0,0,0,0,0,0,0,0,0,0,2,2,2,2,2,2,3,3,3,3,3,3,3,5,8,8,8,8,8,8,8,8,9,15,15,15,15,15,15,15,15,16,16,16,16,16,16,16,16,16,16,16,16,16,16,16,16,16,16,17,17,17,17,17,17,17,17,17,17,17,17,18,18,18,18,18,18,18,18,18,18,18,18,18,19,19,19,19,19,19,19,19,19,19,19,19,19,20,20,20,20,20,20,20,20,20,20,20,20,20,20,21,21,21,21,21,21,21,21,21,21,21,22,22,22,22,22,22,22,22,22,22,22,22,22,22,23,23,23,23,23,23,23,23,23,23,23,24,24,24,24,24,24,24,24,24,25,25,25,25,25,25,25,26,26,26,26,26,26,26,26],"target":[16,17,18,19,20,21,22,23,24,25,26,19,20,22,24,25,26,18,19,20,21,22,23,26,22,17,19,20,21,22,23,24,26,22,16,18,20,21,22,23,24,26,17,18,19,20,21,22,23,24,25,26,27,32,33,34,36,37,38,40,19,20,21,23,24,25,26,27,33,34,38,40,20,21,22,23,24,25,26,29,32,33,34,36,40,20,21,22,23,24,25,26,27,33,34,37,38,40,21,22,23,24,25,26,29,32,33,34,36,37,38,40,22,23,24,25,26,27,29,33,34,37,40,23,24,25,26,27,29,32,33,34,35,36,37,38,40,24,25,26,27,29,33,34,36,37,38,40,25,26,27,29,32,33,34,37,40,26,27,32,33,34,38,40,27,29,32,33,34,37,38,40],"value":[2.63905732961526,1.79175946922805,1.38629436111989,1.79175946922805,2.484906649788,1.38629436111989,3.43398720448515,2.70805020110221,2.63905732961526,2.484906649788,1.94591014905531,0,0,2.39789527279837,0,2.19722457733622,0,0.693147180559945,0,1.09861228866811,0,2.94443897916644,0.693147180559945,0.693147180559945,0,1.09861228866811,0.693147180559945,0,0,4.20469261939097,0.693147180559945,0,0.693147180559945,0,0.693147180559945,0,1.94591014905531,1.38629436111989,2.99573227355399,1.38629436111989,0,0,0.693147180559945,0,0.693147180559945,2.99573227355399,1.38629436111989,0.693147180559945,1.09861228866811,1.6094379124341,3.04452243772342,1.09861228866811,0,1.6094379124341,0.693147180559945,2.56494935746154,0,0,0,2.77258872223978,3.2188758248682,0,0,0.693147180559945,3.04452243772342,0,2.07944154167984,0,0.693147180559945,2.30258509299405,1.09861228866811,1.6094379124341,1.38629436111989,0,1.09861228866811,2.484906649788,2.19722457733622,0,1.38629436111989,0,0,0.693147180559945,1.79175946922805,0.693147180559945,1.79175946922805,0.693147180559945,0,3.09104245335832,0,0.693147180559945,0,1.09861228866811,0,1.09861228866811,2.07944154167984,1.09861228866811,0,2.30258509299405,1.6094379124341,0.693147180559945,1.09861228866811,0.693147180559945,1.79175946922805,0.693147180559945,1.38629436111989,0.693147180559945,0.693147180559945,2.83321334405622,0,0.693147180559945,1.94591014905531,2.77258872223978,1.38629436111989,1.6094379124341,0.693147180559945,3.09104245335832,3.17805383034795,0,0.693147180559945,0,2.39789527279837,0,2.30258509299405,2.39789527279837,2.77258872223978,0.693147180559945,0.693147180559945,2.484906649788,3.61091791264422,3.40119738166216,4.7361984483945,4.54329478227,0.693147180559945,0,2.484906649788,2.56494935746154,3.52636052461616,3.25809653802148,0.693147180559945,3.13549421592915,0,0,1.38629436111989,3.04452243772342,0,0,0.693147180559945,3.17805383034795,1.09861228866811,2.07944154167984,1.38629436111989,0,0.693147180559945,1.09861228866811,3.09104245335832,0.693147180559945,2.484906649788,2.07944154167984,0,0,0,3.13549421592915,1.09861228866811,2.19722457733622,0.693147180559945,0,2.07944154167984,0.693147180559945,2.19722457733622,0,0.693147180559945,3.66356164612965]},"nodes":{"name":["0 - entrada","1 - user_list.xhtml","2 - termos-de-uso.xhtml","3 - quem-somos-os-autores.xhtml","4 - pronto-para-parar-de-fumar-vencendo-a-fissura.xhtml","5 - pronto-para-parar-de-fumar-plano-personalizado.xhtml","6 - pronto-para-parar-de-fumar-metodos-de-parar.xhtml","7 - pronto-para-parar-de-fumar-medicamentos.xhtml","8 - pronto-para-parar-de-fumar-introducao.xhtml","9 - pronto-para-parar-de-fumar-ganho-de-peso.xhtml","10 - pronto-para-parar-de-fumar-data-para-parar.xhtml","11 - pronto-para-parar-de-fumar-como-evitar-recaidas.xhtml","12 - pronto-para-parar-de-fumar-como-evitar-recaidas-sim.xhtml","13 - pronto-para-parar-de-fumar-como-evitar-recaidas-estrategias.xhtml","14 - pronto-para-parar-de-fumar-abstinencia.xhtml","15 - programa.xhtml","16 - preparando-parar-de-fumar-substituir-prazer-cigarro.xhtml","17 - preparando-parar-de-fumar-riscos-fumo-passivo.xhtml","18 - preparando-parar-de-fumar-riscos-do-cigarro.xhtml","19 - preparando-parar-de-fumar-riscos-de-fumar.xhtml","20 - preparando-parar-de-fumar-onde-encontrar-ajuda.xhtml","21 - preparando-parar-de-fumar-medo-nao-conseguir-ganho-de-peso.xhtml","22 - preparando-parar-de-fumar-introducao.xhtml","23 - preparando-parar-de-fumar-calculadora-gastos.xhtml","24 - preparando-parar-de-fumar-beneficios-de-parar.xhtml","25 - preparando-parar-de-fumar-ansiedade-e-depressao.xhtml","26 - preparando-parar-de-fumar-abstinencia-fissura.xhtml","27 - politica-do-site.xhtml","28 - parou-de-fumar-onde-procurar-ajuda.xhtml","29 - parou-de-fumar-acompanhamento.xhtml","30 - parou-de-fumar-acompanhamento-recaidas-identificar-motivos.xhtml","31 - parou-de-fumar-acompanhamento-lapso-identificar-fatores-recaida.xhtml","32 - para-pesquisadores.xhtml","33 - login.xhtml","34 - index.xhtml","35 - escolha-uma-etapa.xhtml","36 - escolha-uma-etapa.xhtml","37 - contato.xhtml","38 - cadastrar-nova-conta.xhtml","39 - outras.xhtml","40 - saida"]},"options":{"NodeID":"label$Label","colourScale":"d3.scale.category20()","fontSize":12,"fontFamily":"serif","nodeWidth":30,"nodePadding":10}},"evals":[]}</script><!--/html_preserve-->

```r
sankeyF(c(16:26), Links2)
```

```
## #Links From - no log transformation due to low level of observations
```

<!--html_preserve--><div id="htmlwidget-3185" style="width:864px;height:960px;" class="sankeyNetwork"></div>
<script type="application/json" data-for="htmlwidget-3185">{"x":{"links":{"source":[16,16,16,16,16,16,16,16,16,16,16,16,16,16,16,16,16,16,17,17,17,17,17,17,17,17,17,17,17,17,18,18,18,18,18,18,18,18,18,18,18,18,18,19,19,19,19,19,19,19,19,19,19,19,19,19,20,20,20,20,20,20,20,20,20,20,20,20,20,20,21,21,21,21,21,21,21,21,21,21,21,22,22,22,22,22,22,22,22,22,22,22,22,22,22,23,23,23,23,23,23,23,23,23,23,23,24,24,24,24,24,24,24,24,24,25,25,25,25,25,25,25,26,26,26,26,26,26,26,26],"target":[17,18,19,20,21,22,23,24,25,26,27,32,33,34,36,37,38,40,19,20,21,23,24,25,26,27,33,34,38,40,20,21,22,23,24,25,26,29,32,33,34,36,40,20,21,22,23,24,25,26,27,33,34,37,38,40,21,22,23,24,25,26,29,32,33,34,36,37,38,40,22,23,24,25,26,27,29,33,34,37,40,23,24,25,26,27,29,32,33,34,35,36,37,38,40,24,25,26,27,29,33,34,36,37,38,40,25,26,27,29,32,33,34,37,40,26,27,32,33,34,38,40,27,29,32,33,34,37,38,40],"value":[0.693147180559945,0,0.693147180559945,2.99573227355399,1.38629436111989,0.693147180559945,1.09861228866811,1.6094379124341,3.04452243772342,1.09861228866811,0,1.6094379124341,0.693147180559945,2.56494935746154,0,0,0,2.77258872223978,3.2188758248682,0,0,0.693147180559945,3.04452243772342,0,2.07944154167984,0,0.693147180559945,2.30258509299405,1.09861228866811,1.6094379124341,1.38629436111989,0,1.09861228866811,2.484906649788,2.19722457733622,0,1.38629436111989,0,0,0.693147180559945,1.79175946922805,0.693147180559945,1.79175946922805,0.693147180559945,0,3.09104245335832,0,0.693147180559945,0,1.09861228866811,0,1.09861228866811,2.07944154167984,1.09861228866811,0,2.30258509299405,1.6094379124341,0.693147180559945,1.09861228866811,0.693147180559945,1.79175946922805,0.693147180559945,1.38629436111989,0.693147180559945,0.693147180559945,2.83321334405622,0,0.693147180559945,1.94591014905531,2.77258872223978,1.38629436111989,1.6094379124341,0.693147180559945,3.09104245335832,3.17805383034795,0,0.693147180559945,0,2.39789527279837,0,2.30258509299405,2.39789527279837,2.77258872223978,0.693147180559945,0.693147180559945,2.484906649788,3.61091791264422,3.40119738166216,4.7361984483945,4.54329478227,0.693147180559945,0,2.484906649788,2.56494935746154,3.52636052461616,3.25809653802148,0.693147180559945,3.13549421592915,0,0,1.38629436111989,3.04452243772342,0,0,0.693147180559945,3.17805383034795,1.09861228866811,2.07944154167984,1.38629436111989,0,0.693147180559945,1.09861228866811,3.09104245335832,0.693147180559945,2.484906649788,2.07944154167984,0,0,0,3.13549421592915,1.09861228866811,2.19722457733622,0.693147180559945,0,2.07944154167984,0.693147180559945,2.19722457733622,0,0.693147180559945,3.66356164612965]},"nodes":{"name":["0 - entrada","1 - user_list.xhtml","2 - termos-de-uso.xhtml","3 - quem-somos-os-autores.xhtml","4 - pronto-para-parar-de-fumar-vencendo-a-fissura.xhtml","5 - pronto-para-parar-de-fumar-plano-personalizado.xhtml","6 - pronto-para-parar-de-fumar-metodos-de-parar.xhtml","7 - pronto-para-parar-de-fumar-medicamentos.xhtml","8 - pronto-para-parar-de-fumar-introducao.xhtml","9 - pronto-para-parar-de-fumar-ganho-de-peso.xhtml","10 - pronto-para-parar-de-fumar-data-para-parar.xhtml","11 - pronto-para-parar-de-fumar-como-evitar-recaidas.xhtml","12 - pronto-para-parar-de-fumar-como-evitar-recaidas-sim.xhtml","13 - pronto-para-parar-de-fumar-como-evitar-recaidas-estrategias.xhtml","14 - pronto-para-parar-de-fumar-abstinencia.xhtml","15 - programa.xhtml","16 - preparando-parar-de-fumar-substituir-prazer-cigarro.xhtml","17 - preparando-parar-de-fumar-riscos-fumo-passivo.xhtml","18 - preparando-parar-de-fumar-riscos-do-cigarro.xhtml","19 - preparando-parar-de-fumar-riscos-de-fumar.xhtml","20 - preparando-parar-de-fumar-onde-encontrar-ajuda.xhtml","21 - preparando-parar-de-fumar-medo-nao-conseguir-ganho-de-peso.xhtml","22 - preparando-parar-de-fumar-introducao.xhtml","23 - preparando-parar-de-fumar-calculadora-gastos.xhtml","24 - preparando-parar-de-fumar-beneficios-de-parar.xhtml","25 - preparando-parar-de-fumar-ansiedade-e-depressao.xhtml","26 - preparando-parar-de-fumar-abstinencia-fissura.xhtml","27 - politica-do-site.xhtml","28 - parou-de-fumar-onde-procurar-ajuda.xhtml","29 - parou-de-fumar-acompanhamento.xhtml","30 - parou-de-fumar-acompanhamento-recaidas-identificar-motivos.xhtml","31 - parou-de-fumar-acompanhamento-lapso-identificar-fatores-recaida.xhtml","32 - para-pesquisadores.xhtml","33 - login.xhtml","34 - index.xhtml","35 - escolha-uma-etapa.xhtml","36 - escolha-uma-etapa.xhtml","37 - contato.xhtml","38 - cadastrar-nova-conta.xhtml","39 - outras.xhtml","40 - saida"]},"options":{"NodeID":"label$Label","colourScale":"d3.scale.category20()","fontSize":12,"fontFamily":"serif","nodeWidth":30,"nodePadding":10}},"evals":[]}</script><!--/html_preserve-->

```r
sankeyT(c(16:26), Links2)
```

```
## #Links To  - no log transformation due to low level of observations
```

<!--html_preserve--><div id="htmlwidget-8167" style="width:864px;height:960px;" class="sankeyNetwork"></div>
<script type="application/json" data-for="htmlwidget-8167">{"x":{"links":{"source":[0,0,0,0,0,0,0,0,0,0,0,2,2,2,2,2,2,3,3,3,3,3,3,3,5,8,8,8,8,8,8,8,8,9,15,15,15,15,15,15,15,15,16,16,16,16,16,16,16,16,16,16,17,17,17,17,17,17,17,18,18,18,18,18,18,18,19,19,19,19,19,19,19,20,20,20,20,20,20,21,21,21,21,21,22,22,22,22,23,23,23,24,24,25],"target":[16,17,18,19,20,21,22,23,24,25,26,19,20,22,24,25,26,18,19,20,21,22,23,26,22,17,19,20,21,22,23,24,26,22,16,18,20,21,22,23,24,26,17,18,19,20,21,22,23,24,25,26,19,20,21,23,24,25,26,20,21,22,23,24,25,26,20,21,22,23,24,25,26,21,22,23,24,25,26,22,23,24,25,26,23,24,25,26,24,25,26,25,26,26],"value":[2.63905732961526,1.79175946922805,1.38629436111989,1.79175946922805,2.484906649788,1.38629436111989,3.43398720448515,2.70805020110221,2.63905732961526,2.484906649788,1.94591014905531,0,0,2.39789527279837,0,2.19722457733622,0,0.693147180559945,0,1.09861228866811,0,2.94443897916644,0.693147180559945,0.693147180559945,0,1.09861228866811,0.693147180559945,0,0,4.20469261939097,0.693147180559945,0,0.693147180559945,0,0.693147180559945,0,1.94591014905531,1.38629436111989,2.99573227355399,1.38629436111989,0,0,0.693147180559945,0,0.693147180559945,2.99573227355399,1.38629436111989,0.693147180559945,1.09861228866811,1.6094379124341,3.04452243772342,1.09861228866811,3.2188758248682,0,0,0.693147180559945,3.04452243772342,0,2.07944154167984,1.38629436111989,0,1.09861228866811,2.484906649788,2.19722457733622,0,1.38629436111989,0.693147180559945,0,3.09104245335832,0,0.693147180559945,0,1.09861228866811,1.6094379124341,0.693147180559945,1.09861228866811,0.693147180559945,1.79175946922805,0.693147180559945,1.38629436111989,1.6094379124341,0.693147180559945,3.09104245335832,3.17805383034795,2.39789527279837,2.77258872223978,0.693147180559945,0.693147180559945,3.25809653802148,0.693147180559945,3.13549421592915,1.09861228866811,2.07944154167984,2.07944154167984]},"nodes":{"name":["0 - entrada","1 - user_list.xhtml","2 - termos-de-uso.xhtml","3 - quem-somos-os-autores.xhtml","4 - pronto-para-parar-de-fumar-vencendo-a-fissura.xhtml","5 - pronto-para-parar-de-fumar-plano-personalizado.xhtml","6 - pronto-para-parar-de-fumar-metodos-de-parar.xhtml","7 - pronto-para-parar-de-fumar-medicamentos.xhtml","8 - pronto-para-parar-de-fumar-introducao.xhtml","9 - pronto-para-parar-de-fumar-ganho-de-peso.xhtml","10 - pronto-para-parar-de-fumar-data-para-parar.xhtml","11 - pronto-para-parar-de-fumar-como-evitar-recaidas.xhtml","12 - pronto-para-parar-de-fumar-como-evitar-recaidas-sim.xhtml","13 - pronto-para-parar-de-fumar-como-evitar-recaidas-estrategias.xhtml","14 - pronto-para-parar-de-fumar-abstinencia.xhtml","15 - programa.xhtml","16 - preparando-parar-de-fumar-substituir-prazer-cigarro.xhtml","17 - preparando-parar-de-fumar-riscos-fumo-passivo.xhtml","18 - preparando-parar-de-fumar-riscos-do-cigarro.xhtml","19 - preparando-parar-de-fumar-riscos-de-fumar.xhtml","20 - preparando-parar-de-fumar-onde-encontrar-ajuda.xhtml","21 - preparando-parar-de-fumar-medo-nao-conseguir-ganho-de-peso.xhtml","22 - preparando-parar-de-fumar-introducao.xhtml","23 - preparando-parar-de-fumar-calculadora-gastos.xhtml","24 - preparando-parar-de-fumar-beneficios-de-parar.xhtml","25 - preparando-parar-de-fumar-ansiedade-e-depressao.xhtml","26 - preparando-parar-de-fumar-abstinencia-fissura.xhtml","27 - politica-do-site.xhtml","28 - parou-de-fumar-onde-procurar-ajuda.xhtml","29 - parou-de-fumar-acompanhamento.xhtml","30 - parou-de-fumar-acompanhamento-recaidas-identificar-motivos.xhtml","31 - parou-de-fumar-acompanhamento-lapso-identificar-fatores-recaida.xhtml","32 - para-pesquisadores.xhtml","33 - login.xhtml","34 - index.xhtml","35 - escolha-uma-etapa.xhtml","36 - escolha-uma-etapa.xhtml","37 - contato.xhtml","38 - cadastrar-nova-conta.xhtml","39 - outras.xhtml","40 - saida"]},"options":{"NodeID":"label$Label","colourScale":"d3.scale.category20()","fontSize":12,"fontFamily":"serif","nodeWidth":30,"nodePadding":10}},"evals":[]}</script><!--/html_preserve-->

#####################################################
##Second Step "Pronto-para-parar-de-fumar" (V4 to 14)#
#####################################################

Selecting links made by at least one node from First Step 

########################################
###1 Model With Loops and Recursive Paths#
########################################

```r
#sankeyFT(c(4:14),Links1)
#sankeyF(c(4:14), Links1)
#sankeyT(c(4:14), Links1)

#These plots are not included as we need to check the Java Scrisct template for Sankey Plot with Recursive Patterns
```

###########################################
###2 Model Without Loops or Recursive Paths#
###########################################

```r
sankeyFT(c(4:14),Links2)
```

```
## #Links Forward (From) and Backward (To),
```

<!--html_preserve--><div id="htmlwidget-9185" style="width:864px;height:960px;" class="sankeyNetwork"></div>
<script type="application/json" data-for="htmlwidget-9185">{"x":{"links":{"source":[0,0,0,0,0,2,3,4,4,4,4,5,5,5,5,5,5,5,6,6,6,6,7,7,8,8,8,8,8,8,8,8,8,8,8,8,8,8,8,8,8,8,8,8,9,10,10,10,11,11,12,12,13,13,14],"target":[5,7,8,10,13,8,8,6,7,14,15,7,8,9,13,22,34,40,7,10,14,40,34,40,14,15,17,19,20,21,22,23,24,26,27,29,32,33,34,35,36,37,38,40,22,11,34,40,12,13,13,40,34,40,40],"value":[1.38629436111989,0,2.484906649788,0.693147180559945,0,2.30258509299405,2.56494935746154,1.6094379124341,0,2.07944154167984,0,0,0,0,0.693147180559945,0,0.693147180559945,1.6094379124341,0.693147180559945,1.94591014905531,0,0,0,0,2.19722457733622,1.94591014905531,1.09861228866811,0.693147180559945,0,0,4.20469261939097,0.693147180559945,0,0.693147180559945,1.38629436111989,3.78418963391826,1.79175946922805,4.85203026391962,3.63758615972639,1.6094379124341,0.693147180559945,2.19722457733622,2.30258509299405,2.94443897916644,0,1.94591014905531,0,1.09861228866811,1.79175946922805,0,1.6094379124341,0,0,1.38629436111989,0.693147180559945]},"nodes":{"name":["0 - entrada","1 - user_list.xhtml","2 - termos-de-uso.xhtml","3 - quem-somos-os-autores.xhtml","4 - pronto-para-parar-de-fumar-vencendo-a-fissura.xhtml","5 - pronto-para-parar-de-fumar-plano-personalizado.xhtml","6 - pronto-para-parar-de-fumar-metodos-de-parar.xhtml","7 - pronto-para-parar-de-fumar-medicamentos.xhtml","8 - pronto-para-parar-de-fumar-introducao.xhtml","9 - pronto-para-parar-de-fumar-ganho-de-peso.xhtml","10 - pronto-para-parar-de-fumar-data-para-parar.xhtml","11 - pronto-para-parar-de-fumar-como-evitar-recaidas.xhtml","12 - pronto-para-parar-de-fumar-como-evitar-recaidas-sim.xhtml","13 - pronto-para-parar-de-fumar-como-evitar-recaidas-estrategias.xhtml","14 - pronto-para-parar-de-fumar-abstinencia.xhtml","15 - programa.xhtml","16 - preparando-parar-de-fumar-substituir-prazer-cigarro.xhtml","17 - preparando-parar-de-fumar-riscos-fumo-passivo.xhtml","18 - preparando-parar-de-fumar-riscos-do-cigarro.xhtml","19 - preparando-parar-de-fumar-riscos-de-fumar.xhtml","20 - preparando-parar-de-fumar-onde-encontrar-ajuda.xhtml","21 - preparando-parar-de-fumar-medo-nao-conseguir-ganho-de-peso.xhtml","22 - preparando-parar-de-fumar-introducao.xhtml","23 - preparando-parar-de-fumar-calculadora-gastos.xhtml","24 - preparando-parar-de-fumar-beneficios-de-parar.xhtml","25 - preparando-parar-de-fumar-ansiedade-e-depressao.xhtml","26 - preparando-parar-de-fumar-abstinencia-fissura.xhtml","27 - politica-do-site.xhtml","28 - parou-de-fumar-onde-procurar-ajuda.xhtml","29 - parou-de-fumar-acompanhamento.xhtml","30 - parou-de-fumar-acompanhamento-recaidas-identificar-motivos.xhtml","31 - parou-de-fumar-acompanhamento-lapso-identificar-fatores-recaida.xhtml","32 - para-pesquisadores.xhtml","33 - login.xhtml","34 - index.xhtml","35 - escolha-uma-etapa.xhtml","36 - escolha-uma-etapa.xhtml","37 - contato.xhtml","38 - cadastrar-nova-conta.xhtml","39 - outras.xhtml","40 - saida"]},"options":{"NodeID":"label$Label","colourScale":"d3.scale.category20()","fontSize":12,"fontFamily":"serif","nodeWidth":30,"nodePadding":10}},"evals":[]}</script><!--/html_preserve-->

```r
sankeyF(c(4:14), Links2)
```

```
## #Links From - no log transformation due to low level of observations
```

<!--html_preserve--><div id="htmlwidget-8403" style="width:864px;height:960px;" class="sankeyNetwork"></div>
<script type="application/json" data-for="htmlwidget-8403">{"x":{"links":{"source":[4,4,4,4,5,5,5,5,5,5,5,6,6,6,6,7,7,8,8,8,8,8,8,8,8,8,8,8,8,8,8,8,8,8,8,8,8,9,10,10,10,11,11,12,12,13,13,14],"target":[6,7,14,15,7,8,9,13,22,34,40,7,10,14,40,34,40,14,15,17,19,20,21,22,23,24,26,27,29,32,33,34,35,36,37,38,40,22,11,34,40,12,13,13,40,34,40,40],"value":[1.6094379124341,0,2.07944154167984,0,0,0,0,0.693147180559945,0,0.693147180559945,1.6094379124341,0.693147180559945,1.94591014905531,0,0,0,0,2.19722457733622,1.94591014905531,1.09861228866811,0.693147180559945,0,0,4.20469261939097,0.693147180559945,0,0.693147180559945,1.38629436111989,3.78418963391826,1.79175946922805,4.85203026391962,3.63758615972639,1.6094379124341,0.693147180559945,2.19722457733622,2.30258509299405,2.94443897916644,0,1.94591014905531,0,1.09861228866811,1.79175946922805,0,1.6094379124341,0,0,1.38629436111989,0.693147180559945]},"nodes":{"name":["0 - entrada","1 - user_list.xhtml","2 - termos-de-uso.xhtml","3 - quem-somos-os-autores.xhtml","4 - pronto-para-parar-de-fumar-vencendo-a-fissura.xhtml","5 - pronto-para-parar-de-fumar-plano-personalizado.xhtml","6 - pronto-para-parar-de-fumar-metodos-de-parar.xhtml","7 - pronto-para-parar-de-fumar-medicamentos.xhtml","8 - pronto-para-parar-de-fumar-introducao.xhtml","9 - pronto-para-parar-de-fumar-ganho-de-peso.xhtml","10 - pronto-para-parar-de-fumar-data-para-parar.xhtml","11 - pronto-para-parar-de-fumar-como-evitar-recaidas.xhtml","12 - pronto-para-parar-de-fumar-como-evitar-recaidas-sim.xhtml","13 - pronto-para-parar-de-fumar-como-evitar-recaidas-estrategias.xhtml","14 - pronto-para-parar-de-fumar-abstinencia.xhtml","15 - programa.xhtml","16 - preparando-parar-de-fumar-substituir-prazer-cigarro.xhtml","17 - preparando-parar-de-fumar-riscos-fumo-passivo.xhtml","18 - preparando-parar-de-fumar-riscos-do-cigarro.xhtml","19 - preparando-parar-de-fumar-riscos-de-fumar.xhtml","20 - preparando-parar-de-fumar-onde-encontrar-ajuda.xhtml","21 - preparando-parar-de-fumar-medo-nao-conseguir-ganho-de-peso.xhtml","22 - preparando-parar-de-fumar-introducao.xhtml","23 - preparando-parar-de-fumar-calculadora-gastos.xhtml","24 - preparando-parar-de-fumar-beneficios-de-parar.xhtml","25 - preparando-parar-de-fumar-ansiedade-e-depressao.xhtml","26 - preparando-parar-de-fumar-abstinencia-fissura.xhtml","27 - politica-do-site.xhtml","28 - parou-de-fumar-onde-procurar-ajuda.xhtml","29 - parou-de-fumar-acompanhamento.xhtml","30 - parou-de-fumar-acompanhamento-recaidas-identificar-motivos.xhtml","31 - parou-de-fumar-acompanhamento-lapso-identificar-fatores-recaida.xhtml","32 - para-pesquisadores.xhtml","33 - login.xhtml","34 - index.xhtml","35 - escolha-uma-etapa.xhtml","36 - escolha-uma-etapa.xhtml","37 - contato.xhtml","38 - cadastrar-nova-conta.xhtml","39 - outras.xhtml","40 - saida"]},"options":{"NodeID":"label$Label","colourScale":"d3.scale.category20()","fontSize":12,"fontFamily":"serif","nodeWidth":30,"nodePadding":10}},"evals":[]}</script><!--/html_preserve-->

```r
sankeyT(c(4:14), Links2)
```

```
## #Links To  - no log transformation due to low level of observations
```

<!--html_preserve--><div id="htmlwidget-38" style="width:864px;height:960px;" class="sankeyNetwork"></div>
<script type="application/json" data-for="htmlwidget-38">{"x":{"links":{"source":[0,0,0,0,0,2,3,4,4,4,5,5,5,5,6,6,6,8,10,11,11,12],"target":[5,7,8,10,13,8,8,6,7,14,7,8,9,13,7,10,14,14,11,12,13,13],"value":[1.38629436111989,0,2.484906649788,0.693147180559945,0,2.30258509299405,2.56494935746154,1.6094379124341,0,2.07944154167984,0,0,0,0.693147180559945,0.693147180559945,1.94591014905531,0,2.19722457733622,1.94591014905531,1.79175946922805,0,1.6094379124341]},"nodes":{"name":["0 - entrada","1 - user_list.xhtml","2 - termos-de-uso.xhtml","3 - quem-somos-os-autores.xhtml","4 - pronto-para-parar-de-fumar-vencendo-a-fissura.xhtml","5 - pronto-para-parar-de-fumar-plano-personalizado.xhtml","6 - pronto-para-parar-de-fumar-metodos-de-parar.xhtml","7 - pronto-para-parar-de-fumar-medicamentos.xhtml","8 - pronto-para-parar-de-fumar-introducao.xhtml","9 - pronto-para-parar-de-fumar-ganho-de-peso.xhtml","10 - pronto-para-parar-de-fumar-data-para-parar.xhtml","11 - pronto-para-parar-de-fumar-como-evitar-recaidas.xhtml","12 - pronto-para-parar-de-fumar-como-evitar-recaidas-sim.xhtml","13 - pronto-para-parar-de-fumar-como-evitar-recaidas-estrategias.xhtml","14 - pronto-para-parar-de-fumar-abstinencia.xhtml","15 - programa.xhtml","16 - preparando-parar-de-fumar-substituir-prazer-cigarro.xhtml","17 - preparando-parar-de-fumar-riscos-fumo-passivo.xhtml","18 - preparando-parar-de-fumar-riscos-do-cigarro.xhtml","19 - preparando-parar-de-fumar-riscos-de-fumar.xhtml","20 - preparando-parar-de-fumar-onde-encontrar-ajuda.xhtml","21 - preparando-parar-de-fumar-medo-nao-conseguir-ganho-de-peso.xhtml","22 - preparando-parar-de-fumar-introducao.xhtml","23 - preparando-parar-de-fumar-calculadora-gastos.xhtml","24 - preparando-parar-de-fumar-beneficios-de-parar.xhtml","25 - preparando-parar-de-fumar-ansiedade-e-depressao.xhtml","26 - preparando-parar-de-fumar-abstinencia-fissura.xhtml","27 - politica-do-site.xhtml","28 - parou-de-fumar-onde-procurar-ajuda.xhtml","29 - parou-de-fumar-acompanhamento.xhtml","30 - parou-de-fumar-acompanhamento-recaidas-identificar-motivos.xhtml","31 - parou-de-fumar-acompanhamento-lapso-identificar-fatores-recaida.xhtml","32 - para-pesquisadores.xhtml","33 - login.xhtml","34 - index.xhtml","35 - escolha-uma-etapa.xhtml","36 - escolha-uma-etapa.xhtml","37 - contato.xhtml","38 - cadastrar-nova-conta.xhtml","39 - outras.xhtml","40 - saida"]},"options":{"NodeID":"label$Label","colourScale":"d3.scale.category20()","fontSize":12,"fontFamily":"serif","nodeWidth":30,"nodePadding":10}},"evals":[]}</script><!--/html_preserve-->

########################################
##Third Step "Parou-de-Fumar" (V28 to 31)#
########################################

Selecting links made by at least one node from First Step 

########################################
###1 Model With Loops and Recursive Paths#
########################################


```r
#sankeyFT(c(28:38),Links1)
#sankeyF(c(28:38), Links1)
#sankeyT(c(28:38), Links1)

#These plots are not included as we need to check the Java Scrisct template for Sankey Plot with Recursive Patterns
```

###########################################
###2 Model Without Loops or Recursive Paths#
###########################################


```r
sankeyFT(c(28:38),Links2)
```

```
## #Links Forward (From) and Backward (To),
```

<!--html_preserve--><div id="htmlwidget-9270" style="width:864px;height:960px;" class="sankeyNetwork"></div>
<script type="application/json" data-for="htmlwidget-9270">{"x":{"links":{"source":[0,0,0,0,0,0,0,0,2,2,2,2,2,2,2,3,3,3,3,3,3,3,5,7,8,8,8,8,8,8,8,8,10,13,15,15,15,15,15,15,15,16,16,16,16,16,16,17,17,17,18,18,18,18,18,19,19,19,19,20,20,20,20,20,20,20,21,21,21,21,22,22,22,22,22,22,22,22,23,23,23,23,23,23,24,24,24,24,24,25,25,25,25,26,26,26,26,26,26,27,27,27,27,27,27,27,29,29,29,29,29,29,29,29,31,32,32,32,32,32,32,33,33,33,33,33,33,34,34,34,34,35,35,36,36,36,37,37,38],"target":[29,32,33,34,35,36,37,38,29,32,33,34,36,37,38,29,32,33,34,36,37,38,34,34,29,32,33,34,35,36,37,38,34,34,29,32,33,34,36,37,38,32,33,34,36,37,38,33,34,38,29,32,33,34,36,33,34,37,38,29,32,33,34,36,37,38,29,33,34,37,29,32,33,34,35,36,37,38,29,33,34,36,37,38,29,32,33,34,37,32,33,34,38,29,32,33,34,37,38,29,32,33,34,36,37,38,32,33,34,35,36,37,38,40,40,33,34,36,37,38,40,34,35,36,37,38,40,36,37,38,40,38,40,37,38,40,38,40,40],"value":[3.13549421592915,3.43398720448515,3.91202300542815,7.25559127425367,0,1.79175946922805,3.87120101090789,3.58351893845611,0,3.36729582998647,2.89037175789616,3.98898404656427,2.19722457733622,3.29583686600433,3.89182029811063,2.07944154167984,4.20469261939097,2.19722457733622,5.18738580584075,2.89037175789616,5.24702407216049,2.83321334405622,0.693147180559945,0,3.78418963391826,1.79175946922805,4.85203026391962,3.63758615972639,1.6094379124341,0.693147180559945,2.19722457733622,2.30258509299405,0,0,2.56494935746154,3.93182563272433,2.19722457733622,5.35185813347607,3.61091791264422,3.97029191355212,3.04452243772342,1.6094379124341,0.693147180559945,2.56494935746154,0,0,0,0.693147180559945,2.30258509299405,1.09861228866811,0,0,0.693147180559945,1.79175946922805,0.693147180559945,1.09861228866811,2.07944154167984,1.09861228866811,0,1.38629436111989,0.693147180559945,0.693147180559945,2.83321334405622,0,0.693147180559945,1.94591014905531,0.693147180559945,0,2.39789527279837,0,3.61091791264422,3.40119738166216,4.7361984483945,4.54329478227,0.693147180559945,0,2.484906649788,2.56494935746154,0,1.38629436111989,3.04452243772342,0,0,0.693147180559945,0,0.693147180559945,1.09861228866811,3.09104245335832,0.693147180559945,0,0,3.13549421592915,1.09861228866811,0,2.07944154167984,0.693147180559945,2.19722457733622,0,0.693147180559945,1.94591014905531,4.76217393479776,1.38629436111989,3.25809653802148,0.693147180559945,3.43398720448515,2.484906649788,2.30258509299405,4.90527477843843,3.09104245335832,0,0,1.6094379124341,0.693147180559945,2.99573227355399,3.68887945411394,2.56494935746154,4.23410650459726,1.38629436111989,5.24702407216049,3.17805383034795,3.63758615972639,4.12713438504509,0,1.09861228866811,3.04452243772342,2.484906649788,4.15888308335967,4.91998092582813,4.39444915467244,4.70048036579242,7.21007962817079,1.94591014905531,3.58351893845611,1.94591014905531,5.03695260241363,2.56494935746154,2.94443897916644,4.90527477843843,3.40119738166216]},"nodes":{"name":["0 - entrada","1 - user_list.xhtml","2 - termos-de-uso.xhtml","3 - quem-somos-os-autores.xhtml","4 - pronto-para-parar-de-fumar-vencendo-a-fissura.xhtml","5 - pronto-para-parar-de-fumar-plano-personalizado.xhtml","6 - pronto-para-parar-de-fumar-metodos-de-parar.xhtml","7 - pronto-para-parar-de-fumar-medicamentos.xhtml","8 - pronto-para-parar-de-fumar-introducao.xhtml","9 - pronto-para-parar-de-fumar-ganho-de-peso.xhtml","10 - pronto-para-parar-de-fumar-data-para-parar.xhtml","11 - pronto-para-parar-de-fumar-como-evitar-recaidas.xhtml","12 - pronto-para-parar-de-fumar-como-evitar-recaidas-sim.xhtml","13 - pronto-para-parar-de-fumar-como-evitar-recaidas-estrategias.xhtml","14 - pronto-para-parar-de-fumar-abstinencia.xhtml","15 - programa.xhtml","16 - preparando-parar-de-fumar-substituir-prazer-cigarro.xhtml","17 - preparando-parar-de-fumar-riscos-fumo-passivo.xhtml","18 - preparando-parar-de-fumar-riscos-do-cigarro.xhtml","19 - preparando-parar-de-fumar-riscos-de-fumar.xhtml","20 - preparando-parar-de-fumar-onde-encontrar-ajuda.xhtml","21 - preparando-parar-de-fumar-medo-nao-conseguir-ganho-de-peso.xhtml","22 - preparando-parar-de-fumar-introducao.xhtml","23 - preparando-parar-de-fumar-calculadora-gastos.xhtml","24 - preparando-parar-de-fumar-beneficios-de-parar.xhtml","25 - preparando-parar-de-fumar-ansiedade-e-depressao.xhtml","26 - preparando-parar-de-fumar-abstinencia-fissura.xhtml","27 - politica-do-site.xhtml","28 - parou-de-fumar-onde-procurar-ajuda.xhtml","29 - parou-de-fumar-acompanhamento.xhtml","30 - parou-de-fumar-acompanhamento-recaidas-identificar-motivos.xhtml","31 - parou-de-fumar-acompanhamento-lapso-identificar-fatores-recaida.xhtml","32 - para-pesquisadores.xhtml","33 - login.xhtml","34 - index.xhtml","35 - escolha-uma-etapa.xhtml","36 - escolha-uma-etapa.xhtml","37 - contato.xhtml","38 - cadastrar-nova-conta.xhtml","39 - outras.xhtml","40 - saida"]},"options":{"NodeID":"label$Label","colourScale":"d3.scale.category20()","fontSize":12,"fontFamily":"serif","nodeWidth":30,"nodePadding":10}},"evals":[]}</script><!--/html_preserve-->

```r
sankeyF(c(28:38), Links2)
```

```
## #Links From - no log transformation due to low level of observations
```

<!--html_preserve--><div id="htmlwidget-1402" style="width:864px;height:960px;" class="sankeyNetwork"></div>
<script type="application/json" data-for="htmlwidget-1402">{"x":{"links":{"source":[29,29,29,29,29,29,29,29,31,32,32,32,32,32,32,33,33,33,33,33,33,34,34,34,34,35,35,36,36,36,37,37,38],"target":[32,33,34,35,36,37,38,40,40,33,34,36,37,38,40,34,35,36,37,38,40,36,37,38,40,38,40,37,38,40,38,40,40],"value":[2.30258509299405,4.90527477843843,3.09104245335832,0,0,1.6094379124341,0.693147180559945,2.99573227355399,3.68887945411394,2.56494935746154,4.23410650459726,1.38629436111989,5.24702407216049,3.17805383034795,3.63758615972639,4.12713438504509,0,1.09861228866811,3.04452243772342,2.484906649788,4.15888308335967,4.91998092582813,4.39444915467244,4.70048036579242,7.21007962817079,1.94591014905531,3.58351893845611,1.94591014905531,5.03695260241363,2.56494935746154,2.94443897916644,4.90527477843843,3.40119738166216]},"nodes":{"name":["0 - entrada","1 - user_list.xhtml","2 - termos-de-uso.xhtml","3 - quem-somos-os-autores.xhtml","4 - pronto-para-parar-de-fumar-vencendo-a-fissura.xhtml","5 - pronto-para-parar-de-fumar-plano-personalizado.xhtml","6 - pronto-para-parar-de-fumar-metodos-de-parar.xhtml","7 - pronto-para-parar-de-fumar-medicamentos.xhtml","8 - pronto-para-parar-de-fumar-introducao.xhtml","9 - pronto-para-parar-de-fumar-ganho-de-peso.xhtml","10 - pronto-para-parar-de-fumar-data-para-parar.xhtml","11 - pronto-para-parar-de-fumar-como-evitar-recaidas.xhtml","12 - pronto-para-parar-de-fumar-como-evitar-recaidas-sim.xhtml","13 - pronto-para-parar-de-fumar-como-evitar-recaidas-estrategias.xhtml","14 - pronto-para-parar-de-fumar-abstinencia.xhtml","15 - programa.xhtml","16 - preparando-parar-de-fumar-substituir-prazer-cigarro.xhtml","17 - preparando-parar-de-fumar-riscos-fumo-passivo.xhtml","18 - preparando-parar-de-fumar-riscos-do-cigarro.xhtml","19 - preparando-parar-de-fumar-riscos-de-fumar.xhtml","20 - preparando-parar-de-fumar-onde-encontrar-ajuda.xhtml","21 - preparando-parar-de-fumar-medo-nao-conseguir-ganho-de-peso.xhtml","22 - preparando-parar-de-fumar-introducao.xhtml","23 - preparando-parar-de-fumar-calculadora-gastos.xhtml","24 - preparando-parar-de-fumar-beneficios-de-parar.xhtml","25 - preparando-parar-de-fumar-ansiedade-e-depressao.xhtml","26 - preparando-parar-de-fumar-abstinencia-fissura.xhtml","27 - politica-do-site.xhtml","28 - parou-de-fumar-onde-procurar-ajuda.xhtml","29 - parou-de-fumar-acompanhamento.xhtml","30 - parou-de-fumar-acompanhamento-recaidas-identificar-motivos.xhtml","31 - parou-de-fumar-acompanhamento-lapso-identificar-fatores-recaida.xhtml","32 - para-pesquisadores.xhtml","33 - login.xhtml","34 - index.xhtml","35 - escolha-uma-etapa.xhtml","36 - escolha-uma-etapa.xhtml","37 - contato.xhtml","38 - cadastrar-nova-conta.xhtml","39 - outras.xhtml","40 - saida"]},"options":{"NodeID":"label$Label","colourScale":"d3.scale.category20()","fontSize":12,"fontFamily":"serif","nodeWidth":30,"nodePadding":10}},"evals":[]}</script><!--/html_preserve-->

```r
sankeyT(c(28:38), Links2)
```

```
## #Links To  - no log transformation due to low level of observations
```

<!--html_preserve--><div id="htmlwidget-9717" style="width:864px;height:960px;" class="sankeyNetwork"></div>
<script type="application/json" data-for="htmlwidget-9717">{"x":{"links":{"source":[0,0,0,0,0,0,0,0,2,2,2,2,2,2,2,3,3,3,3,3,3,3,5,7,8,8,8,8,8,8,8,8,10,13,15,15,15,15,15,15,15,16,16,16,16,16,16,17,17,17,18,18,18,18,18,19,19,19,19,20,20,20,20,20,20,20,21,21,21,21,22,22,22,22,22,22,22,22,23,23,23,23,23,23,24,24,24,24,24,25,25,25,25,26,26,26,26,26,26,27,27,27,27,27,27,27,29,29,29,29,29,29,29,32,32,32,32,32,33,33,33,33,33,34,34,34,35,36,36,37],"target":[29,32,33,34,35,36,37,38,29,32,33,34,36,37,38,29,32,33,34,36,37,38,34,34,29,32,33,34,35,36,37,38,34,34,29,32,33,34,36,37,38,32,33,34,36,37,38,33,34,38,29,32,33,34,36,33,34,37,38,29,32,33,34,36,37,38,29,33,34,37,29,32,33,34,35,36,37,38,29,33,34,36,37,38,29,32,33,34,37,32,33,34,38,29,32,33,34,37,38,29,32,33,34,36,37,38,32,33,34,35,36,37,38,33,34,36,37,38,34,35,36,37,38,36,37,38,38,37,38,38],"value":[3.13549421592915,3.43398720448515,3.91202300542815,7.25559127425367,0,1.79175946922805,3.87120101090789,3.58351893845611,0,3.36729582998647,2.89037175789616,3.98898404656427,2.19722457733622,3.29583686600433,3.89182029811063,2.07944154167984,4.20469261939097,2.19722457733622,5.18738580584075,2.89037175789616,5.24702407216049,2.83321334405622,0.693147180559945,0,3.78418963391826,1.79175946922805,4.85203026391962,3.63758615972639,1.6094379124341,0.693147180559945,2.19722457733622,2.30258509299405,0,0,2.56494935746154,3.93182563272433,2.19722457733622,5.35185813347607,3.61091791264422,3.97029191355212,3.04452243772342,1.6094379124341,0.693147180559945,2.56494935746154,0,0,0,0.693147180559945,2.30258509299405,1.09861228866811,0,0,0.693147180559945,1.79175946922805,0.693147180559945,1.09861228866811,2.07944154167984,1.09861228866811,0,1.38629436111989,0.693147180559945,0.693147180559945,2.83321334405622,0,0.693147180559945,1.94591014905531,0.693147180559945,0,2.39789527279837,0,3.61091791264422,3.40119738166216,4.7361984483945,4.54329478227,0.693147180559945,0,2.484906649788,2.56494935746154,0,1.38629436111989,3.04452243772342,0,0,0.693147180559945,0,0.693147180559945,1.09861228866811,3.09104245335832,0.693147180559945,0,0,3.13549421592915,1.09861228866811,0,2.07944154167984,0.693147180559945,2.19722457733622,0,0.693147180559945,1.94591014905531,4.76217393479776,1.38629436111989,3.25809653802148,0.693147180559945,3.43398720448515,2.484906649788,2.30258509299405,4.90527477843843,3.09104245335832,0,0,1.6094379124341,0.693147180559945,2.56494935746154,4.23410650459726,1.38629436111989,5.24702407216049,3.17805383034795,4.12713438504509,0,1.09861228866811,3.04452243772342,2.484906649788,4.91998092582813,4.39444915467244,4.70048036579242,1.94591014905531,1.94591014905531,5.03695260241363,2.94443897916644]},"nodes":{"name":["0 - entrada","1 - user_list.xhtml","2 - termos-de-uso.xhtml","3 - quem-somos-os-autores.xhtml","4 - pronto-para-parar-de-fumar-vencendo-a-fissura.xhtml","5 - pronto-para-parar-de-fumar-plano-personalizado.xhtml","6 - pronto-para-parar-de-fumar-metodos-de-parar.xhtml","7 - pronto-para-parar-de-fumar-medicamentos.xhtml","8 - pronto-para-parar-de-fumar-introducao.xhtml","9 - pronto-para-parar-de-fumar-ganho-de-peso.xhtml","10 - pronto-para-parar-de-fumar-data-para-parar.xhtml","11 - pronto-para-parar-de-fumar-como-evitar-recaidas.xhtml","12 - pronto-para-parar-de-fumar-como-evitar-recaidas-sim.xhtml","13 - pronto-para-parar-de-fumar-como-evitar-recaidas-estrategias.xhtml","14 - pronto-para-parar-de-fumar-abstinencia.xhtml","15 - programa.xhtml","16 - preparando-parar-de-fumar-substituir-prazer-cigarro.xhtml","17 - preparando-parar-de-fumar-riscos-fumo-passivo.xhtml","18 - preparando-parar-de-fumar-riscos-do-cigarro.xhtml","19 - preparando-parar-de-fumar-riscos-de-fumar.xhtml","20 - preparando-parar-de-fumar-onde-encontrar-ajuda.xhtml","21 - preparando-parar-de-fumar-medo-nao-conseguir-ganho-de-peso.xhtml","22 - preparando-parar-de-fumar-introducao.xhtml","23 - preparando-parar-de-fumar-calculadora-gastos.xhtml","24 - preparando-parar-de-fumar-beneficios-de-parar.xhtml","25 - preparando-parar-de-fumar-ansiedade-e-depressao.xhtml","26 - preparando-parar-de-fumar-abstinencia-fissura.xhtml","27 - politica-do-site.xhtml","28 - parou-de-fumar-onde-procurar-ajuda.xhtml","29 - parou-de-fumar-acompanhamento.xhtml","30 - parou-de-fumar-acompanhamento-recaidas-identificar-motivos.xhtml","31 - parou-de-fumar-acompanhamento-lapso-identificar-fatores-recaida.xhtml","32 - para-pesquisadores.xhtml","33 - login.xhtml","34 - index.xhtml","35 - escolha-uma-etapa.xhtml","36 - escolha-uma-etapa.xhtml","37 - contato.xhtml","38 - cadastrar-nova-conta.xhtml","39 - outras.xhtml","40 - saida"]},"options":{"NodeID":"label$Label","colourScale":"d3.scale.category20()","fontSize":12,"fontFamily":"serif","nodeWidth":30,"nodePadding":10}},"evals":[]}</script><!--/html_preserve-->
