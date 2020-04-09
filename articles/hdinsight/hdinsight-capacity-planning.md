---
title: Planejamento de capacidade de cluster no Azure HDInsight
description: Identifique as principais perguntas para o planejamento de capacidade e desempenho de um cluster Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 04/07/2020
ms.openlocfilehash: 4ede8833fdbdbd57654e6c02147f53e58a17b1de
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80886986"
---
# <a name="capacity-planning-for-hdinsight-clusters"></a>Planejamento de capacidade para clusters HDInsight

Antes de implantar um cluster HDInsight, planeje a capacidade de cluster pretendida determinando o desempenho e a escala necessários. Este planejamento ajuda a otimizar os custos e a usabilidade. Algumas decisões de capacidade de cluster não podem ser alteradas após a implantação. Se os parâmetros de desempenho se alterarem, um cluster poderá ser desmontado e criado novamente sem perder os dados armazenados.

As principais perguntas para o planejamento de capacidade são:

* Em qual região geográfica você deve implantar o cluster?
* Qual a quantidade de armazenamento que você precisa?
* Qual tipo de cluster você deve implantar?
* Qual o tamanho e o tipo de VM (máquina virtual) os nós de cluster devem usar?
* Quantos nós de trabalho seu cluster deve ter?

## <a name="choose-an-azure-region"></a>Escolher uma região do Azure

A região do Azure determina o local em que o cluster é provisionado fisicamente. Para minimizar a latência de leituras e gravações, o cluster deve ficar próximo aos seus dados.

O HDInsight está disponível em muitas regiões do Azure. Para encontrar a região mais próxima, consulte [Produtos disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/?products=hdinsight).

## <a name="choose-storage-location-and-size"></a>Escolher o tamanho e o local de armazenamento

### <a name="location-of-default-storage"></a>Local do armazenamento padrão

