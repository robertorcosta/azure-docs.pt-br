---
title: Monitorar o desempenho do cluster – Azure HDInsight
description: Como monitorar a saúde e o desempenho dos clusters Apache Hadoop no Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 03/09/2020
ms.openlocfilehash: 75ac5a7fc352f877573d79a004d8da761c6f1cef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79082873"
---
# <a name="monitor-cluster-performance-in-azure-hdinsight"></a>Monitore o desempenho do cluster no Azure HDInsight

Monitorar a integridade e o desempenho de um cluster HDInsight é essencial para manter o desempenho ideal e a utilização de recursos. O monitoramento também pode ajudar a detectar e solucionar erros de configuração de cluster e problemas de código de usuário.

As seções a seguir descrevem como monitorar e otimizar a carga em seus clusters, filas do YARN do Apache Hadoop e detectar problemas de limitação de armazenamento.

## <a name="monitor-cluster-load"></a>Carga de cluster de monitor

Os clusters do Hadoop podem oferecer o desempenho mais ideal quando a carga no cluster é distribuída uniformemente em todos os nós. Isso permite que as tarefas de processamento sejam executadas sem serem restringidas por recursos de RAM, CPU ou disco em nós individuais.

Para obter uma olhada de alto nível nos nós do seu cluster e no carregamento, faça login na [UI da Web ambari](hdinsight-hadoop-manage-ambari.md)e selecione a guia **Hosts.** Seus hosts são listados por seus nomes de domínio totalmente qualificados. O status operacional de cada host é mostrado por um indicador de integridade colorido:

| Color | Descrição |
| --- | --- |
| Vermelho | Pelo menos um componente mestre no host está inoperante. Passe o mouse para ver uma dica de ferramenta que lista os componentes afetados. |
| Laranja | Pelo menos um componente secundário no host está desligado. Passe o mouse para ver uma dica de ferramenta que lista os componentes afetados. |
| Amarelo | O Ambari Server não recebe batimentos cardíacos do host há mais de 3 minutos. |
| Verde | Estado de execução normal. |

Você também verá colunas mostrando o número de núcleos e a quantidade de RAM para cada host, bem como o uso do disco e a carga média.

![Apache Ambari hospeda visão geral da guia](./media/hdinsight-key-scenarios-to-monitor/apache-ambari-hosts-tab.png)

Selecione qualquer um dos nomes de host para ter uma visão detalhada dos componentes em execução nesse host, bem como suas métricas. As métricas são mostradas como uma linha do tempo selecionável de uso de CPU, carga, uso do disco, uso de memória, uso de rede e números de processos.

![Chave de detalhes do host Apache Ambari](./media/hdinsight-key-scenarios-to-monitor/apache-ambari-host-details.png)

Consulte [Gerenciar clusters HDInsight usando a interface do usuário da Web do Apache Ambari](hdinsight-hadoop-manage-ambari.md) para obter detalhes sobre a configuração de alertas e exibição de métricas.

## <a name="yarn-queue-configuration"></a>Configuração de fila do YARN

O Hadoop tem vários serviços em execução em sua plataforma distribuída. YARN (Yet Another Resource Negotiator) coordena esses serviços e aloca recursos de cluster para garantir que qualquer carga seja distribuída uniformemente pelo cluster.

O YARN divide as duas responsabilidades do JobTracker, o gerenciamento de recursos e o agendamento / monitoramento de tarefas em dois daemons: um Gerenciador de Recursos global e um ApplicationMaster (AM) por aplicativo.

O Resource Manager é um *planejador puro* e arbitra unicamente os recursos disponíveis entre todos os aplicativos concorrentes. O Gerenciador de Recursos garante que todos os recursos estejam sempre em uso, otimizando para várias constantes, como SLAs, garantias de capacidade e assim por diante. O ApplicationMaster negocia recursos do Gerenciador de Recursos e trabalha com o NodeManager para executar e monitorar os contêineres e seu consumo de recursos.

Quando vários inquilinos compartilham um grande cluster, há competição pelos recursos do cluster. O CapacityScheduler é um agendador conectável que auxilia no compartilhamento de recurso através do enfileiramento de solicitações. O CapacityScheduler também suporta *filas hierárquicas* para garantir que os recursos sejam compartilhados entre as subfilas de uma organização, antes que as filas de outros aplicativos sejam permitidas a usar recursos gratuitos.

O YARN nos permite alocar recursos para essas filas e mostra se todos os recursos disponíveis estão atribuídos. Para visualizar informações sobre suas filas, entre na interface do usuário do Ambari Web e selecione **YARN Queue Manager** no menu superior.

![Gerente de fila do Apache Ambari YARN](./media/hdinsight-key-scenarios-to-monitor/apache-yarn-queue-manager.png)

A página do Gerenciador de Filas do YARN mostra uma lista de suas filas à esquerda, junto com o percentual da capacidade atribuída a cada uma.

![Página de detalhes do Gerenciador de Filas do YARN](./media/hdinsight-key-scenarios-to-monitor/yarn-queue-manager-details.png)

Para obter informações mais detalhada sobre seu filas, no painel do Ambari, selecione o serviço **YARN** na lista à esquerda. Em seguida, no menu suspenso **Links rápidos**, selecione **Interface do usuário do Gerenciador de Recursos** abaixo do nó ativo.

![Links de menu de ida de ia do Gerenciador de recursos](./media/hdinsight-key-scenarios-to-monitor/resource-manager-ui-menu-link.png)

