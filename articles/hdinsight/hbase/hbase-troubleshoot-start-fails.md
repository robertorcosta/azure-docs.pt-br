---
title: Apache HBase Master não consegue iniciar no Azure HDInsight
description: Apache HBase Master (HMaster) não consegue iniciar no Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/14/2019
ms.openlocfilehash: 290b541d9b5e86616373d2e426241fca07e780ed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75887199"
---
# <a name="apache-hbase-master-hmaster-fails-to-start-in-azure-hdinsight"></a>Apache HBase Master (HMaster) não consegue iniciar no Azure HDInsight

Este artigo descreve etapas de solução de problemas e possíveis resoluções para problemas ao interagir com clusters Azure HDInsight.

## <a name="scenario-atomic-renaming-failure"></a>Cenário: Falha na renomeação atômica

### <a name="issue"></a>Problema

Arquivos inesperados identificados durante o processo de inicialização.

### <a name="cause"></a>Causa

Durante o processo de inicialização, o HMaster executa muitas etapas de inicialização, incluindo a movimentação de dados da pasta scratch (.tmp) para a pasta de dados. O HMaster também analisa a pasta de logs (WAL) de gravação antecipada para ver se há algum servidor de região sem resposta.

HMaster faz um comando de lista básica nas pastas WAL. Se, a qualquer momento, o HMaster detectar um arquivo inesperado em qualquer uma dessas pastas, ele lançará uma exceção e não iniciará.

### <a name="resolution"></a>Resolução

Verifique a pilha de chamadas e tente determinar qual pasta pode estar causando o problema (por exemplo, pode ser a pasta WAL ou a pasta .tmp). Em seguida, no Cloud Explorer ou usando comandos do HDFS, tente localizar o arquivo problemático. Normalmente, isso `*-renamePending.json` é um arquivo. (O `*-renamePending.json` arquivo é um arquivo de diário que é usado para implementar a operação de renomeação atômica no driver WASB. Devido a bugs nesta implementação, esses arquivos podem ser deixados após falhas de processo, e assim por diante.) Exclua este arquivo com força no Cloud Explorer ou usando comandos HDFS.

Às vezes, também pode haver um `$$$.$$$` arquivo temporário chamado algo como neste local. Você deve usar o comando `ls` de HDFS para ver esse arquivo; não é possível ver o arquivo no Cloud Explorer. Para excluir este arquivo, use o comando `hdfs dfs -rm /\<path>\/\$\$\$.\$\$\$` de HDFS.

Após a execução desses comandos, o HMaster deve iniciar imediatamente.

---

## <a name="scenario-no-server-address-listed"></a>Cenário: Nenhum endereço do servidor listado

### <a name="issue"></a>Problema

Você pode ver uma mensagem que indica que a `hbase: meta` tabela não está online. A `hbck` execução `hbase: meta table replicaId 0 is not found on any region.` pode relatar que nos registros do `No server address listed in hbase: meta for region hbase: backup <region name>`HMaster, você pode ver a mensagem: .  

### <a name="cause"></a>Causa

O HMaster não pôde inicializar depois de reiniciar o HBase.

### <a name="resolution"></a>Resolução

1. No shell do HBase, insira os comandos a seguir (altere os valores reais conforme aplicável):

    ```hbase
    scan 'hbase:meta'
    delete 'hbase:meta','hbase:backup <region name>','<column name>'
    ```

1. Exclua `hbase: namespace` a entrada. Esta entrada pode ser o mesmo erro `hbase: namespace` que está sendo relatado quando a tabela é digitalizada.

1. Reinicie o HMaster ativo da interface do usuário do Ambari para exibir o HBase em estado de execução.

1. No shell do HBase, execute o seguinte comando para mostrar todas as tabelas offline:

    ```hbase
    hbase hbck -ignorePreCheckPermission -fixAssignments
    ```

---

## <a name="scenario-javaioioexception-timedout"></a>Cenário: java.io.IOException: Timedout

### <a name="issue"></a>Problema

HMaster vezes fora com exceção fatal semelhante a: `java.io.IOException: Timedout 300000ms waiting for namespace table to be assigned`.

### <a name="cause"></a>Causa

