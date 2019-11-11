---
title: Instalar o Jupyter localmente e conectar-se a um cluster do Spark do Azure HDInsight
description: Saiba mais sobre como instalar o bloco de anotações do Jupyter localmente em seu computador e se conectar a um cluster Apache Spark.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/07/2019
ms.openlocfilehash: 225ee7028b9610a4974f9bee05da667d78d3355e
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/10/2019
ms.locfileid: "73903747"
---
# <a name="install-jupyter-notebook-on-your-computer-and-connect-to-apache-spark-on-hdinsight"></a>Instalar o bloco de anotações do Jupyter em seu computador e conectar-se ao Apache Spark no HDInsight

Neste artigo, você aprenderá a instalar o notebook Jupyter, com os kernels PySpark (para Python) e Apache Spark (para Scala) personalizados com o Spark Magic, e conectar o notebook a um cluster do HDInsight. Pode haver inúmeros motivos para instalar o Jupyter no computador local e alguns desafios também. Para saber mais sobre isso, confira a seção [Por que devo instalar o Jupyter no meu computador](#why-should-i-install-jupyter-on-my-computer) no final deste artigo.

Há quatro etapas principais envolvidas na instalação do Jupyter e na conexão com o Apache Spark no HDInsight.

* Configure o cluster Spark.
* Instalar Jupyter notebook.
* Instale os kernels PySpark e Spark com a mágica do Spark.
* Configure a mágica do Spark para acessar o cluster Spark no HDInsight.

Para saber mais sobre os kernels personalizados e a sobre a mágica Spark disponível para blocos de notas Jupyter com o cluster HDInsight, confira [Kernels disponíveis para blocos de notas Jupyter com clusters do Apache Spark Linux no HDInsight](apache-spark-jupyter-notebook-kernels.md).

## <a name="prerequisites"></a>pré-requisitos

* Um cluster do Apache Spark no HDInsight. Para obter instruções, confira [Criar clusters do Apache Spark no Azure HDInsight](apache-spark-jupyter-spark-sql.md). Esse é um pré-requisito para conectar o notebook Jupyter a um cluster HDInsight depois que o notebook for instalado.

* Familiaridade com o uso de anotações do Jupyter com Spark no HDInsight.

## <a name="install-jupyter-notebook-on-your-computer"></a>Instalar bloco de notas Jupyter em seu computador

Você deve instalar o Python antes de instalar blocos de notas Jupyter. A [distribuição Anaconda](https://www.anaconda.com/download/) instalará o, o Python e o Jupyter notebook.

Baixe o [instalador do Anaconda](https://www.anaconda.com/download/) para sua plataforma e execute a instalação. Ao executar o assistente de instalação, não deixe de selecionar a opção de adicionar o Anaconda à variável PATH.  Consulte também, [instalando o Jupyter usando o Anaconda](https://jupyter.readthedocs.io/en/latest/install.html).

## <a name="install-spark-magic"></a>Instalar a mágica do Spark

1. Insira um dos comandos abaixo para instalar o Spark Magic. Consulte também a [documentação do sparkmagic](https://github.com/jupyter-incubator/sparkmagic#installation).

    |Versão do cluster | Comando de instalação |
    |---|---|
    |v 3.6 e v 3.5 |`pip install sparkmagic==0.13.1`|
    |v 3.4|`pip install sparkmagic==0.2.3`|

1. Verifique se `ipywidgets` está instalado corretamente executando o seguinte comando:

    ```cmd
    jupyter nbextension enable --py --sys-prefix widgetsnbextension
    ```

## <a name="install-pyspark-and-spark-kernels"></a>Instalar kernels PySpark e Spark

1. Identifique onde o `sparkmagic` está instalado digitando o seguinte comando:

    ```cmd
    pip show sparkmagic
    ```

    Em seguida, altere seu diretório de trabalho para o local identificado com o comando acima.

1. No seu novo diretório de trabalho, insira um ou mais dos comandos abaixo para instalar os kernels desejados:

    |Kernel | Comando |
    |---|---|
    |Spark|`jupyter-kernelspec install sparkmagic/kernels/sparkkernel`|
    |SparkR|`jupyter-kernelspec install sparkmagic/kernels/sparkrkernel`|
    |PySpark|`jupyter-kernelspec install sparkmagic/kernels/pysparkkernel`|
    |PySpark3|`jupyter-kernelspec install sparkmagic/kernels/pyspark3kernel`|

1. Opcional. Digite o comando a seguir para habilitar a extensão do servidor:

    ```cmd
    jupyter serverextension enable --py sparkmagic
    ```

## <a name="configure-spark-magic-to-connect-to-hdinsight-spark-cluster"></a>Configurar a mágica do Spark para se conectar ao cluster do HDInsight Spark

Nesta seção, você configura a mágica do Spark que você instalou anteriormente para se conectar a um cluster Apache Spark.

1. Inicie o Shell do Python com o seguinte comando:

    ```cmd
    python
    ```

2. As informações de configuração do Jupyter normalmente são armazenadas no diretório base dos usuários. Digite o seguinte comando para identificar o diretório base e crie uma pasta chamada **. sparkmagic**.  O caminho completo será disparado.

    ```python
    import os
    path = os.path.expanduser('~') + "\\.sparkmagic"
    os.makedirs(path)
    print(path)
    exit()
    ```

3. Dentro da pasta `.sparkmagic`, crie um arquivo chamado **config. JSON** e adicione o trecho JSON a seguir dentro dele.  

    ```json
    {
      "kernel_python_credentials" : {
        "username": "{USERNAME}",
        "base64_password": "{BASE64ENCODEDPASSWORD}",
        "url": "https://{CLUSTERDNSNAME}.azurehdinsight.net/livy"
      },

      "kernel_scala_credentials" : {
        "username": "{USERNAME}",
        "base64_password": "{BASE64ENCODEDPASSWORD}",
        "url": "https://{CLUSTERDNSNAME}.azurehdinsight.net/livy"
      },

      "custom_headers" : {
        "X-Requested-By": "livy"
      },

      "heartbeat_refresh_seconds": 5,
      "livy_server_heartbeat_timeout_seconds": 60,
      "heartbeat_retry_seconds": 1
    }
    ```

4. Faça as seguintes edições no arquivo:

    |Valor do modelo | Novo valor |
    |---|---|
    |USU|Logon do cluster, o padrão é `admin`.|
    |CLUSTERDNSNAME|Nome do cluster|
    |{BASE64ENCODEDPASSWORD}|Uma senha codificada em base64 para sua senha real.  Você pode gerar uma senha base64 em [https://www.url-encode-decode.com/base64-encode-decode/](https://www.url-encode-decode.com/base64-encode-decode/).|
    |`"livy_server_heartbeat_timeout_seconds": 60`|Mantenha-se usando `sparkmagic 0.12.7` (clusters v 3.5 e v 3.6).  Se estiver usando `sparkmagic 0.2.3` (clusters v 3.4), substitua por `"should_heartbeat": true`.|

    Você pode ver um arquivo de exemplo completo em [Sample config. JSON](https://github.com/jupyter-incubator/sparkmagic/blob/master/sparkmagic/example_config.json).

   > [!TIP]  
   > As pulsações são enviadas para garantir que as sessões não sejam perdidas. Quando um computador entra em suspensão ou está desligado, a pulsação não é enviada e, como resultado, a sessão é limpa. Para clusters v3.4, se desejar desabilitar esse comportamento, você poderá definir a configuração Livy `livy.server.interactive.heartbeat.timeout` para `0` da interface do usuário do Ambari. Para clusters v3.5, se você não definir a configuração de 3.5 ou acima, a sessão não será excluída.

5. Inicie o Jupyter. Use o comando do prompt de comando a seguir.

    ```cmd
    jupyter notebook
    ```

6. Verifique se você pode usar a mágica do Spark disponível com os kernels. Execute as seguintes etapas.

    a. Crie um novo bloco de anotações. No canto direito, selecione **novo**. Você deve ver o kernel padrão **Python 2** ou **Python 3** e os kernels que você instalou. Os valores reais podem variar dependendo das suas opções de instalação.  Selecione **PySpark**.

    ![Kernels disponíveis no notebook Jupyter](./media/apache-spark-jupyter-notebook-install-locally/jupyter-kernels-notebook.png "Kernels no notebook Jupyter")

    > [!IMPORTANT]  
    > Depois de selecionar **nova** revisão, seu shell para quaisquer erros.  Se você vir o erro `TypeError: __init__() got an unexpected keyword argument 'io_loop'` você pode estar enfrentando um problema conhecido com determinadas versões do tornado.  Nesse caso, pare o kernel e, em seguida, faça o downgrade da instalação do tornado com o seguinte comando: `pip install tornado==4.5.3`.

    b. Execute o snippet de código a seguir.

    ```sql
    %%sql
    SELECT * FROM hivesampletable LIMIT 5
    ```  

    Se você puder recuperar a saída com êxito, a conexão com o cluster HDInsight será testada.

    Se você quiser atualizar a configuração do bloco de anotações para se conectar a um cluster diferente, atualize o config. JSON com o novo conjunto de valores, conforme mostrado na etapa 3, acima.

## <a name="why-should-i-install-jupyter-on-my-computer"></a>Por que devo instalar o Jupyter no meu computador?

Pode haver vários motivos pelos quais você pode querer instalar o Jupyter em seu computador e, em seguida, conectá-lo a um cluster do Apache Spark no HDInsight.

* Mesmo que os blocos de notas Jupyter já estejam disponíveis no cluster Spark no Azure HDInsight, a instalação do Jupyter no seu computador fornece a opção de criar os blocos de notas localmente, de testar o aplicativo em um cluster em execução e de carregar os blocos de notas no cluster. Para carregar os blocos de notas no cluster, você pode carregá-los usando o bloco de notas Jupyter que está em execução ou o cluster, ou salvá-los na pasta /HdiNotebooks na conta de armazenamento associada ao cluster. Para saber mais sobre como os blocos de notas são armazenados no cluster, confira [Onde os blocos de notas Jupyter são armazenados](apache-spark-jupyter-notebook-kernels.md#where-are-the-notebooks-stored)?
* Com os blocos de notas disponíveis localmente, você pode se conectar a clusters Spark diferentes com base nos requisitos do aplicativo.
* É possível usar o GitHub para implementar um sistema de controle de origem e ter o controle de versão para os blocos de notas. Você também pode ter um ambiente colaborativo, onde vários usuários podem trabalhar com o mesmo bloco de notas.
* Você pode trabalhar com blocos de notas localmente sem sequer ter um cluster em operação. É preciso apenas um cluster no qual testar seus blocos de notas, e não para gerenciar manualmente os blocos de notas ou um ambiente de desenvolvimento.
* Pode ser mais fácil configurar seu próprio ambiente de desenvolvimento local do que configurar a instalação do Jupyter no cluster.  Você pode aproveitar todos os softwares que você instalou localmente sem configurar um ou mais clusters remotos.

> [!WARNING]  
> Com o Jupyter instalado no computador local, vários usuários podem executar o mesmo bloco de notas no mesmo cluster Spark ao mesmo tempo. Nessa situação, várias sessões Livy são criadas. Se você tiver um problema e desejar depurá-lo, será uma tarefa complexa rastrear qual sessão Livy pertence a qual usuário.  

## <a name="next-steps"></a>Próximas etapas

* [Visão geral: Apache Spark no Azure HDInsight](apache-spark-overview.md)
* [Apache Spark com BI: execute análise de dados interativa usando o Spark no HDInsight com ferramentas de BI](apache-spark-use-bi-tools.md)
* [Apache Spark com aprendizado de máquina: use o Spark no HDInsight para analisar a temperatura do edifício usando dados de HVAC](apache-spark-ipython-notebook-machine-learning.md)