Na interface do usuário do Gerenciador de Recursos, selecione **Agendador** no menu à esquerda. Você vê uma lista com as suas filas em *Filas de Aplicativo*. Aqui você pode ver a capacidade usada para cada uma das suas filas, como os trabalhos estão distribuídos entre elas e se algum trabalho está com limitação de recursos.

![Menu de II do Apache HAdoop Resource Manager](./media/hdinsight-key-scenarios-to-monitor/resource-manager-ui-menu.png)

## <a name="storage-throttling"></a>Limitação de armazenamento

O gargalo do desempenho de um cluster pode ocorrer no nível de armazenamento. Esse tipo de gargalo é mais frequentemente devido ao *bloqueio* de operações de entrada/saída (IO), que acontecem quando suas tarefas em execução enviam mais IO do que o serviço de armazenamento pode lidar. Esse bloqueio cria uma fila de solicitações de E/S aguardando para serem processadas até que as E/Ss atuais sejam processadas. Os bloqueios são por causa do estrangulamento do *armazenamento,* que não é um limite físico, mas sim um limite imposto pelo serviço de armazenamento por um contrato de nível de serviço (SLA). Esse limite serve para garantir que um único cliente ou locatário não monopolize o serviço. O SLA limita o número de IOs por segundo (IOPS) para o Armazenamento Azure - para [detalhes,](../storage/common/scalability-targets-standard-account.md)consulte metas de escalabilidade e desempenho para contas de armazenamento padrão .

Se você estiver usando o Azure Storage, para obter informações sobre o monitoramento de problemas relacionados ao armazenamento, incluindo estrangulamento, consulte [Monitor, diagnosticar e solucionar problemas do Microsoft Azure Storage](https://docs.microsoft.com/azure/storage/storage-monitoring-diagnosing-troubleshooting).

Se o armazenamento de backup do seu cluster for o ADLS (ADLS), o seu estrangulamento é provavelmente devido aos limites de largura de banda. A limitação, nesse caso, poderia ser identificada pela observação de erros de limitação nos logs de tarefa. Para o ADLS, consulte a seção sobre limitação do serviço apropriado nesses artigos:

* [Diretrizes de ajuste do desempenho para o Apache Hive no HDInsight e Azure Data Lake Storage](../data-lake-store/data-lake-store-performance-tuning-hive.md)
* [Diretrizes de ajuste do desempenho para o MapReduce no HDInsight e Azure Data Lake Storage](../data-lake-store/data-lake-store-performance-tuning-mapreduce.md)
* [Diretrizes de ajuste do desempenho para o Apache Storm no HDInsight e Azure Data Lake Storage](../data-lake-store/data-lake-store-performance-tuning-storm.md)

## <a name="troubleshoot-sluggish-node-performance"></a>Solucionando problemas desempenho lento do nó

Em alguns casos, a lentidão pode ocorrer devido ao baixo espaço em disco no cluster. Investigue com estas etapas:

1. Use [o comando ssh](./hdinsight-hadoop-linux-use-ssh-unix.md) para se conectar a cada um dos nades.

1. Verifique o uso do disco executando um dos seguintes comandos:

    ```bash
    df -h
    du -h --max-depth=1 / | sort -h
    ```

1. Revise a saída e verifique se há `mnt` presença de arquivos grandes na pasta ou em outras pastas. Normalmente, `usercache`as `appcache` pastas (mnt/resource/hadoop/yarn/local/usercache/hive/appcache/) contêm arquivos grandes.

1. Se houver arquivos grandes, ou um trabalho atual está causando o crescimento do arquivo ou um trabalho anterior fracassado pode ter contribuído para esse problema. Para verificar se esse comportamento é causado por um trabalho atual, execute o seguinte comando:

    ```bash
    sudo du -h --max-depth=1 /mnt/resource/hadoop/yarn/local/usercache/hive/appcache/
    ```

1. Se este comando indicar um trabalho específico, você pode optar por encerrar o trabalho usando um comando que se assemelha ao seguinte:

    ```bash
    yarn application -kill -applicationId <application_id>
    ```

    Substitua pelo `application_id` ID do aplicativo. Se não forem indicados trabalhos específicos, vá para a próxima etapa.

1. Depois que o comando acima for concluído, ou se nenhum trabalho específico for indicado, exclua os arquivos grandes que você identificou executando um comando que se assemelha ao seguinte:

    ```bash
    rm -rf filecache usercache
    ```

Para obter mais informações sobre problemas de espaço em disco, consulte [Out of disk space](./hadoop/hdinsight-troubleshoot-out-disk-space.md).

> [!NOTE]  
> Se você tem arquivos grandes que deseja manter, mas estão contribuindo para o problema do espaço em disco baixo, você tem que escalar seu cluster HDInsight e reiniciar seus serviços. Depois de concluir este procedimento e esperar por alguns minutos, você notará que o armazenamento está liberado e o desempenho habitual do nó é restaurado.

## <a name="next-steps"></a>Próximas etapas

Visite os links a seguir para obter mais informações sobre o monitoramento e a solução de problemas dos seus clusters:

* [Analisar logs do HDInsight](hdinsight-debug-jobs.md)
* [Depurar aplicativos com logs do YARN do Apache Hadoop](hdinsight-hadoop-access-yarn-app-logs-linux.md)
* [Habilitar despejos de heap para serviços do Apache Hadoop no HDInsight baseado em Linux](hdinsight-hadoop-collect-debug-heap-dump-linux.md)