Você pode enfrentar esse problema se tiver muitas tabelas e regiões que não foram liberadas ao reiniciar os serviços do HMaster. O tempo hámenos é um defeito conhecido com o HMaster. Tarefas de inicialização de cluster geral podem levar muito tempo. HMaster é desligado se a tabela de namespace ainda não estiver atribuída. As tarefas de inicialização demoradas acontecem onde existe uma grande quantidade de dados não lavados e um intervalo de cinco minutos não é suficiente.

### <a name="resolution"></a>Resolução

1. A partir da UI Apache Ambari, vá para **HBase** > **Configs**. No arquivo `hbase-site.xml` personalizado, adicione a seguinte configuração:

    ```
    Key: hbase.master.namespace.init.timeout Value: 2400000  
    ```

1. Reinicie os serviços necessários (HMaster e possivelmente outros serviços do HBase).

---

## <a name="scenario-frequent-region-server-restarts"></a>Cenário: Reinicialização frequente do servidor de região

### <a name="issue"></a>Problema

Os nódulos reiniciam periodicamente. A partir dos logs de servidor da região, você pode ver entradas semelhantes a:

```
2017-05-09 17:45:07,683 WARN  [JvmPauseMonitor] util.JvmPauseMonitor: Detected pause in JVM or host machine (eg GC): pause of approximately 31000ms
2017-05-09 17:45:07,683 WARN  [JvmPauseMonitor] util.JvmPauseMonitor: Detected pause in JVM or host machine (eg GC): pause of approximately 31000ms
2017-05-09 17:45:07,683 WARN  [JvmPauseMonitor] util.JvmPauseMonitor: Detected pause in JVM or host machine (eg GC): pause of approximately 31000ms
```

### <a name="cause"></a>Causa

Longa `regionserver` pausa JVM GC. A pausa `regionserver` fará com que não responda e não seja capaz de enviar batimentos cardíacos ao HMaster dentro do tempo de 40 s. HMaster acreditará que `regionserver` está `regionserver` morto e abortará o e reiniciará.

### <a name="resolution"></a>Resolução

Altere o tempo de intervalo `hbase-site` `zookeeper.session.timeout` da sessão `zoo.cfg` zookeeper, não apenas a configuração, mas também a configuração `maxSessionTimeout` do Zookeeper precisa ser alterada.

1. Acesse ambari UI, vá para **HBase -> Configs -> Settings**, na seção Timeouts, altere o valor do Tempo-tempo da sessão do Zookeeper.

1. Acesse ambari UI, vá para **Zookeeper -> Configs -> Custom** `zoo.cfg`, adicione/altere a configuração a seguir. Certifique-se de que o `zookeeper.session.timeout`valor é o mesmo que HBase .

    ```
    Key: maxSessionTimeout Value: 120000  
    ```

1. Reiniciar os serviços necessários.

---

## <a name="scenario-log-splitting-failure"></a>Cenário: Falha na divisão de log

### <a name="issue"></a>Problema

HMasters não conseguiu aparecer em um cluster HBase.

### <a name="cause"></a>Causa

Configurações HDFS e HBase mal configuradas para uma conta de armazenamento secundária.

### <a name="resolution"></a>Resolução

definir hbase.rootdir: wasb://@.blob.core.windows.net/hbase e reiniciar serviços no Ambari.

---

## <a name="next-steps"></a>Próximas etapas

Se você não encontrou seu problema ou não conseguiu resolver seu problema, visite um dos seguintes canais para obter mais suporte:

* Obtenha respostas de especialistas do Azure através [do Azure Community Support](https://azure.microsoft.com/support/community/).

* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport) - a conta oficial do Microsoft Azure para melhorar a experiência do cliente. Conectando a comunidade Azure aos recursos certos: respostas, suporte e especialistas.

* Se você precisar de mais ajuda, você pode enviar uma solicitação de suporte do [portal Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selecione **Suporte** na barra de menus ou abra o centro **de suporte Ajuda +.** Para obter informações mais [detalhadas, consulte Como criar uma solicitação de suporte ao Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). O acesso ao gerenciamento de assinaturas e suporte ao faturamento está incluído na assinatura do Microsoft Azure, e o suporte técnico é fornecido através de um dos Planos de Suporte do [Azure](https://azure.microsoft.com/support/plans/).
