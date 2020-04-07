---
title: Explorar o Linux
titleSuffix: Azure Data Science Virtual Machine
description: Aprenda a completar várias tarefas comuns de ciência de dados usando a Máquina Virtual de Data Science do Linux.
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: conceptual
ms.date: 04/02/2020
ms.openlocfilehash: 1298d5fe1d7407e836f454c2130a913dde6f8eec
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80755151"
---
# <a name="data-science-with-a-linux-data-science-virtual-machine-in-azure"></a>Ciência de dados com uma Máquina Virtual de Data Science Linux no Azure

Este passo a passo mostra como completar várias tarefas comuns de ciência de dados usando a Máquina Virtual de Data Science (DSVM) do Linux. O Linux DSVM é uma imagem de máquina virtual disponível no Azure que é pré-instalada com uma coleção de ferramentas comumente usadas para análise de dados e aprendizado de máquina. Os principais componentes do software estão itemizados no [Provision the Linux Data Science Virtual Machine](linux-dsvm-intro.md). A imagem DSVM facilita o início da ciência de dados em minutos, sem ter que instalar e configurar cada uma das ferramentas individualmente. Você pode facilmente escalar o DSVM se precisar, e você pode pará-lo quando ele não estiver em uso. O recurso DSVM é elástico e econômico.

As tarefas de ciência de dados demonstradas neste passo a passo seguem as etapas descritas no [Qual é o Processo de Ciência de Dados da Equipe?](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/overview) O Processo de Ciência de Dados da Equipe é uma abordagem sistemática da ciência de dados que ajuda equipes de cientistas de dados a colaborar efetivamente sobre o ciclo de vida da construção de aplicações inteligentes. O processo da ciência de dados também fornece uma estrutura iterativa para a ciência de dados que pode ser seguida por uma pessoa.

