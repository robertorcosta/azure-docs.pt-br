---
title: Definir as configurações do Spark - Azure HDInsight
description: Como visualizar e configurar configurações do Apache Spark para um cluster Azure HDInsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 04/15/2020
ms.openlocfilehash: e13390067f8767e8c07b9c189264444e6d999a7a
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81411292"
---
# <a name="configure-apache-spark-settings"></a>Definir configurações do Apache Spark

Um cluster HDInsight Spark inclui uma instalação da biblioteca [Apache Spark.](https://spark.apache.org/)  Cada cluster HDInsight inclui parâmetros de configuração padrão para todos os seus serviços instalados, incluindo Spark.  Um aspecto fundamental do gerenciamento de um cluster HDInsight Apache Hadoop é o monitoramento da carga de trabalho, incluindo spark jobs. Para executar melhor os trabalhos do Spark, considere a configuração de cluster físico ao determinar a configuração lógica do cluster.

O cluster padrão do HDInsight Apache Spark inclui os seguintes nós: três nós do [Apache ZooKeeper](https://zookeeper.apache.org/), dois nós principais e um ou mais nós do trabalhador:

![Arquitetura do HDInsight Spark](./media/apache-spark-settings/spark-hdinsight-arch.png)

O número de tamanhos de VMs e VM, para os nós do cluster HDInsight pode afetar a configuração do Spark. Valores de configuração do HDInsight não padrão geralmente requerem valores de configuração do Spark não padrão. Quando você cria um cluster HDInsight Spark, você é mostrado tamanhos de VM sugeridos para cada um dos componentes. Atualmente os [tamanhos de VM do Linux com otimização de memória](../../virtual-machines/linux/sizes-memory.md) do Azure são D12 v2 ou posterior.

## <a name="apache-spark-versions"></a>Versões Apache Spark

Use a versão recomendada do Spark para seu cluster.  O serviço HDInsight inclui várias versões do Spark e HDInsight em si.  Cada versão do Spark inclui um conjunto de configurações de cluster padrão.  

Quando você cria um novo cluster, aqui estão as versões atuais do Spark para escolher. Para ver a lista completa, [COMPONENTES e Versões HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning).

> [!NOTE]  
> A versão padrão de Apache Spark para o serviço HDInsight pode ser alterada sem aviso prévio. Se você tem uma dependência de versão, a Microsoft recomenda que você especifique essa versão específica ao criar clusters usando o SDK do .NET, o Azure PowerShell e a CLI Clássica do Azure.

Apache Spark tem três locais de configuração do sistema:

* Propriedades do Spark controla a maioria dos parâmetros do aplicativo e pode ser definida usando um `SparkConf` objeto, ou por meio de propriedades do sistema Java.
* Variáveis de ambiente podem ser usadas para definir configurações por computador, como o endereço IP, por meio de `conf/spark-env.sh` script em cada nó.
* Registro em log pode ser configurado por meio de `log4j.properties`.

Quando você seleciona uma versão específica do Spark, seu cluster inclui as definições de configuração padrão.  Você pode alterar os valores de configuração do Spark padrão usando um arquivo de configuração personalizado do Spark.  Um exemplo é mostrado abaixo.

```
spark.hadoop.io.compression.codecs org.apache.hadoop.io.compress.GzipCodec
spark.hadoop.mapreduce.input.fileinputformat.split.minsize 1099511627776
spark.hadoop.parquet.block.size 1099511627776
spark.sql.files.maxPartitionBytes 1099511627776
spark.sql.files.openCostInBytes 1099511627776
```

O exemplo mostrado acima substitui vários valores padrão para cinco parâmetros de configuração do Spark.  Esses valores são o codec de compressão, Apache Hadoop MapReduce tamanho mínimo dividido e tamanhos de bloco sumário. Além disso, a partição Spar SQL e tamanhos de arquivo abertos tamanhos de valores padrão.  Essas alterações de configuração são escolhidas porque os dados e empregos associados (neste exemplo, dados genômicos) têm características particulares. Essas características serão melhores usando essas configurações personalizadas.

---

## <a name="view-cluster-configuration-settings"></a>Exibir definições de configuração do cluster

Verifique as configurações atuais do cluster HDInsight antes de fazer a otimização de desempenho no cluster. Iniciar o dashboard de HDInsight do portal do Azure clicando no link **Dashboard** no painel de cluster Spark. Faça login com o nome de usuário e senha do administrador do cluster.

A UI da Web Apache Ambari aparece, com um painel de métricas de uso de recursos de cluster de chave.  O Painel Ambari mostra a configuração do Apache Spark e outros serviços instalados. O Painel de controle inclui uma guia **Histórico de configuração,** onde você visualiza informações para serviços instalados, incluindo o Spark.

Para ver os valores de configuração para o Apache Spark, selecione **Histórico de Configuração**, em seguida, selecione **Spark2**.  Selecione a guia **configurações** e, em seguida, selecione o link `Spark` (ou `Spark2`, dependendo de sua versão) na lista de serviços.  Você verá uma lista de valores de configuração para o cluster:

![Configurações Spark](./media/apache-spark-settings/spark-configurations.png)

Para ver e alterar valores individuais de configuração do Spark, selecione qualquer link com "faísca" no título.  Configurações para Spark incluem os dois valores de configuração avançada e personalizada nestas categorias:

* Spark2-defaults personalizada
* Spark2-metrics-properties personalizada
* Spark2-defaults avançada
* Spark2-env avançada
* Spark2-hive-site-override avançada

Se você criar um conjunto não padrão de valores de configuração, seu histórico de atualização será visível.  Esse histórico de configuração pode ser útil para ver qual configuração não-padrão tem um desempenho ideal.

> [!NOTE]  
> Para ver, mas não alterar, definições de configurações de cluster Spark comuns, selecione a guia **Ambiente** na interface superior **interface do usuário de trabalho Spark**.

## <a name="configuring-spark-executors"></a>Configurando executores Spark

O diagrama a seguir mostra os objetos de chave Spark: o programa de driver e seu contexto Spark associado e o gerenciador de cluster e seus *n* nós de trabalho.  Cada nó de trabalho inclui um Executor, um cache, e *n* instâncias da tarefa.

![Objetos de cluster](./media/apache-spark-settings/hdi-spark-architecture.png)

Os trabalhos de Spark usam recursos de trabalho, particularmente, memória, portanto, é comum para ajustar os valores de configuração do Spark para executores de nó de trabalho.

Três parâmetros de chave que geralmente são ajustados para ajustar as configurações do Spark para melhorar os requisitos do aplicativo são `spark.executor.instances`, `spark.executor.cores`, e `spark.executor.memory`. Um Executor é um processo iniciado por um aplicativo Spark. Um Executor é executado no nó de trabalho e é responsável por realizar as tarefas do aplicativo. O número de nódulos operários e o tamanho do nó do trabalhador determina o número de executores e tamanhos de executores. Esses valores `spark-defaults.conf` são armazenados nos nós de cabeça do cluster.  Você pode editar esses valores em um cluster em execução selecionando **padrões de faísca personalizados** na ui da Web Ambari.  Depois de fazer alterações, você será solicitado pela interface do usuário para **reiniciar** todos os serviços afetados.

> [!NOTE]  
> Esses três parâmetros de configuração podem ser definidos no nível de cluster (para todos os aplicativos que são executados no cluster) ou também especificados para cada aplicativo individualmente.

Outra fonte de informações sobre os recursos usados pela Spark Executors é a UI spark Application.  Na ui, **executores** exibe mera exibição resumo e detalhes da configuração e recursos consumidos.  Determine se altera os valores dos executores para um cluster inteiro ou um conjunto específico de execuções de trabalho.

![Executores Spark](./media/apache-spark-settings/apache-spark-executors.png)

Ou você pode usar a API Ambari REST para verificar programáticamente as configurações de configuração do cluster HDInsight e Spark.  Mais informações estão disponíveis na [Referência da API do Apache Ambari no GitHub](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).

Dependendo da carga de trabalho do Spark, você poderá determinar que uma configuração Spark não padrão proporcione execuções de trabalho do Spark mais otimizadas.  Faça testes de benchmark com cargas de trabalho de amostra para validar quaisquer configurações de cluster não padrão.  Estes são alguns dos parâmetros comuns que você pode considerar ajustar:

|Parâmetro |Descrição|
|---|---|
|--num-executores|Define o número de executores.|
|--executor-núcleos|Define o número de núcleos para cada executor. É recomendável usar executores de tamanho médio, pois outros processos também consomem parte da memória disponível.|
|--memória executora|Controla o tamanho da memória (tamanho do monte) de cada executor no [Apache Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html), e você precisará deixar alguma memória para a sobrecarga de execução.|

Aqui está um exemplo de dois nós de trabalho com valores de configuração diferentes:

![Duas configurações de nó](./media/apache-spark-settings/executor-configuration.png)

A lista a seguir mostra os principais parâmetros de memória de executor Spark.

|Parâmetro |Descrição|
|---|---|
|spark.executor.memory|Define a quantidade total de memória disponível para um executor.|
|spark.storage.memoryFraction| (padrão aproximadamente 60%) define a quantidade de memória disponível para o armazenamento RDDs persistentes.|
|spark.shuffle.memoryFraction| (padrão aproximadamente 20%) define a quantidade de memória reservada para ordem aleatória.|
|spark.storage.unrollFraction e spark.storage.safetyFraction|(totalizando ~30% da memória total) - esses valores são usados internamente pela Spark e não devem ser alterados.|

O YARN controla a soma máxima da memória usada por todos os contêineres em cada nó do Spark. O diagrama a seguir mostra as relações por nó entre objetos de configuração YARN e objetos Spark.

![Gerenciamento de memória YARN do Spark](./media/apache-spark-settings/hdi-yarn-spark-memory.png)

## <a name="change-parameters-for-an-application-running-in-jupyter-notebook"></a>Alterar parâmetros para um aplicativo em execução no bloco de notas do Jupyter

Os clusters Spark no HDInsight incluem um número de componentes por padrão. Cada um desses componentes inclui valores de configuração padrão que podem ser substituídos conforme necessário.

|Componente |Descrição|
|---|---|
|Núcleo de Faísca|Spark Core, Spark SQL, Spark streaming APIs, GraphX e Apache Spark MLlib.|
|Anaconda|Um gerenciador de pacotes python.|
|[Apache Livy](https://livy.incubator.apache.org/)|A API Apache Spark REST, usada para enviar trabalhos remotos para um cluster HDInsight Spark.|
|[Cadernos Jupyter](https://jupyter.org/) e [Apache Zeppelin](https://zeppelin.apache.org/)|Interface do usuário interativa baseada em navegador para interagir com seu cluster Spark.|
|Driver ODBC|Conecta clusters Spark no HDInsight a ferramentas de business intelligence (BI), como Microsoft Power BI e Tableau.|

Para aplicativos em execução no bloco de notas Jupyter, você pode usar o comando `%%configure` para fazer as alterações na configuração no próprio bloco de notas. Essas alterações de configuração serão aplicadas aos trabalhos Spark executados da sua instância do bloco de notas. Faça tais alterações no início do aplicativo, antes de executar sua primeira célula de código. A configuração alterada é aplicada à sessão Livy, quando ela é criada.

> [!NOTE]  
> Para alterar a configuração em uma fase posterior no aplicativo, use o parâmetro `-f` (força). No entanto, todo o progresso do aplicativo será perdido.

O código abaixo mostra como alterar a configuração para um aplicativo em execução em um bloco de notas do Jupyter.

```
%%configure
{"executorMemory": "3072M", "executorCores": 4, "numExecutors":10}
```

## <a name="conclusion"></a>Conclusão

Monitore as configurações da configuração principal para garantir que seus trabalhos spark são executados de forma previsível e performática. Essas configurações ajudam a determinar a melhor configuração de cluster do Spark para suas cargas de trabalho específicas.  Você também precisará monitorar a execução de execuções de trabalho de longa duração e, ou de consumo de recursos spark.  Os desafios mais comuns giram em torno da pressão da memória de configurações inadequadas, como executores de tamanho incorreto. Além disso, operações de longa duração, e tarefas, que resultam em operações cartesianas.

## <a name="next-steps"></a>Próximas etapas

* [Componentes e versões do Apache Hadoop disponíveis com o HDInsight?](../hdinsight-component-versioning.md)
* [Gerenciar recursos para um cluster do Apache Spark no HDInsight](apache-spark-resource-manager.md)
* [Configure os clusters no HDInsight com o Apache Hadoop, o Apache Spark, o Apache Kafka e muito mais](../hdinsight-hadoop-provision-linux-clusters.md)
* [Configuração de faísca apache](https://spark.apache.org/docs/latest/configuration.html)
* [Executando o Apache Spark no Apache Hadoop YARN](https://spark.apache.org/docs/latest/running-on-yarn.html)
