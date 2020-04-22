---
title: 'Executar r script: referência do módulo'
titleSuffix: Azure Machine Learning
description: Aprenda a usar o módulo Execute R Script no Azure Machine Learning para executar o código R.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 03/10/2020
ms.openlocfilehash: eb778c8d24639320b60927438de76a29de724ac2
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81684705"
---
# <a name="execute-r-script"></a>Executar script R

Este artigo descreve como usar o módulo **Execute R Script** para executar o código R no pipeline de designer (preview) do Azure Machine Learning.

Com R, você pode executar tarefas que não são suportadas atualmente por módulos existentes, tais como: 
- Criar transformações de dados personalizadas
- Use suas próprias métricas para avaliar previsões
- Construir modelos usando algoritmos que não são implementados como módulos autônomos no designer

## <a name="r-version-support"></a>Suporte à versão R

O designer de Machine Learning do Azure usa a distribuição CRAN (Comprehensive R Archive Network) de R. A versão utilizada atualmente é CRAN 3.5.1.

## <a name="supported-r-packages"></a>Pacotes R suportados

O ambiente R é pré-instalado com mais de 100 pacotes. Para obter uma lista completa, consulte a seção [Pacotes R pré-instalados](#pre-installed-r-packages).

Você também pode adicionar o seguinte código a qualquer módulo **execute R Script** e ver os pacotes instalados.

```R
azureml_main <- function(dataframe1, dataframe2){
  print("R script run.")
  dataframe1 <- data.frame(installed.packages())
  return(list(dataset1=dataframe1, dataset2=dataframe2))
}
```

## <a name="installing-r-packages"></a>Instalando pacotes R
Para instalar pacotes R `install.packages()` adicionais, use o método. Os pacotes são instalados para cada módulo **execute R Script** e não são compartilhados em outros módulos execute R **Script.**

> [!NOTE]
> Por favor, especifique o repositório CRAN ao instalar pacotes como`install.packages("zoo",repos = "http://cran.us.r-project.org")`

Esta amostra mostra como instalar o Zoo:
```R
# R version: 3.5.1
# The script MUST contain a function named azureml_main
# which is the entry point for this module.

# Please note that functions dependant on X11 library
# such as "View" are not supported because X11 library
# is not pre-installed.

# The entry point function MUST have two input arguments.
# If the input port is not connected, the corresponding
# dataframe argument will be null.
#   Param<dataframe1>: a R DataFrame
#   Param<dataframe2>: a R DataFrame
azureml_main <- function(dataframe1, dataframe2){
  print("R script run.")
  
  if(!require(zoo)) install.packages("zoo",repos = "http://cran.us.r-project.org")
  library(zoo)
  # Return datasets as a Named List
  return(list(dataset1=dataframe1, dataset2=dataframe2))
}
```
 > [!NOTE]
  > Verifique se o pacote já existe antes de instalá-lo para evitar a repetição da instalação. Como `  if(!require(zoo)) install.packages("zoo",repos = "http://cran.us.r-project.org")` no código de amostra acima. A instalação repetida pode causar o tempo de intervalo da solicitação de serviço web.     

## <a name="upload-files"></a>Carregar arquivos
O **Script Execute R** suporta o upload de arquivos usando o Azure Machine Learning R SDK.

O exemplo a seguir mostra como carregar um arquivo de imagem no **Script Execute R**:
```R

# R version: 3.5.1
# The script MUST contain a function named azureml_main
# which is the entry point for this module.

# Please note that functions dependant on X11 library
# such as "View" are not supported because X11 library
# is not pre-installed.

# The entry point function MUST have two input arguments.
# If the input port is not connected, the corresponding
# dataframe argument will be null.
#   Param<dataframe1>: a R DataFrame
#   Param<dataframe2>: a R DataFrame
azureml_main <- function(dataframe1, dataframe2){
  print("R script run.")

  # Generate a jpeg graph
  img_file_name <- "rect.jpg"
  jpeg(file=img_file_name)
  example(rect)
  dev.off()

  upload_files_to_run(names = list(file.path("graphic", img_file_name)), paths=list(img_file_name))


  # Return datasets as a Named List
  return(list(dataset1=dataframe1, dataset2=dataframe2))
}
```

Depois que a execução do pipeline estiver concluída, você pode visualizar a imagem no painel direito do módulo

> [!div class="mx-imgBorder"]
> ![Imagem enviada](media/module/upload-image-in-r-script.png)

## <a name="how-to-configure-execute-r-script"></a>Como configurar o script Execute R

O módulo **Execute R Script** contém código de amostra que você pode usar como ponto de partida. Para configurar o módulo **Execute R Script,** forneça um conjunto de entradas e código para executar.

![Módulo R](media/module/execute-r-script.png)

Os conjuntos de dados armazenados no designer são automaticamente convertidos em um quadro de dados R quando carregados com este módulo.

1.  Adicione o módulo **Execute R Script** ao seu pipeline.

  

1. Conecte todas as entradas necessárias pelo script. As entradas são opcionais e podem incluir dados e código R adicional.

    * **Conjunto de dados1**: `dataframe1`Consulte a primeira entrada como . O conjunto de dados de entrada deve ser formatado como um CSV, TSV, ARFF ou você pode conectar um conjunto de dados Azure Machine Learning.

    * **Conjunto de dados2**: `dataframe2`Referenciar a segunda entrada como . Esse conjunto de dados também deve ser formatado como um arquivo CSV, TSV, ARFF ou como um conjunto de dados Azure Machine Learning.

    * **Pacote de script**: A terceira entrada aceita arquivos ZIP. O arquivo com zíper pode conter vários arquivos e vários tipos de arquivos.

1. Na caixa de texto **r script,** digite ou cole o script R válido.

    > [!NOTE]
    > Por favor, tenha muito cuidado ao escrever seu script e certifique-se de que não há erro de sintaxe, como usar uma variável não declarada ou um módulo ou função não importada. Também preste atenção extra à lista de pacotes pré-instalados no final deste documento. Para usar pacotes que não estão listados, instale-os em seu script, como`install.packages("zoo",repos = "http://cran.us.r-project.org")`
    
    > [!NOTE]
    > Funções dependentes da biblioteca X11, como "View", não são suportadas porque a biblioteca X11 não está pré-instalada.
    
    Para ajudá-lo a começar, a caixa de texto **R Script** está pré-preenchida com código de amostra, que você pode editar ou substituir.
    
    ```R
    # R version: 3.5.1
    # The script MUST contain a function named azureml_main
    # which is the entry point for this module.

    # Please note that functions dependant on X11 library
    # such as "View" are not supported because X11 library
    # is not pre-installed.
    
    # The entry point function MUST have two input arguments.
    # If the input port is not connected, the corresponding
    # dataframe argument will be null.
    #   Param<dataframe1>: a R DataFrame
    #   Param<dataframe2>: a R DataFrame
    azureml_main <- function(dataframe1, dataframe2){
    print("R script run.")

    # If a zip file is connected to the third input port, it is
    # unzipped under "./Script Bundle". This directory is added
    # to sys.path.

    # Return datasets as a Named List
    return(list(dataset1=dataframe1, dataset2=dataframe2))
    }
    ```

 * O script deve conter `azureml_main`uma função nomeada , que é o ponto de entrada para este módulo.

 * A função de ponto de entrada `Param<dataframe1>` `Param<dataframe2>`deve ter dois argumentos de entrada: e , mesmo quando esses dois argumentos não são usados na função.

   > [!NOTE]
    > Os dados passados para o módulo `dataframe1` Execute `dataframe2` **R Script** são referenciados como e , `dataset1`o `dataset2`que é diferente do designer de Machine Learning do Azure (a referência do designer como , ). Verifique se os dados de entrada estão corretos no seu script.  
 
    > [!NOTE]
    >  O código R existente pode precisar de pequenas alterações para ser executado em um pipeline de designer. Por exemplo, os dados de entrada fornecidos no formato CSV devem ser explicitamente convertidos em um conjunto de dados antes que você possa usá-los em seu código. Os tipos de dados e colunas usados na linguagem R também diferem em alguns aspectos dos tipos de dados e colunas usados no designer.

1.  **Sementes Aleatórias**: Digite um valor para usar dentro do ambiente R como o valor aleatório da semente. Este parâmetro é equivalente a chamar `set.seed(value)` no código de R.  

1. Envie o oleoduto.  

## <a name="results"></a>Resultados

Os módulos **Execute R Script** podem retornar várias saídas, mas devem ser fornecidos como quadros de dados R. Os quadros de dados são automaticamente convertidos em conjuntos de dados no designer para compatibilidade com outros módulos.

As mensagens padrão e os erros de R são devolvidos ao registro do módulo.

Se você precisar imprimir resultados no script R, você pode encontrar os resultados impressos na **70_driver_log** na guia **Saídas+logs** no painel direito do módulo.

## <a name="sample-scripts"></a>Scripts de exemplo

Existem muitas maneiras que você pode estender seu pipeline usando script R personalizado.  Esta seção fornece código de amostra para tarefas comuns.


### <a name="add-r-script-as-an-input"></a>Adicionar script R como uma entrada

O módulo **Execute R Script** suporta arquivos de script R arbitrários como entradas. Para isso, eles devem ser carregados no seu espaço de trabalho como parte do arquivo ZIP.

1. Para carregar um arquivo ZIP contendo código R para o seu espaço de trabalho, vá para a página de ativos **Datasets,** clique **em Criar conjunto de dados**e, em seguida, selecione A partir do arquivo **local** e da opção Tipo de conjunto de dados **arquivo.**  

1. Verifique se o arquivo com zíper está disponível na lista **Meus Conjuntos de dados** na categoria **Conjuntos de dados** na árvore do módulo esquerdo.

1.  Conecte o conjunto de dados à porta de entrada **Script Bundle.**

1. Todos os arquivos contidos no arquivo ZIP estão disponíveis durante o tempo de execução do pipeline. 

    Se o arquivo de pacote de script contiver uma estrutura de diretório, a estrutura será preservada. No entanto, você deve alterar seu código para preparar o diretório **./Script Bundle** para o caminho.

### <a name="process-data"></a>Processar dados

A amostra a seguir mostra como dimensionar e normalizar os dados de entrada:

```R
# R version: 3.5.1
# The script MUST contain a function named azureml_main
# which is the entry point for this module.

# Please note that functions dependant on X11 library
# such as "View" are not supported because X11 library
# is not pre-installed.

# The entry point function MUST have two input arguments.
# If the input port is not connected, the corresponding
# dataframe argument will be null.
#   Param<dataframe1>: a R DataFrame
#   Param<dataframe2>: a R DataFrame
azureml_main <- function(dataframe1, dataframe2){
  print("R script run.")
  # If a zip file is connected to the third input port, it is
  # unzipped under "./Script Bundle". This directory is added
  # to sys.path.
  series <- dataframe1$width
  # find the maximum and minimum values of width column in dataframe1
  max_v <- max(series)
  min_v <- min(series)
  # calculate the scale and bias
  scale <- max_v - min_v
  bias <- min_v / dis
  # apply min-max normalizing
  dataframe1$width <- dataframe1$width / scale - bias
  dataframe2$width <- dataframe2$width / scale - bias
  # Return datasets as a Named List
  return(list(dataset1=dataframe1, dataset2=dataframe2))
}
 ```

### <a name="read-a-zip-file-as-input"></a>Leia um arquivo ZIP como entrada

Esta amostra mostra como usar um conjunto de dados em um arquivo ZIP como uma entrada para o módulo **Execute R Script.**

1. Crie o arquivo de dados no formato CSV e nomeie-o de "mydatafile.csv".
1. Crie um arquivo ZIP e adicione o arquivo CSV ao arquivo.
1. Carregue o arquivo fechado para o seu espaço de trabalho azure Machine Learning. 
1. Conecte o conjunto de dados resultante à entrada **ScriptBundle** do módulo **Execute R Script.**
1. Usando o código a seguir para ler os dados do CSV do arquivo fechado.

```R
azureml_main <- function(dataframe1, dataframe2){
  print("R script run.")
  mydataset<-read.csv("./Script Bundle/mydatafile.csv",encoding="UTF-8");  
  # Return datasets as a Named List
  return(list(dataset1=mydataset, dataset2=dataframe2))
}
```

### <a name="replicate-rows"></a>Replicar linhas

Esta amostra mostra como replicar registros positivos em um conjunto de dados para equilibrar a amostra:

```R
azureml_main <- function(dataframe1, dataframe2){
  data.set <- dataframe1[dataframe1[,1]==-1,]  
  # positions of the positive samples
  pos <- dataframe1[dataframe1[,1]==1,]
  # replicate the positive samples to balance the sample  
  for (i in 1:20) data.set <- rbind(data.set,pos)  
  row.names(data.set) <- NULL
  # Return datasets as a Named List
  return(list(dataset1=data.set, dataset2=dataframe2))
}
```

### <a name="pass-r-objects-between-execute-r-script-modules"></a>Passe objetos R entre executar módulos de script R

Você pode passar objetos R entre instâncias do módulo **Executar Script R** usando o mecanismo de serialização interna. Este exemplo pressupõe que você deseja `A` mover o objeto R nomeado entre dois módulos **Execute R Script.**

1. Adicione o primeiro módulo **Execute R Script** ao seu pipeline e digite o seguinte código na caixa de texto R **Script** para criar um objeto `A` serializado como uma coluna na tabela de dados de saída do módulo:  
  
    ```R
    azureml_main <- function(dataframe1, dataframe2){
      print("R script run.")
      # some codes generated A
      
      serialized <- as.integer(serialize(A,NULL))  
      data.set <- data.frame(serialized,stringsAsFactors=FALSE)

      return(list(dataset1=data.set, dataset2=dataframe2))
    }
    ```

    A conversão explícita para o tipo inteiro é feita porque a `Raw` função de serialização produz dados no formato R, que não é suportado pelo designer.

1. Adicione uma segunda instância do módulo **Execute R Script** e conecte-o à porta de saída do módulo anterior.

1. Digite o seguinte código na caixa `A` de texto R **Script** para extrair objeto da tabela de dados de entrada. 

    ```R
    azureml_main <- function(dataframe1, dataframe2){
      print("R script run.")
      A <- unserialize(as.raw(dataframe1$serialized))  
      # Return datasets as a Named List
      return(list(dataset1=dataframe1, dataset2=dataframe2))
    }
    ```

## <a name="pre-installed-r-packages"></a>Pacotes R pré-instalados

A lista atual de pacotes R pré-instalados disponíveis para uso:

|              |            | 
|--------------|------------| 
| Pacote      | Versão    | 
| askpass      | 1,1        | 
| assertthat   | 0.2.1      | 
| backports    | 1.1.4      | 
| base         | 3.5.1      | 
| base64enc    | 0.1-3      | 
| BH           | 1.69.0-1   | 
| bindr        | 0.1.1      | 
| bindrcpp     | 0.2.2      | 
| bitops       | 1.0-6      | 
| ciar         | 1.3-22     | 
| broom        | 0.5.2      | 
| callr        | 3.2.0      | 
| caret        | 6.0-84     | 
| caTools      | 1.17.1.2   | 
| cellranger   | 1.1.0      | 
| classe        | 7.3-15     | 
| cli          | 1.1.0      | 
| clipr        | 0.6.0      | 
| cluster      | 2.0.7-1    | 
| codetools    | 0.2-16     | 
| colorspace   | 1.4-1      | 
| compilador     | 3.5.1      | 
| crayon       | 1.3.4      | 
| curl         | 3.3        | 
| data.table   | 1.12.2     | 
| conjuntos de dados     | 3.5.1      | 
| DBI          | 1.0.0      | 
| dbplyr       | 1.4.1      | 
| digest       | 0.6.19     | 
| dplyr        | 0.7.6      | 
| e1071        | 1.7-2      | 
| avaliar     | 0.14       | 
| fansi        | 0.4.0      | 
| forcats      | 0.3.0      | 
| foreach      | 1.4.4      | 
| foreign      | 0.8-71     | 
| Fs           | 1.3.1      | 
| gdata        | 2.18.0     | 
| Genéricos     | 0.0.2      | 
| ggplot2      | 3.2.0      | 
| glmnet       | 2.0-18     | 
| glue         | 1.3.1      | 
| gower        | 0.2.1      | 
| gplots       | 3.0.1.1    | 
| elemento gráfico     | 3.5.1      | 
| grDevices    | 3.5.1      | 
| grade         | 3.5.1      | 
| gtable       | 0.3.0      | 
| gtools       | 3.8.1      | 
| haven        | 2.1.0      | 
| highr        | 0.8        | 
| hms          | 0.4.2      | 
| htmltools    | 0.3.6      | 
| httr         | 1.4.0      | 
| ipred        | 0.9-9      | 
| iterators    | 1.0.10     | 
| jsonlite     | 1.6        | 
| KernSmooth   | 2.23-15    | 
| knitr        | 1,23       | 
| labeling     | 0.3        | 
| lattice      | 0.20-38    | 
| lava         | 1.6.5      | 
| lazyeval     | 0.2.2      | 
| lubridate    | 1.7.4      | 
| magrittr     | 1.5        | 
| markdown     | 1          | 
| MASS         | 7.3-51.4   | 
| Matriz       | 1.2-17     | 
| methods      | 3.5.1      | 
| mgcv         | 1.8-28     | 
| mime         | 0,7        | 
| ModelMetrics | 1.2.2      | 
| modelr       | 0.1.4      | 
| munsell      | 0.5.0      | 
| nlme         | 3.1-140    | 
| nnet         | 7.3-12     | 
| numDeriv     | 2016.8-1.1 | 
| openssl      | 1.4        | 
| parallel     | 3.5.1      | 
| pillar       | 1.4.1      | 
| pkgconfig    | 2.0.2      | 
| plogr        | 0.2.0      | 
| plyr         | 1.8.4      | 
| prettyunits  | 1.0.2      | 
| processx     | 3.3.1      | 
| prodlim      | 2018.04.18 | 
| progresso     | 1.2.2      | 
| ps           | 1.3.0      | 
| purrr        | 0.3.2      | 
| quadprog     | 1.5-7      | 
| quantmod     | 0.4-15     | 
| R6           | 2.4.0      | 
| randomForest | 4.6-14     | 
| RColorBrewer | 1.1-2      | 
| Rcpp         | 1.0.1      | 
| RcppRoll     | 0.3.0      | 
| readr        | 1.3.1      | 
| readxl       | 1.3.1      | 
| recipes      | 0.1.5      | 
| rematch      | 1.0.1      | 
| reprex       | 0.3.0      | 
| reshape2     | 1.4.3      | 
| reticulate   | 1.12       | 
| rlang        | 0.4.0      | 
| rmarkdown    | 1.13       | 
| ROCR         | 1.0-7      | 
| rpart        | 4.1-15     | 
| rstudioapi   | 0,1        | 
| rvest        | 0.3.4      | 
| scales       | 1.0.0      | 
| selectr      | 0.4-1      | 
| espacial      | 7.3-11     | 
| splines      | 3.5.1      | 
| SQUAREM      | 2017.10-1  | 
| stats        | 3.5.1      | 
| stats4       | 3.5.1      | 
| stringi      | 1.4.3      | 
| stringr      | 1.3.1      | 
| survival     | 2.44-1.1   | 
| sys          | 3.2        | 
| tcltk        | 3.5.1      | 
| tibble       | 2.1.3      | 
| tidyr        | 0.8.3      | 
| tidyselect   | 0.2.5      | 
| tidyverse    | 1.2.1      | 
| timeDate     | 3043.102   | 
| tinytex      | 0.13       | 
| ferramentas        | 3.5.1      | 
| tseries      | 0.10-47    | 
| TTR          | 0.23-4     | 
| utf8         | 1.1.4      | 
| utils        | 3.5.1      | 
| vctrs        | 0.1.0      | 
| viridisLite  | 0.3.0      | 
| whisker      | 0.3-2      | 
| withr        | 2.1.2      | 
| xfun         | 0.8        | 
| xml2         | 1.2.0      | 
| xts          | 0.11-2     | 
| yaml         | 2.2.0      | 
| zeallot      | 0.1.0      | 
| zoológico          | 1.8-6      | 

## <a name="next-steps"></a>Próximas etapas

Veja o [conjunto de módulos disponíveis](module-reference.md) para o Azure Machine Learning. 
