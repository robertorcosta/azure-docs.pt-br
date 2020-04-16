---
title: Arquitetura de rede virtual Azure HDInsight
description: Aprenda os recursos disponíveis ao criar um cluster HDInsight em uma rede virtual do Azure.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/14/2020
ms.openlocfilehash: ad0e0250b32f2bdef4944e6e148be3215f3822f7
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81390214"
---
# <a name="azure-hdinsight-virtual-network-architecture"></a>Arquitetura de rede virtual Azure HDInsight

Este artigo explica os recursos presentes quando você implanta um cluster HDInsight em uma rede virtual personalizada do Azure. Essas informações ajudarão você a conectar recursos locais ao seu cluster HDInsight no Azure. Para obter mais informações sobre redes virtuais do Azure, consulte [O que é a Rede Virtual Do Azure?](../virtual-network/virtual-networks-overview.md).

## <a name="resource-types-in-azure-hdinsight-clusters"></a>Tipos de recursos em clusters Azure HDInsight

Os clusters Azure HDInsight têm diferentes tipos de máquinas virtuais ou nós. Cada tipo de nó desempenha um papel na operação do sistema. A tabela a seguir resume esses tipos de nós e seus papéis no cluster.

| Type | Descrição |
| --- | --- |
| Nó de cabeçalho |  Para todos os tipos de cluster, exceto apache storm, os nós de cabeça hospedam os processos que gerenciam a execução do aplicativo distribuído. O nó principal também é o nó em que você pode entrar e executar aplicativos que são coordenados para executar os recursos do cluster. O número de nós de cabeça é fixado em dois para todos os tipos de cluster. |
| Nó ZooKeeper | Zookeeper coordena tarefas entre os nós que estão fazendo o processamento de dados. Ele também faz a eleição líder do nó principal, e mantém o controle de qual nó de cabeça está executando um serviço mestre específico. O número de nódulos ZooKeeper é fixado em três. |
| Nó de trabalho | Representa os nós que suportam a funcionalidade de processamento de dados. Os nós do trabalhador podem ser adicionados ou removidos do cluster para dimensionar a capacidade de computação e gerenciar os custos. |
| Nó de borda do servidor R | O nó de borda do servidor R representa o nó em que você pode entrar e executar aplicativos que são coordenados para executar os recursos do cluster. Um nó de borda não participa da análise de dados dentro do cluster. Este nó também hospeda o R Studio Server, permitindo que você execute o aplicativo R usando um navegador. |
| Nó da região | Para o tipo de cluster HBase, o nó de região (também chamado de Nó de dados) executa o Servidor de Região. Os servidores da região servem e gerenciam uma parte dos dados gerenciados pelo HBase. Os nós de região podem ser adicionados ou removidos do cluster para dimensionar a capacidade de computação e gerenciar os custos.|
| Nó nimbus | Para o tipo de cluster Tempestade, o nó Nimbus fornece funcionalidade semelhante ao nó Cabeça. O nó Nimbus atribui tarefas a outros nódulos em um aglomerado através do Zookeeper, que coordena o funcionamento das topologias da tempestade. |
| Nó de supervisor | Para o tipo de cluster Tempestade, o nó supervisor executa as instruções fornecidas pelo nó Nimbus para fazer o processamento. |

## <a name="resource-naming-conventions"></a>Convenções de nomeação de recursos

Use nomes de domínio totalmente qualificados (FQDNs) ao abordar os nós em seu cluster. Você pode obter os FQDNs para vários tipos de nós em seu cluster usando a [API Ambari](hdinsight-hadoop-manage-ambari-rest-api.md).

Estes FQDNs serão `<node-type-prefix><instance-number>-<abbreviated-clustername>.<unique-identifier>.cx.internal.cloudapp.net`do formulário .

O `<node-type-prefix>` será *hn* para cabeçadas, *wn* para nódulos operários e *zn* para nódulos zookeeper.

Se você precisar apenas do nome do host, use apenas a primeira parte do FQDN:`<node-type-prefix><instance-number>-<abbreviated-clustername>`

## <a name="basic-virtual-network-resources"></a>Recursos básicos de rede virtual

O diagrama a seguir mostra a colocação de nós HDInsight e recursos de rede no Azure.

![Diagrama de entidades HDInsight criadas no VNET personalizado do Azure](./media/hdinsight-virtual-network-architecture/hdinsight-vnet-diagram.png)

Os recursos padrão em uma Rede Virtual Do Azure incluem os tipos de nó de cluster mencionados na tabela anterior. E dispositivos de rede que suportam a comunicação entre a rede virtual e redes externas.

A tabela a seguir resume os nove nós de cluster criados quando o HDInsight é implantado em uma rede virtual personalizada do Azure.

| Tipo de recurso | Número presente | Detalhes |
| --- | --- | --- |
|Nó de cabeçalho | dois |    |
|Nó do ZooKeeper | três | |
|Nó de trabalho | dois | Esse número pode variar de acordo com a configuração e o dimensionamento do cluster. Um mínimo de três nódulos operários são necessários para Apache Kafka.  |
|Nó de gateway | dois | Nós gateway são máquinas virtuais Azure que são criadas no Azure, mas não são visíveis em sua assinatura. Suporte de contato se você precisar reiniciar esses nós. |

Os seguintes recursos de rede presentes são criados automaticamente dentro da rede virtual usada com o HDInsight:

| Recurso de rede | Número presente | Detalhes |
| --- | --- | --- |
|Balanceador de carga | três | |
|Interfaces de Rede | Nove | Esse valor é baseado em um cluster normal, onde cada nó tem sua própria interface de rede. As nove interfaces são para: dois nós de cabeça, três nós de zookeeper, dois nós operianos e dois nós de gateway mencionados na tabela anterior. |
|Endereços IP públicos | dois |    |

## <a name="endpoints-for-connecting-to-hdinsight"></a>Pontos finais para se conectar ao HDInsight

Você pode acessar seu cluster HDInsight de três maneiras:

- Um ponto final HTTPS fora `CLUSTERNAME.azurehdinsight.net`da rede virtual em .
- Um ponto final ssh para conexão direta `CLUSTERNAME-ssh.azurehdinsight.net`ao cabeçada em .
- Um ponto final HTTPS `CLUSTERNAME-int.azurehdinsight.net`dentro da rede virtual . Observe o`-int`" " nesta URL. Este ponto final será resolvido para um IP privado nessa rede virtual e não está acessível a partir da internet pública.

Estes três pontos finais são cada um atribuído um balanceador de carga.

Endereços IP públicos também são fornecidos para os dois pontos finais que permitem a conexão de fora da rede virtual.

1. Um IP público é atribuído ao balanceador de carga para o nome de domínio totalmente qualificado `CLUSTERNAME.azurehdinsight.net`(FQDN) para usar ao se conectar ao cluster a partir da internet .
1. O segundo endereço IP público é usado para `CLUSTERNAME-ssh.azurehdinsight.net`o nome de domínio somente SSH .

## <a name="next-steps"></a>Próximas etapas

- [Tráfego de entrada seguro para clusters HDInsight em uma rede virtual com ponto final privado](https://azure.microsoft.com/blog/secure-incoming-traffic-to-hdinsight-clusters-in-a-vnet-with-private-endpoint/)
