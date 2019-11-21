---
title: Script action for Python packages with Jupyter on Azure HDInsight
description: Instruções passo a passo sobre como usar ação de script para configurar os blocos de anotações do Jupyter disponíveis com clusters Spark no HDInsight para usar pacotes Python externos.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 11/19/2019
ms.openlocfilehash: a8654f6c9c6c6d020872d2c89e0dd141db4e0451
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74215633"
---
# <a name="safely-manage-python-environment-on-azure-hdinsight-using-script-action"></a>Safely manage Python environment on Azure HDInsight using Script Action

> [!div class="op_single_selector"]
> * [Usando a mágica da célula](apache-spark-jupyter-notebook-use-external-packages.md)
> * [Usando a ação de script](apache-spark-python-package-installation.md)

HDInsight has two built-in Python installations in the Spark cluster, Anaconda Python 2.7 and Python 3.5. In some cases, customers need to customize the Python environment, like installing external Python packages or another Python version. In this article, we show the best practice of safely managing Python environments for an [Apache Spark](https://spark.apache.org/) cluster on HDInsight.

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Consulte [Obter avaliação gratuita do Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

* Um cluster do Apache Spark no HDInsight. Para obter instruções, consulte o artigo sobre como [Criar clusters do Apache Spark no Azure HDInsight](apache-spark-jupyter-spark-sql.md).

   > [!NOTE]  
   > Se você ainda não tiver um cluster Spark no HDInsight Linux, poderá executar ações de script durante a criação do cluster. Acesse a documentação em [como usar ações de script personalizadas](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux).

## <a name="support-for-open-source-software-used-on-hdinsight-clusters"></a>Suporte para software livre utilizado em clusters do HDInsight

O serviço Microsoft Azure HDInsight usa um ecossistema de tecnologias de código aberto formado em torno do Apache Hadoop. O Microsoft Azure fornece um nível geral de suporte para tecnologias de software livre. For more information, see [Azure Support FAQ website](https://azure.microsoft.com/support/faq/). O serviço HDInsight fornece um nível adicional de suporte a componentes internos.

Há dois tipos de componentes de software livre disponíveis no serviço HDInsight:

* **Componentes internos** : estes componentes estão pré-instalado em clusters HDInsight e fornecem a funcionalidade básica do cluster. For example, Apache Hadoop YARN Resource Manager, the Apache Hive query language (HiveQL), and the Mahout library belong to this category. Uma lista completa de componentes de cluster está disponível em [O que há de novo nas versões de cluster do Apache Hadoop fornecidas pelo HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning).
* **Componentes personalizados** : como usuário do cluster, você pode instalar ou usar em sua carga de trabalho qualquer componente disponível na comunidade ou criado por você.

> [!IMPORTANT]
> Componentes fornecidos com o cluster HDInsight contam com suporte total. O Suporte da Microsoft ajuda a isolar e resolver problemas relacionados a esses componentes.
>
> Componentes personalizados recebem suporte comercialmente razoável para ajudá-lo a solucionar o problema. O suporte da Microsoft pode estar apto a resolver o problema OU pode solicitar que você contate canais disponíveis para as tecnologias de software livre em que é encontrado conhecimento profundo sobre essa tecnologia. Por exemplo, há muitos sites de comunidades que podem ser usados, como o [Fórum do MSDN para o HDInsight](https://social.msdn.microsoft.com/Forums/azure/home?forum=hdinsight), [https://stackoverflow.com](https://stackoverflow.com). Also Apache projects have project sites on [https://apache.org](https://apache.org), for example: [Hadoop](https://hadoop.apache.org/).

## <a name="understand-default-python-installation"></a>Understand default Python installation

HDInsight Spark cluster is created with Anaconda installation. There are two Python installations in the cluster, Anaconda Python 2.7 and Python 3.5. The table below shows the default Python settings for Spark, Livy, and Jupyter.

| |Python 2.7|Python 3.5|
|----|----|----|
|path|/usr/bin/anaconda/bin|/usr/bin/anaconda/envs/py35/bin|
|Spark|Default set to 2.7|N/D|
|Livy|Default set to 2.7|N/D|
|Jupyter|PySpark kernel|PySpark3 kernel|

## <a name="safely-install-external-python-packages"></a>Safely install external Python packages

HDInsight cluster depends on the built-in Python environment, both Python 2.7 and Python 3.5. Directly installing custom packages in those default built-in environments may cause unexpected library version changes, and break the cluster further. In order to safely install custom external Python packages for your Spark applications, follow below steps.

1. Create Python virtual environment using conda. A virtual environment provides an isolated space for your projects without breaking others. When creating the Python virtual environment, you can specify python version that you want to use. Note that you still need to create virtual environment even though you would like to use Python 2.7 and 3.5. This is to make sure the cluster’s default environment not getting broke. Run script actions on your cluster for all nodes with below script to create a Python virtual environment. 

    -   `--prefix` specifies a path where a conda virtual environment lives. There are several configs that need to be changed further based on the path specified here. In this example, we use the py35new, as the cluster has an existing virtual environment called py35 already.
    -   `python=` specifies the Python version for the virtual environment. In this example, we use version 3.5, the same version as the cluster built in one. You can also use other Python versions to create the virtual environment.
    -   `anaconda` specifies the package_spec as anaconda to install Anaconda packages in the virtual environment.
    
    ```bash
    sudo /usr/bin/anaconda/bin/conda create --prefix /usr/bin/anaconda/envs/py35new python=3.5 anaconda --yes 
    ```

2. Install external Python packages in the created virtual environment if needed. Run script actions on your cluster for all nodes with below script to install external Python packages. You need to have sudo privilege here in order to write files to the virtual environment folder.

    Você pode pesquisar o [índice do pacote](https://pypi.python.org/pypi) para obter uma lista de pacotes que estão disponíveis. Você também pode obter uma lista de pacotes disponíveis de outras fontes. Por exemplo, você pode instalar pacotes disponibilizados por meio [conda-forge](https://conda-forge.org/feedstocks/).

    -   `seaborn` is the package name that you would like to install.
    -   `-n py35new` specify the virtual environment name that just gets created. Make sure to change the name correspondingly based on your virtual environment creation.

    ```bash
    sudo /usr/bin/anaconda/bin/conda install seaborn -n py35new --yes
    ```

    if you don't know the virtual environment name, you can SSH to the header node of the cluster and run `/usr/bin/anaconda/bin/conda info -e` to show all virtual environments.

3. Change Spark and Livy configs and point to the created virtual environment.

    1. Open Ambari UI, go to Spark2 page, Configs tab.
    
        ![Change Spark and Livy config through Ambari](./media/apache-spark-python-package-installation/ambari-spark-and-livy-config.png)
 
    2. Expand Advanced livy2-env, add below statements at bottom. If you installed the virtual environment with a different prefix, change the path correspondingly.

        ```
        export PYSPARK_PYTHON=/usr/bin/anaconda/envs/py35new/bin/python
        export PYSPARK_DRIVER_PYTHON=/usr/bin/anaconda/envs/py35new/bin/python
        ```

        ![Change Livy config through Ambari](./media/apache-spark-python-package-installation/ambari-livy-config.png)

    3. Expand Advanced spark2-env, replace the existing export PYSPARK_PYTHON statement at bottom. If you installed the virtual environment with a different prefix, change the path correspondingly.

        ```
        export PYSPARK_PYTHON=${PYSPARK_PYTHON:-/usr/bin/anaconda/envs/py35new/bin/python}
        ```

        ![Change Spark config through Ambari](./media/apache-spark-python-package-installation/ambari-spark-config.png)

    4. Save the changes and restart affected services. These changes need a restart of Spark2 service. Ambari UI will prompt a required restart reminder, click Restart to restart all affected services.

        ![Change Spark config through Ambari](./media/apache-spark-python-package-installation/ambari-restart-services.png)
 
4.  If you would like to use the new created virtual environment on Jupyter. You need to change Jupyter configs and restart Jupyter. Run script actions on all header nodes with below statement to point Jupyter to the new created virtual environment. Make sure to modify the path to the prefix you specified for your virtual environment. After running this script action, restart Jupyter service through Ambari UI to make this change available.

    ```
    sudo sed -i '/python3_executable_path/c\ \"python3_executable_path\" : \"/usr/bin/anaconda/envs/py35new/bin/python3\"' /home/spark/.sparkmagic/config.json
    ```

    You could double confirm the Python environment in Jupyter Notebook by running below code:

    ![Check Python version in Jupyter Notebook](./media/apache-spark-python-package-installation/check-python-version-in-jupyter.png)

## <a name="known-issue"></a>Problema conhecido

There is a known bug for Anaconda version 4.7.11 and 4.7.12. If you see your script actions hanging at `"Collecting package metadata (repodata.json): ...working..."` and failing with `"Python script has been killed due to timeout after waiting 3600 secs"`. You can download [this script](https://gregorysfixes.blob.core.windows.net/public/fix-conda.sh) and run it as script actions on all nodes to fix the issue.

To check your Anaconda version, you can SSH to the cluster header node and run `/usr/bin/anaconda/bin/conda --v`.

## <a name="seealso"></a>Consulte também

* [Visão geral: Apache Spark no Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Cenários

* [Apache Spark com BI: execute análise de dados interativa usando o Spark no HDInsight com ferramentas de BI](apache-spark-use-bi-tools.md)
* [Apache Spark com Machine Learning: use o Spark no HDInsight para analisar a temperatura do edifício usando dados de HVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark com Machine Learning: use o Spark no HDInsight para prever os resultados da inspeção de alimentos](apache-spark-machine-learning-mllib-ipython.md)
* [Análise de log do site usando o Apache Spark no HDInsight](apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Criar e executar aplicativos

* [Criar um aplicativo autônomo usando Scala](apache-spark-create-standalone-application.md)
* [Execute trabalhos remotamente em um cluster do Apache Spark usando o Apache Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Ferramentas e extensões

* [Usar pacotes externos com notebooks Jupyter em clusters Apache Spark no HDInsight](apache-spark-jupyter-notebook-use-external-packages.md)
* [Use o Plug-in de Ferramentas do HDInsight para IntelliJ IDEA para criar e enviar aplicativos Spark Scala](apache-spark-intellij-tool-plugin.md)
* [Use o Plugin do HDInsight Tools para o IntelliJ IDEA para depurar os aplicativos do Apache Spark remotamente](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Use os blocos de anotações do Apache Zeppelin com um cluster do Apache Spark no HDInsight](apache-spark-zeppelin-notebook.md)
* [Kernels disponíveis para o notebook Jupyter no cluster do Apache Spark para HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Instalar o Jupyter em seu computador e conectar-se a um cluster Spark do HDInsight](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Gerenciar recursos

* [Gerenciar os recursos de cluster do Apache Spark no Azure HDInsight](apache-spark-resource-manager.md)
* [Rastrear e depurar trabalhos em execução em um cluster do Apache Spark no HDInsight](apache-spark-job-debugging.md)
