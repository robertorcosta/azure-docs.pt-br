---
title: Componentes de alta disponibilidade no Azure HDInsight
description: Visão geral dos vários componentes de alta disponibilidade usados pelos clusters HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 11/11/2019
ms.openlocfilehash: 38fb45fd339b5e2c7cab6f66a1ed6c0df73fb29e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74069621"
---
# <a name="high-availability-services-supported-by-azure-hdinsight"></a>Serviços de alta disponibilidade suportados pelo Azure HDInsight

 Para fornecer-lhe níveis ideais de disponibilidade para seus componentes de análise, o HDInsight foi desenvolvido com uma arquitetura exclusiva para garantir alta disponibilidade (HA) de serviços críticos. Alguns componentes desta arquitetura foram desenvolvidos pela Microsoft para fornecer failover automático. Outros componentes são componentes Apache padrão que são implantados para suportar serviços específicos. Este artigo explica a arquitetura do modelo de serviço HA no HDInsight, como o HDInsight suporta failover para serviços ha e práticas recomendadas para se recuperar de outras interrupções de serviço.

## <a name="high-availability-infrastructure"></a>Infra-estrutura de alta disponibilidade

O HDInsight fornece infra-estrutura personalizada para garantir que quatro serviços principais sejam de alta disponibilidade com recursos automáticos de failover:

- Servidor Apache Ambari
- Servidor de linha do tempo do aplicativo para Apache YARN
- Servidor de histórico de trabalho para Hadoop MapReduce
- Apache Livy

Esta infra-estrutura consiste em uma série de serviços e componentes de software, alguns dos quais são projetados pela Microsoft. Os seguintes componentes são exclusivos da plataforma HDInsight:

- Controlador de failover escravo
- Controlador de failover mestre
- Serviço de alta disponibilidade escravo
- Serviço de alta disponibilidade mestre

![infra-estrutura de alta disponibilidade](./media/hdinsight-high-availability-components/high-availability-architecture.png)

Há também outros serviços de alta disponibilidade, que são suportados por componentes de confiabilidade Apache de código aberto. Esses componentes também estão presentes nos clusters HDInsight:

- NameNode do Sistema de Arquivos Hadoop (HDFS)
- Gerenciador de recursos YARN
- Mestre Hbase

As seções a seguir fornecerão mais detalhes sobre como esses serviços funcionam juntos.

## <a name="hdinsight-high-availability-services"></a>HDInsight serviços de alta disponibilidade

A Microsoft oferece suporte para os quatro serviços Apache na tabela a seguir em clusters HDInsight. Para distingui-los dos serviços de alta disponibilidade suportados por componentes do Apache, eles são chamados *de serviços HDInsight HA*.

| Serviço | Nós de cluster | Tipos de cluster | Finalidade |
|---|---|---|---|
| Servidor Apache Ambari| Cabeça ativa | Todos | Monitora e gerencia o cluster.|
| Servidor de linha do tempo do aplicativo para Apache YARN | Cabeça ativa | Todos, exceto Kafka. | Mantém informações de depuração sobre trabalhos de YARN em execução no cluster.|
| Servidor de histórico de trabalho para Hadoop MapReduce | Cabeça ativa | Todos, exceto Kafka. | Mantém a depuração de dados para mapearreduzir empregos.|
| Apache Livy | Cabeça ativa | Spark | Permite fácil interação com um cluster Spark sobre uma interface REST |

>[!Note]
> Os clusters do HDInsight Enterprise Security Package (ESP) atualmente fornecem apenas a alta disponibilidade do servidor Ambari.

### <a name="architecture"></a>Arquitetura

Cada cluster HDInsight tem dois headnodes nos modos ativo e de espera, respectivamente. Os serviços HDInsight HA são executados apenas em headnodes. Esses serviços devem estar sempre funcionando no headnode ativo, e parados e colocados no modo de manutenção no headnode de espera.

Para manter os estados corretos dos serviços de HA e fornecer um failover rápido, o HDInsight utiliza o Apache ZooKeeper, que é um serviço de coordenação para aplicativos distribuídos, para realizar uma eleição ativa de cabeça. O HDInsight também disponibiliza alguns processos Java em segundo plano, que coordenam o procedimento de failover para serviços HDInsight HA. Estes serviços são os seguintes: o controlador de failover mestre, o controlador de failover escravo, o *master-ha-service*e o *slave-ha-service*.

