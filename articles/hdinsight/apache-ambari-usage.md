---
title: Uso do Apache Ambari no Azure HDInsight
description: Discussão sobre como o Apache Ambari é usado no Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/05/2020
ms.openlocfilehash: fe7d6d4e70bc55a6a91d3c1a1b910db4b5469fe6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84197084"
---
# <a name="apache-ambari-usage-in-azure-hdinsight"></a>Uso do Apache Ambari no Azure HDInsight

O HDInsight usa o Apache Ambari para implantação e gerenciamento de cluster. Os agentes do Ambari são executados em cada nó (cabeçalho, nó de trabalho, Zookeeper e edgenode, se existir). O servidor Ambari só é executado em cabeçalho (hn0 ou hn1). Somente uma instância do Ambari Server deverá ser executada ao mesmo tempo. Isso é controlado pelo controlador de failover do HDInsight. Quando um dos cabeçalho está inativo para reinicialização ou manutenção, o outro cabeçalho se tornará ativo e Ambari servidor no segundo cabeçalho será iniciado.

Toda a configuração de cluster deve ser feita por meio da [interface do usuário do amAmbari](./hdinsight-hadoop-manage-ambari.md), qualquer alteração local será substituída quando o nó for reiniciado.

## <a name="failover-controller-services"></a>Serviços de controlador de failover

O controlador de failover do HDInsight também é responsável por atualizar o endereço IP do host cabeçalho, que aponta para o nó principal ativo atual. Todos os agentes do Ambari estão configurados para relatar seu estado e pulsação para o host cabeçalho. O controlador de failover é um conjunto de serviços em execução em cada nó do cluster, se eles não estiverem em execução, o failover do cabeçalho poderá não funcionar corretamente e você acabará com o HTTP 502 ao tentar acessar o servidor Ambari.

Para verificar qual cabeçalho está ativo, uma maneira é fazer o ssh para um dos nós no cluster e, em seguida, executar `ping headnodehost` e comparar o IP com aquele dos dois cabeçalho.

Se os serviços do controlador de failover não estiverem em execução, o failover do cabeçalho poderá não acontecer corretamente, o que pode acabar sem executar o Ambari Server. Para verificar se os serviços do controlador de failover estão em execução, execute:

```bash
ps -ef | grep failover
```

## <a name="logs"></a>Logs

No cabeçalho ativo, você pode verificar os logs do servidor Ambari em:

```
/var/log/ambari-server/ambari-server.log
/var/log/ambari-server/ambari-server-check-database.log
```

Em qualquer nó no cluster, você pode verificar os logs do agente do Ambari em:

```bash
/var/log/ambari-agent/ambari-agent.log
```

## <a name="service-start-sequences"></a>Sequências de início do serviço

Esta é a sequência de serviço iniciar no momento da inicialização:

1. O Hdinsight-Agent inicia os serviços do controlador de failover.
1. Os serviços do controlador de failover iniciam o agente Ambari em cada nó e Ambari Server no Active cabeçalho.

## <a name="ambari-database"></a>Banco de dados Ambari

O HDInsight cria um banco de dados no banco de dados SQL nos bastidores para servir como o banco de dados do Ambari Server. A [camada de serviço padrão é S0](../azure-sql/database/elastic-pool-scale.md).

Para qualquer cluster com contagem de nós de trabalho maior que 16 ao criar o cluster, S2 é a camada de serviço de banco de dados.

## <a name="takeaway-points"></a>Pontos importantes

Nunca inicie/interrompa manualmente os serviços ambari-Server ou ambari-Agent, a menos que você esteja tentando reiniciar o serviço para solucionar um problema. Para forçar um failover, você pode reinicializar o cabeçalho ativo.

Nunca modifique manualmente nenhum arquivo de configuração em qualquer nó de cluster, deixe que a interface do usuário do Ambari faça o trabalho para você.

## <a name="next-steps"></a>Próximas etapas

* [Gerenciar clusters HDInsight usando a interface do usuário da Web do Apache Ambari](hdinsight-hadoop-manage-ambari.md)
* [Gerenciar clusters HDInsight usando a API REST do Apache Ambari](hdinsight-hadoop-manage-ambari-rest-api.md)

Se você não encontrou seu problema ou não conseguiu resolver seu problema, visite um dos seguintes canais para obter mais suporte:

* Obtenha respostas de especialistas do Azure por meio do [Suporte da Comunidade do Azure](https://azure.microsoft.com/support/community/).

* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport), a conta oficial do Microsoft Azure para melhorar a experiência do cliente. Como se conectar à comunidade do Azure para os recursos certos: respostas, suporte e especialistas.

* Se precisar de mais ajuda, poderá enviar uma solicitação de suporte do [portal do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selecione **Suporte** na barra de menus ou abra o hub **Ajuda + suporte**. Para obter informações mais detalhadas, consulte [Como criar uma solicitação de Suporte do Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). O acesso ao Gerenciamento de assinaturas e ao suporte de cobrança está incluído na sua assinatura do Microsoft Azure, e o suporte técnico é fornecido por meio de um dos [Planos de suporte do Azure](https://azure.microsoft.com/support/plans/).
