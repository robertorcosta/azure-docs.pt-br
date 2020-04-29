---
title: Planejamento de capacidade de cluster no Azure HDInsight
description: Identifique as principais perguntas para a capacidade e o planejamento de desempenho de um cluster do Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 04/07/2020
ms.openlocfilehash: 4ede8833fdbdbd57654e6c02147f53e58a17b1de
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80886986"
---
# <a name="capacity-planning-for-hdinsight-clusters"></a>Planejamento de capacidade para clusters HDInsight

Antes de implantar um cluster HDInsight, planeje a capacidade de cluster desejada determinando o desempenho e a escala necessários. Este planejamento ajuda a otimizar os custos e a usabilidade. Algumas decisões de capacidade de cluster não podem ser alteradas após a implantação. Se os parâmetros de desempenho se alterarem, um cluster poderá ser desmontado e criado novamente sem perder os dados armazenados.

As principais perguntas para o planejamento de capacidade são:

* Em qual região geográfica você deve implantar o cluster?
* Qual a quantidade de armazenamento que você precisa?
* Qual tipo de cluster você deve implantar?
* Qual o tamanho e o tipo de VM (máquina virtual) os nós de cluster devem usar?
* Quantos nós de trabalho seu cluster deve ter?

## <a name="choose-an-azure-region"></a>Escolher uma região do Azure

A região do Azure determina o local em que o cluster é provisionado fisicamente. Para minimizar a latência de leituras e gravações, o cluster deve ficar próximo aos seus dados.

O HDInsight está disponível em muitas regiões do Azure. Para localizar a região mais próxima, consulte [produtos disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/?products=hdinsight).

## <a name="choose-storage-location-and-size"></a>Escolher o tamanho e o local de armazenamento

### <a name="location-of-default-storage"></a>Local do armazenamento padrão