### <a name="apache-zookeeper"></a>Apache ZooKeeper

Apache ZooKeeper é um serviço de coordenação de alto desempenho para aplicativos distribuídos. Na produção, o ZooKeeper geralmente é executado no modo replicado onde um grupo replicado de servidores ZooKeeper forma um quórum. Cada cluster HDInsight possui três nós ZooKeeper que permitem que três servidores ZooKeeper formem um quórum. O HDInsight tem dois quórum zookeeper funcionando em paralelo entre si. Um quórum decide o headnode ativo em um cluster no qual os serviços HDInsight HA devem ser executados. Outro quórum é usado para coordenar os serviços de HA fornecidos pelo Apache, conforme detalhado em seções posteriores.

### <a name="slave-failover-controller"></a>Controlador de failover escravo

O controlador de failover escravo é executado em todos os tópicos em um cluster HDInsight. Este controlador é responsável por iniciar o agente Ambari e *o serviço de escravos* em cada nó. Ele consulta periodicamente o primeiro quórum zookeeper sobre o cabeçada ativo. Quando os headnodes ativos e de prontidão mudam, o controlador de failover escravo executa o seguinte:

1. Atualiza o arquivo de configuração do host.
1. Reinicia o agente Ambari.

O *serviço de escravos* é responsável por parar os serviços HDInsight HA (exceto servidor Ambari) no headnode de espera.

### <a name="master-failover-controller"></a>Controlador de failover mestre

Um controlador de failover mestre é executado em ambos os headnodes. Ambos os controladores de failover mestre se comunicam com o primeiro quórum ZooKeeper para nomear o cabeçanode que eles estão executando como o cabeçanode ativo.

Por exemplo, se o controlador de failover mestre no headnode 0 ganhar a eleição, as seguintes mudanças ocorrem:

1. Headnode 0 torna-se ativo.
1. O controlador de failover mestre inicia o servidor Ambari e o *serviço mestre-ha* no headnode 0.
1. O outro controlador de failover mestre interrompe o servidor Ambari e o *serviço mestre-ha* no headnode 1.

O serviço mestre-ha só é executado no headnode ativo, ele pára os serviços HDInsight HA (exceto servidor Ambari) em headnode standby e os inicia em headnode ativo.

### <a name="the-failover-process"></a>O processo de failover

![processo de failover](./media/hdinsight-high-availability-components/failover-steps.png)

Um monitor de saúde é executado em cada cabeçada, juntamente com o controlador de failover mestre para enviar notificações de hearbeat para o quórum zookeeper. O headnode é considerado um serviço de HA neste cenário. O monitor de saúde verifica se cada serviço de alta disponibilidade é saudável e se está pronto para participar da eleição de liderança. Se sim, este cabeçalhos vai competir na eleição. Se não, ele vai sair da eleição até que fique pronto novamente.

Se o headnode de espera alcançar a liderança e se tornar ativo (como no caso de uma falha com o nó ativo anterior), seu controlador de failover mestre iniciará todos os serviços HDInsight HA nele. O controlador de failover mestre também interromperá esses serviços no outro cabeçalé.

Para falhas de serviço do HDInsight HA, como um serviço estar desligado ou insalubre, o controlador de failover mestre deve reiniciar ou interromper automaticamente os serviços de acordo com o status de headnode. Os usuários não devem iniciar manualmente os serviços HDInsight HA em ambos os nós de cabeça. Em vez disso, permita failover automático ou manual para ajudar o serviço a se recuperar.

### <a name="inadvertent-manual-intervention"></a>Intervenção manual inadvertida

Os serviços HDInsight HA só devem ser executados no headnode ativo e serão reiniciados automaticamente quando necessário. Uma vez que os serviços individuais de HA não têm seu próprio monitor de saúde, o failover não pode ser acionado ao nível do serviço individual. O failover é assegurado no nível do nó e não no nível de serviço.

