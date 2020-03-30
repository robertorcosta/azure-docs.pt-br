---
title: Uso do Apache Ambari no Azure HDInsight
description: Discussão sobre como o Apache Ambari é usado no Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/05/2020
ms.openlocfilehash: 466c170985715be52a90d579c19ca23aefefe2e5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77067390"
---
# <a name="apache-ambari-usage-in-azure-hdinsight"></a>Uso do Apache Ambari no Azure HDInsight

O HDInsight usa o Apache Ambari para implantação e gerenciamento de clusters. Os agentes ambari correm em cada nó (cabeça, nó de trabalhador, zookeeper e edgenode, se existe). O servidor Ambari é executado apenas em headnode (hn0 ou hn1). Apenas uma instância do servidor Ambari será executada ao mesmo tempo. Isso é controlado pelo controlador de failover HDInsight. Quando um dos headnodes estiver desligado para reinicialização ou manutenção, o outro headnode ficará ativo e o servidor Ambari no segundo cabeçado será iniciado.

Toda a configuração do cluster deve ser feita através da [UI Ambari,](./hdinsight-hadoop-manage-ambari.md)qualquer alteração local será substituída quando o nó for reiniciado.

## <a name="failover-controller-services"></a>Serviços de controlador failover

O controlador de failover HDInsight também é responsável pela atualização do endereço IP do headnode host, que aponta para o nó de cabeça ativo atual. Todos os agentes Ambari estão configurados para relatar seu estado e batimentos cardíacos ao host de cabeça. O controlador failover é um conjunto de serviços em execução em cada nó do cluster, se eles não estiverem em execução, o failover de cabeça pode não funcionar corretamente e você acabará com HTTP 502 ao tentar acessar o servidor Ambari.

Para verificar qual cabeçade está ativa, uma maneira é ssh para um dos `ping headnodehost` nós no cluster, em seguida, executar e comparar o IP com o dos dois headnodes.

Se os serviços do controlador failover não forem executados, o failover de cabeça não acontecerá corretamente, o que pode acabar não executando o servidor Ambari. Para verificar se os serviços do controlador failover estão sendo executados, execute:

```bash
ps -ef | grep failover
```

## <a name="logs"></a>Logs

No headnode ativo, você pode verificar os logs do servidor Ambari em:

```
/var/log/ambari-server/ambari-server.log
/var/log/ambari-server/ambari-server-check-database.log
```

Em qualquer nó no cluster, você pode verificar os logs do agente Ambari em:

```bash
/var/log/ambari-agent/ambari-agent.log
```

## <a name="service-start-sequences"></a>Seqüências de início de serviço

Esta é a seqüência de início de serviço na hora da inicialização:

1. O hdinsight-agent inicia os serviços de controlador failover.
1. Os serviços do controlador failover iniciam o agente Ambari em cada nó e servidor Ambari no headnode ativo.

## <a name="ambari-database"></a>Banco de dados Ambari

O HDInsight cria o Banco de Dados SQL Azure o capô para servir como banco de dados para o servidor Ambari. O nível de serviço padrão [é S0](../sql-database/sql-database-elastic-pool-scale.md).

Para qualquer cluster com contagem de nó de trabalhador maior que 16 ao criar o cluster, S2 é o nível de serviço do banco de dados.

## <a name="takeaway-points"></a>Pontos de retirada

Nunca inicie/pare manualmente os serviços de ambari-servidor ou ambari-agent, a menos que você esteja tentando reiniciar o serviço para contornar um problema. Para forçar um failover, você pode reiniciar o headnode ativo.

Nunca modifique manualmente nenhum arquivo de configuração em qualquer nó de cluster, deixe a Ambari UI fazer o trabalho para você.

## <a name="next-steps"></a>Próximas etapas

* [Gerenciar clusters do HDInsight usando a interface do usuário da Web do Apache Ambari](hdinsight-hadoop-manage-ambari.md)
* [Gerenciar clusters HDInsight usando a API REST do Apache Ambari](hdinsight-hadoop-manage-ambari-rest-api.md)

Se você não encontrou seu problema ou não conseguiu resolver seu problema, visite um dos seguintes canais para obter mais suporte:

* Obtenha respostas de especialistas do Azure através [do Azure Community Support](https://azure.microsoft.com/support/community/).

* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport) - a conta oficial do Microsoft Azure para melhorar a experiência do cliente. Conectando a comunidade Azure aos recursos certos: respostas, suporte e especialistas.

* Se você precisar de mais ajuda, você pode enviar uma solicitação de suporte do [portal Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selecione **Suporte** na barra de menus ou abra o centro **de suporte Ajuda +.** Para obter informações mais [detalhadas, consulte Como criar uma solicitação de suporte ao Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). O acesso ao gerenciamento de assinaturas e suporte ao faturamento está incluído na assinatura do Microsoft Azure, e o suporte técnico é fornecido através de um dos Planos de Suporte do [Azure](https://azure.microsoft.com/support/plans/).