O armazenamento padrão, seja uma conta de Armazenamento do Azure ou um Azure Data Lake Storage, deve estar no mesmo local que o cluster. O Armazenamento do Azure está disponível em todos os locais. Data Lake Storage Gen1 está disponível em algumas regiões – consulte a atual [Data Lake Storage disponibilidade](https://azure.microsoft.com/global-infrastructure/services/?products=storage).

### <a name="location-of-existing-data"></a>Local dos dados existentes

Se você quiser usar uma conta de armazenamento existente ou Data Lake Storage como o armazenamento padrão do cluster, deverá implantar o cluster no mesmo local.

### <a name="storage-size"></a>Tamanho de armazenamento

Em um cluster implantado, você pode anexar mais contas de armazenamento do Azure ou acessar outros Data Lake Storage. Todas as suas contas de armazenamento devem residir no mesmo local que o cluster. Um Data Lake Storage pode estar em um local diferente, embora as grandes distâncias possam apresentar alguma latência.

O armazenamento do Azure tem alguns [limites de capacidade](../azure-resource-manager/management/azure-subscription-service-limits.md#storage-limits), enquanto data Lake Storage Gen1 é quase ilimitado.

Um cluster pode acessar uma combinação de contas de armazenamento diferentes. Exemplos comuns incluem:

* Quando é provável que a quantidade de dados exceda a capacidade de armazenamento de um único contêiner de armazenamento de blobs.
* Quando a taxa de acesso ao contêiner de blob pode exceder o máximo em que a limitação ocorre.
* Quando desejar tornar os dados, você já carregou em um contêiner de blob disponível para o cluster.
* Quando você quiser isolar diferentes partes do armazenamento por questões de segurança ou para simplificar a administração.

Para obter melhor desempenho, use apenas um contêiner por conta de armazenamento.

## <a name="choose-a-cluster-type"></a>Escolher um tipo de cluster

O tipo de cluster determina a carga de trabalho que o cluster HDInsight está configurado para executar. Os tipos incluem [Apache Hadoop](./hadoop/apache-hadoop-introduction.md), [Apache Storm](./storm/apache-storm-overview.md), [Apache Kafka](./kafka/apache-kafka-introduction.md)ou [Apache Spark](./spark/apache-spark-overview.md). Para obter uma descrição detalhada dos tipos de cluster disponíveis, consulte [Introdução ao Azure HDInsight](hdinsight-overview.md#cluster-types-in-hdinsight). Cada tipo de cluster tem uma topologia de implantação específica que inclui os requisitos de tamanho e número de nós.

## <a name="choose-the-vm-size-and-type"></a>Escolher tipo e tamanho da VM

Cada tipo de cluster tem um conjunto de tipos de nós e cada tipo de nó tem opções específicas quanto ao tamanho e o tipo de VM.

Para determinar o tamanho de cluster ideal para sua aplicação, você pode submeter a capacidade do cluster a submeter a benchmark e aumentar o tamanho conforme indicado. Por exemplo, você pode usar uma carga de trabalho simulada ou uma *consulta canary*. Execute suas cargas de trabalho simuladas em clusters de tamanho diferente. Aumente gradualmente o tamanho até que o desempenho pretendido seja atingido. Uma consulta canário pode ser inserida periodicamente entre as outras consultas de produção para mostrar se o cluster tem recursos suficientes.

Para obter mais informações sobre como escolher a família de VMs correta para sua carga de trabalho, consulte [selecionando o tamanho correto da VM para o cluster](hdinsight-selecting-vm-size.md).

## <a name="choose-the-cluster-scale"></a>Escolher a escala do cluster

A escala do cluster é determinada pela quantidade de seus nós de VM. Para todos os tipos de cluster, há tipos de nós que têm uma escala específica e tipos de nós que dão suporte à expansão. Por exemplo, um cluster pode exigir exatamente três nós [Apache ZooKeeper](https://zookeeper.apache.org/) ou dois nós de cabeçalho. Nós de trabalho que realizam o processamento de dados de maneira distribuída dos nós de trabalho adicionais.

Dependendo do tipo de cluster, aumentar o número de nós de trabalho adiciona capacidade computacional adicional (como mais núcleos). Mais nós aumentarão a memória total necessária para o cluster inteiro para dar suporte ao armazenamento na memória de dados que estão sendo processados. Assim como acontece com a escolha do tamanho e tipo da VM, selecionar a escala de cluster correta normalmente é atingido empiricamente. Use cargas de trabalho simuladas ou consultas canário.

Você pode escalar horizontalmente o cluster para atender às demandas de pico de carga. Em seguida, dimensione-o de volta quando esses nós extras não forem mais necessários. O [recurso de dimensionamento automático](hdinsight-autoscale-clusters.md) permite dimensionar automaticamente o cluster com base nas métricas e nos tempos predeterminados. Para obter mais informações sobre como dimensionar seus clusters manualmente, consulte [dimensionar clusters HDInsight](hdinsight-scaling-best-practices.md).

### <a name="cluster-lifecycle"></a>Ciclo de vida do cluster

Você é cobrado pelo tempo de vida de um cluster. Se houver apenas alguns momentos específicos de que você precisa do cluster, [crie clusters sob demanda usando Azure data Factory](hdinsight-hadoop-create-linux-clusters-adf.md). Você também pode criar scripts do PowerShell que provisionam e excluem o cluster e, em seguida, agendar esses scripts usando a [Automação do Azure](https://azure.microsoft.com/services/automation/).

> [!NOTE]  
> Quando um cluster é excluído, seu metastore do Hive padrão também é excluído. Para manter o metastore para a próxima recriação de cluster, use um repositório de metadados externo, como o Banco de Dados do Azure ou [Apache Oozie](https://oozie.apache.org/).
<!-- see [Using external metadata stores](hdinsight-using-external-metadata-stores.md). -->

### <a name="isolate-cluster-job-errors"></a>Isolar os erros de trabalho do cluster

Às vezes, podem ocorrer erros devido à execução paralela de vários mapas e à redução de componentes em um cluster de vários nós. Para ajudar a isolar o problema, experimente o teste distribuído. Executar vários trabalhos simultâneos em um único cluster de nó de trabalho. Em seguida, expanda essa abordagem para executar vários trabalhos simultaneamente em clusters que contenham mais de um nó. Para criar um cluster HDInsight de nó único no Azure, use a *`Custom(size, settings, apps)`* opção e use um valor de 1 para o *número de nós de trabalho* na seção **tamanho do cluster** ao provisionar um novo cluster no Portal.

## <a name="quotas"></a>Cotas

Depois de determinar o tamanho, a escala e o tipo da VM do cluster de destino, verifique os limites de capacidade de cota atuais da sua assinatura. Quando você atinge um limite de cota, não é possível implantar novos clusters. Ou escale horizontalmente os clusters existentes adicionando mais nós de trabalho. O único limite de cota é a cota de Núcleos de CPU que existe no nível da região para cada assinatura. Por exemplo, sua assinatura pode ter o limite de 30 núcleos na região Leste dos EUA.

Para verificar os núcleos disponíveis, execute as seguintes etapas:

1. Entre no [portal do Azure](https://portal.azure.com/).
2. Navegue até a página **visão geral** do cluster HDInsight.
3. No menu à esquerda, selecione **limites de cota**.

   A página exibe o número de núcleos em uso, o número de núcleos disponíveis e o total de núcleos.

Se precisar solicitar um aumento de cota, siga estes passos:

1. Entre no [portal do Azure](https://portal.azure.com/).
1. Selecione **ajuda + suporte** no lado inferior esquerdo da página.
1. Selecione **Nova solicitação de suporte**.
1. Na página **Nova solicitação de suporte**, na guia **Noções básicas**, selecione as opções a seguir:

   - **Tipo de problema**: **limites de serviço e assinatura (cotas)**
   - **Assinatura**: a assinatura que você deseja modificar
   - **Tipo de cota**: **HDInsight**

     ![Crie uma solicitação de suporte para aumentar a cota de núcleos do HDInsight](./media/hdinsight-capacity-planning/hdinsight-quota-support-request.png)

1. Selecione **Avançar: soluções >>**.
1. Na página **detalhes** , insira uma descrição do problema, selecione a severidade do problema, seu método de contato preferencial e outros campos obrigatórios.
1. Selecione **Avançar: revisar + criar >>**.
1. Na guia **Revisar + criar**, selecione **Criar**.

> [!NOTE]  
> Se precisar aumentar a cota de núcleos do HDInsight em uma região privada, [envie uma solicitação de lista de permissões](https://aka.ms/canaryintwhitelist).

Você pode [entrar em contato com o suporte para solicitar um aumento de cota](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request).

Há alguns limites de cota fixos. Por exemplo, uma única assinatura do Azure pode ter no máximo 10.000 núcleos. Para detalhes sobre esses limites, confira [Assinatura do Azure e limites, cotas e restrições de serviços](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits).

## <a name="next-steps"></a>Próximas etapas

* [Configure clusters no hdinsight com Apache Hadoop, Spark, Kafka e mais](hdinsight-hadoop-provision-linux-clusters.md): saiba como configurar e configurar clusters no HDInsight.
* [Monitorar o desempenho do cluster](hdinsight-key-scenarios-to-monitor.md): saiba mais sobre os principais cenários a serem monitorados em seu cluster HDInsight e que podem afetar a capacidade do seu cluster.