O armazenamento padrão, seja uma conta de Armazenamento do Azure ou um Azure Data Lake Storage, deve estar no mesmo local que o cluster. O Armazenamento do Azure está disponível em todos os locais. Data Lake Storage Gen1 está disponível em algumas regiões - veja a disponibilidade atual do [Data Lake Storage](https://azure.microsoft.com/global-infrastructure/services/?products=storage).

### <a name="location-of-existing-data"></a>Local dos dados existentes

Se você quiser usar uma conta de armazenamento existente ou o Data Lake Storage como o armazenamento padrão do seu cluster, então você deve implantar seu cluster nesse mesmo local.

### <a name="storage-size"></a>Tamanho de armazenamento

Em um cluster implantado, você pode anexar contas adicionais do Azure Storage ou acessar outros Data Lake Storage. Todas as suas contas de armazenamento devem viver no mesmo local que seu cluster. Um Data Lake Storage pode estar em um local diferente, embora grandes distâncias possam introduzir alguma latência.

O Azure Storage tem alguns [limites de capacidade,](../azure-resource-manager/management/azure-subscription-service-limits.md#storage-limits)enquanto o Data Lake Storage Gen1 é quase ilimitado.

Um cluster pode acessar uma combinação de contas de armazenamento diferentes. Exemplos comuns incluem:

* Quando é provável que a quantidade de dados exceda a capacidade de armazenamento de um único contêiner de armazenamento de blobs.
* Quando a taxa de acesso ao contêiner de blob pode exceder o máximo em que a limitação ocorre.
* Quando você deseja fazer dados, você já fez o upload para um recipiente blob disponível para o cluster.
* Quando você quiser isolar diferentes partes do armazenamento por questões de segurança ou para simplificar a administração.

Para obter melhor desempenho, use apenas um contêiner por conta de armazenamento.

## <a name="choose-a-cluster-type"></a>Escolher um tipo de cluster

O tipo de cluster determina a carga de trabalho que o cluster HDInsight está configurado para ser executado. Os tipos incluem [Apache Hadoop,](./hadoop/apache-hadoop-introduction.md) [Apache Storm,](./storm/apache-storm-overview.md) [Apache Kafka](./kafka/apache-kafka-introduction.md)ou [Apache Spark.](./spark/apache-spark-overview.md) Para obter uma descrição detalhada dos tipos de cluster disponíveis, consulte [Introdução ao Azure HDInsight](hdinsight-overview.md#cluster-types-in-hdinsight). Cada tipo de cluster tem uma topologia de implantação específica que inclui os requisitos de tamanho e número de nós.

## <a name="choose-the-vm-size-and-type"></a>Escolher tipo e tamanho da VM

Cada tipo de cluster tem um conjunto de tipos de nós e cada tipo de nó tem opções específicas quanto ao tamanho e o tipo de VM.

Para determinar o tamanho de cluster ideal para sua aplicação, você pode submeter a capacidade do cluster a submeter a benchmark e aumentar o tamanho conforme indicado. Por exemplo, você pode usar uma carga de trabalho simulada ou uma *consulta canary*. Execute suas cargas de trabalho simuladas em diferentes clusters de tamanho. Aumente gradualmente o tamanho até que o desempenho pretendido seja alcançado. Uma consulta canária pode ser inserida periodicamente entre as outras consultas de produção para mostrar se o cluster tem recursos suficientes.

Para obter mais informações sobre como escolher a família VM certa para sua carga de trabalho, consulte [Selecionando o tamanho de VM certo para o cluster](hdinsight-selecting-vm-size.md).

## <a name="choose-the-cluster-scale"></a>Escolher a escala do cluster

A escala do cluster é determinada pela quantidade de seus nós de VM. Para todos os tipos de cluster, existem tipos de nós que têm uma escala específica e tipos de nó que suportam a escala. Por exemplo, um cluster pode exigir exatamente três nódulos [Apache ZooKeeper](https://zookeeper.apache.org/) ou dois nós de cabeça. Os nódulos do trabalhador que fazem o processamento de dados de forma distribuída beneficiam-se dos nódulos adicionais do trabalhador.

Dependendo do seu tipo de cluster, aumentar o número de nós do trabalhador adiciona capacidade computacional adicional (como mais núcleos). Mais nós aumentarão a memória total necessária para todo o cluster para suportar o armazenamento em memória dos dados que estão sendo processados. Assim como na escolha do tamanho e do tipo de VM, a seleção da escala de cluster certa é tipicamente alcançada empiricamente. Use cargas de trabalho simuladas ou consultas canárias.

Você pode dimensionar seu cluster para atender às demandas de carga máxima. Em seguida, dimensione-o de volta para baixo quando esses nós extras não são mais necessários. O [recurso Autoscale](hdinsight-autoscale-clusters.md) permite que você dimensione automaticamente seu cluster com base em métricas e tempos predeterminados. Para obter mais informações sobre o dimensionamento manual de seus clusters, consulte [Clusters Scale HDInsight](hdinsight-scaling-best-practices.md).

### <a name="cluster-lifecycle"></a>Ciclo de vida do cluster

Você é cobrado pela vida de um cluster. Se houver apenas horários específicos em que você precise do seu cluster, [crie clusters sob demanda usando o Azure Data Factory](hdinsight-hadoop-create-linux-clusters-adf.md). Você também pode criar scripts do PowerShell que provisionam e excluem o cluster e, em seguida, agendar esses scripts usando a [Automação do Azure](https://azure.microsoft.com/services/automation/).

> [!NOTE]  
> Quando um cluster é excluído, seu metastore do Hive padrão também é excluído. Para manter o metastore para a próxima recriação de cluster, use um repositório de metadados externo, como o Banco de Dados do Azure ou [Apache Oozie](https://oozie.apache.org/).
<!-- see [Using external metadata stores](hdinsight-using-external-metadata-stores.md). -->

### <a name="isolate-cluster-job-errors"></a>Isolar os erros de trabalho do cluster

Às vezes, erros podem ocorrer devido à execução paralela de vários mapas e reduzir componentes em um cluster de vários nós. Para ajudar a isolar o problema, tente testar distribuídos. Execute vários trabalhos simultâneos em um único cluster de nó de trabalhador. Em seguida, expanda essa abordagem para executar vários trabalhos simultaneamente em clusters contendo mais de um nó. Para criar um cluster HDInsight de nó único *`Custom(size, settings, apps)`* no Azure, use a opção e use um valor de 1 para *números de nó sofríveis* na seção Tamanho do **Cluster** ao provisionar um novo cluster no portal.

## <a name="quotas"></a>Cotas

Depois de determinar o tamanho, a escala e o tipo da VM do cluster de destino, verifique os limites de capacidade de cota atuais da sua assinatura. Quando você atinge um limite de cotas, você não pode implantar novos clusters. Ou dimensionar os clusters existentes adicionando mais nós de trabalhadores. O único limite de cota é a cota de Núcleos de CPU que existe no nível da região para cada assinatura. Por exemplo, sua assinatura pode ter o limite de 30 núcleos na região Leste dos EUA.

Para verificar seus núcleos disponíveis, faça as seguintes etapas:

1. Entre no [portal do Azure](https://portal.azure.com/).
2. Navegue até a página **Visão Geral** para o cluster HDInsight.
3. No menu à esquerda, selecione **Limites de cota**.

   A página exibe o número de núcleos em uso, o número de núcleos disponíveis e os núcleos totais.

Se precisar solicitar um aumento de cota, siga estes passos:

1. Entre no [portal do Azure](https://portal.azure.com/).
1. Selecione **Ajuda + suporte** no lado inferior esquerdo da página.
1. Selecione **Nova solicitação de suporte**.
1. Na página **Nova solicitação de suporte**, na guia **Noções básicas**, selecione as opções a seguir:

   - **Tipo de emissão**: **Limites de serviço e assinatura (cotas)**
   - **Assinatura**: a assinatura que você deseja modificar
   - **Tipo de cota**: **HDInsight**

     ![Crie uma solicitação de suporte para aumentar a cota de núcleos do HDInsight](./media/hdinsight-capacity-planning/hdinsight-quota-support-request.png)

1. Selecione **A seguir: Soluções >>**.
1. Na página **Detalhes,** digite uma descrição do problema, selecione a gravidade do problema, seu método de contato preferido e outros campos necessários.
1. Selecione **A seguir: Revisar + criar >>**.
1. Na guia **Revisar + criar**, selecione **Criar**.

> [!NOTE]  
> Se precisar aumentar a cota de núcleos do HDInsight em uma região privada, [envie uma solicitação de lista de permissões](https://aka.ms/canaryintwhitelist).

Você pode [entrar em contato com o suporte para solicitar um aumento de cota](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request).

Existem alguns limites de cotas fixas. Por exemplo, uma única assinatura do Azure pode ter no máximo 10.000 núcleos. Para detalhes sobre esses limites, confira [Assinatura do Azure e limites, cotas e restrições de serviços](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits).

## <a name="next-steps"></a>Próximas etapas

* [Configure clusters no HDInsight com Apache Hadoop, Spark, Kafka e muito mais](hdinsight-hadoop-provision-linux-clusters.md): Saiba como configurar e configurar clusters no HDInsight.
* [Monitorar o desempenho do cluster](hdinsight-key-scenarios-to-monitor.md): saiba mais sobre os principais cenários a serem monitorados em seu cluster HDInsight e que podem afetar a capacidade do seu cluster.
