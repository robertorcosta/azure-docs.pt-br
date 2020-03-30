---
title: Plataformas de dados com compatíveis
titleSuffix: Azure Data Science Virtual Machine
description: Conheça as plataformas e ferramentas de dados suportadas para a Máquina Virtual de Data Science do Azure.
keywords: ferramentas de ciência de dados, máquina virtual de ciência de dados, ferramentas para ciência de dados, ciência de dados do linux
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: lobrien
ms.author: laobri
ms.topic: conceptual
ms.date: 12/12/2019
ms.openlocfilehash: cd787881957d78f179107e46b2650de4618c7724
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80282317"
---
# <a name="data-platforms-supported-on-the-data-science-virtual-machine"></a>Plataformas de dados compatíveis com a Máquina Virtual de Ciência de Dados

Com uma Máquina Virtual de Data Science (DSVM), você pode construir suas análises contra uma ampla gama de plataformas de dados. Além das interfaces para plataformas de dados remotas, a DSVM oferece uma instância local para rápido desenvolvimento e criação de protótipos.

As seguintes ferramentas de plataforma de dados são suportadas no DSVM.

## <a name="sql-server-developer-edition"></a>SQL Server Developer Edition

| | |
| ------------- | ------------- |
| O que é?   | Uma instância de banco de dados relacional local      |
| Edições DSVM suportadas      | Windows 2016: SQL Server 2017, Windows 2019: SQL Server 2019      |
| Usos típicos      | Desenvolvimento rápido localmente com o menor conjunto de dados <br/> Executar R no banco de dados   |
| Links para exemplos      |    Uma pequena amostra de um conjunto de dados da cidade de Nova York é carregada no banco de dados SQL:<br/>  `nyctaxi` <br/> A amostra jupyter mostrando o Microsoft Machine Learning Server e análises no banco de dados pode ser encontrada em:<br/> `~notebooks/SQL_R_Services_End_to_End_Tutorial.ipynb`  |
| Ferramentas relacionadas no DSVM       | SQL Server Management Studio <br/> Drivers ODBC/JDBC<br/> pyodbc, RODBC<br />Análise do Apache      |

> [!NOTE]
> SQL Server Developer Edition pode ser usado apenas para fins de desenvolvimento e teste. Você precisa de uma licença ou de uma das VMs do SQL Server para executá-lo em produção.


### <a name="setup"></a>Instalação

O servidor de banco de dados já está pré-configurado `SQL Server (MSSQLSERVER)`e os serviços do Windows relacionados ao SQL Server (like) são definidos para serem executados automaticamente. A única etapa manual envolve ativar a análise no banco de dados usando o Microsoft Machine Learning Server. Você pode habilitar a análise executando o seguinte comando como uma ação única no SQL Server Management Studio (SSMS). Execute este comando depois de fazer login como administrador da máquina, abra uma nova consulta `master`no SSMS e certifique-se de que o banco de dados selecionado é:

        CREATE LOGIN [%COMPUTERNAME%\SQLRUserGroup] FROM WINDOWS 

        (Replace %COMPUTERNAME% with your VM name.)
       
Para executar o SQL Server Management Studio, você pode procurar por "SQL Server Management Studio" na lista de programas ou usar o Windows Search para encontrá-lo e executá-lo. Quando solicitado para credenciais, selecione **Autenticação** ```localhost``` do Windows e use o nome da máquina ou no campo **Nome do servidor SQL.**

### <a name="how-to-use-and-run-it"></a>Como usá-lo e executá-lo

Por padrão, o servidor de banco de dados com a instância padrão do banco de dados é executado automaticamente. Você pode usar ferramentas como o SQL Server Management Studio na VM para acessar o banco de dados do SQL Server localmente. As contas do administrador local têm acesso ao administrador no banco de dados.

Além disso, o DSVM vem com drivers ODBC e JDBC para conversar com sql server, bancos de dados SQL Azure e Azure SQL data warehouse a partir de aplicativos escritos em vários idiomas, incluindo Python e Machine Learning Server.

### <a name="how-is-it-configured-and-installed-on-the-dsvm"></a>Como ele está configurado e instalado no DSVM? 

 O SQL Server é instalado da maneira padrão. Ele pode ser encontrado em `C:\Program Files\Microsoft SQL Server`. A instância do Servidor de Aprendizagem de Máquina no banco de dados é encontrada em `C:\Program Files\Microsoft SQL Server\MSSQL13.MSSQLSERVER\R_SERVICES`. O DSVM também tem uma instância separada do Servidor `C:\Program Files\Microsoft\R Server\R_SERVER`de Aprendizado de Máquina autônomo, que é instalada em . Essas duas instâncias do Machine Learning Server não compartilham bibliotecas.


## <a name="apache-spark-2x-standalone"></a>Apache Spark 2.x (autônomo)

