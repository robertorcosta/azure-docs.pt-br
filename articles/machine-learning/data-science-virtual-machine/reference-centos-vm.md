---
title: 'Referência: Centos Data Science Virtual Machine'
titleSuffix: Azure Data Science Virtual Machine
description: Detalhes sobre ferramentas incluídas na Máquina Virtual de Data Science do CentOS
author: gvashishtha
ms.service: machine-learning
ms.subservice: data-science-vm
ms.author: gopalv
ms.date: 09/11/2019
ms.topic: reference
ms.openlocfilehash: 05da812023e7525b87a3d7ef1ce1940339f99791
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/01/2020
ms.locfileid: "80475486"
---
# <a name="reference-centos-linux-data-science-virtual-machine"></a>Referência: CentOS (Linux) Data Science Virtual Machine

A DSVM (Máquina Virtual de Ciência de Dados) do Linux é uma máquina virtual do Azure baseada no CentOS. A DSVM do Linux vem com uma coleção de ferramentas pré-instaladas que você pode usar para análise de dados e aprendizado de máquina. 

Os principais componentes de software incluídos em uma DSVM do Linux são:

* Sistema operacional de distribuição do CentOS Linux.
* Microsoft Machine Learning Server.
* Distribuição do Anaconda Python (versões 3.5 e 2.7), incluindo bibliotecas de análise de dados populares.
* JuliaPro, uma distribuição curada da linguagem Julia e de bibliotecas populares de análise de dados e científicas.
* Instância autônoma do Spark e Hadoop de nó único (HDFS, YARN).
* JupyterHub, um servidor do Jupyter Notebook multiusuário compatível com kernels R, Python, PySpark e Julia.
* Gerenciador de Armazenamento do Azure.
* CLI do Azure, a interface de linha de comando do Azure para gerenciar recursos do Azure.
* Banco de dados PostgresSQL.
* Ferramentas de aprendizado de máquina:
  * [CNTK (Microsoft Cognitive Toolkit)](https://github.com/Microsoft/CNTK), um kit de ferramentas do software de aprendizado profundo da Microsoft Research.
  * [Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit), um sistema de aprendizado de máquina rápido compatível com técnicas como online, hashing, allreduce, reduções, learning2search, aprendizado ativo e interativo.
  * [XGBoost](https://xgboost.readthedocs.org/en/latest/), uma ferramenta que fornece implementação de árvore aumentada rápida e precisa.
  * [Rattle](https://togaware.com/rattle/), uma ferramenta que facilita o processo de começar a usar análise de dados e aprendizado de máquina em R. O Rattle oferece exploração e modelagem de dados baseados em GUI usando a geração automática de código R.
* SDK do Azure em Java, Python, Node.js, Ruby e PHP.
* Bibliotecas em R e Python para uso no Azure Machine Learning e em outros serviços do Azure.
* Ferramentas de desenvolvimento e editores (RStudio, PyCharm, IntelliJ, Emacs, gedit, vi).

Ciência de dados envolve a iteração em uma sequência de tarefas:

1. Localizar, carregar e pré-processar dados.
1. Compilar e testar modelos.
1. Implantar os modelos para consumo em aplicativos inteligentes.

Cientistas de dados usam várias ferramentas para concluir essas tarefas. Pode ser demorado encontrar as versões corretas do software e, depois, baixar, compilar e instalá-las.

A DSVM do Linux pode aliviar essa carga substancialmente. Use a DSVM do Linux para iniciar rapidamente seu projeto de análise. Ela ajuda você a trabalhar em tarefas em várias linguagens, incluindo R, Python, SQL, Java e C++. O Eclipse fornece um IDE fácil de usar para desenvolver e testar seu código. O SDK do Azure, incluído na DSVM, ajuda você a criar seus aplicativos usando vários serviços em Linux para a plataforma de nuvem da Microsoft. Outras linguagens são pré-instaladas, incluindo Ruby, Perl, PHP e Node.js.

Não há encargos de software para essa imagem da DSVM. Você paga apenas pelos valores de uso de hardware do Azure, que são avaliadas com base no tamanho da máquina virtual que você provisiona com a imagem da DSVM. Para obter mais informações sobre as taxas de computação, consulte a [lista de Data Science Virtual Machine for Linux](https://azure.microsoft.com/marketplace/apps/microsoft-dsvm.linux-data-science-vm-ubuntu) no Azure Marketplace.


## <a name="machine-learning-server"></a>Machine Learning Server

R é uma das linguagens mais populares para análise de dados e aprendizado de máquina. Se deseja usar o R para sua análise, a DSVM tem o Machine Learning Server com o Microsoft R Open e a Math Kernel Library. A Math Kernel Library otimiza operações matemáticas comuns em algoritmos analíticos. O R Open é completamente compatível com CRAN-R e qualquer uma das bibliotecas R publicadas em CRAN pode ser instalada no R Open. 

Você pode usar o Machine Learning Server para dimensionar e colocar em operação modelos de R em serviços Web. Edite seus programas R em um dos editores padrão como RStudio, vi ou Emacs. O editor Emacs é pré-instalado na DSVM. O pacote Emacs ESS (Emacs Speaks Statistics) simplifica o trabalho com arquivos R no editor Emacs.

Para abrir o console R, na concha, digite **R**. Este comando leva você a um ambiente interativo. Para desenvolver seu programa R, normalmente você usa um editor como vi ou Emacs e, em seguida, executa os scripts no R. O RStudio oferece um IDE gráfico completo para desenvolver seu programa R.

Um script R que você pode usar para instalar os [20 principais pacotes de R](https://www.kdnuggets.com/2015/06/top-20-r-packages.html) está incluído na DSVM. Você poderá executar esse script quando estiver na interface do R Interativo. Conforme mencionado anteriormente, para abrir essa interface, digite **R** no shell.  

## <a name="python"></a>Python

O Anaconda Python é instalado com os ambientes Python 3.5 e 2.7. O ambiente 2.7 é chamado _raiz_ e o ambiente 3.5 é chamado _de py35_. Essa distribuição contém o Python base com aproximadamente 300 dos mais populares pacotes de matemática, engenharia e análise de dados.

O ambiente py35 é o padrão. Para ativar o ambiente raiz (2.7), use este comando:

```bash
source activate root
```

Para ativar o ambiente py35 novamente, use este comando:

```bash
source activate py35
```

Para invocar uma sessão interativa do Python, insira **python** no shell. 

Instale outras bibliotecas Python usando Conda ou pip. Para pip, ative o ambiente correto primeiro se você não quiser o padrão:

```bash
source activate root
pip install <package>
```

Ou especifique o caminho completo até o pip:

```bash
/anaconda/bin/pip install <package>
```

Para Conda, você deve sempre especificar o nome do ambiente (py35 ou raiz):

```bash
conda install <package> -n py35
```

Se estiver em uma interface gráfica ou tiver a configuração do encaminhamento X11, você poderá inserir o comando **pycharm** para abrir o IDE do PyCharm Python. Você pode usar os editores de texto padrão. Além disso, você pode usar o Spyder, um IDE do Python que é fornecido com distribuições do Anaconda Python. O Spyder precisa de uma área de trabalho gráfica ou de encaminhamento X11. A área de trabalho gráfica tem um atalho para o Spyder.

## <a name="jupyter-notebook"></a>Bloco de anotações do Jupyter

A distribuição do Anaconda também acompanha um Jupyter Notebook, um ambiente de compartilhamento de código e de análise. Acessar o Jupyter Notebook por meio do JupyterHub. Entre usando seu nome de usuário e senha locais do Linux.

O servidor do Jupyter Notebook é pré-configurado com os kernels do Python 2, do Python 3 e do R. Use o ícone de área de trabalho do **Jupyter Notebook** para abrir o navegador e acessar o servidor do Jupyter Notebook. Se acessar a DSVM via cliente SSH ou X2Go, você também poderá acessar o servidor do Jupyter Notebook em https:\//localhost:8000/.

> [!NOTE]
> Continue se você obtiver quaisquer avisos de certificado.

Você pode acessar o servidor de bloco de anotações do Jupyter por meio de qualquer host. Digite **\//\<https: DSVM DNS\>nome ou endereço IP :8000/**.

> [!NOTE]
> A porta 8000 é aberta no firewall por padrão quando a DSVM é provisionada. 

A Microsoft empacotou exemplos de notebooks, um em Python em outro em R. Você pode ver o link para os exemplos na home page do Jupyter Notebook após efetuar a autenticação no Jupyter Notebook usando a senha e o nome de usuário Linux locais. Para criar um novo notebook, selecione **Novo** e, em seguida, selecione o kernel da linguagem que deseja usar. Caso não veja o botão **Novo**, selecione o ícone do **Jupyter** na parte superior esquerda para acessar a home page do servidor de notebook.

## <a name="spark-standalone"></a>Spark autônomo 

Uma instância do modo autônomo do Spark é pré-instalada na DSVM Linux para ajudar você a desenvolver aplicativos Spark localmente antes de testá-los e implantá-los em clusters grandes. 

Execute programas PySpark através do kernel de Jupyter. Ao abrir o Jupyter, selecione o botão **Novo** e você verá uma lista de kernels disponíveis. O **Spark – Python** é o kernel PySpark que permite a criação de aplicativos Spark usando a linguagem Python. Também é possível usar um IDE Python como PyCharm ou Spyder para criar seu programa em Spark. 

Nessa instância autônoma, a pilha do Spark é executada no programa de chamada cliente. Esse recurso torna mais rápido e fácil solucionar problemas, em comparação com o desenvolvimento em um cluster Spark.

O Jupyter oferece um notebook PySpark de exemplo. Você pode encontrá-lo no diretório SparkML no diretório base do Jupyter ($HOME/notebooks/SparkML/pySpark). 

Se você estiver programando em R para Spark, use o Machine Learning Server, SparkR ou sparklyr. 

Antes de executar em um contexto do Spark no Machine Learning Server, você precisa executar uma etapa de configuração única para habilitar uma instância local de HDFS Hadoop e YARN de nó único. Por padrão, os serviços do Hadoop serão instalados, mas desabilitados no DSVM. Para habilitar os serviços do Hadoop, execute os seguintes comandos como raiz na primeira vez:

```bash
echo -e 'y\n' | ssh-keygen -t rsa -P '' -f ~hadoop/.ssh/id_rsa
cat ~hadoop/.ssh/id_rsa.pub >> ~hadoop/.ssh/authorized_keys
chmod 0600 ~hadoop/.ssh/authorized_keys
chown hadoop:hadoop ~hadoop/.ssh/id_rsa
chown hadoop:hadoop ~hadoop/.ssh/id_rsa.pub
chown hadoop:hadoop ~hadoop/.ssh/authorized_keys
systemctl start hadoop-namenode hadoop-datanode hadoop-yarn
```

Será possível interromper os serviços relacionados ao Hadoop relacionados quando você não precisar deles executando `systemctl stop hadoop-namenode hadoop-datanode hadoop-yarn`.

O diretório /dsvm/samples/MRS oferece um exemplo que demonstra como desenvolver e testar o Machine Learning Server em um contexto de Spark remoto (a instância de Spark autônoma na DSVM).

## <a name="ides-and-editors"></a>IDEs e editores

Você pode escolher entre vários editores de código, incluindo vi/VIM, Emacs, gedit, PyCharm, RStudio, Eclipse, LaTeX e IntelliJ. 

* gedit, Eclipse, IntelliJ, R Studio e PyCharm são editores gráficos. Para usá-los, você precisa estar conectado a uma área de trabalho gráfica. Você os abre usando os atalhos de menu de área de trabalho e de aplicativo.

* Vim e Emacs são editores baseados em texto. No Emacs, o pacote complementar do ESS facilita o trabalho com R no editor Emacs. Você pode encontrar mais informações no [site do ESS](https://ess.r-project.org/).

* Eclipse é um IDE de software livre, extensível e que dá suporte a vários idiomas. O IDE do Eclipse para desenvolvedores de Java é a versão instalada na DSVM. Você pode instalar plug-ins de várias linguagens populares para estender o ambiente. 

  O plug-in Azure Toolkit for Eclipse é instalado com o Eclipse na DSVM. Você pode usar o Azure Toolkit for Eclipse para criar, desenvolver, testar e implantar aplicativos do Azure usando o ambiente de desenvolvimento do Eclipse, que dá suporte a linguagens como Java.

  O SDK do Azure para Java também é instalado com o Azure Toolkit for Eclipse na DSVM. O SDK do Azure para Java permite o acesso a diferentes serviços do Azure de dentro de um ambiente Java. 
  
  Para saber mais, confira [Kit de ferramentas do Azure para Eclipse](/java/azure/eclipse/azure-toolkit-for-eclipse).

* O LaTeX é instalado por meio do pacote texlive, juntamente com um pacote complementar do Emacs chamado [AUCTeX](https://www.gnu.org/software/auctex/manual/auctex/auctex.html). Esse pacote simplifica a criação de seus documentos do LaTeX no Emacs. 

## <a name="databases"></a>Bancos de dados

A DSVM do Linux fornece acesso a várias ferramentas de linha de comando e banco de dados.

### <a name="postgressql"></a>PostgresSQL

O banco de dados open-source PostgresSQL está disponível na DSVM, com os serviços em execução e initdb concluído. Você precisa criar bancos de dados e usuários. Para obter mais informações, confira a [Documentação do PostgresSQL](https://www.postgresql.org/docs/).  

### <a name="squirrel-sql"></a>SQuirreL SQL

O SQuirreL SQL é um cliente gráfico do SQL que pode se conectar a vários bancos de dados (incluindo o SQL Server, o PostgresSQL e o MySQL) e executar consultas SQL. É possível executar o SQuirreL SQL em uma sessão de área de trabalho gráfica (usando o cliente X2Go, por exemplo) usando um ícone de área de trabalho. Ou você pode executar o cliente usando o seguinte comando no shell:

```bash
/usr/local/squirrel-sql-3.7/squirrel-sql.sh    /usr/local/squirrel-sql-3.7/squirrel-sql.sh
```

Antes do primeiro uso, configure os drivers e aliases de banco de dados. Os drivers JDBC estão localizados em /usr/share/java/jdbcdrivers.

Para saber mais, confira [SQuirreL SQL](http://squirrel-sql.sourceforge.net/index.php?page=screenshots).

### <a name="command-line-tools-for-accessing-sql-server"></a>Ferramentas de linha de comando para acessar o SQL Server

O pacote de driver ODBC do SQL Server também vem com duas ferramentas de linha de comando:

* **bcp**: A ferramenta bcp copia em massa dados entre uma instância do SQL Server e um arquivo de dados em um formato especificado pelo usuário. Você pode usá-la para importar grandes números de novas linhas para tabelas do SQL Server ou para exportar dados de tabelas para arquivos de dados. Para importar dados para uma tabela, você deve usar um arquivo de formato criado para essa tabela. Ou você deve entender a estrutura da tabela e os tipos de dados que são válidos para suas colunas.

  Para saber mais, confira [Conectar-se com o bcp](https://msdn.microsoft.com/library/hh568446.aspx).

* **sqlcmd**: Você pode usar o utilitário sqlcmd para inserir instruções Transact-SQL, procedimentos do sistema e arquivos de script no prompt de comando. O utilitário sqlcmd usa o ODBC para executar lotes do Transact-SQL.

  Para saber mais, confira [Conectar-se com sqlcmd](https://msdn.microsoft.com/library/hh568447.aspx).

  > [!NOTE]
  > Há algumas diferenças nessa ferramenta entre as plataformas Linux e Windows. Consulte a documentação para obter detalhes.

### <a name="database-access-libraries"></a>Bibliotecas de acesso do banco de dados

As bibliotecas para acesso de banco de dados estão disponíveis em R e Python:

* No R, você pode usar o pacote RODBC ou o dplyr para consultar ou executar instruções SQL no servidor de banco de dados.
* No Python, a biblioteca pyodbc fornece acesso ao banco de dados com o ODBC como a camada subjacente.

## <a name="azure-tools"></a>Ferramentas do Azure

As ferramentas do Azure a seguir são instaladas na DSVM:

* **Azure CLI**: Você pode usar a interface de linha de comando no Azure para criar e gerenciar recursos do Azure através de comandos shell. Para abrir as ferramentas do Azure, insira **ajuda do azure**. Para saber mais, confira a [página de documentação da CLI do Azure](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2).
* **Azure Storage Explorer**: Azure Storage Explorer é uma ferramenta gráfica que você pode usar para navegar pelos objetos armazenados em sua conta de armazenamento Do Zure e para carregar e baixar dados de e para blobs do Azure. Você pode acessar o Gerenciador de Armazenamento do ícone de atalho da área de trabalho. Você também pode abri-lo em um prompt do shell inserindo **StorageExplorer**. É necessário estar conectado em um cliente X2Go ou ter a configuração de encaminhamento X11.
* **Bibliotecas Azure**: As seguintes bibliotecas estão pré-instaladas no DSVM:
  
  * **Python**: As bibliotecas relacionadas ao Azure em Python são *azul,* *azureml,* *pydocumentdb*e *pyodbc*. Com as três primeiras bibliotecas, você pode acessar os serviços de armazenamento do Azure, o Azure Machine Learning e o Azure Cosmos DB (um banco de dados NoSQL no Azure). A quarta biblioteca, pyodbc (juntamente com o Microsoft ODBC Driver for SQL Server), habilita, do Python, o acesso ao SQL Server, ao Banco de Dados SQL do Azure e ao SQL Data Warehouse do Azure pelo uso de uma interface do ODBC. Insira **pip list** para ver todas as bibliotecas listadas. Certifique-se de executar este comando nos ambientes do Python 2.7 e 3.5.
  * **R**: As bibliotecas relacionadas ao Azure em R são AzureML e RODBC.
  * **Java**: A lista de bibliotecas Java do Azure pode ser encontrada no diretório /dsvm/sdk/AzureSDKJava no DSVM. As bibliotecas principais são as APIs de armazenamento e gerenciamento do Azure, o Azure Cosmos DB e os drivers JDBC para SQL Server.  

Você pode acessar o [portal do Azure](https://portal.azure.com) do navegador Firefox pré-instalado. No portal do Azure, você pode criar, gerenciar e monitorar recursos do Azure.

## <a name="azure-machine-learning"></a>Azure Machine Learning

O Azure Machine Learning é um serviço de nuvem totalmente gerenciado que permite compilar, implantar e compartilhar soluções de análise preditiva. Você constrói seus experimentos e modelos a partir do Azure Machine Learning Studio (clássico). Para acessar o Azure Machine Learning a partir de um navegador web no DSVM, acesse o [Microsoft Azure Machine Learning](https://studio.azureml.net).

Depois de entrar no Azure Machine Learning Studio (clássico), você pode usar uma tela de experimentação para construir um fluxo lógico para os algoritmos de aprendizagem de máquina. Você também tem acesso a um Jupyter Notebook hospedado no Azure Machine Learning. O notebook pode trabalhar perfeitamente com os experimentos no Azure Machine Learning Studio (clássico). 

Coloque em operação os modelos de machine learning compilados encapsulando-os em uma interface de serviço Web. A operacionalização de modelos de machine learning permite que clientes escritos em qualquer linguagem invoquem previsões desses modelos. Para saber mais, confira a [Documentação do Machine Learning](https://azure.microsoft.com/documentation/services/machine-learning/).

Você também pode criar seus modelos em R ou Python na DSVM e, em seguida, implantá-los em produção no Azure Machine Learning. A Microsoft instalou bibliotecas em R (**AzureML**) e Python (**azureml**) para dar suporte a essa funcionalidade.

Para saber mais sobre como implantar modelos em R e Python no Azure Machine Learning, confira [Dez coisas que você pode fazer na Máquina Virtual de Ciência de Dados](vm-do-ten-things.md).

> [!NOTE]
> As instruções em [Dez coisas que você pode fazer na Máquina Virtual de Ciência de Dados](vm-do-ten-things.md) foram escritas para a versão do Windows da DSVM. No entanto, as informações sobre a implantação de modelos no Azure Machine Learning também se aplicam à DSVM do Linux.

## <a name="machine-learning-tools"></a>Ferramentas de Machine Learning

A DSVM vem com algumas ferramentas e algoritmos de aprendizado de máquina pré-compiladas e pré-instaladas localmente. Eles incluem:

* **Microsoft Cognitive Toolkit**: Um kit de ferramentas de aprendizagem profunda.
* **Vowpal Wabbit**: um algoritmo de aprendizado rápido online.
* **XGBoost**: Uma ferramenta que fornece algoritmos de árvore otimizados e impulsionados.
* **Python**: o Anaconda Python é fornecido com os algoritmos de aprendizado de máquina com bibliotecas como Scikit-learn. Você pode instalar outras bibliotecas usando o comando `pip install` .
* **R**: Uma rica biblioteca de funções de aprendizado de máquina está disponível para R. Bibliotecas pré-instaladas incluem lm, glm, randomForest e rpart. Você pode instalar outras bibliotecas executando `install.packages(<lib name>)`.

Microsoft Cognitive Toolkit, Vowpal wabbit e XGBoost são discutidos mais detalhadamente nas próximas seções.

### <a name="microsoft-cognitive-toolkit"></a>Kit de Ferramentas Cognitivas da Microsoft

O Microsoft Cognitive Toolkit é um kit de ferramentas de aprendizado profundo open-source. Ele é uma CNTK (ferramenta de linha de comando) e já está em PATH.

Para executar um exemplo básico, execute os seguintes comandos no shell:

```bash
cd /home/[USERNAME]/notebooks/CNTK/HelloWorld-LogisticRegression
cntk configFile=lr_bs.cntk makeMode=false command=Train
```

Para saber mais, confira o [Repositório de CNTK do GitHub](https://github.com/Microsoft/CNTK) e a [wiki do CNTK](https://github.com/Microsoft/CNTK/wiki).

### <a name="vowpal-wabbit"></a>Vowpal Wabbit

O Vowpal Wabbit é um sistema de aprendizado de máquina rápido que usa técnicas como online, hashing, allreduce, reduções, learning2search, aprendizado ativo e interativo.

Para executar a ferramenta em um exemplo básico, execute os seguintes comandos:

```bash
cp -r /dsvm/tools/VowpalWabbit/demo vwdemo
cd vwdemo
vw house_dataset
```

O diretório de demonstração do Vowpal Wabbit inclui outras demonstrações mais amplas. Para saber mais sobre o Vowpal Wabbit, confira o [Repositório do Vowpal Wabbit no GitHub](https://github.com/JohnLangford/vowpal_wabbit) e a [wiki do Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit/wiki).

### <a name="xgboost"></a>XGBoost

A biblioteca XGBoost foi projetada e otimizada para algoritmos aumentados (de árvore). O objetivo da biblioteca XGBoost é estender os limites de computação das máquinas para os extremos necessários de modo a fornecer aumento de árvore de grande escala escalonável, portátil e preciso.

O XGBoost é fornecido como uma linha de comando, bem como uma biblioteca do R.

Para usar a biblioteca XGBoost em R, inicie uma sessão interativa de R (no shell, digite **R**) e, em seguida, carregue a biblioteca.

Veja um exemplo simples que você pode executar no prompt do R:

```R
library(xgboost)

data(agaricus.train, package='xgboost')
data(agaricus.test, package='xgboost')
train <- agaricus.train
test <- agaricus.test
bst <- xgboost(data = train$data, label = train$label, max.depth = 2,
                eta = 1, nthread = 2, nround = 2, objective = "binary:logistic")
pred <- predict(bst, test$data)
```

Para executar a linha de comando do XGBoost, execute estes comandos no shell:

```bash
cp -r /dsvm/tools/xgboost/demo/binary_classification/ xgboostdemo
cd xgboostdemo
xgboost mushroom.conf
```

Um arquivo .model é gravado no diretório especificado. Para saber sobre este exemplo de demonstração no GitHub, confira [Classificação Binária](https://github.com/dmlc/xgboost/tree/master/demo/binary_classification).

Para saber mais sobre o XGBoost, confira a [documentação do XGBoost](https://xgboost.readthedocs.org/en/latest/) e o [Repositório do XGBoost no GitHub](https://github.com/dmlc/xgboost).

### <a name="rattle"></a>Rattle

Rattle (*R* *A*nalytical *T*ool *T*o *L*earn *E*asily – Ferramenta Analítica do R para Aprender com Facilidade) usa exploração e modelagem de dados com base em GUI. Rattle:
- Apresenta resumos estatísticos e visuais dos dados.
- Transforma dados que podem ser modelados prontamente.
- Compila modelos não supervisionados e supervisionados dos dados.
- Apresenta o desempenho dos modelos graficamente.
- Classifica novos conjuntos de dados.
- Gera código R.
- Replica operações na interface do usuário que podem ser executadas diretamente em R ou usadas como ponto de partida para análise posterior.

Para executar o Rattle, você precisa estar conectado a uma sessão da área de trabalho gráfica. Em um terminal, insira **R** para abrir o ambiente R. No prompt do R, digite os seguintes comandos:

```R
library(rattle)
rattle()
```

É aberta uma interface gráfica com um conjunto de guias. Use as seguintes etapas de início rápido no Rattle para usar um conjunto de dados meteorológicos de exemplo e criar um modelo. Em algumas das etapas, você deve instalar e carregar automaticamente alguns pacotes do R que ainda não estão no sistema.

> [!NOTE]
> Se não tiver permissões para instalar o pacote no diretório do sistema (o padrão), você poderá ver uma solicitação na janela do console do R para instalar pacotes na sua biblioteca pessoal. Caso veja esses prompts, insira **y**.

1. Selecione **Executar**.
1. Uma caixa de diálogo solicita que você carregue o conjunto de dados meteorológicos de exemplo. Selecione **Sim** para carregar o exemplo.
1. Selecione a guia **Modelo**.
1. Selecione **Executar** para criar uma árvore de decisão.
1. Selecione **Desenhar** para exibir a árvore de decisão.
1. Selecione a opção **Floresta** e, em seguida, selecione **Executar** para criar uma floresta aleatória.
1. Selecione a guia **Avaliar**.
1. Selecione a opção **Risco** e, em seguida, selecione **Executar** para exibir dois gráficos de desempenho de **Risco (Cumulativo)**.
1. Selecione a guia **Log** para mostrar o código R gerado para as operações anteriores. (Por causa de um bug na versão atual **#** do Rattle, você deve inserir um caractere na frente de **Exportar este registro** no texto do log.)
1. Selecione o botão **Exportar** para salvar o arquivo de script do R chamado *weather_script.R* na pasta base.

Você pode sair do Rattle e do R. Agora, você pode modificar o script de R gerado. Ou você pode usá-lo no estado em que se encontra e executá-lo em qualquer momento, para repetir tudo o que foi feito na interface do usuário do Rattle. Essa é uma maneira, especialmente para iniciantes em R, de fazer análise e aprendizado de máquina rapidamente em uma interface gráfica e, ao mesmo tempo, gerar código em R automaticamente para modificar ou aprender.

## <a name="next-steps"></a>Próximas etapas

Tem mais dúvidas? Considere criar um [bilhete de suporte](https://azure.microsoft.com/support/create-ticket/).