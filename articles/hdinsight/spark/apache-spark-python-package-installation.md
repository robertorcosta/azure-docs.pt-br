---
title: Ação de script para pacotes Python com Jupyter no Azure HDInsight
description: Instruções passo a passo sobre como usar ação de script para configurar os blocos de anotações do Jupyter disponíveis com clusters Spark no HDInsight para usar pacotes Python externos.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 03/16/2020
ms.openlocfilehash: 659af8b85cb3736d663e79676b04af8041aeabfb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80129667"
---
# <a name="safely-manage-python-environment-on-azure-hdinsight-using-script-action"></a>Gerenciar com segurança o ambiente do Python no Azure HDInsight usando a Ação de Script

> [!div class="op_single_selector"]
> * [Usando a mágica da célula](apache-spark-jupyter-notebook-use-external-packages.md)
> * [Usando a ação de script](apache-spark-python-package-installation.md)

O HDInsight possui duas instalações Python incorporadas no cluster Spark, Anaconda Python 2.7 e Python 3.5. Em alguns casos, os clientes precisam personalizar o ambiente Python, como instalar pacotes Python externos ou outra versão Python. Neste artigo, mostramos as melhores práticas de gerenciamento seguro de ambientes Python para um cluster [Apache Spark](./apache-spark-overview.md) no HDInsight.

## <a name="prerequisites"></a>Pré-requisitos

Um cluster do Apache Spark no HDInsight. Para obter instruções, consulte o artigo sobre como [Criar clusters do Apache Spark no Azure HDInsight](apache-spark-jupyter-spark-sql.md). Se você ainda não tiver um cluster Spark no HDInsight, você pode executar ações de script durante a criação de clusters.  Acesse a documentação em [como usar ações de script personalizadas](../hdinsight-hadoop-customize-cluster-linux.md).

## <a name="support-for-open-source-software-used-on-hdinsight-clusters"></a>Suporte para software livre utilizado em clusters do HDInsight