| | |
| ------------- | ------------- |
| O que é?   | Uma instância autônoma (nó único em processo) da popular plataforma Apache Spark; um sistema para processamento rápido de dados em larga escala e aprendizado de máquina     |
| Edições DSVM suportadas      | Linux     |
| Usos típicos      | * Desenvolvimento rápido de aplicativos Spark/PySpark localmente com um conjunto de dados menor e posterior implantação em grandes clusters Spark, como o Azure HDInsight<br/> * Teste o contexto de faísca do microsoft machine learning server <br />* Use a biblioteca [MMLSpark](https://github.com/Azure/mmlspark) de código aberto da SparkML ou da Microsoft para criar aplicativos ML |
| Links para exemplos      |    Exemplo de Jupyter: <br />&nbsp;&nbsp;* ~/notebooks/SparkML/pySpark <br /> &nbsp;&nbsp;* ~/notebooks/MMLSpark <br /> Microsoft Machine Learning Server (contexto spark): /dsvm/samples/MRS/MRSSparkContextSample.R |
| Ferramentas relacionadas no DSVM       | PySpark, Scala<br/>Jupyter (kernels Spark/PySpark)<br/>Microsoft Machine Learning Server, SparkR, Sparklyr <br />Análise do Apache      |

### <a name="how-to-use-it"></a>Como usar
Você pode enviar trabalhos spark na `spark-submit` `pyspark` linha de comando executando o comando ou. Você também pode criar um bloco de anotações do Jupyter ao criar um novo bloco de anotações com o kernel Spark.

Você pode usar o Spark from R usando bibliotecas como SparkR, Sparklyr e Microsoft Machine Learning Server, que estão disponíveis no DSVM. Consulte ponteiros para exemplos na tabela anterior.

### <a name="setup"></a>Instalação
Antes de ser executado em um contexto Spark no Microsoft Machine Learning Server na edição DSVM do Ubuntu Linux, você deve concluir uma etapa de configuração única para habilitar uma instância local de um único nó Hadoop HDFS e Yarn. Por padrão, os serviços do Hadoop serão instalados, mas desabilitados no DSVM. Para habilitá-los, execute os seguintes comandos como raiz na primeira vez:

    echo -e 'y\n' | ssh-keygen -t rsa -P '' -f ~hadoop/.ssh/id_rsa
    cat ~hadoop/.ssh/id_rsa.pub >> ~hadoop/.ssh/authorized_keys
    chmod 0600 ~hadoop/.ssh/authorized_keys
    chown hadoop:hadoop ~hadoop/.ssh/id_rsa
    chown hadoop:hadoop ~hadoop/.ssh/id_rsa.pub
    chown hadoop:hadoop ~hadoop/.ssh/authorized_keys
    systemctl start hadoop-namenode hadoop-datanode hadoop-yarn

Você pode parar os serviços relacionados ao Hadoop ```systemctl stop hadoop-namenode hadoop-datanode hadoop-yarn```quando você não precisar mais deles executando .

Uma amostra que demonstra como desenvolver e testar a MRS em um contexto remoto de Faísca (que é `/dsvm/samples/MRS` a instância de Faísca autônoma no DSVM) é fornecida e disponível no diretório.


### <a name="how-is-it-configured-and-installed-on-the-dsvm"></a>Como ele está configurado e instalado no DSVM? 
|Plataforma|Local de instalação ($SPARK_HOME)|
|:--------|:--------|
|Linux   | /dsvm/tools/spark-X.X.X-bin-hadoopX.X|


Bibliotecas para acessar dados do armazenamento Azure Blob ou do Azure Data Lake Storage, usando as bibliotecas de aprendizado de máquina microsoft MMLSpark, são pré-instaladas em $SPARK_HOME/jars. Esses JARs são carregados automaticamente quando o Spark é inicializado. Por padrão, o Spark usa dados no disco local. 

Para a instância Spark no DSVM para acessar dados armazenados no armazenamento Blob ou `core-site.xml` no Azure Data Lake Storage, você deve criar e configurar o arquivo com base no modelo encontrado em $SPARK_HOME/conf/core-site.xml.template. Você também deve ter as credenciais apropriadas para acessar o armazenamento Blob e o Azure Data Lake Storage. (Observe que os arquivos de modelo usam espaços reservados para armazenamento Blob e configurações de armazenamento do Lago de Dados Do Azure.)

Para obter informações mais detalhadas sobre a criação de credenciais do serviço de armazenamento do Lago de Dados Azure, consulte [Autenticação com o Azure Data Lake Storage Gen1](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-authenticate-using-active-directory). Depois que as credenciais para armazenamento Blob ou Armazenamento do Lago de Dados Do Azure forem inseridas no arquivo core-site.xml, você pode referenciar os dados armazenados nessas fontes através do prefixo URI de wasb:// ou adl://.

