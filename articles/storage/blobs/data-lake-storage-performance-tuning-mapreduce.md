---
title: 'Desempenho da sintonia: MapReduce, HDInsight & Azure Data Lake Storage Gen2 | Microsoft Docs'
description: Diretrizes de Ajuste de Desempenho do MapReduce do Azure Data Lake Storage Gen2
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 11/18/2019
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: a3ea6858355d6cb921f629bf36134d96371f6244
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74327924"
---
# <a name="tune-performance-mapreduce-hdinsight--azure-data-lake-storage-gen2"></a>Desempenho da sintonia: MapReduce, HDInsight & Azure Data Lake Storage Gen2

Entenda os fatores que devem ser considerados ao ajustar o desempenho dos trabalhos do Map Reduce. Este artigo abrange uma variedade de diretrizes de ajuste de desempenho.

## <a name="prerequisites"></a>Pré-requisitos

* **Uma assinatura do Azure.** Consulte [Obter a avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Uma conta Azure Data Lake Storage Gen2**. Para obter instruções sobre como criar um, consulte [Quickstart: Crie uma conta de armazenamento Azure Data Lake Storage Gen2](data-lake-storage-quickstart-create-account.md).
* **Cluster do Azure HDInsight** com acesso a uma conta do Azure Data Lake Storage Gen2. Veja [Usar o Azure Data Lake Storage Gen2 com clusters Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2)
* **Usando mapReduce no HDInsight**.  Para obter mais informações, consulte [Usar o MapReduce no Hadoop no HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-use-mapreduce)
* **Diretrizes de ajuste de desempenho no Data Lake Storage Gen2**.  Para obter conceitos gerais de desempenho, consulte [Data Lake Storage Gen2 Performance Tuning Guidance](data-lake-storage-performance-tuning-guidance.md)

## <a name="parameters"></a>Parâmetros

Ao executar trabalhos do MapReduce, aqui estão os parâmetros que você pode configurar para aumentar o desempenho no Data Lake Storage Gen2:

* **Mapreduce.map.memory.mb** – a quantidade de memória para alocar para cada mapeador
* **Mapreduce.job.maps** – o número de tarefas de mapeamento por trabalho
* **Mapreduce.reduce.memory.mb** – a quantidade de memória para alocar para cada redutor
* **Mapreduce.job.reduces** – o número de tarefas de redução por trabalho

**Mapreduce.map.memory / Mapreduce.reduce.memory** Esse número deve ser ajustado com base na quantidade de memória que é necessária para a tarefa de mapeamento e/ou redução.  Os valores padrão de mapreduce.map.memory e mapreduce.reduce.memory podem ser exibidos no Ambari por meio da configuração do Yarn.  Em Ambari, navegue até O Fio e visualize a guia Configs.  A memória YARN será exibida.  

**Mapreduce.job.maps / Mapreduce.job.reduces** Isso determinará o número máximo de mapeadores ou redutores a serem criados.  O número de divisões determinará quantas mapeadores serão criados para o trabalho MapReduce.  Portanto, você poderá obter menos mapeadores que o solicitado se houver menos divisões do que o número de mapeadores solicitado.       

## <a name="guidance"></a>Orientação

> [!NOTE]
> As diretrizes neste documento pressupõem que seu aplicativo é o único aplicativo em execução no cluster.

**Passo 1: Determine o número de empregos em execução**

Por padrão, o MapReduce usará o cluster inteiro para o trabalho.  Você pode usar uma parte menor do cluster ao usar um número de mapeadores menor do que o número de contêineres disponíveis existentes.        

**Passo 2: Definir mapreduce.map.memory/mapreduce.reduce.memory**

O tamanho da memória para tarefas de mapeamento e redução dependerá do trabalho específico.  Caso deseje aumentar a simultaneidade, você poderá reduzir o tamanho da memória.  O número de tarefas em execução simultânea depende do número de contêineres.  Diminuindo a quantidade de memória por mapeador ou redutor, mais contêineres podem ser criados, o que permite a execução simultânea de mais mapeadores ou redutores.  Diminuir muito a quantidade de memória pode fazer com que alguns processos fiquem sem memória.  Se você receber um erro de heap quando executar seu trabalho, você deverá aumentar a memória por mapeador ou redutor.  Considere que adicionar mais contêineres adicionará sobrecarga extra para cada contêiner adicional, o que pode degradar o desempenho.  Outra alternativa é de obter mais memória pelo uso de um cluster com maiores quantidades de memória ou pelo aumento do número de nós no cluster.  Mais memória permitirá o uso de mais contêineres, o que significa mais simultaneidade.  

**Etapa 3: determinar o total de memória YARN**

Para ajustar mapreduce.job.maps/mapreduce.job.reduces, você deve considerar a quantidade de memória YARN total disponível para uso.  Essas informações estão disponíveis no Ambari.  Navegue até O Fio e visualize a guia Configs.  A memória YARN é exibida nesta janela.  Para obter a memória YARN total, você deve multiplicar a memória YARN por nó pelo número de nós em seu cluster.

    Total YARN memory = nodes * YARN memory per node

Se você estiver usando um cluster vazio, a memória poderá ser a memória YARN total para seu cluster.  Se outros aplicativos estiverem usando memória, você poderá usar apenas uma parte da memória do cluster, reduzindo o número de mapeadores ou redutores para o número de contêineres que você deseja usar.  

**Passo 4: Calcule o número de recipientes De Fio**

Contêineres YARN determinam a quantidade de simultaneidade disponível para o trabalho.  Pegar a memória YARN total e divida-a por mapreduce.map.memory.  

    # of YARN containers = total YARN memory / mapreduce.map.memory

**Etapa 5: definir mapreduce.job.maps/mapreduce.job.reduces**

Defina mapreduce.job.maps/mapreduce.job.reduces para, no mínimo, o número de contêineres disponíveis.  Você pode experimentar ainda mais aumentando o número de mapeadores e redutores para ver se obtém um melhor desempenho.  Tenha em mente que mais mapeadores terão uma sobrecarga adicional, então ter um número excessivo de mapeadores pode degradar o desempenho.  

O isolamento de CPU e agendamento de CPU são desligados por padrão para que o número de contêineres YARN seja restrito pela memória.

## <a name="example-calculation"></a>Exemplo de cálculo

Suponhamos que você tenha um cluster composto de 8 nós D14 e queira executar um trabalho com uso intensivo de E/S.  Aqui estão os cálculos que você deve fazer:

**Passo 1: Determine o número de empregos em execução**

Neste exemplo, vamos supor que nosso trabalho é o único que está em execução.  

**Passo 2: Definir mapreduce.map.memory/mapreduce.reduce.memory**

Neste exemplo, estamos executando um trabalho com uso intensivo de E/S e decidimos que 3GB de memória para tarefas de mapeamento será suficiente.

    mapreduce.map.memory = 3GB

**Etapa 3: determinar o total de memória YARN**

    Total memory from the cluster is 8 nodes * 96GB of YARN memory for a D14 = 768GB
**Etapa 4: calcular o número de contêineres YARN**

    # of YARN containers = 768GB of available memory / 3 GB of memory =   256

**Etapa 5: definir mapreduce.job.maps/mapreduce.job.reduces**

    mapreduce.map.jobs = 256

## <a name="examples-to-run"></a>Exemplos para execução

Para demonstrar como o MapReduce é executado no Data Lake Storage Gen2, abaixo está um código de exemplo que foi executado em um cluster com as seguintes configurações:

* 16 nós D14v2
* Cluster Hadoop executando HDI 3.6

Para um ponto de partida, aqui estão alguns comandos de exemplo para executar o MapReduce Teragen, Terasort e Teravalidate.  Você pode ajustar esses comandos com base em seus recursos.

**Teragen**

    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teragen -Dmapreduce.job.maps=2048 -Dmapreduce.map.memory.mb=3072 10000000000 abfs://example/data/1TB-sort-input

**Terasort**

    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar terasort -Dmapreduce.job.maps=2048 -Dmapreduce.map.memory.mb=3072 -Dmapreduce.job.reduces=512 -Dmapreduce.reduce.memory.mb=3072 abfs://example/data/1TB-sort-input abfs://example/data/1TB-sort-output

**Teravalid**

    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teravalidate -Dmapreduce.job.maps=512 -Dmapreduce.map.memory.mb=3072 abfs://example/data/1TB-sort-output abfs://example/data/1TB-sort-validate