Neste passo a passo, analisamos o conjunto de dados [spambase.](https://archive.ics.uci.edu/ml/datasets/spambase) Spambase é um conjunto de e-mails que são marcados como spam ou presunto (não spam). Spambase também contém algumas estatísticas sobre o conteúdo dos e-mails. Falamos sobre as estatísticas mais tarde no passo a passo.

## <a name="prerequisites"></a>Pré-requisitos

Antes de usar um DSVM Linux, você deve ter os seguintes pré-requisitos:

* **Assinatura do Azure**. Para obter uma assinatura do Azure, consulte [Criar sua conta gratuita do Azure hoje](https://azure.microsoft.com/free/).
* [**Máquina Virtual de Ciência de Dados do Linux**](https://azure.microsoft.com/marketplace/apps/microsoft-dsvm.linux-data-science-vm-ubuntu). Para obter informações sobre o provisionamento da máquina virtual, consulte [Provision the Linux Data Science Virtual Machine](linux-dsvm-intro.md).
* [**X2Go**](https://wiki.x2go.org/doku.php) instalado em seu computador com uma sessão XFCE aberta. Para obter mais informações, consulte [Instalar e configurar o cliente X2Go](linux-dsvm-intro.md#x2go).
* Para uma experiência de rolagem mais suave, no navegador Firefox da `gfx.xrender.enabled` DSVM, alterne a bandeira em `about:config`. [Saiba mais](https://www.reddit.com/r/firefox/comments/4nfmvp/ff_47_unbearable_slow_over_remote_x11/). Considere também `mousewheel.enable_pixel_scrolling` `False`a configuração para . [Saiba mais](https://support.mozilla.org/questions/981140).
* **Conta de Aprendizado de Máquina do Azure**. Se você ainda não tiver uma, inscreva-se para uma nova conta na página inicial do [Azure Machine Learning](https://azure.microsoft.com/free/services/machine-learning//).

## <a name="download-the-spambase-dataset"></a>Baixar o conjunto de dados baseado em spam

O conjunto de dados [spambase](https://archive.ics.uci.edu/ml/datasets/spambase) é um conjunto relativamente pequeno de dados que contém 4.601 exemplos. O conjunto de dados é um tamanho conveniente para demonstrar algumas das principais características do DSVM porque mantém os requisitos de recursos modestos.

> [!NOTE]
> Este passo a passo foi criado usando um DSVM Linux de tamanho D2 v2 (Ubuntu 18.04 Edition). Você pode usar um DSVM deste tamanho para completar os procedimentos que são demonstrados neste passo a passo.

Se você precisar de mais espaço de armazenamento, você pode criar discos adicionais e anexá-los ao seu DSVM. Os discos usam armazenamento Azure persistente, de modo que seus dados sejam preservados mesmo que o servidor seja reprovisionado devido ao redimensionamento ou seja desligado. Para adicionar um disco e anexá-lo ao seu DSVM, complete as etapas em [Adicionar um disco a uma VM Linux](../../virtual-machines/linux/add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). As etapas para adicionar um disco usam o Azure CLI, que já está instalado no DSVM. Você pode completar os passos inteiramente a partir do próprio DSVM. Outra opção para aumentar o armazenamento é usar [arquivos Do Zure](../../storage/files/storage-how-to-use-files-linux.md).

Para baixar os dados, abra uma janela de terminal e execute este comando:

```bash
wget --no-check-certificate https://archive.ics.uci.edu/ml/machine-learning-databases/spambase/spambase.data
```

O arquivo baixado não tem uma linha de cabeçalho. Vamos criar outro arquivo que tenha um cabeçalho. Execute este comando para criar um arquivo com os devidos cabeçalhos:

```bash
echo 'word_freq_make, word_freq_address, word_freq_all, word_freq_3d,word_freq_our, word_freq_over, word_freq_remove, word_freq_internet,word_freq_order, word_freq_mail, word_freq_receive, word_freq_will,word_freq_people, word_freq_report, word_freq_addresses, word_freq_free,word_freq_business, word_freq_email, word_freq_you, word_freq_credit,word_freq_your, word_freq_font, word_freq_000, word_freq_money,word_freq_hp, word_freq_hpl, word_freq_george, word_freq_650, word_freq_lab,word_freq_labs, word_freq_telnet, word_freq_857, word_freq_data,word_freq_415, word_freq_85, word_freq_technology, word_freq_1999,word_freq_parts, word_freq_pm, word_freq_direct, word_freq_cs, word_freq_meeting,word_freq_original, word_freq_project, word_freq_re, word_freq_edu,word_freq_table, word_freq_conference, char_freq_semicolon, char_freq_leftParen,char_freq_leftBracket, char_freq_exclamation, char_freq_dollar, char_freq_pound, capital_run_length_average,capital_run_length_longest, capital_run_length_total, spam' > headers
```

Então, concatenaos os dois arquivos juntos:

```bash
cat spambase.data >> headers
mv headers spambaseHeaders.data
```

O conjunto de dados tem vários tipos de estatísticas para cada e-mail:

* Colunas **como\_\_word freq WORD** indicam a porcentagem de palavras no e-mail que correspondem ao *WORD*. Por exemplo, se **a palavra\_freq\_fazer** é **1**, então 1% de todas as palavras no e-mail foram *fazer*.
* Colunas **como\_\_char freq CHAR** indicam a porcentagem de todos os caracteres no e-mail que são *CHAR*.
* **capital\_run\_length\_longest** é o maior comprimento de uma sequência de letras maiúsculas.
* **capital\_run\_length\_average** é a duração média de todas as sequências de letras maiúsculas.
* **capital\_run\_length\_total** é o comprimento total de todas as sequências de letras maiúsculas.
* **spam** indica se o email foi considerado spam ou não (1 = spam, 0 = não spam).

## <a name="explore-the-dataset-by-using-r-open"></a>Explorar o conjunto de dados usando o R Open

Vamos examinar os dados e fazer algum aprendizado básico de máquina usando R. O DSVM vem com [o Microsoft R Open](https://mran.revolutionanalytics.com/open/) pré-instalado. As bibliotecas de matemática multithreaded na versão pré-instalada do R oferecem melhor desempenho do que as versões de um único segmento. O R Open também fornece reprodutibilidade através de um instantâneo do repositório do pacote CRAN.

Para obter cópias das amostras de código que são usadas neste passo a passo, use o git para clonar o repositório Azure-Machine-Learning-Data-Science. O Git é pré-instalado no DSVM. Na linha de comando git, corra:

```bash
git clone https://github.com/Azure/Azure-MachineLearning-DataScience.git
```

Abra uma janela de terminal e inicie uma nova sessão R no console interativo R. Você também pode usar o RStudio, que está pré-instalado no DSVM.

Para importar os dados e configurar o ambiente:

```R
data <- read.csv("spambaseHeaders.data")
set.seed(123)
```

Para ver as estatísticas de resumo sobre cada coluna:

```R
summary(data)
```

Para obter uma exibição diferente dos dados:

```R
str(data)
```

Esta exibição mostra o tipo de cada variável e os primeiros valores no conjunto de dados.

A coluna **spam** foi lida como um número inteiro, mas é realmente uma variável categórica (ou fator). Para definir seu tipo:

```R
data$spam <- as.factor(data$spam)
```

Para fazer alguma análise exploratória, use o pacote [ggplot2,](https://ggplot2.tidyverse.org/) uma biblioteca de gráficos popular para R que está pré-instalada no DSVM. Com base nos dados de resumo exibidos anteriormente, temos estatísticas resumidas sobre a frequência do caractere ponto de exclamação. Vamos traçar essas freqüências aqui executando os seguintes comandos:

```R
library(ggplot2)
ggplot(data) + geom_histogram(aes(x=char_freq_exclamation), binwidth=0.25)
```

Porque a barra zero está distorcendo o enredo, vamos eliminá-lo:

```R
email_with_exclamation = data[data$char_freq_exclamation > 0, ]
ggplot(email_with_exclamation) + geom_histogram(aes(x=char_freq_exclamation), binwidth=0.25)
```

Há uma densidade não trivial acima de 1 que parece interessante. Vamos olhar apenas para esses dados:

```R
ggplot(data[data$char_freq_exclamation > 1, ]) + geom_histogram(aes(x=char_freq_exclamation), binwidth=0.25)
```

Em seguida, divida-o por spam versus presunto:

```R
ggplot(data[data$char_freq_exclamation > 1, ], aes(x=char_freq_exclamation)) +
geom_density(lty=3) +
geom_density(aes(fill=spam, colour=spam), alpha=0.55) +
xlab("spam") +
ggtitle("Distribution of spam \nby frequency of !") +
labs(fill="spam", y="Density")
```

Esses exemplos devem ajudá-lo a fazer tramas semelhantes e explorar dados nas outras colunas.

## <a name="train-and-test-a-machine-learning-model"></a>Treinar e testar um modelo de aprendizagem de máquina

Vamos treinar alguns modelos de aprendizado de máquina para classificar os e-mails no conjunto de dados como contendo spam ou presunto. Nesta seção, treinamos um modelo de árvore de decisão e um modelo florestal aleatório. Então, testamos a precisão das previsões.

> [!NOTE]
> O pacote *rpart* (Recursive Partitioning and Regression Trees) usado no seguinte código já está instalado no DSVM.

Primeiro, vamos dividir o conjunto de dados em conjuntos de treinamento e conjuntos de testes:

```R
rnd <- runif(dim(data)[1])
trainSet = subset(data, rnd <= 0.7)
testSet = subset(data, rnd > 0.7)
```

Em seguida, crie uma árvore de decisão para classificar os e-mails:

```R
require(rpart)
model.rpart <- rpart(spam ~ ., method = "class", data = trainSet)
plot(model.rpart)
text(model.rpart)
```

Eis o resultado:

![Um diagrama da árvore de decisão que foi criada](./media/linux-dsvm-walkthrough/decision-tree.png)

Para determinar a execução no conjunto de treinamento, use o seguinte código:

```R
trainSetPred <- predict(model.rpart, newdata = trainSet, type = "class")
t <- table(`Actual Class` = trainSet$spam, `Predicted Class` = trainSetPred)
accuracy <- sum(diag(t))/sum(t)
accuracy
```

Para determinar a execução no conjunto de teste:

```R
testSetPred <- predict(model.rpart, newdata = testSet, type = "class")
t <- table(`Actual Class` = testSet$spam, `Predicted Class` = testSetPred)
accuracy <- sum(diag(t))/sum(t)
accuracy
```

Também tentaremos um modelo de floresta aleatória. Florestas aleatórias treinam uma infinidade de árvores de decisão e saída de uma classe que é o modo das classificações de todas as árvores de decisão individuais. Eles fornecem uma abordagem de aprendizado de máquina mais poderosa porque corrijam para a tendência de um modelo de árvore de decisão para sobreajustar um conjunto de dados de treinamento.

```R
require(randomForest)
trainVars <- setdiff(colnames(data), 'spam')
model.rf <- randomForest(x=trainSet[, trainVars], y=trainSet$spam)

trainSetPred <- predict(model.rf, newdata = trainSet[, trainVars], type = "class")
table(`Actual Class` = trainSet$spam, `Predicted Class` = trainSetPred)

testSetPred <- predict(model.rf, newdata = testSet[, trainVars], type = "class")
t <- table(`Actual Class` = testSet$spam, `Predicted Class` = testSetPred)
accuracy <- sum(diag(t))/sum(t)
accuracy
```

<a name="deep-learning"></a>

## <a name="deep-learning-tutorials-and-walkthroughs"></a>Tutoriais e passo a passo de aprendizagem profunda

Além das amostras baseadas em estruturas, um conjunto de passoados abrangentes também é fornecido. Essas orientações passo a passo ajudam a iniciar o desenvolvimento de aplicativos de aprendizagem profunda em domínios como reconhecimento vocal/texto e imagem.

- [Executando redes neurais em diferentes estruturas](https://github.com/ilkarman/DeepLearningFrameworks): Um passo a passo abrangente que mostra como migrar código de uma estrutura para outra. Ele também demonstra como comparar o desempenho do modelo e do tempo de execução entre as estruturas. 

- [Um guia de como construir uma solução completa para detectar produtos dentro de imagens](https://github.com/Azure/cortana-intelligence-product-detection-from-images): A detecção de imagens é uma técnica que pode localizar e classificar objetos dentro de imagens. A tecnologia tem o potencial de trazer enormes recompensas em muitos domínios de negócios da vida real. Por exemplo, os varejistas podem usar essa técnica para determinar qual produto um cliente retirou da prateleira. Por sua vez, essas informações ajudam as lojas a gerenciar o estoque do produtos. 

- [Aprendizado profundo para áudio](https://blogs.technet.microsoft.com/machinelearning/2018/01/30/hearing-ai-getting-started-with-deep-learning-for-audio-on-azure/): Este tutorial mostra como treinar um modelo de aprendizagem profunda para detecção de eventos de áudio no [conjunto de dados de sons urbanos](https://urbansounddataset.weebly.com/). O tutorial fornece uma visão geral de como trabalhar com dados de áudio.

- [Classificação de documentos de texto](https://github.com/anargyri/lstm_han): Este passo a passo demonstra como construir e treinar duas arquiteturas de rede neural diferentes: Rede de Atenção Hierárquica e Memória de Longo Prazo (LSTM). Essas redes neurais usam a API Keras para aprendizagem profunda para classificar documentos de texto. Keras é um front-end para três dentre as estruturas mais populares de aprendizagem profunda: Microsoft Cognitive Toolkit, TensorFlow e Theano.

## <a name="other-tools"></a>Outras ferramentas

As demais seções mostram como usar algumas das ferramentas instaladas no DSVM Linux. Discutimos essas ferramentas:

* XGBoost
* Python
* JupyterHub
* Rattle
* PostgreSQL e SQuirreL SQL
* SQL Server Data Warehouse

### <a name="xgboost"></a>XGBoost

[O XGBoost](https://xgboost.readthedocs.org/en/latest/) fornece uma implementação rápida e precisa de árvores impulsionadas.

```R
require(xgboost)
data <- read.csv("spambaseHeaders.data")
set.seed(123)

rnd <- runif(dim(data)[1])
trainSet = subset(data, rnd <= 0.7)
testSet = subset(data, rnd > 0.7)

bst <- xgboost(data = data.matrix(trainSet[,0:57]), label = trainSet$spam, nthread = 2, nrounds = 2, objective = "binary:logistic")

pred <- predict(bst, data.matrix(testSet[, 0:57]))
accuracy <- 1.0 - mean(as.numeric(pred > 0.5) != testSet$spam)
print(paste("test accuracy = ", accuracy))
```

XGBoost também pode ligar do Python ou de uma linha de comando.

### <a name="python"></a>Python

Para o desenvolvimento python, as distribuições Anaconda Python 3.5 e 2.7 são instaladas no DSVM.

> [!NOTE]
> A distribuição anaconda inclui [Conda](https://conda.pydata.org/docs/index.html). Você pode usar o Conda para criar ambientes Python personalizados que tenham versões ou pacotes diferentes instalados neles.

Vamos ler em alguns dos conjuntos de dados spambase e classificar os e-mails com máquinas vetoriais de suporte em Scikit-learn:

```Python
import pandas
from sklearn import svm
data = pandas.read_csv("spambaseHeaders.data", sep = ',\s*')
X = data.ix[:, 0:57]
y = data.ix[:, 57]
clf = svm.SVC()
clf.fit(X, y)
```

Para fazer previsões:

```Python
clf.predict(X.ix[0:20, :])
```

Para demonstrar como publicar um ponto final do Azure Machine Learning, vamos fazer um modelo mais básico. Usaremos as três variáveis que usamos quando publicamos o modelo R anteriormente:

```Python
X = data[["char_freq_dollar", "word_freq_remove", "word_freq_hp"]]
y = data.ix[:, 57]
clf = svm.SVC()
clf.fit(X, y)
```

Para publicar o modelo no Azure Machine Learning:

```Python
# Publish the model.
workspace_id = "<workspace-id>"
workspace_token = "<workspace-token>"
from azureml import services
@services.publish(workspace_id, workspace_token)
@services.types(char_freq_dollar = float, word_freq_remove = float, word_freq_hp = float)
@services.returns(int) # 0 or 1
def predictSpam(char_freq_dollar, word_freq_remove, word_freq_hp):
    inputArray = [char_freq_dollar, word_freq_remove, word_freq_hp]
    return clf.predict(inputArray)

# Get some info about the resulting model.
predictSpam.service.url
predictSpam.service.api_key

# Call the model
predictSpam.service(1, 1, 1)
```

> [!NOTE]
> Esta opção está disponível apenas para Python 2.7. Ainda não é suportado no Python 3.5. Para executar, use **/anaconda/bin/python2.7**.

### <a name="jupyterhub"></a>JupyterHub

A distribuição anaconda no DSVM vem com um Jupyter Notebook, um ambiente multiplataforma para compartilhar código e análise Python, R ou Julia. O Jupyter Notebook é acessado através do JupyterHub. Você faz login usando seu nome de\<usuário e senha do Linux\>local em https:// nome DSVM DNS ou endereço IP :8000/. Todos os arquivos de configuração do JupyterHub são encontrados em /etc/jupyterhub.

> [!NOTE]
> Para usar o Gerenciador `pip` de Pacotes Python (via comando) de um Notebook Jupyter no kernel atual, use este comando na célula de código:
>
>   ```Python
>    import sys
>    ! {sys.executable} -m pip install numpy -y
>   ```
> 
> Para usar o instalador Conda (via `conda` comando) de um Notebook Jupyter no kernel atual, use este comando em uma célula de código:
>
>   ```Python
>    import sys
>    ! {sys.prefix}/bin/conda install --yes --prefix {sys.prefix} numpy
>   ```

Vários notebooks de exemplo já estão instalados no DSVM:

* Amostra de notebooks Python:
  * [IntroToJupyterPython.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Data-Science-Virtual-Machine/Samples/Notebooks/IntroToJupyterPython.ipynb)
  * [IrisClassifierPyMLWebService](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Data-Science-Virtual-Machine/Samples/Notebooks/IrisClassifierPyMLWebService.ipynb)
* Amostra r notebook:
  * [IntroTutorialinR](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Data-Science-Virtual-Machine/Samples/Notebooks/IntroTutorialinR.ipynb) 

> [!NOTE]
> O idioma Julia também está disponível a partir da linha de comando no DSVM Linux.

### <a name="rattle"></a>Rattle

[Rattle](https://cran.r-project.org/web/packages/rattle/index.html) *(R* *A*nalytical *T*ool *T*o *L*earn *E*asily) é uma ferramenta gráfica R para mineração de dados. O Rattle tem uma interface intuitiva que facilita o carregamento, a exploração e a transformação de dados e a construção e avaliação de modelos. [Rattle: Uma GUI de mineração de dados para R](https://journal.r-project.org/archive/2009-2/RJournal_2009-2_Williams.pdf) fornece um passo a passo que demonstra as características de Rattle.

Instale e inicie o Rattle executando estes comandos:

```R
if(!require("rattle")) install.packages("rattle")
require(rattle)
rattle()
```

> [!NOTE]
> Você não precisa instalar Rattle no DSVM. No entanto, você pode ser solicitado a instalar pacotes adicionais quando Rattle abrir.

O Rattle usa uma interface baseada em guias. A maioria das guias corresponde a etapas do Processo de Ciência de Dados da [Equipe,](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/)como carregar dados ou explorar dados. O processo da ciência de dados flui da esquerda para a direita nas guias. A última guia contém um registro dos comandos R que foram executados por Rattle.

Para carregar e configurar o conjunto de dados:

1. Para carregar o arquivo, selecione a guia **Dados.**
1. Escolha o seletor ao lado **de Filename**e selecione **spambaseHeaders.data**.
1. Para carregar o arquivo. Selecione **Executar**. Você deve ver um resumo de cada coluna, incluindo seu tipo de dados identificado; seja uma entrada, um alvo ou outro tipo de variável; e o número de valores únicos.
1. O Rattle identifica corretamente a coluna de **spam** como o destino. Selecione a coluna **de spam** e, em seguida, defina o **Tipo de dados de destino** como **Categórico**.

Para explorar os dados:

1. Selecione a guia **Explorar** .
1. Para ver informações sobre os tipos de variáveis e algumas estatísticas de resumo, selecione **Executar resumo** > **.**
1. Para visualizar outros tipos de estatísticas sobre cada variável, selecione **outras**opções, como **Descrever** ou Básico .

Você também pode usar a guia **Explorar** para gerar tramas perspicazes. Para criar gráficos com um histograma dos dados:

1. Selecione **Distribuições**.
1. Para **word_freq_remove** e **word_freq_you,** selecione **Histograma**.
1. Selecione **Executar**. Você deve ver ambas as parcelas de densidade em uma única janela de gráfico, onde é claro que a palavra _que você_ aparece com muito mais freqüência em e-mails do que _remover_.

As tramas de **Correlação** também são interessantes. Para criar um enredo:

1. Para **Tipo,** selecione **Correlação**.
1. Selecione **Executar**.
1. O Rattle avisa que ele recomenda um máximo de 40 variáveis. Selecione **Sim** para exibir a criação de gráficos.

Existem algumas correlações interessantes que surgem: _a tecnologia_ está fortemente correlacionada com _a HP_ e _os laboratórios,_ por exemplo. Também está fortemente correlacionado com _650_ porque o código de área dos doadores do conjunto de dados é 650.

Os valores numéricos para as correlações entre palavras estão disponíveis na janela **Explorar.** É interessante notar, por exemplo, que a _tecnologia_ está negativamente correlacionada com _o seu_ e _o dinheiro._

O Rattle pode transformar o conjunto de dados para lidar com alguns problemas comuns. Por exemplo, ele pode redimensionar recursos, imputar valores ausentes, lidar com outliers e remover variáveis ou observações que tenham dados ausentes. Rattle também pode identificar regras de associação entre observações e variáveis. Essas guias não estão cobertas neste passo a passo introdutório.

Rattle também pode executar análise de cluster. Iremos excluir alguns recursos para facilitar a leitura da saída. Na guia **Dados,** **selecione Ignorar** ao lado de cada uma das variáveis, exceto estes 10 itens:

* word_freq_hp
* word_freq_technology
* word_freq_george
* word_freq_remove
* word_freq_your
* word_freq_dollar
* word_freq_money
* capital_run_length_longest
* word_freq_business
* spam

Retorne à guia **Cluster.** Selecione **KMeans**e, em seguida, defina **número de clusters** para **4**. Selecione **Executar**. Os resultados são exibidos na janela de saída. Um cluster tem alta frequência de _george_ e _hp,_ e é provavelmente um e-mail de negócios legítimo.

Para construir um modelo básico de aprendizado de máquina de árvore de decisão:

1. Selecione a guia **Modelo,**
1. Para o **tipo,** selecione **Árvore**.
1. Selecione **Executar** para exibir a árvore em formato de texto na janela de saída.
1. Selecione o botão **Desenhar** para exibir uma versão gráfica. A árvore de decisão se parece com a árvore que obtivemos anteriormente usando rpart.

Uma característica útil do Rattle é sua capacidade de executar vários métodos de aprendizado de máquina e avaliá-los rapidamente. Aqui estão os passos:

1. Para **tipo,** selecione **Todos**.
1. Selecione **Executar**.
1. Quando o Rattle terminar de ser executado, você pode selecionar qualquer **tipo** de valor, como **SVM,** e visualizar os resultados.
1. Você também pode comparar o desempenho dos modelos no conjunto de validação usando a guia **Avaliar.** Por exemplo, a seleção **Matriz de erro** mostra a matriz de confusão, erro geral e erro de classe médio para cada modelo no conjunto de validação. Você também pode traçar curvas ROC, executar análises de sensibilidade e fazer outros tipos de avaliações de modelo.

Quando terminar de construir modelos, selecione a guia **Log** para visualizar o código R que foi executado pelo Rattle durante a sessão. Você pode selecionar o botão **Exportar** para salvá-lo.

> [!NOTE]
> A versão atual de Rattle contém um bug. Para modificar o script ou usá-lo para repetir **#** seus passos mais tarde, você deve inserir um caractere na frente de *Exportar este registro...* no texto do log.

### <a name="postgresql-and-squirrel-sql"></a>PostgreSQL e SQuirreL SQL

A DSVM vem com o PostgreSQL instalado. O PostgreSQL é um banco de dados relacional sofisticado de fonte aberta. Esta seção mostra como carregar o conjunto de dados spambase no PostgreSQL e, em seguida, consulta-lo.

Antes de carregar os dados, você deve permitir a autenticação de senha do host local. Em um prompt de comando, execute:

```Bash
sudo gedit /var/lib/pgsql/data/pg_hba.conf
```

Na parte inferior do arquivo de configuração, há várias linhas que detalham as conexões permitidas:

```
# "local" is only for Unix domain socket connections:
local   all             all                                     trust
# IPv4 local connections:
host    all             all             127.0.0.1/32            ident
# IPv6 local connections:
host    all             all             ::1/128                 ident
```

Altere a linha **de conexões locais IPv4** para usar **md5** em vez de **identificar,** para que possamos fazer login usando um nome de usuário e senha:

```
# IPv4 local connections:
host    all             all             127.0.0.1/32            md5
```

Em seguida, reinicie o serviço PostgreSQL:

```Bash
sudo systemctl restart postgresql
```

Para iniciar *o psql* (um terminal interativo para PostgreSQL) como usuário de postgres incorporado, execute este comando:

```Bash
sudo -u postgres psql
```

Crie uma nova conta de usuário usando o nome de usuário da conta Linux que você usou para fazer login. Crie uma senha:

```Bash
CREATE USER <username> WITH CREATEDB;
CREATE DATABASE <username>;
ALTER USER <username> password '<password>';
\quit
```

Faça login no psql:

```Bash
psql
```

Importe os dados para um novo banco de dados:

```SQL
CREATE DATABASE spam;
\c spam
CREATE TABLE data (word_freq_make real, word_freq_address real, word_freq_all real, word_freq_3d real,word_freq_our real, word_freq_over real, word_freq_remove real, word_freq_internet real,word_freq_order real, word_freq_mail real, word_freq_receive real, word_freq_will real,word_freq_people real, word_freq_report real, word_freq_addresses real, word_freq_free real,word_freq_business real, word_freq_email real, word_freq_you real, word_freq_credit real,word_freq_your real, word_freq_font real, word_freq_000 real, word_freq_money real,word_freq_hp real, word_freq_hpl real, word_freq_george real, word_freq_650 real, word_freq_lab real,word_freq_labs real, word_freq_telnet real, word_freq_857 real, word_freq_data real,word_freq_415 real, word_freq_85 real, word_freq_technology real, word_freq_1999 real,word_freq_parts real, word_freq_pm real, word_freq_direct real, word_freq_cs real, word_freq_meeting real,word_freq_original real, word_freq_project real, word_freq_re real, word_freq_edu real,word_freq_table real, word_freq_conference real, char_freq_semicolon real, char_freq_leftParen real,char_freq_leftBracket real, char_freq_exclamation real, char_freq_dollar real, char_freq_pound real, capital_run_length_average real, capital_run_length_longest real, capital_run_length_total real, spam integer);
\copy data FROM /home/<username>/spambase.data DELIMITER ',' CSV;
\quit
```

Agora, vamos explorar os dados e executar algumas consultas usando o SQuirreL SQL, uma ferramenta gráfica que você pode usar para interagir com bancos de dados através de um driver JDBC.

Para começar, no menu **Aplicativos,** abra O SQL SQuirreL. Para configurar o driver:

1. Selecione **drivers** > **de exibição do Windows**.
1. Clique com o botão direito do mouse **postgreSQL** e selecione **Modificar driver**.
1. Selecione **Adicionar caminho de classe** > **extra**.
1. Para **Nome do arquivo,** digite **/usr/share/java/jdbcdrivers/postgresql-9.4.1208.jre6.jar**.
1. Selecione **Abrir**.
1. Selecione **Drivers de lista**. Em **Nome da classe,** selecione **org.postgresql.Driver**e selecione **OK**.

Para configurar a conexão com o servidor local:

1. Selecione **'Exibir** > **aliases do Windows'.**
1. Selecione **+** o botão para criar um novo alias. Para o novo nome de alias, digite **o banco de dados spam**. 
1. Para **Driver,** selecione **PostgreSQL**.
1. Define a URL como **jdbc:postgresql://localhost/spam**.
1. Insira seu nome de usuário e sua senha.
1. Selecione **OK**.
1. Para abrir a janela **Conexão**, clique duas vezes no alias do **Banco de dados de spam**.
1. Selecione **Conectar**.

Para executar algumas consultas:

1. Selecione a guia **SQL** .
1. Na caixa de consulta na parte superior da guia **SQL,** digite uma consulta básica, como `SELECT * from data;`.
1. Pressione Ctrl+Enter para executar a consulta. Por padrão, o SQuirreL SQL retorna as primeiras 100 linhas da sua consulta.

Há muito mais consultas que você pode executar para explorar esses dados. Por exemplo, como a frequência da palavra *make* difere entre o spam e o ham?

```SQL
SELECT avg(word_freq_make), spam from data group by spam;
```

Ou, quais são as características do e-mail que frequentemente contêm *3d?*

```SQL
SELECT * from data order by word_freq_3d desc;
```

A maioria dos e-mails que têm uma alta ocorrência de *3d* aparentemente são spam. Essas informações podem ser úteis para a construção de um modelo preditivo para classificar e-mails.

Se você quiser fazer aprendizado de máquina usando dados armazenados em um banco de dados PostgreSQL, considere usar [o MADlib](https://madlib.incubator.apache.org/).

### <a name="sql-data-warehouse"></a>SQL Data Warehouse

O Azure SQL Data Warehouse é um banco de dados baseado em nuvem e em escala que pode processar volumes maciços de dados, tanto relacionais quanto não relacionais. Para obter mais informações, consulte [O que é Azure SQL Data Warehouse?](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md)

Para conectar o data warehouse e criar a tabela, execute o seguinte comando em um prompt de comando:

```Bash
sqlcmd -S <server-name>.database.windows.net -d <database-name> -U <username> -P <password> -I
```

No prompt sqlcmd, execute este comando:

```SQL
CREATE TABLE spam (word_freq_make real, word_freq_address real, word_freq_all real, word_freq_3d real,word_freq_our real, word_freq_over real, word_freq_remove real, word_freq_internet real,word_freq_order real, word_freq_mail real, word_freq_receive real, word_freq_will real,word_freq_people real, word_freq_report real, word_freq_addresses real, word_freq_free real,word_freq_business real, word_freq_email real, word_freq_you real, word_freq_credit real,word_freq_your real, word_freq_font real, word_freq_000 real, word_freq_money real,word_freq_hp real, word_freq_hpl real, word_freq_george real, word_freq_650 real, word_freq_lab real,word_freq_labs real, word_freq_telnet real, word_freq_857 real, word_freq_data real,word_freq_415 real, word_freq_85 real, word_freq_technology real, word_freq_1999 real,word_freq_parts real, word_freq_pm real, word_freq_direct real, word_freq_cs real, word_freq_meeting real,word_freq_original real, word_freq_project real, word_freq_re real, word_freq_edu real,word_freq_table real, word_freq_conference real, char_freq_semicolon real, char_freq_leftParen real,char_freq_leftBracket real, char_freq_exclamation real, char_freq_dollar real, char_freq_pound real, capital_run_length_average real, capital_run_length_longest real, capital_run_length_total real, spam integer) WITH (CLUSTERED COLUMNSTORE INDEX, DISTRIBUTION = ROUND_ROBIN);
GO
```

Copiar os dados usando bcp:

```bash
bcp spam in spambaseHeaders.data -q -c -t  ',' -S <server-name>.database.windows.net -d <database-name> -U <username> -P <password> -F 1 -r "\r\n"
```

> [!NOTE]
> O arquivo baixado contém finais de linha no estilo Windows. A ferramenta bcp espera finais de linha no estilo Unix. Use a bandeira -r para dizer bcp.

Em seguida, consulta usando sqlcmd:

```sql
select top 10 spam, char_freq_dollar from spam;
GO
```

Você também pode consultar usando SQuirreL SQL. Siga etapas semelhantes ao PostgreSQL usando o driver JDBC do SQL Server. O driver JDBC está na pasta /usr/share/java/jdbcdrivers/sqljdbc42.jar.

## <a name="next-steps"></a>Próximas etapas

Para obter uma visão geral dos artigos que o guiam sobre as tarefas que compõem o processo de ciência de dados no Azure, consulte [Team Data Science Process](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/overview).

Para obter uma descrição dos passoados de ponta a ponta que demonstram as etapas do Processo de Ciência de Dados da Equipe para cenários específicos, consulte [os passoados](../team-data-science-process/walkthroughs.md)do Processo de Ciência de Dados da Equipe . As orientações também mostram como combinar ferramentas e serviços de nuvem e locais em um fluxo de trabalho ou pipeline para criar um aplicativo inteligente.
