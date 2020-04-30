---
title: Usar R com ML Studio (clássico) – Azure
description: Use este tutorial de programação R para começar a usar o Azure Machine Learning Studio (clássico) em R para criar uma solução de previsão.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 03/01/2019
ms.openlocfilehash: 1dcda3efe3872100100d6e85b68a36359b7eab84
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82209495"
---
# <a name="get-started-with-azure-machine-learning-studio-classic-in-r"></a>Introdução ao Azure Machine Learning Studio (clássico) em R

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

<!-- Stephen F Elston, Ph.D. -->
Este tutorial ajuda você a começar a estender Azure Machine Learning Studio (clássico) usando a linguagem de programação R. Siga este tutorial de programação de R para criar, testar e executar o código R no estúdio (clássico). Ao trabalhar com o tutorial, você criará uma solução de previsão completa usando a linguagem R no Studio (clássico).  

Azure Machine Learning Studio (clássico) contém muitos módulos poderosos de aprendizado de máquina e de manipulação de dados. A poderosa linguagem de R tem sido descrita como a língua franca da análise. Felizmente, a análise e a manipulação de dados no Studio (clássico) podem ser estendidas usando o R. Essa combinação fornece a escalabilidade e a facilidade de implantação do estúdio (clássico) com a flexibilidade e análise profunda de R.

### <a name="forecasting-and-the-dataset"></a>Previsão e conjunto de dados

A previsão é um método analítico amplamente empregado e bastante útil. O uso típico varia de previsão de vendas de itens sazonais, determinar níveis de estoque ideal a prever variáveis macroeconômicas. A previsão normalmente é feita com modelos de série de tempo.

Dados de série temporal são dados em que os valores têm um índice de tempo. O índice de tempo pode ser regular, por exemplo, a cada mês ou a cada minuto, ou irregulares. Um modelo de série de tempo se baseia em dados de série de tempo. A linguagem de programação de R contém uma estrutura flexível e uma análise abrangente para dados de série temporal.

Neste guia, vamos trabalhar com os dados de preço e de produção de laticínios da Califórnia. Esses dados incluem informações mensais sobre a produção de vários produtos derivados do leite e o preço da gordura do leite, uma mercadoria que é um parâmetro de comparação.