O serviço Microsoft Azure HDInsight usa um ecossistema de tecnologias de código aberto formado em torno do Apache Hadoop. O Microsoft Azure fornece um nível geral de suporte para tecnologias de software livre. Para obter mais informações, consulte [o site de perguntas frequentes do Azure Support .](https://azure.microsoft.com/support/faq/) O serviço HDInsight fornece um nível adicional de suporte a componentes internos.

Há dois tipos de componentes de software livre disponíveis no serviço HDInsight:

|Componente |Descrição |
|---|---|
|Interno|Esses componentes são pré-instalados em clusters HDInsight e fornecem a funcionalidade principal do cluster. Por exemplo, apache Hadoop YARN Resource Manager, a língua de consulta apache colmeia (HiveQL) e a biblioteca Mahout pertencem a esta categoria. Uma lista completa de componentes de cluster está disponível em [O que há de novo nas versões de cluster do Apache Hadoop fornecidas pelo HDInsight](../hdinsight-component-versioning.md).|
|Personalizado|Você, como usuário do cluster, pode instalar ou usar em sua carga de trabalho qualquer componente disponível na comunidade ou criado por você.|

> [!IMPORTANT]
> Componentes fornecidos com o cluster HDInsight contam com suporte total. O Suporte da Microsoft ajuda a isolar e resolver problemas relacionados a esses componentes.
>
> Componentes personalizados recebem suporte comercialmente razoável para ajudá-lo a solucionar o problema. O suporte da Microsoft pode estar apto a resolver o problema OU pode solicitar que você contate canais disponíveis para as tecnologias de software livre em que é encontrado conhecimento profundo sobre essa tecnologia. Por exemplo, existem muitos sites comunitários que podem ser usados, [https://stackoverflow.com](https://stackoverflow.com)como: [fórum MSDN para HDInsight](https://social.msdn.microsoft.com/Forums/azure/home?forum=hdinsight), . Também os projetos Apache [https://apache.org](https://apache.org)têm sites de projetos em , por exemplo: [Hadoop](https://hadoop.apache.org/).

## <a name="understand-default-python-installation"></a>Entenda a instalação padrão do Python

O cluster HDInsight Spark é criado com a instalação do Anaconda. Existem duas instalações Python no cluster, Anaconda Python 2.7 e Python 3.5. A tabela abaixo mostra as configurações padrão do Python para Spark, Livy e Jupyter.

| |Python 2,7|Python 3.5|
|----|----|----|
|Caminho|/usr/bin/anaconda/bin|/usr/bin/anaconda/envs/py35/bin|
|Spark|Padrão definido como 2.7|N/D|
|Livy|Padrão definido como 2.7|N/D|
|Jupyter|Kernel PySpark|Núcleo PySpark3|

## <a name="safely-install-external-python-packages"></a>Instale com segurança pacotes Python externos

O cluster HDInsight depende do ambiente Python incorporado, tanto o Python 2.7 quanto o Python 3.5. A instalação direta de pacotes personalizados nesses ambientes integrados padrão pode causar alterações inesperadas na versão da biblioteca e quebrar ainda mais o cluster. Para instalar com segurança pacotes Python externos personalizados para seus aplicativos Spark, siga as etapas abaixo.

1. Crie o ambiente virtual Python usando conda. Um ambiente virtual proporciona um espaço isolado para seus projetos sem quebrar outros. Ao criar o ambiente virtual Python, você pode especificar a versão python que deseja usar. Observe que você ainda precisa criar ambiente virtual, mesmo que você gostaria de usar Python 2.7 e 3.5. Isso é para garantir que o ambiente padrão do cluster não seja quebrado. Execute ações de script em seu cluster para todos os nós com script abaixo para criar um ambiente virtual Python.

    -   `--prefix`especifica um caminho onde vive um ambiente virtual conda. Existem várias configurações que precisam ser alteradas ainda mais com base no caminho especificado aqui. Neste exemplo, usamos o py35new, pois o cluster já tem um ambiente virtual existente chamado py35.
    -   `python=`especifica a versão Python para o ambiente virtual. Neste exemplo, usamos a versão 3.5, a mesma versão do cluster construído em um. Você também pode usar outras versões Python para criar o ambiente virtual.
    -   `anaconda`especifica o package_spec como anaconda para instalar pacotes Anaconda no ambiente virtual.
    
    ```bash
    sudo /usr/bin/anaconda/bin/conda create --prefix /usr/bin/anaconda/envs/py35new python=3.5 anaconda --yes
    ```

2. Instale pacotes Python externos no ambiente virtual criado, se necessário. Execute ações de script em seu cluster para todos os nós com script abaixo para instalar pacotes Python externos. Você precisa ter privilégio sudo aqui para escrever arquivos para a pasta do ambiente virtual.

    Você pode pesquisar o [índice do pacote](https://pypi.python.org/pypi) para obter uma lista de pacotes que estão disponíveis. Você também pode obter uma lista de pacotes disponíveis de outras fontes. Por exemplo, você pode instalar pacotes disponibilizados por meio [conda-forge](https://conda-forge.org/feedstocks/).

    Use abaixo o comando se quiser instalar uma biblioteca com sua versão mais recente:

    - Use o canal conda:

        -   `seaborn`é o nome do pacote que você gostaria de instalar.
        -   `-n py35new`especificar o nome do ambiente virtual que acaba de ser criado. Certifique-se de alterar o nome correspondentemente com base na criação do ambiente virtual.

        ```bash
        sudo /usr/bin/anaconda/bin/conda install seaborn -n py35new --yes
        ```

    - Ou use o repo `seaborn` `py35new` PyPi, altere e correspondentemente:
        ```bash
        sudo /usr/bin/anaconda/env/py35new/bin/pip install seaborn
        ```

    Use abaixo o comando se quiser instalar uma biblioteca com uma versão específica:

    - Use o canal conda:

        -   `numpy=1.16.1`é o nome do pacote e a versão que você gostaria de instalar.
        -   `-n py35new`especificar o nome do ambiente virtual que acaba de ser criado. Certifique-se de alterar o nome correspondentemente com base na criação do ambiente virtual.

        ```bash
        sudo /usr/bin/anaconda/bin/conda install numpy=1.16.1 -n py35new --yes
        ```

    - Ou use o repo `numpy==1.16.1` `py35new` PyPi, altere e correspondentemente:

        ```bash
        sudo /usr/bin/anaconda/env/py35new/bin/pip install numpy==1.16.1
        ```

    se você não sabe o nome do ambiente virtual, você pode SSH `/usr/bin/anaconda/bin/conda info -e` para o nó principal do cluster e correr para mostrar todos os ambientes virtuais.

3. Altere as configurações Spark e Livy e aponte para o ambiente virtual criado.

    1. Abra a UI ambari, vá para a página Spark2, guia Configs.

        ![Mude spark e livy config através de Ambari](./media/apache-spark-python-package-installation/ambari-spark-and-livy-config.png)

    2. Expanda o livy2-env avançado, adicione abaixo as instruções na parte inferior. Se você instalou o ambiente virtual com um prefixo diferente, mude o caminho correspondentemente.

        ```
        export PYSPARK_PYTHON=/usr/bin/anaconda/envs/py35new/bin/python
        export PYSPARK_DRIVER_PYTHON=/usr/bin/anaconda/envs/py35new/bin/python
        ```

        ![Mude a configuração livy através de Ambari](./media/apache-spark-python-package-installation/ambari-livy-config.png)

    3. Expanda o Advanced spark2-env, substitua a declaração de PYSPARK_PYTHON de exportação existente na parte inferior. Se você instalou o ambiente virtual com um prefixo diferente, mude o caminho correspondentemente.

        ```
        export PYSPARK_PYTHON=${PYSPARK_PYTHON:-/usr/bin/anaconda/envs/py35new/bin/python}
        ```

        ![Alterar a configuração de faísca através de Ambari](./media/apache-spark-python-package-installation/ambari-spark-config.png)

    4. Salve as alterações e reinicie os serviços afetados. Essas alterações precisam de uma reinicialização do serviço Spark2. A Ambari UI solicitará um lembrete de reinicialização necessário, clique em Reiniciar para reiniciar todos os serviços afetados.

        ![Alterar a configuração de faísca através de Ambari](./media/apache-spark-python-package-installation/ambari-restart-services.png)

4. Se você quiser usar o novo ambiente virtual criado no Jupyter. Você precisa mudar as configurações de Jupyter e reiniciar Jupyter. Execute ações de script em todos os nós de cabeçalho com a declaração abaixo para apontar Jupyter para o novo ambiente virtual criado. Certifique-se de modificar o caminho para o prefixo especificado para o seu ambiente virtual. Depois de executar esta ação de script, reinicie o serviço Jupyter através da Ambari UI para disponibilizar essa alteração.

    ```bash
    sudo sed -i '/python3_executable_path/c\ \"python3_executable_path\" : \"/usr/bin/anaconda/envs/py35new/bin/python3\"' /home/spark/.sparkmagic/config.json
    ```

    Você pode confirmar duas vezes o ambiente Python no Jupyter Notebook executando abaixo do código:

    ![Verifique a versão Python no Jupyter Notebook](./media/apache-spark-python-package-installation/check-python-version-in-jupyter.png)

## <a name="known-issue"></a>Problema conhecido

Há um bug conhecido para Anaconda versão 4.7.11, 4.7.12 e 4.8.0. Se você ver suas `"Collecting package metadata (repodata.json): ...working..."` ações de `"Python script has been killed due to timeout after waiting 3600 secs"`script penduradas e falhando com . Você pode baixar [este script](https://gregorysfixes.blob.core.windows.net/public/fix-conda.sh) e executá-lo como ações de script em todos os nós para corrigir o problema.

Para verificar sua versão Anaconda, você pode SSH `/usr/bin/anaconda/bin/conda --v`no nó de cabeçalho do cluster e executar .

## <a name="next-steps"></a>Próximas etapas

* [Visão geral: Apache Spark no Azure HDInsight](apache-spark-overview.md)
* [Apache Spark com BI: execute análise de dados interativa usando o Spark no HDInsight com ferramentas de BI](apache-spark-use-bi-tools.md)
* [Gerenciar os recursos de cluster do Apache Spark no Azure HDInsight](apache-spark-resource-manager.md)
* [Rastrear e depurar trabalhos em execução em um cluster do Apache Spark no HDInsight](apache-spark-job-debugging.md)