### <a name="some-known-issues"></a>Alguns problemas conhecidos

- Ao iniciar manualmente um serviço HA no headnode de espera, ele não vai parar até que o próximo failover aconteça. Quando os serviços de HA estão sendo executados em ambos os headnodes, alguns problemas potenciais incluem: Ambari UI é inacessível, Ambari lança erros, YARN, Spark e Oozie trabalhos podem ficar presos.

- Quando um serviço HA no headnode ativo pára, ele não será reiniciado até que o próximo failover aconteça ou o controlador de failover mestre/master-ha-service seja reiniciado. Quando um ou mais serviços de HA param no headnode ativo, especialmente quando o servidor Ambari pára, a Ambari UI está inacessível, outros problemas potenciais incluem falhas de empregos De SArn, Spark e Oozie.

## <a name="apache-high-availability-services"></a>Serviços de alta disponibilidade apache

O Apache oferece alta disponibilidade para HDFS NameNode, YARN ResourceManager e HBase Master, que também estão disponíveis em clusters HDInsight. Ao contrário dos serviços HDInsight HA, eles são suportados em clusters ESP. Os serviços apache ha comunicam-se com o segundo quórum zookeeper (descrito na seção acima) para eleger estados ativos/em standby e realizar failover automático. As seções a seguir detalham como esses serviços funcionam.

### <a name="hadoop-distributed-file-system-hdfs-namenode"></a>Hadoop Distributed File System (HDFS) NameNode

Os clusters HDInsight baseados no Apache Hadoop 2.0 ou superior fornecem à NameNode alta disponibilidade. Há dois NameNodes em execução nos headnodes, que são configurados para failover automático. Os NameNodes usam o *ZKFailoverController* para se comunicar com o Zookeeper para eleger o status ativo/em standby. O *ZKFailoverController* é executado em ambos os headnodes, e funciona da mesma forma que o controlador de failover mestre acima.

O segundo quórum zookeeper é independente do primeiro quórum, de modo que o NameNode ativo pode não funcionar no cabeçado ativo. Quando o NameNode ativo está morto ou insalubre, o NameNode de prontidão ganha a eleição e se torna ativo.

### <a name="yarn-resourcemanager"></a>Gerenciador de recursos YARN

Os clusters HDInsight baseados no Apache Hadoop 2.4 ou superior, suportam a alta disponibilidade do YARN ResourceManager. Existem dois ResourceManagers, rm1 e rm2, rodando no headnode 0 e headnode 1, respectivamente. Como NameNode, o YARN ResourceManager também está configurado para failover automático. Outro ResourceManager é eleito automaticamente para estar ativo quando o Atual ResourceManager ativo for para baixo ou sem resposta.

O YARN ResourceManager usa seu *ActiveStandbyElector* incorporado como detector de falhas e eleitor líder. Ao contrário do HDFS NameNode, o YARN ResourceManager não precisa de um daemon ZKFC separado. O ResourceManager ativo escreve seus estados no Apache Zookeeper.

A alta disponibilidade do YARN ResourceManager é independente do NameNode e de outros serviços HDInsight HA. O ResourceManager ativo pode não ser executado no headnode ativo ou no headnode onde o NameNode ativo está sendo executado. Para obter mais informações sobre a alta disponibilidade do YARN ResourceManager, consulte [ResourceManager High Availability](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/ResourceManagerHA.html).

### <a name="hbase-master"></a>Mestre Hbase

Os clusters HDInsight HBase suportam a alta disponibilidade do HBase Master. Ao contrário de outros serviços de HA, que são executados em headnodes, o HBase Masters é executado nos três nós zookeeper, onde um deles é o mestre ativo e os outros dois estão de prontidão. Como NameNode, hbase master coordena com Apache Zookeeper para a eleição de líder e faz failover automático quando o mestre ativo atual tem problemas. Há apenas um HBase Master ativo a qualquer momento.

## <a name="next-steps"></a>Próximas etapas

- [Disponibilidade e confiabilidade dos clusters Apache Hadoop em HDInsight](hdinsight-high-availability-linux.md)
- [Arquitetura de rede virtual Azure HDInsight](hdinsight-virtual-network-architecture.md)