Os dados usados neste artigo, juntamente com os scripts do R, podem ser baixados de [MachineLearningSamples-notebooks/Studio-Samples](https://github.com/Azure-Samples/MachineLearningSamples-Notebooks/tree/master/studio-samples). Os dados no arquivo `cadairydata.csv` foram originalmente sintetizados a partir das informações disponíveis na Universidade de Wisconsin [https://dairymarkets.com](https://dairymarkets.com)em.

### <a name="organization"></a>Organização

Avançaremos por várias etapas à medida que você aprender a criar, testar e executar o código de R de análise e manipulação de dados no ambiente Azure Machine Learning Studio (clássico).  

* Primeiro, exploraremos as noções básicas do uso da linguagem R no ambiente Azure Machine Learning Studio (clássico).
* Em seguida, vamos progredir para discutir vários aspectos de e/s para dados, código R e gráficos no ambiente Azure Machine Learning Studio (clássico).
* Então, construiremos a primeira parte de uma solução de previsão criando o código de limpeza de dados e transformação.
* Com nossos dados preparados, executaremos uma análise das correlações entre diversas variáveis de nosso conjunto de dados.
* Por fim, criaremos um modelo de previsão de série de tempos sazonais da produção de leite.

## <a name="interact-with-r-language-in-machine-learning-studio-classic"></a><a id="mlstudio"></a>Interagir com a linguagem R no Machine Learning Studio (clássico)

Esta seção orienta você por algumas noções básicas de interação com a linguagem de programação R no ambiente Machine Learning Studio (clássico). A linguagem R fornece uma poderosa ferramenta para criar módulos de análise e manipulação de dados personalizados dentro do ambiente Azure Machine Learning Studio (clássico).

Usarei o RStudio para desenvolver, testar e depurar o código R em pequena escala. Esse código é, então, recortado e colado em um módulo [Executar script R][execute-r-script] pronto para ser executado no Azure Machine Learning Studio (clássico).  

### <a name="the-execute-r-script-module"></a>O módulo Executar Script R

Dentro de Machine Learning Studio (clássico), os scripts do R são executados no módulo [Executar script R][execute-r-script] . Um exemplo do módulo [Executar script R][execute-r-script] no Machine Learning Studio (clássico) é mostrado na Figura 1.

 ![Linguagem de programação r: o módulo executar script R selecionado no Machine Learning Studio (clássico)](./media/r-quickstart/fig1.png)

*Figura 1. O ambiente Machine Learning Studio (clássico) mostrando o módulo executar script R selecionado.*

Fazendo referência à figura 1, vamos dar uma olhada em algumas das principais partes do ambiente Machine Learning Studio (clássico) para trabalhar com o módulo [Executar script R][execute-r-script] .

* Os módulos do teste são mostrados no painel central.
* A parte superior do painel direito contém uma janela para exibir e editar seus scripts de R.  
* A parte inferior do painel direito mostra algumas propriedades de [Executar Script R][execute-r-script]. Você pode exibir os logs de erro e saída selecionando os pontos adequados deste painel.

Certamente, vamos discutir o [script R execute][execute-r-script] com mais detalhes no restante deste artigo.

Ao trabalhar com funções R complexas, é recomendável que você edite, teste e depure no RStudio. Assim como acontece com qualquer desenvolvimento de software, estenda o código de forma incremental e teste-o em pequenos casos de teste simples. Em seguida, recorte e cole suas funções na janela de script R do módulo [Executar Script R][execute-r-script]. Essa abordagem permite que você aproveite o IDE (ambiente de desenvolvimento integrado) do RStudio e o poder do Azure Machine Learning Studio (clássico).  

#### <a name="execute-r-code"></a>Executar código R

Qualquer código R no módulo [Executar script r][execute-r-script] será executado quando você executar o experimento selecionando o botão **executar** . Quando a execução for concluída, uma marca de seleção aparecerá no ícone [Executar Script R][execute-r-script].

#### <a name="defensive-r-coding-for-azure-machine-learning"></a>Codificação R defensiva para o Azure Machine Learning

Se você estiver desenvolvendo código R para, digamos, um serviço Web usando Azure Machine Learning Studio (clássico), você deve, definitivamente, planejar como seu código lidará com uma entrada de dados inesperada e exceções. Para ficar claro, eu não incluí muito em relação a verificação ou a manipulação de exceção na maioria dos exemplos de código mostrados. No entanto, ao prosseguirmos, darei vários exemplos de funções usando o recurso de tratamento de exceção do R.  

Se você precisar de um tratamento mais completo da manipulação de exceção de R, recomendo que leia as seções aplicáveis do livro por Wickham listadas abaixo em [leituras adicionais](#appendixb).

#### <a name="debug-and-test-r-in-machine-learning-studio-classic"></a>Depurar e testar R em Machine Learning Studio (clássico)

Para reiterar, é recomendável testar e depurar seu código R em pequena escala no RStudio. No entanto, há casos em que você precisará detectar problemas de código R no próprio módulo [Executar Script R][execute-r-script]. Além disso, é uma prática recomendada verificar os resultados em Machine Learning Studio (clássico).

A saída da execução do código R e da plataforma Azure Machine Learning Studio (clássica) é encontrada principalmente em Output. log. Algumas informações adicionais serão vistas no arquivo error.log.  

Se ocorrer um erro no Machine Learning Studio (clássico) ao executar o código do R, seu primeiro curso de ação deve ser examinar Error. log. Esse arquivo pode conter mensagens de erro úteis para ajudá-lo a entender e corrigir o erro. Para exibir Error. log, selecione **Exibir log de erros** no **painel Propriedades** para [executar o script R][execute-r-script] que contém o erro.

Por exemplo, executei o seguinte código R, com uma variável y indefinida, em um módulo [Executar Script R][execute-r-script]:

```R
x <- 1.0
z <- x + y
```

Esse código não foi executado resultando em uma condição de erro. Selecionar **Exibir log de erros** no **painel Propriedades** produz a exibição mostrada na Figura 2.

  ![A mensagem de erro é exibida](./media/r-quickstart/fig2.png)

*Figura 2. Mensagem de erro pop-up.*

Parece que precisamos examinar output.g para ver a mensagem de erro de R. Selecione [Executar script R][execute-r-script] e, em seguida, selecione o item **Exibir saída. log** no **painel Propriedades** à direita. Uma nova janela do navegador é aberta e vejo o item a seguir.

    [Critical]     Error: Error 0063: The following error occurred during evaluation of R script:
    ---------- Start of error message from R ----------
    object 'y' not found


    object 'y' not found
    ----------- End of error message from R -----------

Essa mensagem de erro contém surpresas e claramente identifica o problema.

Para inspecionar o valor de qualquer objeto em R, você pode imprimir esses valores no arquivo output.log. As regras para examinar os valores de objeto são essencialmente as mesmas de uma sessão interativa de R. Por exemplo, se você digitar um nome de variável em uma linha, o valor do objeto será impresso no arquivo de output.log.  

#### <a name="packages-in-machine-learning-studio-classic"></a>Pacotes no Machine Learning Studio (clássico)

O Studio vem com mais de 350 pacotes de linguagem R pré-instalados. Você pode usar o código a seguir no módulo [Executar Script R][execute-r-script] para recuperar uma lista dos pacotes pré-instalados.

```R
data.set <- data.frame(installed.packages())
maml.mapOutputPort("data.set")
```

Se você não entender a última linha do código no momento, continue lendo. No restante deste artigo, discutiremos extensivamente o uso do R no ambiente Studio (clássico).

### <a name="introduction-to-rstudio"></a>Introdução ao RStudio

RStudio é um IDE amplamente usado para R. Usarei o RStudio para editar, testar e depurar alguns dos códigos R usados neste guia. Depois que o código R é testado e pronto, você pode simplesmente recortar e colar do editor RStudio em um módulo de [execução de script R][execute-r-script] Machine Learning Studio (clássico).  

Se você não tiver a linguagem de programação R instalada em seu computador desktop, recomendo que você faça isso agora. Downloads gratuitos de linguagem R de software livre estão disponíveis na CRAN (rede de arquivos de R) abrangente [https://www.r-project.org/](https://www.r-project.org/)em. Há downloads disponíveis para Windows, Mac OS e Linux/UNIX. Escolha um espelho próximo e siga as instruções de download. Além disso, CRAN contém uma grande quantidade de pacotes de manipulação de dados e análise úteis.

Se você for novo no RStudio, você deve baixar e instalar a versão para desktop. Você pode encontrar os downloads do RStudio para Windows, Mac OS e Linux/UNIX em http://www.rstudio.com/products/RStudio/. Siga as instruções fornecidas para instalar o RStudio em seu computador desktop.  

Um tutorial de introdução ao RStudio está disponível em [usando o IDE do RStudio](https://support.rstudio.com/hc/sections/200107586-Using-RStudio).

Eu fornece algumas informações adicionais sobre como usar o RStudio no [guia para a documentação do RStudio](#appendixa) abaixo.  

## <a name="get-data-in-and-out-of-the-execute-r-script-module"></a><a id="scriptmodule"></a>Obter dados de entrada e saída no módulo Executar Script R

Nesta seção, discutiremos como você obtém dados de entrada e saída no módulo [Executar Script R][execute-r-script]. Analisaremos como lidar com vários tipos de dados de leitura de entrada e saída do módulo [Executar Script R][execute-r-script].

O código completo para esta seção está em [MachineLearningSamples-notebooks/Studio-Samples](https://github.com/Azure-Samples/MachineLearningSamples-Notebooks/tree/master/studio-samples).

### <a name="load-and-check-data-in-machine-learning-studio-classic"></a>Carregar e verificar dados em Machine Learning Studio (clássico)

#### <a name="load-the-dataset"></a><a id="loading"></a>Carregar o conjunto de dados

Vamos começar carregando o arquivo **csdairydata. csv** em Azure Machine Learning Studio (clássico).

1. Inicie seu ambiente de Azure Machine Learning Studio (clássico).
1. Selecione **+ novo** na parte inferior esquerda da tela e selecione **conjunto**de espaço.
1. Selecione **Do Arquivo Local**, e **Procurar** para selecionar o arquivo.
1. Verifique se você selecionou **Arquivo CSV genérico com cabeçalho (.csv)** como o tipo do conjunto de dados.
1. Selecione a marca de seleção.
1. Depois que o conjunto de um for carregado, você deverá ver o novo conjunto de DataSet selecionando a guia **DataSets** .  

#### <a name="create-an-experiment"></a>Criar uma experiência

Agora que temos alguns dados em Machine Learning Studio (clássico), precisamos criar um experimento para fazer a análise.  

1. Selecione **+ novo** na parte inferior esquerda e selecione **experimento**e **experimento em branco**.
1. Você pode nomear o seu teste selecionando e modificando o título **Teste criado em...** no início da página. Por exemplo, alterando-o para **Análise da AC**.
1. À esquerda da página de teste, expanda **Conjuntos de Dados Salvos** e **Meus Conjuntos de Dados**. Você deve ver o **cadairydata.csv** que carregou anteriormente.
1. Arraste e solte o **conjunto de dados csdairydata.csv** no teste.
1. Na caixa **Pesquisar itens de teste** na parte superior do painel à esquerda, digite [Executar Script R][execute-r-script]. O módulo irá aparecer na lista de pesquisa.
1. Arraste e solte o módulo [Executar Script R][execute-r-script] em seu palete.  
1. Conecte a saída do **conjunto de dados csdairydata.csv** à entrada à esquerda (**Dataset1**) de [Executar Script R][execute-r-script].
1. **Não se esqueça de selecionar ' salvar '!**  

Agora seu teste deve ser similar a Figura 3.

![O teste de Análise de Produtos Derivados do Leite da Califórnia, com o conjunto de dados e o módulo Executar Script R](./media/r-quickstart/fig3.png)

*Figura 3. O teste Análise de produtos derivados de leite da Califórnia com o conjunto de dados e o módulo Executar Script R.*

#### <a name="check-on-the-data"></a>Verificar os dados

Vamos dar uma olhada nos dados que carregou em nosso teste. No experimento, selecione a saída do conjunto de **cadairydata. csv** e selecione **Visualizar**. Você deve ver algo semelhante à Figura 4.  

![Resumo do conjunto de dados cadairydata.csv](./media/r-quickstart/fig4.png)

*Figura 4. Resumo de conjunto de dados cadairydata.csv.*

Nessa exibição, vemos muitas informações úteis. Podemos ver as primeiras linhas do conjunto de dados. Se selecionarmos uma coluna, a seção Estatísticas mostra mais informações sobre a coluna. Por exemplo, a linha tipo de recurso mostra quais tipos de dados Azure Machine Learning Studio (clássico) atribuídos à coluna. Dar uma olhada rápida como esta é boa uma verificação de integridade para ser feita antes de começar qualquer trabalho sério.

### <a name="first-r-script"></a>Primeiro script R

Vamos criar um primeiro script R simples para fazer experimentos em Azure Machine Learning Studio (clássico). Criei e testei o seguinte script no RStudio:  

```R
## Only one of the following two lines should be used
## If running in Machine Learning Studio (classic), use the first line with maml.mapInputPort()
## If in RStudio, use the second line with read.csv()
cadairydata <- maml.mapInputPort(1)
# cadairydata  <- read.csv("cadairydata.csv", header = TRUE, stringsAsFactors = FALSE)
str(cadairydata)
pairs(~ Cotagecheese.Prod + Icecream.Prod + Milk.Prod + N.CA.Fat.Price, data = cadairydata)
## The following line should be executed only when running in
## Azure Machine Learning Studio (classic)
maml.mapOutputPort('cadairydata')
```

Agora preciso transferir esse script para Azure Machine Learning Studio (clássico). Eu poderia simplesmente recortar e colar. No entanto, nesse caso, eu vou transferir o meu script R por meio de um arquivo zip.

### <a name="data-input-to-the-execute-r-script-module"></a>Entrada de dados para o módulo Executar Script R

Vejamos as entradas do módulo [Executar Script R][execute-r-script]. Neste exemplo, vamos ler dados de laticínios da Califórnia no módulo [Executar Script R][execute-r-script].  

Há três entradas possíveis para o módulo [Executar Script R][execute-r-script]. Você pode usar qualquer uma ou todas essas entradas, dependendo do seu aplicativo. Também é totalmente aceitável usar um script R que não receba entrada.  

Vamos examinar cada uma dessa entradas, da esquerda para a direita. Você pode ver os nomes de cada uma das entradas colocando o cursor sobre a entrada e lendo a dica de ferramenta.  

#### <a name="script-bundle"></a>Pacote de script

A entrada de Pacote de Script permite que você passe o conteúdo de um arquivo zip para o módulo [Executar Script R][execute-r-script]. Você pode usar um dos comandos a seguir para ler o conteúdo do arquivo zip em seu código R.

```R
source("src/yourfile.R") # Reads a zipped R script
load("src/yourData.rdata") # Reads a zipped R data file
```

> [!NOTE]
> Azure Machine Learning Studio (clássico) trata os arquivos no zip como se eles estivessem no diretório src/, portanto, você precisa prefixar os nomes de arquivo com esse nome de diretório. Por exemplo, se o zip contiver os arquivos `yourfile.R` e `yourData.rdata` na raiz, aborde-os como `src/yourfile.R` e `src/yourData.rdata` ao usar `source` e `load`.

Já discutimos o carregamento de DataSets em [Load the DataSet](#loading). Após criar e testar o script R mostrado na seção anterior, faça o seguinte:

1. Salve o script R em um arquivo .R. Eu chamo meu arquivo script "simpleplot.R". Este é o conteúdo.

   ```R
   ## Only one of the following two lines should be used
   ## If running in Machine Learning Studio (classic), use the first line with maml.mapInputPort()
   ## If in RStudio, use the second line with read.csv()
   cadairydata <- maml.mapInputPort(1)
   # cadairydata  <- read.csv("cadairydata.csv", header = TRUE, stringsAsFactors = FALSE)
   str(cadairydata)
   pairs(~ Cotagecheese.Prod + Icecream.Prod + Milk.Prod + N.CA.Fat.Price, data = cadairydata)
   ## The following line should be executed only when running in
   ## Azure Machine Learning Studio (classic)
   maml.mapOutputPort('cadairydata')
   ```

1. Crie um arquivo zip e copie o script no arquivo zip. No Windows, você pode clicar com o botão direito do mouse no arquivo e selecionar **Enviar para**e, em seguida, **pasta compactada**. Isso criará um novo arquivo zip contendo o arquivo "simpleplot.R".

1. Adicione o arquivo aos **conjuntos de valores** em Azure Machine Learning Studio (clássico), especificando o tipo como **zip**. Agora você deve ver o arquivo zip em seus conjuntos de dados.

1. Arraste e solte o arquivo zip de **conjuntos de valores** na **tela ml Studio (clássica)**.

1. Conecte a saída do ícone **dados de zip** à entrada do **Pacote de Scripts** do módulo [Executar Script R][execute-r-script].

1. Digite a função `source()` com o nome do arquivo zip na janela de código do módulo [Executar Script R][execute-r-script]. No meu caso, digitei `source("src/simpleplot.R")`.  

1. Certifique-se de selecionar **salvar**.

Uma vez concluídas essas etapas, o módulo [Executar Script R][execute-r-script] executará o script R no arquivo zip quando o experimento for executado. Agora seu teste deve ser semelhante à Figura 5.

![Teste usando o script de R compactado](./media/r-quickstart/fig6.png)

*Figura 5. Experimente usar o R script compactado.*

#### <a name="dataset1"></a>Dataset1

Você pode passar uma tabela retangular de dados para seu código R usando a entrada Dataset1. Em nosso script simples, a função `maml.mapInputPort(1)` lê os dados da porta 1. Esses dados são então atribuídos a um nome de variável de quadro de dados em seu código. Em nosso script simples a primeira linha de código realiza a atribuição.

```R
cadairydata <- maml.mapInputPort(1)
```

Execute o experimento selecionando o botão **executar** . Quando a execução for concluída, selecione o módulo [Executar script R][execute-r-script] e, em seguida, selecione **Exibir log de saída** no painel Propriedades. Uma nova página deve aparecer em seu navegador, exibindo o conteúdo do arquivo output.log. Ao rolar para baixo, você deve ver algo como o seguinte exemplo:

    [ModuleOutput] InputDataStructure
    [ModuleOutput]
    [ModuleOutput] {
    [ModuleOutput]  "InputName":Dataset1
    [ModuleOutput]  "Rows":228
    [ModuleOutput]  "Cols":9
    [ModuleOutput]  "ColumnTypes":System.Int32,3,System.Double,5,System.String,1
    [ModuleOutput] }

Mais adiante na página, há informações mais detalhadas sobre as colunas, que parecerão com o seguinte.

    [ModuleOutput] [1] "Loading variable port1..."
    [ModuleOutput]
    [ModuleOutput] 'data.frame':    228 obs. of  9 variables:
    [ModuleOutput]
    [ModuleOutput]  $ Column 0         : int  1 2 3 4 5 6 7 8 9 10 ...
    [ModuleOutput]
    [ModuleOutput]  $ Year.Month       : num  1995 1995 1995 1995 1995 ...
    [ModuleOutput]
    [ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
    [ModuleOutput]
    [ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
    [ModuleOutput]
    [ModuleOutput]  $ Month            : chr  "Jan" "Feb" "Mar" "Apr" ...
    [ModuleOutput]
    [ModuleOutput]  $ Cotagecheese.Prod: num  4.37 3.69 4.54 4.28 4.47 ...
    [ModuleOutput]
    [ModuleOutput]  $ Icecream.Prod    : num  51.6 56.1 68.5 65.7 73.7 ...
    [ModuleOutput]
    [ModuleOutput]  $ Milk.Prod        : num  2.11 1.93 2.16 2.13 2.23 ...
    [ModuleOutput]
    [ModuleOutput]  $ N.CA.Fat.Price   : num  0.98 0.892 0.892 0.897 0.897 ...

Esses resultados são como o esperado, com 228 observações e 9 colunas no dataframe. Podemos ver os nomes de coluna, o tipo de dados R e um exemplo de cada coluna.

> [!NOTE]
> Essa mesma saída impressa está convenientemente disponível na saída do Dispositivo R do módulo [Executar Script R][execute-r-script]. Discutiremos as saídas do módulo [Executar Script R][execute-r-script] na próxima seção.  

#### <a name="dataset2"></a>Dataset2

O comportamento da entrada do Dataset2 é idêntico ao do Dataset1. Usando essa entrada, você pode passar uma segunda tabela retangular de dados para o seu código R. A função `maml.mapInputPort(2)`, com o argumento 2, é usada para passar esses dados.  

### <a name="execute-r-script-outputs"></a>Execute as saídas do Script R

#### <a name="output-a-dataframe"></a>Exibir um dataframe

Você pode exibir o conteúdo de um dataframe R como tabela retangular por meio da porta de resultados do Dataset1 usando a função `maml.mapOutputPort()` . Em nosso script R simples, isso é realizado pela seguinte linha:

```
maml.mapOutputPort('cadairydata')
```

Depois de executar o experimento, selecione a porta de saída dataSet1 do resultado e, em seguida, selecione **Visualizar**. Você deve ver algo como na Figura 6.

![A visualização da saída dos dados de derivados do leite da Califórnia](./media/r-quickstart/fig7.png)

*Figura 6. A visualização da saída dos dados dos derivados de leite da Califórnia.*

Esta saída parece idêntica à entrada, exatamente como se esperava.  

### <a name="r-device-output"></a>Saída do Dispositivo R

A saída do Dispositivo do módulo [Executar Script R][execute-r-script] contém mensagens e saída de gráficos. As duas mensagens de erro padrão e de saída padrão de R são enviadas para a porta de saída do dispositivo R.  

Para exibir a saída do dispositivo R, selecione a porta e, em seguida, em **Visualizar**. Podemos ver a saída padrão e o erro padrão do script R na Figura 7.

![Saída padrão e erro padrão da porta do Dispositivo R](./media/r-quickstart/fig8.png)

*Figura 7. Saída padrão e erro padrão da porta do Dispositivo R.*

Rolando para baixo podemos ver a saída de gráficos do nosso script R na Figura 8.  

![Saída de gráficos da porta do Dispositivo R](./media/r-quickstart/fig9.png)

*Figura 8. Saída de gráficos da porta do Dispositivo R.*  

## <a name="data-filtering-and-transformation"></a><a id="filtering"></a>Filtragem de dados e transformação

Nesta seção, vamos executar alguns dados básicos de filtragem e operações de transformação nos dados de produtos derivados de leite da Califórnia. No final desta seção, teremos dados em um formato adequado para a criação de um modelo de análise.  

Mais especificamente, nesta seção vamos executar várias tarefas de transformação e de limpeza de dados comuns: transformação de tipo, filtragem por dataframes, adição de novas colunas computadas e transformações de valor. Este histórioco deve ajudá-lo a lidar com as diversas variações encontradas em problemas do mundo real.

O código R completo para esta seção está disponível em [MachineLearningSamples-notebooks/Studio-Samples](https://github.com/Azure-Samples/MachineLearningSamples-Notebooks/tree/master/studio-samples).

### <a name="type-transformations"></a>Transformações de tipo

Agora que podemos ler os dados dos laticínios da Califórnia no código R no módulo [Executar Script R][execute-r-script], precisamos garantir que os dados nas colunas tenham o tipo e o formato desejados.  

R é uma linguagem tipificada dinamicamente, o que significa que os tipos de dados são forçados entre si quando necessário. Os tipos de dados atômicos em R incluem numérico, lógico e de caractere. O fator de tipo é usado para o armazenamento de dados categóricos de forma compacta. Você pode encontrar muito mais informações sobre os tipos de dados nas referências em [leituras adicionais](#appendixb) abaixo.

Quando dados tabulares são lidos em R de a partir de uma fonte externa, é sempre uma boa idéia verificar os tipos resultantes nas colunas. Você pode desejar uma coluna de tipo de caractere, mas em muitos casos isso aparecerá como fator ou vice-versa. Em outros casos, uma coluna que você acha que deve ser numérica é representada por dados de caracteres, por exemplo, '1,23' em vez de 1,23 como número de ponto flutuante.  

Felizmente, é fácil converter de um tipo para outro, desde que o mapeamento seja possível. Por exemplo, você não pode converter “Nevada” para um valor numérico, mas você pode convertê-la em um fator (variável categórica). Um outro exemplo, você pode converter um numérico 1 em um caractere “1” ou um fator.  

A sintaxe para qualquer uma dessas conversões é simples: `as.datatype()`. Essas funções de conversão de tipo incluem os itens a seguir.

* `as.numeric()`
* `as.character()`
* `as.logical()`
* `as.factor()`

Observando os tipos de dados das colunas usadas como entrada na seção anterior: todas as colunas são do tipo numérico, exceto a coluna rotulada como 'Month', que é do tipo de caracteres. Vamos converter isso em um fator e os resultados do teste.  

Excluí a linha que criou a matriz de dispersão e adicionei uma linha para converter a coluna 'Month' em um fator. Em meu teste, vou simplesmente recortar e colar o código R na janela de código do módulo [Executar Script R][execute-r-script]. Você também pode atualizar o arquivo zip e carregá-lo para Azure Machine Learning Studio (clássico), mas isso requer várias etapas.  

```R
## Only one of the following two lines should be used
## If running in Machine Learning Studio (classic), use the first line with maml.mapInputPort()
## If in RStudio, use the second line with read.csv()
cadairydata <- maml.mapInputPort(1)
# cadairydata  <- read.csv("cadairydata.csv", header = TRUE, stringsAsFactors = FALSE)
## Ensure the coding is consistent and convert column to a factor
cadairydata$Month <- as.factor(cadairydata$Month)
str(cadairydata) # Check the result
## The following line should be executed only when running in
## Azure Machine Learning Studio (classic)
maml.mapOutputPort('cadairydata')
```

Vamos executar esse código e examinar o log de saída para o script R. Os dados relevantes do log são mostrados na Figura 9.

    [ModuleOutput] [1] "Loading variable port1..."
    [ModuleOutput] 
    [ModuleOutput] 'data.frame':    228 obs. of  9 variables:
    [ModuleOutput] 
    [ModuleOutput]  $ Column 0         : int  1 2 3 4 5 6 7 8 9 10 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Year.Month       : num  1995 1995 1995 1995 1995 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month            : Factor w/ 14 levels "Apr","April",..: 6 5 9 1 11 8 7 3 14 13 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Cotagecheese.Prod: num  4.37 3.69 4.54 4.28 4.47 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Icecream.Prod    : num  51.6 56.1 68.5 65.7 73.7 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Milk.Prod        : num  2.11 1.93 2.16 2.13 2.23 ...
    [ModuleOutput] 
    [ModuleOutput]  $ N.CA.Fat.Price   : num  0.98 0.892 0.892 0.897 0.897 ...
    [ModuleOutput] 
    [ModuleOutput] [1] "Saving variable  cadairydata  ..."
    [ModuleOutput] 
    [ModuleOutput] [1] "Saving the following item(s):  .maml.oport1"

*Figura 9. Resumo do dataframe com uma variável de fator.*

O tipo de Mês deve agora indicar '**Fator c/ 14 níveis**'. Isso é um problema, pois há apenas 12 meses no ano. Você também pode verificar se o tipo em **Visualizar** da porta do Conjunto de dados de resultado é “**Categórico**”.

O problema é que a coluna “Month” não foi codificada sistematicamente. Em alguns casos, um mês é chamado de abril e, em outros, é abreviado como abr. Podemos resolver esse problema cortando a cadeia de caracteres para 3 caracteres. Agora, a linha de código deve ser assim:

```R
## Ensure the coding is consistent and convert column to a factor
cadairydata$Month <- as.factor(substr(cadairydata$Month, 1, 3))
```

Execute novamente o teste e exiba o log de saída. Os resultados esperados são mostrados na Figura 10.  

    [ModuleOutput] [1] "Loading variable port1..."
    [ModuleOutput] 
    [ModuleOutput] 'data.frame':    228 obs. of  9 variables:
    [ModuleOutput] 
    [ModuleOutput]  $ Column 0         : int  1 2 3 4 5 6 7 8 9 10 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Year.Month       : num  1995 1995 1995 1995 1995 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month            : Factor w/ 12 levels "Apr","Aug","Dec",..: 5 4 8 1 9 7 6 2 12 11 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Cotagecheese.Prod: num  4.37 3.69 4.54 4.28 4.47 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Icecream.Prod    : num  51.6 56.1 68.5 65.7 73.7 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Milk.Prod        : num  2.11 1.93 2.16 2.13 2.23 ...
    [ModuleOutput] 
    [ModuleOutput]  $ N.CA.Fat.Price   : num  0.98 0.892 0.892 0.897 0.897 ...
    [ModuleOutput] 
    [ModuleOutput] [1] "Saving variable  cadairydata  ..."
    [ModuleOutput] 
    [ModuleOutput] [1] "Saving the following item(s):  .maml.oport1"

*Figura 10. Resumo do dataframe com o número correto de níveis de fator.*

Nossa variável fator agora tem os 12 níveis desejados.

### <a name="basic-data-frame-filtering"></a>Filtragem de dataframe básico

Os dataframes R oferecem suporte a recursos avançados de filtragem. Conjuntos de dados podem ser subdivididos usando filtros lógicos em linhas ou colunas. Em muitos casos, serão necessários critérios complexos de filtro. As referências em [leitura adicional](#appendixb) abaixo contêm exemplos extensos de filtragem de quadros de molduras.  

Há algumas filtragens que devemos fazer em nosso conjunto de dados. Se examinar as colunas no dataframe cadariydata, você verá duas colunas desnecessárias. A primeira coluna contém apenas um número de linha que não é muito útil. A segunda coluna, Year.Month, contém informações redundantes. Podemos facilmente excluir essas colunas usando o código R a seguir.

> [!NOTE]
> De agora em diante nesta seção, só mostrarei o código adicional que estou adicionando ao módulo [Executar Script R][execute-r-script]. Adicionarei cada nova linha **antes** da função `str()`. Eu uso essa função para verificar meus resultados em Azure Machine Learning Studio (clássico).

Adiciono a linha a seguir a meu código R no módulo [Executar Script R][execute-r-script].

```R
# Remove two columns we do not need
cadairydata <- cadairydata[, c(-1, -2)]
```

Execute esse código em seu teste e verifique o resultado no log de saída. Esses resultados são mostrados na Figura 11.

    [ModuleOutput] [1] "Loading variable port1..."
    [ModuleOutput] 
    [ModuleOutput] 'data.frame':    228 obs. of  7 variables:
    [ModuleOutput] 
    [ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month            : Factor w/ 12 levels "Apr","Aug","Dec",..: 5 4 8 1 9 7 6 2 12 11 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Cotagecheese.Prod: num  4.37 3.69 4.54 4.28 4.47 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Icecream.Prod    : num  51.6 56.1 68.5 65.7 73.7 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Milk.Prod        : num  2.11 1.93 2.16 2.13 2.23 ...
    [ModuleOutput] 
    [ModuleOutput]  $ N.CA.Fat.Price   : num  0.98 0.892 0.892 0.897 0.897 ...
    [ModuleOutput] 
    [ModuleOutput] [1] "Saving variable  cadairydata  ..."
    [ModuleOutput] 
    [ModuleOutput] [1] "Saving the following item(s):  .maml.oport1"

*Figura 11. Resumo do dataframe com duas colunas removidas.*

Boas notícias! Podemos obter os resultados esperados.

### <a name="add-a-new-column"></a>Adicionar uma nova coluna

Para criar modelos de série de tempo é conveniente ter uma coluna que contenha os meses desde o início da série de tempo. Criaremos uma nova coluna “Month.Count”.

Para ajudar a organizar o código, vamos criar nossa primeira função simples, `num.month()`. Em seguida, aplicaremos essa função para criar uma nova coluna no dataframe. O novo código é indicado a seguir.

```R
## Create a new column with the month count
## Function to find the number of months from the first
## month of the time series
num.month <- function(Year, Month) {
  ## Find the starting year
  min.year  <- min(Year)

  ## Compute the number of months from the start of the time series
  12 * (Year - min.year) + Month - 1
}

## Compute the new column for the dataframe
cadairydata$Month.Count <- num.month(cadairydata$Year, cadairydata$Month.Number)
```

Agora execute o teste atualizado e use o log de saída para exibir os resultados. Esses resultados são mostrados na Figura 12.

    [ModuleOutput] [1] "Loading variable port1..."
    [ModuleOutput] 
    [ModuleOutput] 'data.frame':    228 obs. of  8 variables:
    [ModuleOutput] 
    [ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month            : Factor w/ 12 levels "Apr","Aug","Dec",..: 5 4 8 1 9 7 6 2 12 11 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Cotagecheese.Prod: num  4.37 3.69 4.54 4.28 4.47 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Icecream.Prod    : num  51.6 56.1 68.5 65.7 73.7 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Milk.Prod        : num  2.11 1.93 2.16 2.13 2.23 ...
    [ModuleOutput] 
    [ModuleOutput]  $ N.CA.Fat.Price   : num  0.98 0.892 0.892 0.897 0.897 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month.Count      : num  0 1 2 3 4 5 6 7 8 9 ...
    [ModuleOutput] 
    [ModuleOutput] [1] "Saving variable  cadairydata  ..."
    [ModuleOutput] 
    [ModuleOutput] [1] "Saving the following item(s):  .maml.oport1"

*Figura 12. Resumo do dataframe com a coluna adicional.*

Parece que tudo está funcionando. Temos a nova coluna com os valores esperados em nosso dataframe

### <a name="value-transformations"></a>Transformações de valor

Nesta seção, vamos executar algumas transformações simples nos valores de algumas das colunas de nosso dataframe. A linguagem R suporta transformações de valor quase que de forma arbitrária. As referências em [leitura adicional](#appendixb) abaixo contêm exemplos extensivos.

Se examinar os valores nos resumos de nosso datafame, você notará algo estranho. Há mais sorvetes do que leite produzido na Califórnia? Não, certamente não, pois isso não faz sentido, além de triste pois alguns de nós amam sorvetes. As unidades são diferentes. O preço está em unidades de libras dos EUA, o leite está em unidades de um milhão de libras dos EUA, o sorvete está em unidades de 1.000 galões dos EUA e o queijo cottage está em unidades de 1.000 libras dos EUA. Supondo que o sorvete pese aproximadamente 6,5 libras por galão, podemos facilmente fazer a multiplicação a fim de converter esses valores para que eles estejam todos em unidades iguais de 1.000 libras.

Para nosso modelo de previsão, usamos um modelo de multiplicação para tendência e ajuste sazonal desses dados. Uma transformação de log nos permite usar um modelo linear, simplificando esse processo. Podemos aplicar a transformação de log na mesma função em que o multiplicador será aplicado.

No código a seguir, defino uma nova função, `log.transform()`, e a aplico às linhas que contêm os valores numéricos. A função R `Map()` é usada para aplicar a função `log.transform()` às colunas selecionadas do dataframe. `Map()` é semelhante a `apply()` mas permite mais de uma lista de argumentos para a função. Observe que uma lista de multiplicadores fornece o segundo argumento para a função `log.transform()` . A função `na.omit()` é usada como um pouco de limpeza, para garantir que não haja valores ausentes ou indefinidos no dataframe.

```R
log.transform <- function(invec, multiplier = 1) {
  ## Function for the transformation, which is the log
  ## of the input value times a multiplier

  warningmessages <- c("ERROR: Non-numeric argument encountered in function log.transform",
                       "ERROR: Arguments to function log.transform must be greate than zero",
                       "ERROR: Aggurment multiplier to funcition log.transform must be a scaler",
                       "ERROR: Invalid time seies value encountered in function log.transform"
                       )

  ## Check the input arguments
  if(!is.numeric(invec) | !is.numeric(multiplier)) {warning(warningmessages[1]); return(NA)}  
  if(any(invec < 0.0) | any(multiplier < 0.0)) {warning(warningmessages[2]); return(NA)}
  if(length(multiplier) != 1) {{warning(warningmessages[3]); return(NA)}}

  ## Wrap the multiplication in tryCatch
  ## If there is an exception, print the warningmessage to
  ## standard error and return NA
  tryCatch(log(multiplier * invec),
           error = function(e){warning(warningmessages[4]); NA})
}


## Apply the transformation function to the 4 columns
## of the dataframe with production data
multipliers  <- list(1.0, 6.5, 1000.0, 1000.0)
cadairydata[, 4:7] <- Map(log.transform, cadairydata[, 4:7], multipliers)

## Get rid of any rows with NA values
cadairydata <- na.omit(cadairydata)  
```

Muitas coisas estão acontecendo na função `log.transform()` . A maior parte do código verifica se há possíveis problemas com os argumentos ou lida com exceções, que ainda podem surgir durante os cálculos. Apenas algumas linhas desse código realmente fazem os cálculos.

O objetivo da programação de defesa é evitar a falha de uma única função que impeça que o processamento continue. Uma falha abrupta de uma análise de execução longa pode ser muito frustrante para os usuários. Para evitar essa situação, devem ser escolhidos valores de retorno padrão que limitem os danos ao processamento downstream. Uma mensagem também é emitida para alertar os usuários que algo deu errado.

Se você não está acostumado à programação defensiva em R, todo esse código pode parecer um pouco sobrecarregado. Eu o orientarei durante as etapas principais:

1. Um vetor de quatro mensagens é definido. Essas mensagens são usadas para comunicar informações sobre alguns dos possíveis erros e exceções que podem ocorrer com esse código.
2. Posso retornar um valor NA para cada caso. Há muitas outras possibilidades que podem ter menos efeitos colaterais. Eu poderia retornar um vetor de zeros ou o vetor de entrada original, por exemplo.
3. As verificações são executadas nos argumentos para a função. Em cada caso, se for detectado um erro, será retornado um valor padrão, e uma mensagem será produzida pela função `warning()`. Estou usando `warning()` em vez de `stop()`, pois essa segunda opção encerrará a execução, justamente o que estou tentando evitar. Observe que eu escrevi este código em um estilo de procedimento, pois nesse caso uma abordagem funcional me parece complexa e obscura.
4. As computações de log são encapsuladas em `tryCatch()` para que as exceções não causem uma interrupção abrupta no processamento. Sem `tryCatch()` , a maioria dos erros gerada pelas funções de R resulta em um sinal de interrupção, que faz exatamente isso.

Executar esse código R em seu teste e veja a saída impressa no output.log. Agora, você verá os valores transformados das quatro colunas no log, conforme mostrado na Figura 13.

    [ModuleOutput] [1] "Loading variable port1..."
    [ModuleOutput] 
    [ModuleOutput] 'data.frame':    228 obs. of  8 variables:
    [ModuleOutput] 
    [ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month            : Factor w/ 12 levels "Apr","Aug","Dec",..: 5 4 8 1 9 7 6 2 12 11 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Cotagecheese.Prod: num  1.47 1.31 1.51 1.45 1.5 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Icecream.Prod    : num  5.82 5.9 6.1 6.06 6.17 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Milk.Prod        : num  7.66 7.57 7.68 7.66 7.71 ...
    [ModuleOutput] 
    [ModuleOutput]  $ N.CA.Fat.Price   : num  6.89 6.79 6.79 6.8 6.8 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month.Count      : num  0 1 2 3 4 5 6 7 8 9 ...
    [ModuleOutput] 
    [ModuleOutput] [1] "Saving variable  cadairydata  ..."
    [ModuleOutput] 
    [ModuleOutput] [1] "Saving the following item(s):  .maml.oport1"

*Figura 13. Resumo dos valores transformados no dataframe.*

Vemos que os valores foram transformados. Agora, a produção de leite excede bastante a produção de todos os outros produtos derivados do leite, lembrando que agora estamos analisando uma escala logarítmica.

Neste momento, os dados são limpos e estamos prontos para a modelagem. Observando o resumo da visualização para a saída do Conjunto de Dados de Resultado de nosso módulo [Executar Script R][execute-r-script], você verá que a coluna “Month” é “Categorical”, com 12 valores exclusivos, novamente, como desejávamos.

## <a name="time-series-objects-and-correlation-analysis"></a><a id="timeseries"></a>Análise de correlação e objetos de série temporal

Nesta seção vamos explorar alguns objetos básicos de série de tempo de R e vamos analisar as correlações entre algumas das variáveis. Nosso objetivo é obter um dataframe de saída que contenha as informações de correlação de pares em várias defasagens.

O código R completo para esta seção está em [MachineLearningSamples-notebooks/Studio-Samples](https://github.com/Azure-Samples/MachineLearningSamples-Notebooks/tree/master/studio-samples).

### <a name="time-series-objects-in-r"></a>Objetos de série temporal em R

Como já mencionado, as série de tempo são uma série de valores de dados indexados por tempo. Objetos de série de tempo R são usados para criar e gerenciar o índice de tempo. Há diversas vantagens em usar objetos de série de tempo. Os objetos de série temporal evitam que você tenha que lidar com os vários detalhes do gerenciamento dos valores de índice da série temporal que são encapsulados no objeto. Além disso, os objetos de série de tempo permitem que você use vários métodos de série de tempo para criar gráficos, imprimir, modelar etc.

A classe de série de tempo POSIXct é comumente usada e é relativamente simples. Essa classe de série de tempo mede o tempo a partir do início da época, 1º de janeiro de 1970. Vamos usar objetos de série de tempo POSIXct neste exemplo. Outras classes de objeto de série de tempo amplamente utilizados em R incluem zoo e xts, série temporal extensível.

### <a name="time-series-object-example"></a>Exemplo de objeto de série temporal

Vamos começar com o nosso exemplo. Arraste e solte um **novo módulo ** [Executar Script R][execute-r-script] em seu experimento. Conecte a porta de saída do Conjunto de Dados de Resultado 1 do módulo [Executar Script R][execute-r-script] existente à porta de entrada do Conjunto de Dados de Resultado 1 do novo módulo [Executar Script R][execute-r-script].

Como fiz nos primeiros exemplos, ao progredirmos no exemplo, em alguns pontos vou mostrar apenas as linhas adicionais incrementais do código R em cada etapa.  

#### <a name="reading-the-dataframe"></a>Lendo o dataframe

Como primeira etapa, vamos ler um dataframe e verificar se obtemos os resultados esperados. O código a seguir deve ser suficiente.

```R
# Comment the following if using RStudio
cadairydata <- maml.mapInputPort(1)
str(cadairydata) # Check the results
```

Agora, execute o teste. O log da nova forma Executar Script R deve parecer com a Figura 14.

    [ModuleOutput] [1] "Loading variable port1..."
    [ModuleOutput] 
    [ModuleOutput] 'data.frame':    228 obs. of  8 variables:
    [ModuleOutput] 
    [ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month            : Factor w/ 12 levels "Apr","Aug","Dec",..: 5 4 8 1 9 7 6 2 12 11 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Cotagecheese.Prod: num  1.47 1.31 1.51 1.45 1.5 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Icecream.Prod    : num  5.82 5.9 6.1 6.06 6.17 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Milk.Prod        : num  7.66 7.57 7.68 7.66 7.71 ...
    [ModuleOutput] 
    [ModuleOutput]  $ N.CA.Fat.Price   : num  6.89 6.79 6.79 6.8 6.8 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month.Count      : num  0 1 2 3 4 5 6 7 8 9 ...

*Figura 14. Resumo do dataframe no módulo Executar Script R.*

Esses dados são dos tipos e formato esperados. Observe que a coluna “Month” é do fator tipo e tem o número esperado de níveis.

#### <a name="creating-a-time-series-object"></a>Criando um objeto de série temporal

Precisamos adicionar um objeto de série de tempo ao nosso dataframe. Substitua o código atual pelo exemplo a seguir, que adiciona uma nova coluna da classe POSIXct.

```R
# Comment the following if using RStudio
cadairydata <- maml.mapInputPort(1)

## Create a new column as a POSIXct object
Sys.setenv(TZ = "PST8PDT")
cadairydata$Time <- as.POSIXct(strptime(paste(as.character(cadairydata$Year), "-", as.character(cadairydata$Month.Number), "-01 00:00:00", sep = ""), "%Y-%m-%d %H:%M:%S"))

str(cadairydata) # Check the results
```

Agora, verifique o log. Deve se parecer com a Figura 15.

    [ModuleOutput] [1] "Loading variable port1..."
    [ModuleOutput] 
    [ModuleOutput] 'data.frame':    228 obs. of  9 variables:
    [ModuleOutput] 
    [ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month            : Factor w/ 12 levels "Apr","Aug","Dec",..: 5 4 8 1 9 7 6 2 12 11 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Cotagecheese.Prod: num  1.47 1.31 1.51 1.45 1.5 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Icecream.Prod    : num  5.82 5.9 6.1 6.06 6.17 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Milk.Prod        : num  7.66 7.57 7.68 7.66 7.71 ...
    [ModuleOutput] 
    [ModuleOutput]  $ N.CA.Fat.Price   : num  6.89 6.79 6.79 6.8 6.8 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month.Count      : num  0 1 2 3 4 5 6 7 8 9 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Time             : POSIXct, format: "1995-01-01" "1995-02-01" ...

*Figura 15. Resumo do dataframe com um objeto de série temporal.*

Podemos ver o resumo a nova coluna é de fato da classe POSIXct.

### <a name="exploring-and-transforming-the-data"></a>Explorando e transformando os dados

Vamos explorar algumas das variáveis deste conjunto de dados. Uma matriz de scatterplot é uma boa maneira para darmos uma olhada rápida. Estou substituindo a função `str()` no código R anterior pela linha a seguir.

```R
pairs(~ Cotagecheese.Prod + Icecream.Prod + Milk.Prod + N.CA.Fat.Price, data = cadairydata, main = "Pairwise Scatterplots of dairy time series")
```

Execute esse código e veja o que acontece. O gráfico produzido na porta do dispositivo R deve parecer com a Figura 16.

![Matriz de dispersão de variáveis selecionadas](./media/r-quickstart/fig17.png)

*Figura 16. Matriz de dispersão de variáveis selecionadas.*

Há alguma estrutura estranha nas relações entre essas variáveis. Talvez isso surja de tendências nos dados e do fato de que não padronizamos as variáveis.

### <a name="correlation-analysis"></a>Análise de correlação

Para executar análise de correlação, precisamos padronizar e eliminar tendências das variáveis. Poderíamos simplesmente usar a função `scale()` de R, que centraliza e dimensiona variáveis. Essa função também pode ser executada mais rapidamente. No entanto, eu quero mostrar um exemplo de programação defensiva em R.

A função `ts.detrend()` abaixo executa ambas essas operações. As duas linhas de código a seguir eliminam os dados de tendência e, em seguida, padronizam os valores.

```R
ts.detrend <- function(ts, Time, min.length = 3){
  ## Function to de-trend and standardize a time series

  ## Define some messages if they are NULL  
  messages <- c('ERROR: ts.detrend requires arguments ts and Time to have the same length',
                'ERROR: ts.detrend requires argument ts to be of type numeric',
                paste('WARNING: ts.detrend has encountered a time series with length less than', as.character(min.length)),
                'ERROR: ts.detrend has encountered a Time argument not of class POSIXct',
                'ERROR: Detrend regression has failed in ts.detrend',
                'ERROR: Exception occurred in ts.detrend while standardizing time series in function ts.detrend'
  )
  # Create a vector of zeros to return as a default in some cases
  zerovec  <- rep(length(ts), 0.0)

  # The input arguments are not of the same length, return ts and quit
  if(length(Time) != length(ts)) {warning(messages[1]); return(ts)}

  # If the ts is not numeric, just return a zero vector and quit
  if(!is.numeric(ts)) {warning(messages[2]); return(zerovec)}

  # If the ts is too short, just return it and quit
  if((ts.length <- length(ts)) < min.length) {warning(messages[3]); return(ts)}

  ## Check that the Time variable is of class POSIXct
  if(class(cadairydata$Time)[[1]] != "POSIXct") {warning(messages[4]); return(ts)}

  ## De-trend the time series by using a linear model
  ts.frame  <- data.frame(ts = ts, Time = Time)
  tryCatch({ts <- ts - fitted(lm(ts ~ Time, data = ts.frame))},
           error = function(e){warning(messages[5]); zerovec})

  tryCatch( {stdev <- sqrt(sum((ts - mean(ts))^2))/(ts.length - 1)
             ts <- ts/stdev},
            error = function(e){warning(messages[6]); zerovec})

  ts
}  
## Apply the detrend.ts function to the variables of interest
df.detrend <- data.frame(lapply(cadairydata[, 4:7], ts.detrend, cadairydata$Time))

## Plot the results to look at the relationships
pairs(~ Cotagecheese.Prod + Icecream.Prod + Milk.Prod + N.CA.Fat.Price, data = df.detrend, main = "Pairwise Scatterplots of detrended standardized time series")
```

Muitas coisas estão acontecendo na função `ts.detrend()` . A maior parte do código verifica se há possíveis problemas com os argumentos ou lida com exceções, que ainda podem surgir durante os cálculos. Apenas algumas linhas desse código realmente fazem os cálculos.

Já abordamos um exemplo de programação defensiva em Transformações de valor. Os dois blocos de computação estão encapsulados em `tryCatch()`. Para alguns erros, faz sentido retornar ao vetor original de entrada e, em outros casos, retornar a um vetor de zeros.  

Observe que a regressão linear usada para a eliminação de tendência é uma regressão de uma série de tempo. A variável preditora é um objeto de série de tempo.  

Quando `ts.detrend()` é definido, aplicamos as variáveis de interesse em nosso dataframe. Devemos forçar a lista resultante criada por `lapply()` nos dados do dataframe usando `as.data.frame()`. Devido aos aspectos defensivos de `ts.detrend()`, se uma das variáveis não for processada, isso não impedirá o processamento correto das outras.  

A última linha do código cria um par scatterplot. Após a execução do código R, os resultados da dispersão são mostrados na Figura 17.

![Emparelhar a dispersão da série temporal padronizada e sem tendências](./media/r-quickstart/fig18.png)

*Figura 17. Emparelhe o scatterplot da séries de tempo padronizada e sem tendências.*

Você pode comparar esses resultados aos mostrados na Figura 16. Com a tendência removida e as variáveis padronizadas, vemos muito menos estrutura nas relações entre essas variáveis.

O código para calcular as correlações como objetos ccf R é indicado a seguir.

```R
## A function to compute pairwise correlations from a
## list of time series value vectors
pair.cor <- function(pair.ind, ts.list, lag.max = 1, plot = FALSE){
  ccf(ts.list[[pair.ind[1]]], ts.list[[pair.ind[2]]], lag.max = lag.max, plot = plot)
}

## A list of the pairwise indices
corpairs <- list(c(1,2), c(1,3), c(1,4), c(2,3), c(2,4), c(3,4))

## Compute the list of ccf objects
cadairycorrelations <- lapply(corpairs, pair.cor, df.detrend)  

cadairycorrelations
```

Executar esse código produz o log mostrado na Figura 18.

    [ModuleOutput] Loading objects:
    [ModuleOutput]   port1
    [ModuleOutput] [1] "Loading variable port1..."
    [ModuleOutput] [[1]]
    [ModuleOutput] 
    [ModuleOutput] 
    [ModuleOutput] Autocorrelations of series 'X', by lag
    [ModuleOutput] 
    [ModuleOutput] 
    [ModuleOutput]    -1     0     1 
    [ModuleOutput] 0.148 0.358 0.317 
    [ModuleOutput] 
    [ModuleOutput] 
    [ModuleOutput] [[2]]
    [ModuleOutput] 
    [ModuleOutput] 
    [ModuleOutput] Autocorrelations of series 'X', by lag
    [ModuleOutput] 
    [ModuleOutput] 
    [ModuleOutput]     -1      0      1 
    [ModuleOutput] -0.395 -0.186 -0.238 
    [ModuleOutput] 
    [ModuleOutput] 
    [ModuleOutput] [[3]]
    [ModuleOutput] 
    [ModuleOutput] 
    [ModuleOutput] Autocorrelations of series 'X', by lag
    [ModuleOutput] 
    [ModuleOutput] 
    [ModuleOutput]     -1      0      1 
    [ModuleOutput] -0.059 -0.089 -0.127 
    [ModuleOutput] 
    [ModuleOutput] 
    [ModuleOutput] [[4]]
    [ModuleOutput] 
    [ModuleOutput] 
    [ModuleOutput] Autocorrelations of series 'X', by lag
    [ModuleOutput] 
    [ModuleOutput] 
    [ModuleOutput]    -1     0     1 
    [ModuleOutput] 0.140 0.294 0.293 
    [ModuleOutput] 
    [ModuleOutput] 
    [ModuleOutput] [[5]]
    [ModuleOutput] 
    [ModuleOutput] 
    [ModuleOutput] Autocorrelations of series 'X', by lag
    [ModuleOutput] 
    [ModuleOutput] 
    [ModuleOutput]     -1      0      1 
    [ModuleOutput] -0.002 -0.074 -0.124 

*Figura 18. Lista de objetos ccf de análise de correlação emparelhados.*

Há um valor de correlação para cada intervalo. Nenhum desses valores de correlação é grande o suficiente para ser significativo. Podemos, portanto, concluir que é possível modelar cada variável de forma independente.

### <a name="output-a-dataframe"></a>Exibir um dataframe
Podemos ter calculado as correlações emparelhadas como uma lista de objetos ccf R. Isso apresenta um pequeno problema, pois a porta de saída do conjunto de dados resultado exige um dataframe. Além disso, o objeto ccf é uma lista e queremos apenas os valores no primeiro elemento dessa lista, as correlações em vários intervalos.

O código a seguir extrai os valores de intervalo da lista de objetos ccf, que também são listas.

```R
df.correlations <- data.frame(do.call(rbind, lapply(cadairycorrelations, '[[', 1)))

c.names <- c("correlation pair", "-1 lag", "0 lag", "+1 lag")
r.names  <- c("Corr Cot Cheese - Ice Cream",
              "Corr Cot Cheese - Milk Prod",
              "Corr Cot Cheese - Fat Price",
              "Corr Ice Cream - Mik Prod",
              "Corr Ice Cream - Fat Price",
              "Corr Milk Prod - Fat Price")

## Build a dataframe with the row names column and the
## correlation data frame and assign the column names
outframe <- cbind(r.names, df.correlations)
colnames(outframe) <- c.names
outframe


## WARNING!
## The following line works only in Azure Machine Learning Studio (classic)
## When running in RStudio, this code will result in an error
#maml.mapOutputPort('outframe')
```

A primeira linha de código é um pouco complicada, e algumas explicações podem ajudá-lo a entendê-la. Trabalhando de dentro para fora, temos o seguinte:

1. O operador '**[[**' com o argumento '**1**' seleciona o vetor de correlações nas defasagens do primeiro elemento da lista de objetos ccf.
2. A função `do.call()` aplica-se à função `rbind()` nos elementos de lista retornados por `lapply()`.
3. A função `data.frame()` converte o resultado produzido por `do.call()` em um dataframe.

Observe que os nomes das linhas estão na coluna do dataframe. Fazer isso preserva os nomes de linha quando eles são a saída de [Executar Script R][execute-r-script].

A execução do código produz o resultado mostrado na Figura 19 quando eu **Visualizar** a saída da porta do conjunto de dados do resultado. Os nomes de linha estão na primeira coluna, conforme o esperado.

![Resultados da saída da análise de correlação](./media/r-quickstart/fig20.png)

*Figura 19. Saída de resultados da análise de correlação.*

## <a name="time-series-example-seasonal-forecasting"></a><a id="seasonalforecasting"></a>Exemplo de série temporal: previsão sazonal

Nossos dados agora estão em um formato adequado para análise, e determinamos que não há correlação significativa entre as variáveis. Vamos continuar e criar um modelo de previsão de série de tempo. Usando esse modelo vamos prever a produção de leite da Califórnia para os 12 meses de 2013.

Nosso modelo de previsão terá dois componentes, um componente de tendência e um componente sazonal. A previsão concluída é o produto desses dois componentes. Esse tipo de modelo é conhecido como modelo de multiplicação. A alternativa é um modelo de adição. Já aplicamos uma transformação logarítmica às variáveis de interesse, o que torna essa análise manejável.

O código R completo para esta seção está em [MachineLearningSamples-notebooks/Studio-Samples](https://github.com/Azure-Samples/MachineLearningSamples-Notebooks/tree/master/studio-samples).

### <a name="creating-the-dataframe-for-analysis"></a>Criando o dataframe para análise

Comece adicionando um **novo módulo ** [Executar Script R][execute-r-script] ao seu experimento. Conecte a saída do **Conjunto de Dados de Resultado** do módulo [Executar Script R][execute-r-script] existente à entrada do **Conjunto de Dados 1** do novo módulo. O resultado deve ser semelhante a Figura 20.

![O teste com o novo módulo Executar Script R adicionado](./media/r-quickstart/fig21.png)

*Figura 20. O teste com o novo módulo Executar Script R adicionado.*

Como a análise de correlação que acabamos de concluir, precisamos adicionar uma coluna com um objeto de série de tempo POSIXct. O código a seguir fará exatamente isso.

```R
# If running in Machine Learning Studio (classic), uncomment the first line with maml.mapInputPort()
cadairydata <- maml.mapInputPort(1)

## Create a new column as a POSIXct object
Sys.setenv(TZ = "PST8PDT")
cadairydata$Time <- as.POSIXct(strptime(paste(as.character(cadairydata$Year), "-", as.character(cadairydata$Month.Number), "-01 00:00:00", sep = ""), "%Y-%m-%d %H:%M:%S"))

str(cadairydata)
```

Execute esse código e examine o log. O resultado deve ser semelhante a Figura 21.

    [ModuleOutput] [1] "Loading variable port1..."
    [ModuleOutput] 
    [ModuleOutput] 'data.frame':    228 obs. of  9 variables:
    [ModuleOutput] 
    [ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month            : Factor w/ 12 levels "Apr","Aug","Dec",..: 5 4 8 1 9 7 6 2 12 11 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Cotagecheese.Prod: num  1.47 1.31 1.51 1.45 1.5 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Icecream.Prod    : num  5.82 5.9 6.1 6.06 6.17 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Milk.Prod        : num  7.66 7.57 7.68 7.66 7.71 ...
    [ModuleOutput] 
    [ModuleOutput]  $ N.CA.Fat.Price   : num  6.89 6.79 6.79 6.8 6.8 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month.Count      : num  0 1 2 3 4 5 6 7 8 9 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Time             : POSIXct, format: "1995-01-01" "1995-02-01" ...

*Figura 21. Resumo do dataframe.*

Com esse resultado, você está pronto para iniciar nossa análise.

### <a name="create-a-training-dataset"></a>Criar um conjunto de dados de treinamento

Com o dataframe construído, precisamos criar um conjunto de dados de treinamento. Esses dados incluirão todas as observações exceto as últimas 12, do ano de 2013, as quais são o nosso conjunto de dados de teste. O seguinte código subagrupa o dataframe e cria gráficos de produção de derivados de leite e de as variáveis de preço. Então, eu crio gráficos das quatro produções e variáveis de preços. Uma função anônima é usada para definir alguns argumentos para a criação de gráficos e, em seguida, para iterar a lista dos outros dois argumentos com `Map()`. Se você estiver pensando que um loop funcionaria bem aqui, você está certo. Mas, como R é uma linguagem funcional, estou lhe mostrando uma abordagem funcional.

```R
cadairytrain <- cadairydata[1:216, ]

Ylabs  <- list("Log CA Cotage Cheese Production, 1000s lb",
               "Log CA Ice Cream Production, 1000s lb",
               "Log CA Milk Production 1000s lb",
               "Log North CA Milk Milk Fat Price per 1000 lb")

Map(function(y, Ylabs){plot(cadairytrain$Time, y, xlab = "Time", ylab = Ylabs, type = "l")}, cadairytrain[, 4:7], Ylabs)
```

Executar o código produz séries de gráficos de série de tempo da saída do dispositivo R mostrada na Figura 22. Observe que o eixo de tempo é em unidades de datas, uma bela vantagem do método de gráfico de série de tempo.

![Primeira das gráficos de série temporal dos dados de produção e preço de derivados de leite da Califórnia](./media/r-quickstart/unnamed-chunk-161.png)

![Segunda das gráficos de série temporal dos dados de produção e preço de derivados de leite da Califórnia](./media/r-quickstart/unnamed-chunk-162.png)

![Terceira das gráficos de série temporal dos dados de produção e preço de derivados de leite da Califórnia](./media/r-quickstart/unnamed-chunk-163.png)

![Quarta das gráficos de série temporal dos dados de produção e preço de derivados de leite da Califórnia](./media/r-quickstart/unnamed-chunk-164.png)

*Figura 22. Gráficos de série temporal dos dados de produção e preço de derivados do leite da Califórnia.*

### <a name="a-trend-model"></a>Um modelo de tendência

Depois de criar um objeto de série de tempo e de ter examinado os dados, vamos começar a construir um modelo de tendência para os dados de produção leite da Califórnia. Podemos fazer isso com uma regressão da série de tempo. No entanto, fica claro no gráfico que é necessário mais do que uma inclinação e uma interceptação para modelar com precisão a tendência observada nos dados de treinamento.

Considerando a pequena escala dos dados, criarei o modelo de tendência em RStudio e, em seguida, Recortarei e colarei o modelo resultante em Azure Machine Learning Studio (clássico). O RStudio fornece um ambiente adequado para esse tipo de análise interativa.

Como primeira tentativa, tentarei uma regressão polinomial com poderes de até 3. Existe um perigo real de sobreajuste desses tipos de modelos. Portanto, é melhor evitar condições de ordem mais alta. A função `I()` inibe a interpretação do conteúdo (interpreta o conteúdo 'como está') e permite que você grave uma função interpretada literalmente em uma equação de regressão.

```R
milk.lm <- lm(Milk.Prod ~ Time + I(Month.Count^2) + I(Month.Count^3), data = cadairytrain)
summary(milk.lm)
```

Isso gera o resultado a seguir.

    ##
    ## Call:
    ## lm(formula = Milk.Prod ~ Time + I(Month.Count^2) + I(Month.Count^3),
    ##     data = cadairytrain)
    ##
    ## Residuals:
    ##      Min       1Q   Median       3Q      Max
    ## -0.12667 -0.02730  0.00236  0.02943  0.10586
    ##
    ## Coefficients:
    ##                   Estimate Std. Error t value Pr(>|t|)
    ## (Intercept)       6.33e+00   1.45e-01   43.60   <2e-16 ***
    ## Time              1.63e-09   1.72e-10    9.47   <2e-16 ***
    ## I(Month.Count^2) -1.71e-06   4.89e-06   -0.35    0.726
    ## I(Month.Count^3) -3.24e-08   1.49e-08   -2.17    0.031 *  
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ##
    ## Residual standard error: 0.0418 on 212 degrees of freedom
    ## Multiple R-squared:  0.941,    Adjusted R-squared:  0.94
    ## F-statistic: 1.12e+03 on 3 and 212 DF,  p-value: <2e-16

A partir de valores`Pr(>|t|)`P () nessa saída, podemos ver que o termo quadrado pode não ser significativo. Vou usar a função `update()` para modificar esse modelo eliminado o termo ao quadrado.

```R
milk.lm <- update(milk.lm, . ~ . - I(Month.Count^2))
summary(milk.lm)
```

Isso gera o resultado a seguir.

    ##
    ## Call:
    ## lm(formula = Milk.Prod ~ Time + I(Month.Count^3), data = cadairytrain)
    ##
    ## Residuals:
    ##      Min       1Q   Median       3Q      Max
    ## -0.12597 -0.02659  0.00185  0.02963  0.10696
    ##
    ## Coefficients:
    ##                   Estimate Std. Error t value Pr(>|t|)
    ## (Intercept)       6.38e+00   4.07e-02   156.6   <2e-16 ***
    ## Time              1.57e-09   4.32e-11    36.3   <2e-16 ***
    ## I(Month.Count^3) -3.76e-08   2.50e-09   -15.1   <2e-16 ***
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ##
    ## Residual standard error: 0.0417 on 213 degrees of freedom
    ## Multiple R-squared:  0.941,  Adjusted R-squared:  0.94
    ## F-statistic: 1.69e+03 on 2 and 213 DF,  p-value: <2e-16

Assim parece melhor. Todos os termos são significativos. No entanto, o valor de 2e-16 é um valor padrão e não deve ser levado muito a sério.  

Para teste de sensatez, vamos criar um gráfico de série de tempo dos dados da produção de derivados de leite da Califórnia com a curva de tendência mostrada. Adicionei o seguinte código na Azure Machine Learning Studio (clássico) modelo [Executar script R][execute-r-script] (não RStudio) para criar o modelo e fazer uma plotagem. O resultado é mostrado na Figura 23.

```R
milk.lm <- lm(Milk.Prod ~ Time + I(Month.Count^3), data = cadairytrain)

plot(cadairytrain$Time, cadairytrain$Milk.Prod, xlab = "Time", ylab = "Log CA Milk Production 1000s lb", type = "l")
lines(cadairytrain$Time, predict(milk.lm, cadairytrain), lty = 2, col = 2)
```

![Dados de produção de leite da Califórnia com o modelo de tendência mostrado](./media/r-quickstart/unnamed-chunk-18.png)

*Figura 23. Dados de produção leite da Califórnia com o modelo de tendência mostrado.*

Parece que o modelo de tendência se ajusta aos dados muito bem. Além disso, não parece ter evidência de ajuste excessivo, como agitações estranhas na curva de modelo.  

### <a name="seasonal-model"></a>Modelo sazonal

Com um modelo de tendência em mãos, precisamos continuar e incluir efeitos sazonais. Usaremos o mês do ano como uma variável fictícia no modelo linear para capturar o efeito de mês a mês. Observe que quando você introduz variáveis fator em um modelo, a interceptação não deve ser computada. Se você não fizer isso, a fórmula será muito especificada e R descartará um dos fatores desejados, mas manterá o termo de interceptação.

Como temos um modelo de tendência satisfatório, podemos usar a função `update()` para adicionar novos termos ao modelo existente. -1 na atualização da fórmula descarta o termo de interceptação. Continuando em RStudio:

```R
milk.lm2 <- update(milk.lm, . ~ . + Month - 1)
summary(milk.lm2)
```

Isso gera o resultado a seguir.

    ##
    ## Call:
    ## lm(formula = Milk.Prod ~ Time + I(Month.Count^3) + Month - 1,
    ##     data = cadairytrain)
    ##
    ## Residuals:
    ##      Min       1Q   Median       3Q      Max
    ## -0.06879 -0.01693  0.00346  0.01543  0.08726
    ##
    ## Coefficients:
    ##                   Estimate Std. Error t value Pr(>|t|)
    ## Time              1.57e-09   2.72e-11    57.7   <2e-16 ***
    ## I(Month.Count^3) -3.74e-08   1.57e-09   -23.8   <2e-16 ***
    ## MonthApr          6.40e+00   2.63e-02   243.3   <2e-16 ***
    ## MonthAug          6.38e+00   2.63e-02   242.2   <2e-16 ***
    ## MonthDec          6.38e+00   2.64e-02   241.9   <2e-16 ***
    ## MonthFeb          6.31e+00   2.63e-02   240.1   <2e-16 ***
    ## MonthJan          6.39e+00   2.63e-02   243.1   <2e-16 ***
    ## MonthJul          6.39e+00   2.63e-02   242.6   <2e-16 ***
    ## MonthJun          6.38e+00   2.63e-02   242.4   <2e-16 ***
    ## MonthMar          6.42e+00   2.63e-02   244.2   <2e-16 ***
    ## MonthMay          6.43e+00   2.63e-02   244.3   <2e-16 ***
    ## MonthNov          6.34e+00   2.63e-02   240.6   <2e-16 ***
    ## MonthOct          6.37e+00   2.63e-02   241.8   <2e-16 ***
    ## MonthSep          6.34e+00   2.63e-02   240.6   <2e-16 ***
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ##
    ## Residual standard error: 0.0263 on 202 degrees of freedom
    ## Multiple R-squared:     1,    Adjusted R-squared:     1
    ## F-statistic: 1.42e+06 on 14 and 202 DF,  p-value: <2e-16

Vemos que o modelo não tem um termo de interceptação e tem 12 fatores mês significativos. Isso é exatamente o que queremos.

Vamos fazer outro gráfico de série de tempo dos dados de produção de derivados de leite da Califórnia para ver até que ponto o modelo sazonal está funcionando. Adicionei o código a seguir no Azure Machine Learning Studio (clássico) [Executar script R][execute-r-script] para criar o modelo e fazer uma plotagem.

```R
milk.lm2 <- lm(Milk.Prod ~ Time + I(Month.Count^3) + Month - 1, data = cadairytrain)

plot(cadairytrain$Time, cadairytrain$Milk.Prod, xlab = "Time", ylab = "Log CA Milk Production 1000s lb", type = "l")
lines(cadairytrain$Time, predict(milk.lm2, cadairytrain), lty = 2, col = 2)
```

A execução desse código no Azure Machine Learning Studio (clássico) produz a plotagem mostrada na Figura 24.

![Produção de leite da Califórnia com o modelo, incluindo efeitos sazonais](./media/r-quickstart/unnamed-chunk-20.png)

*Figura 24. Produção de leite na Califórnia com o modelo dos efeitos sazonais.*

O ajuste para os dados mostrados na Figura 24 é bastante encorajador. A tendência e o efeito sazonal (variação mensal) parecem razoáveis.

Como outra verificação em nosso modelo, vamos dar uma olhada nos resíduos. O código a seguir calcula os valores previstos de nossos dois modelos, calcula os resíduos para o modelo sazonal e, então, cria gráficos com esses resíduos nos dados de treinamento.

```R
## Compute predictions from our models
predict1  <- predict(milk.lm, cadairydata)
predict2  <- predict(milk.lm2, cadairydata)

## Compute and plot the residuals
residuals <- cadairydata$Milk.Prod - predict2
plot(cadairytrain$Time, residuals[1:216], xlab = "Time", ylab ="Residuals of Seasonal Model")
```

O gráfico restante é mostrado na Figura 25.

![Resíduos do modelo sazonal para os dados de treinamento](./media/r-quickstart/unnamed-chunk-21.png)

*Figura 25. Resíduos do modelo sazonal para os dados de treinamento.*

Esses resíduos parecem razoáveis. Não há nenhuma estrutura em particular, exceto o efeito da recessão de 2008 e 2009, que nosso modelo não leva em conta bem.

O gráfico mostrado na Figura 25 é útil para detectar qualquer padrão dependente de tempo nos resíduos. A abordagem explícita de computação e plotagem residual que eu sei coloca os resíduos na ordem de tempo no gráfico. Se, por outro lado, eu tivesse plotado `milk.lm$residuals`, o gráfico não estaria em ordem de tempo.

Também é possível usar `plot.lm()` para produzir uma série de gráficos de diagnóstico:

```R
## Show the diagnostic plots for the model
plot(milk.lm2, ask = FALSE)
```

Esse código gera uma série de gráficos de diagnóstico mostrados na Figura 26.

![Primeira das gráficos de diagnóstico do modelo sazonal](./media/r-quickstart/unnamed-chunk-221.png)

![Segunda das gráficos de diagnóstico do modelo sazonal](./media/r-quickstart/unnamed-chunk-222.png)

![Terceira das gráficos de diagnóstico do modelo sazonal](./media/r-quickstart/unnamed-chunk-223.png)

![Quarta das gráficos de diagnóstico do modelo sazonal](./media/r-quickstart/unnamed-chunk-224.png)

*Figura 26. Gráficos de diagnóstico de gráficos para o modelo sazonal.*

Há alguns pontos de grande influencia identificados nessas representações gráficas, mas eles não causam muita preocupação. Além disso, podemos ver pelo gráfico Q-Q Normal que os resíduos são próximos ao normalmente distribuídos, uma suposição importante para os modelos lineares.

### <a name="forecasting-and-model-evaluation"></a>Avaliação de previsão e modelo

Tem só mais uma coisa para concluir o nosso exemplo. Precisamos calcular as previsões e medir o erro em relação aos dados reais. Nossa previsão será para os 12 meses de 2013. Podemos calcular uma medida de erro para essa previsão para os dados reais que não fazem parte de nosso conjunto de dados de treinamento. Além disso, podemos comparar o desempenho em 18 anos de dados de treinamento com os 12 meses de dados de teste.  

Um número de métricas é usado para medir o desempenho dos modelos de série de tempo. Em nosso caso, usaremos o erro de RMS (raiz quadrada média). A função a seguir calcula o erro RMS entre duas séries.  

```R
RMS.error <- function(series1, series2, is.log = TRUE, min.length = 2){
  ## Function to compute the RMS error or difference between two
  ## series or vectors

  messages <- c("ERROR: Input arguments to function RMS.error of wrong type encountered",
                "ERROR: Input vector to function RMS.error is too short",
                "ERROR: Input vectors to function RMS.error must be of same length",
                "WARNING: Funtion rms.error has received invald input time series.")

  ## Check the arguments
  if(!is.numeric(series1) | !is.numeric(series2) | !is.logical(is.log) | !is.numeric(min.length)) {
    warning(messages[1])
    return(NA)}

  if(length(series1) < min.length) {
    warning(messages[2])
    return(NA)}

  if((length(series1) != length(series2))) {
       warning(messages[3])
    return(NA)}

  ## If is.log is TRUE exponentiate the values, else just copy
  if(is.log) {
    tryCatch( {
      temp1 <- exp(series1)
      temp2 <- exp(series2) },
      error = function(e){warning(messages[4]); NA}
    )
  } else {
    temp1 <- series1
    temp2 <- series2
  }

 ## Compute predictions from our models
predict1  <- predict(milk.lm, cadairydata)
predict2  <- predict(milk.lm2, cadairydata)

## Compute the RMS error in a dataframe
  tryCatch( {
    sqrt(sum((temp1 - temp2)^2) / length(temp1))},
    error = function(e){warning(messages[4]); NA})
}
```

Assim como na função `log.transform()` , discutida na seção "Transformações de valor", há muitas verificações de erros e exceções de código de recuperação nessa função. Os princípios empregados são os mesmos. O trabalho é feito em dois lugares ajustados em `tryCatch()`. Em primeiro lugar, a série de tempo é de exponenciação, pois temos trabalhado com os valores dos logaritmos. Em segundo lugar, o erro real do RMS é computado.  

Equipado com uma função para medir o erro RMS, vamos criar e exibir um dataframe que contenha os erros de RMS. Incluiremos termos para o modelo de tendência sozinho e o modelo completo com fatores sazonais. O código a seguir faz o trabalho usando os dois modelos lineares que construímos.

```R
## Compute the RMS error in a dataframe
## Include the row names in the first column so they will
## appear in the output of the Execute R Script
RMS.df  <-  data.frame(
rowNames = c("Trend Model", "Seasonal Model"),
  Traing = c(
  RMS.error(predict1[1:216], cadairydata$Milk.Prod[1:216]),
  RMS.error(predict2[1:216], cadairydata$Milk.Prod[1:216])),
  Forecast = c(
    RMS.error(predict1[217:228], cadairydata$Milk.Prod[217:228]),
    RMS.error(predict2[217:228], cadairydata$Milk.Prod[217:228]))
)
RMS.df

## The following line should be executed only when running in
## Azure Machine Learning Studio (classic)
maml.mapOutputPort('RMS.df')
```

Executar esse código produz a saída mostrada na Figura 27 na porta de saída de conjunto de dados do resultado.

![Comparação de erro do RMS para os modelos](./media/r-quickstart/fig26.png)

*Figura 27. Comparação de erro do RMS com os modelos.*

Com base nesses resultados, podemos ver que a adição dos fatores sazonais ao modelo reduz o erro RMS significativamente. Não é muito surpreendente, o erro RMS para os dados de treinamento é um pouco menor que para a previsão.

## <a name="guide-to-rstudio-documentation"></a><a id="appendixa"></a>Guia para a documentação do RStudio

RStudio é bem documentado. Aqui estão alguns links para as seções principais da documentação do RStudio para ajudá-lo a começar.

* **Criando projetos** – você pode organizar e gerenciar seu código R em projetos usando o RStudio. Consulte [usando projetos](https://support.rstudio.com/hc/articles/200526207-Using-Projects) para obter detalhes. Recomendo que você siga estas instruções e crie um projeto para os exemplos de código R neste artigo.  
* **Editando e executando o código r** -RStudio fornece um ambiente integrado para edição e execução do código r. Consulte [edição e execução de código](https://support.rstudio.com/hc/articles/200484448-Editing-and-Executing-Code) para obter detalhes.
* O **Debugging** -RStudio inclui recursos avançados de depuração. Consulte [Depurando com RStudio](https://support.rstudio.com/hc/articles/200713843-Debugging-with-RStudio) para obter mais informações sobre esses recursos. Para obter informações sobre os recursos de solução de problemas de ponto de interrupção, consulte [pontos de solução de problemas](https://support.rstudio.com/hc/articles/200534337-Breakpoint-Troubleshooting)

## <a name="further-reading"></a><a id="appendixb"></a>Leitura adicional

Este tutorial de programação R aborda as noções básicas do que você precisa para usar a linguagem R com Azure Machine Learning Studio (clássico). Se você não estiver familiarizado com R, duas introduções estão disponíveis no CRAN:

* [R para iniciantes](https://cran.r-project.org/doc/contrib/Paradis-rdebuts_en.pdf) de Emmanuel Paradis é um bom lugar para começar.  
* [Uma introdução ao R](https://cran.r-project.org/doc/manuals/R-intro.html) por W. N. Venables et. al. apresenta um pouco mais de profundidade.

Existem muitos livros sobre R que podem ajudá-lo a começar. Aqui estão alguns que considero úteis:

* A **arte da programação de R: um tour pelo design de software estatístico** por Norman Matloff é uma excelente introdução à programação em R.  
* O **r Cookbook** de Paul Teetor fornece um problema e uma abordagem de solução para o uso do R.  
* **R em ação** por Robert Kabacoff é outro livro introdutório útil. O [site de R rápido](https://www.statmethods.net/) do Companion é um recurso útil.
* **R inferno** by Patrick queimaduras é um livro surpreendentemente bem-humorada que lida com vários tópicos complicados e difíceis que podem ser encontrados durante a programação em R. O livro está disponível gratuitamente no [inferno do R](https://www.burns-stat.com/documents/books/the-r-inferno/).
* Se você quiser aprofundar-se nos tópicos avançados em R, veja o livro **Advanced R** por Hadley Wickham. A versão online deste livro está disponível gratuitamente em [http://adv-r.had.co.nz/](http://adv-r.had.co.nz/).

Um catálogo de pacotes de série temporal do R pode ser encontrado na [exibição de tarefa Cran: análise de série temporal](https://cran.r-project.org/web/views/TimeSeries.html). Para obter informações sobre pacotes de objetos de série temporal específicos, consulte a documentação do pacote.

A **série de tempo introdutório** de livros com r por Paul Cowpertwait e Andrew Metcalfe fornece uma introdução ao uso do R para análise de série temporal. Muitos textos mais teóricos fornecem exemplos de R.

Aqui estão alguns ótimos recursos da Internet:

* O DataCamp ensina R no conforto de seu navegador, com lições em vídeo e exercícios de codificação. Há tutoriais interativos sobre as técnicas de R e os pacotes mais recentes. Faça o [tutorial de R interativo](https://www.datacamp.com/courses/introduction-to-r)gratuito.
* [Aprenda a programação de R, o guia definitivo](https://www.programiz.com/r-programming) da Programiz.
* Um rápido [tutorial de R](https://www.cyclismo.org/tutorial/R/) por Kelly preto da Clarkson University.
* Há mais de 60 de recursos de R listados em [recursos de linguagem r superiores para melhorar suas habilidades de dados](https://www.computerworld.com/article/2497464/business-intelligence-60-r-resources-to-improve-your-data-skills.html).

<!-- Module References -->
[execute-r-script]: /azure/machine-learning/studio-module-reference/execute-r-script
