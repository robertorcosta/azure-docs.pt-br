---
title: Solucionar problemas de ações de script no Azure HDInsight
description: Etapas gerais de solução de problemas para ações de script no Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.custom: seoapr2020
ms.date: 04/21/2020
ms.openlocfilehash: e2a2f6abfd6b7c644e95649f3c9832e4cc986037
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82188439"
---
# <a name="troubleshoot-script-actions-in-azure-hdinsight"></a>Solucionar problemas de ações de script no Azure HDInsight

Este artigo descreve as etapas de solução de problemas e as possíveis resoluções para problemas ao interagir com clusters do Azure HDInsight.

## <a name="viewing-logs"></a>Exibição de logs

Você pode usar a interface do usuário da Web do Apache Ambari para exibir informações registradas por ações de script. Se o script falhar durante a criação do cluster, os logs estarão na conta de armazenamento de cluster padrão. Esta seção fornece informações sobre como recuperar os logs usando ambas as opções.

### <a name="apache-ambari-web-ui"></a>Interface do usuário da Web do Apache Ambari

1. Em um navegador da Web, navegue `https://CLUSTERNAME.azurehdinsight.net`até, `CLUSTERNAME` em que é o nome do cluster.

1. Na barra na parte superior da página, selecione a entrada **ops**. Uma lista exibe as operações atuais e anteriores realizadas no cluster por meio do Ambari.

    ![Barra de interface do usuário da Web do Ambari com o item "ops" selecionado](./media/troubleshoot-script-action/hdi-apache-ambari-nav.png)

1. Localize as entradas com **run\_customscriptaction** na coluna **Operações**. Essas entradas são criadas quando as ações de script são executadas.

    ![Operações de ação de script do Apache Ambari](./media/troubleshoot-script-action/ambari-script-action.png)

    Para exibir as saídas **STDOUT** e **STDERR**, selecione a entrada **run\customscriptaction** e faça drill down pelos links. Essa saída é gerada ao executar o script e pode conter informações úteis.

### <a name="default-storage-account"></a>Conta de armazenamento padrão

Se a criação do cluster falhar devido a um erro de script, os logs serão mantidos na conta de armazenamento de cluster.

* Os logs de armazenamento estão disponíveis em `\STORAGE_ACCOUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\CLUSTER_NAME\DATE`.

    ![Logs de ação de script](./media/troubleshoot-script-action/script-action-logs-in-storage.png)

    Nesse diretório, os logs são organizados separadamente em **nó de cabeçalho**, **nó de trabalho** e **nó do zookeeper**. Veja os exemplos a seguir:

    * **Cabeçalho**:`<ACTIVE-HEADNODE-NAME>.cloudapp.net`

    * **Nó de trabalho**:`<ACTIVE-WORKERNODE-NAME>.cloudapp.net`

    * **Nó Zookeeper**:`<ACTIVE-ZOOKEEPERNODE-NAME>.cloudapp.net`

* Todos os **stdout** e **stderr** do host correspondente são carregados na conta de armazenamento. Há um **output-\*.txt** e um **errors-\*.txt** para cada ação de script. O arquivo **output-*.txt** contém informações sobre o URI do script que foi executado no host. O texto a seguir é um exemplo destas informações:

        'Start downloading script locally: ', u'https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh'

* É possível criar repetidamente um cluster de ação de script com o mesmo nome. Nesse caso, você pode distinguir os logs relevantes com base no nome da pasta **DATE**. Por exemplo, a estrutura de pastas de um cluster, **mycluster**, criado em datas diferentes será semelhante às seguintes entradas de log:

    `\STORAGE_ACCOUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\mycluster\2015-10-04` `\STORAGE_ACCOUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\mycluster\2015-10-05`

* Se você criar um cluster de ação de script com o mesmo nome no mesmo dia, você poderá usar o prefixo exclusivo para identificar os arquivos de log relevantes.

* Se você criar um cluster perto de 00h00, meia-noite, é possível que os arquivos de log incluam dois dias. Nesses casos, você verá duas pastas com datas diferentes para o mesmo cluster.

* Carregar arquivos de log no contêiner padrão pode levar até cinco minutos, especialmente para clusters grandes. Portanto, caso queira acessar os logs, você não deve excluir imediatamente o cluster se uma ação de script falhar.

## <a name="ambari-watchdog"></a>Ambari Watchdog

Não altere a senha do watchdog Ambari, hdinsightwatchdog, no seu cluster do HDInsight baseado em Linux. Uma alteração de senha interrompe a capacidade de executar novas ações de script no cluster HDInsight.

## <a name="cant-import-name-blobservice"></a>Não é possível importar o BlobService de nome

__Sintomas__. Falha ao executar a ação de script. Um texto semelhante ao seguinte erro é exibido quando você visualiza a operação no Ambari:

```
Traceback (most recent call list):
  File "/var/lib/ambari-agent/cache/custom_actions/scripts/run_customscriptaction.py", line 21, in <module>
    from azure.storage.blob import BlobService
ImportError: cannot import name BlobService
```

__Causa__. Esse erro ocorre se você atualizar o cliente de Armazenamento do Microsoft Azure do Python incluído com o cluster do HDInsight. O HDInsight espera o cliente de Armazenamento do Azure 0.20.0.

__Resolução__. Para resolver esse erro, conecte-se manualmente a cada nó de cluster usando `ssh`. Execute o seguinte comando para reinstalar a versão de cliente de armazenamento correta:

```bash
sudo pip install azure-storage==0.20.0
```

Para saber mais sobre como se conectar ao cluster com SSH, confira [Conectar ao HDInsight (Apache Hadoop) usando SSH](hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="history-doesnt-show-the-scripts-used-during-cluster-creation"></a>O histórico não mostra os scripts usados durante a criação do cluster

Se o cluster tiver sido criado antes de 15 de março de 2016, talvez você não veja uma entrada no histórico de ação de script. Redimensionar o cluster faz com que os scripts apareçam em um histórico de ação de script.

Há duas exceções:

* O cluster foi criado antes de 1º de setembro de 2015. Esta data é quando as ações de script foram introduzidas. Qualquer cluster criado antes dessa data não poderia ter usado as ações de script para a criação do cluster.

* Você usou várias ações de script durante a criação do cluster. Ou usou o mesmo nome para vários scripts, ou o mesmo nome e o mesmo URI, mas parâmetros diferentes para vários scripts. Nesses casos, você verá o seguinte erro:

    ```
    No new script actions can be run on this cluster because of conflicting script names in existing scripts. Script names provided at cluster creation must be all unique. Existing scripts are run on resize.
    ```

## <a name="next-steps"></a>Próximas etapas

Se você não encontrou seu problema ou não conseguiu resolver seu problema, visite um dos seguintes canais para obter mais suporte:

* Obtenha respostas de especialistas do Azure por meio do [suporte da Comunidade do Azure](https://azure.microsoft.com/support/community/).

* Conecte- [@AzureSupport](https://twitter.com/azuresupport) se com a conta de Microsoft Azure oficial para melhorar a experiência do cliente. Conectando a Comunidade do Azure aos recursos certos: respostas, suporte e especialistas.

* Se precisar de mais ajuda, você poderá enviar uma solicitação de suporte do [portal do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selecione **suporte** na barra de menus ou abra o Hub **ajuda + suporte** . Para obter informações mais detalhadas, consulte [como criar uma solicitação de suporte do Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). O acesso ao gerenciamento de assinaturas e ao suporte de cobrança está incluído na sua assinatura do Microsoft Azure, e o suporte técnico é fornecido por meio de um dos [planos de suporte do Azure](https://azure.microsoft.com/support/plans/).