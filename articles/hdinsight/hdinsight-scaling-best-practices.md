---
title: Dimensionar tamanhos de cluster – Azure HDInsight
description: Dimensione um cluster Apache Hadoop de forma elelástica para corresponder à sua carga de trabalho no Azure HDInsight
author: ashishthaps
ms.author: ashish
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/26/2020
ms.openlocfilehash: 96a72541255ad0059abe5ad280f1728518dbf68c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80234743"
---
# <a name="scale-azure-hdinsight-clusters"></a>Scale Azure HDInsight clusters

O HDInsight proporciona elasticidade, oferecendo a opção de escalar e reduzir verticalmente o número de nós de trabalho em seus clusters. Essa elasticidade permite que você reduza um cluster após horas ou nos fins de semana, e expanda-o durante o pico de demandas de negócios.

Se você tiver processamento periódico em lote, o cluster HDInsight pode ser dimensionado alguns minutos antes dessa operação, para que seu cluster tenha memória e energia adequadas da CPU. Posteriormente, depois que o processamento for concluído e uso reduzir novamente, você poderá reduzir verticalmente o cluster HDInsight, obtendo menos nós de trabalho.

Você pode dimensionar um cluster manualmente usando um dos métodos descritos abaixo ou usar opções [de autoescala](hdinsight-autoscale-clusters.md) para que o sistema seja escalado automaticamente para cima e para baixo em resposta à CPU, memória e outras métricas.

> [!NOTE]  
> Somente clusters HDInsight versão 3.1.3 ou superior são compatíveis. Se não tiver certeza quanto à versão de seu cluster, você poderá verificar a página Propriedades.

## <a name="utilities-to-scale-clusters"></a>Utilitários para dimensionar clusters

A Microsoft fornece os seguintes utilitários para dimensionar clusters:

|Utilitário | Descrição|
|---|---|
|[PowerShell Az](https://docs.microsoft.com/powershell/azure)|[Set-AzHDInsightClusterSize](https://docs.microsoft.com/powershell/module/az.hdinsight/set-azhdinsightclustersize) -ClusterName \<Nome do cluster \<Nome de> -TargetInstanceCount NewSize>|
|[AzureRM do PowerShell](https://docs.microsoft.com/powershell/azure/azurerm) |[Set-AzureRMHDClusterCluster](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/set-azurermhdinsightclustersize) -ClusterName \<Name> -TargetInstanceCount \<NewSize>|
|[Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)| [az hdinsight redimensionar](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-resize) --grupo \<de recursos \<Grupo de recursos Grupo de \<recursos> --nome nome Nome do> --workernode-count NewSize>|
|[Cli Clássico do Azure](hdinsight-administer-use-command-line.md)|cluster azure hdinsight \<redimensiona clusterNome> \<Contagem de ocorrência de destino> |
|[Portal Azure](https://portal.azure.com)|Abra o painel de cluster DO HDInsight, selecione **tamanho de cluster** no menu à esquerda e, em seguida, no painel tamanho do cluster, digite o número de nomes do trabalhador e selecione Salvar.|  

![Opção de cluster de escala do portal Azure](./media/hdinsight-scaling-best-practices/azure-portal-settings-nodes.png)

Usando qualquer um desses métodos, você pode aumentar ou reduzir verticalmente seu cluster HDInsight em apenas alguns minutos.

> [!IMPORTANT]  
> * O Azure classic CLI é preterido e só deve ser usado com o modelo clássico de implantação. Para todas as outras implantações, use o [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest).  
> * O módulo PowerShell AzureRM é preterido.  Por favor, use [o módulo Az](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-1.4.0) sempre que possível.

## <a name="impact-of-scaling-operations"></a>Impacto das operações de dimensionamento

Quando você **adiciona** números ao cluster HDInsight em execução (scale up), quaisquer trabalhos pendentes ou em execução não serão afetados. Novos trabalhos podem ser enviados com segurança enquanto o processo de colocação em escala está em execução. Se a operação de dimensionamento falhar por qualquer motivo, a falha será tratada para deixar seu cluster em um estado funcional.

Se você **remover** os números (scale down), quaisquer trabalhos pendentes ou em execução falharão quando a operação de dimensionamento for concluída. Essa falha deve-se à retomada de alguns dos serviços durante o processo de dimensionamento. Há também o risco de que o cluster possa ficar preso no modo de segurança durante uma operação manual de dimensionamento.

O impacto da alteração do número de nós de dados varia em cada tipo de cluster com suporte do HDInsight:

* Apache Hadoop

    Você pode aumentar continuamente o número de nós de trabalhador em um cluster Hadoop em execução sem afetar os trabalhos pendentes ou em execução. Novos trabalhos também podem ser enviados enquanto a operação está em andamento. Falhas em uma operação de dimensionamento são normalmente manipuladas para que o cluster sempre seja deixado em um estado funcional.

    Quando um cluster Hadoop é reduzido verticalmente pela diminuição do número de nós de dados, alguns dos serviços no cluster são reiniciados. Esse comportamento faz com que todos os trabalhos em execução e pendentes falhem após a conclusão da operação de dimensionamento. Você pode, no entanto, reenviar os trabalhos quando a operação for concluída.

* HBase no Apache

    Você pode adicionar ou remover diretamente nós do cluster HBase enquanto ele é executado. Servidores Regionais são equilibrados automaticamente em alguns minutos após o término da operação de dimensionamento. No entanto, você pode equilibrar manualmente os servidores regionais fazendo logon no nó de cabeçalho do cluster e executando os seguintes comandos em uma janela de prompt de comando:

    ```bash
    pushd %HBASE_HOME%\bin
    hbase shell
    balancer
    ```

    Para obter mais informações sobre como usar o shell do HBase, consulte [Introdução a um exemplo do Apache HBase em HDInsight](hbase/apache-hbase-tutorial-get-started-linux.md).

* Apache Storm

    Você pode adicionar ou remover nós de dados continuamente para seu cluster Strom enquanto ele é executado. Contudo, após a conclusão bem-sucedida da operação de colocação em escala, será necessário redistribuir a topologia.

    A redistribuição pode ser feita de duas maneiras:

  * Interface da Web Storm
  * Ferramenta CLI (interface de linha de comando)

    Consulte a [documentação do Apache Storm](https://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html) para obter mais detalhes.

    A IU da Web do Storm está disponível no cluster HDInsight:

    ![Redistribuir escala do Storm do HDInsight](./media/hdinsight-scaling-best-practices/hdinsight-portal-scale-cluster-storm-rebalance.png)

    Veja um exemplo do comando CLI para redistribuir a topologia do Storm:

    ```console
    ## Reconfigure the topology "mytopology" to use 5 worker processes,
    ## the spout "blue-spout" to use 3 executors, and
    ## the bolt "yellow-bolt" to use 10 executors
    $ storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10
    ```

## <a name="how-to-safely-scale-down-a-cluster"></a>Como reduzir com segurança um cluster

### <a name="scale-down-a-cluster-with-running-jobs"></a>Reduza um cluster com empregos em execução

Para evitar que seus trabalhos em execução falhem durante uma operação de redimensionação, você pode tentar três coisas:

1. Aguarde que os trabalhos se completem antes de reduzir o cluster.
1. Termine manualmente os trabalhos.
1. Reenviar os trabalhos após a conclusão da operação de escala.

Para ver uma lista de trabalhos pendentes e em execução, você pode usar a IA do **Gerenciador de Recursos**Do YARN , seguindo estas etapas:

1. No [portal Azure,](https://portal.azure.com/)selecione seu cluster.  Consulte [lista e mostrar clusters](./hdinsight-administer-use-portal-linux.md#showClusters) para obter instruções. O cluster é aberto em uma nova página do portal.
2. Na vista principal, navegue até **cluster dashboards** > **Ambari home**. Digite suas credenciais de cluster.
3. Na UI Ambari, selecione **YARN** na lista de serviços no menu à esquerda.  
4. Na página Do YARN, selecione **Links rápidos** e paire sobre o nó de cabeça ativo e selecione **IU ResourceManager**.

    ![Apache Ambari links rápidos ResourceManager UI](./media/hdinsight-scaling-best-practices/resource-manager-ui1.png)

Você pode acessar diretamente a interface do usuário do ResourceManager com `https://<HDInsightClusterName>.azurehdinsight.net/yarnui/hn/cluster`.

Você vê uma lista de trabalhos, com os respectivos estados atuais. Na captura de tela, há um trabalho atualmente em execução:

![Aplicativos da interface do usuário do ResourceManager](./media/hdinsight-scaling-best-practices/resourcemanager-ui-applications.png)

Para eliminar manualmente o aplicativo que está em execução, execute o seguinte comando no shell SSH:

```bash
yarn application -kill <application_id>
```

Por exemplo: 

```bash
yarn application -kill "application_1499348398273_0003"
```

### <a name="getting-stuck-in-safe-mode"></a>Ficar preso no modo de segurança

Quando você reduz um cluster, o HDInsight usa interfaces de gerenciamento Apache Ambari para primeiro desativar os nomes extras do trabalhador, que replicam seus blocos HDFS para outros nomes de trabalhadores on-line. Depois disso, o HDInsight reduz com segurança o cluster. O HDFS entra em modo de segurança durante a operação de dimensionamento, e deve sair assim que o dimensionamento estiver concluído. Em alguns casos, no entanto, o HDFS fica preso no modo de segurança durante uma operação de dimensionamento por causa da sub-replicação do bloco de arquivos.

Por padrão, o HDFS `dfs.replication` é configurado com uma configuração de 1, que controla quantas cópias de cada bloco de arquivo estão disponíveis. Cada cópia de um bloco de arquivos é armazenada em um nó diferente do cluster.

Quando o HDFS detecta que o número esperado de cópias de blocos não está disponível, o HDFS entra no modo de segurança e o Ambari gera alertas. Se o HDFS entrar no modo de segurança para uma operação de escala, mas não puder sair do modo de segurança porque o número necessário de nós não for detectado para replicação, o cluster pode ficar preso no modo de segurança.

### <a name="example-errors-when-safe-mode-is-turned-on"></a>Erros de exemplo quando o modo de segurança está ativado

```output
org.apache.hadoop.hdfs.server.namenode.SafeModeException: Cannot create directory /tmp/hive/hive/819c215c-6d87-4311-97c8-4f0b9d2adcf0. Name node is in safe mode.
```

```output
org.apache.http.conn.HttpHostConnectException: Connect to active-headnode-name.servername.internal.cloudapp.net:10001 [active-headnode-name.servername. internal.cloudapp.net/1.1.1.1] failed: Connection refused
```

Você pode examinar os logs de nó de nome na pasta `/var/log/hadoop/hdfs/`, próximos ao horário em que o cluster foi dimensionado, para ver quando ele entrou em modo de segurança. Os arquivos de log são nomeados `Hadoop-hdfs-namenode-<active-headnode-name>.*`.

A causa raiz dos erros anteriores é que o Hive depende de arquivos temporários do HDFS durante a execução de consultas. Quando o HDFS entra em modo de segurança, o Hive não consegue executar consultas porque não pode gravar no HDFS. Os arquivos temporários do HDFS ficam localizados na unidade local, montada para as VMs de cada nó de trabalho e replicados entre os outros nós de trabalho em, no mínimo, três réplicas.

### <a name="how-to-prevent-hdinsight-from-getting-stuck-in-safe-mode"></a>Como evitar que o HDInsight se acomode no modo de segurança

Há várias maneiras de impedir que o HDInsight permaneça no modo de segurança:

* Pare todos os trabalhos do Hive antes de reduzir verticalmente o HDInsight. Como alternativa, agende o processo de redução vertical para evitar conflito com trabalhos do Hive em execução.
* Limpe manualmente os arquivos do diretório `tmp` temporários do Hive no HDFS antes de reduzir verticalmente.
* Reduza verticalmente o HDInsight somente até o mínimo de três nós de trabalho. Evite chegar até somente um nó de trabalho.
* Execute o comando para sair do modo de segurança, se necessário.

As seções a seguir descrevem essas opções.

#### <a name="stop-all-hive-jobs"></a>Interromper todos os trabalhos do Hive

Interrompa todos os trabalhos do Hive antes de reduzir verticalmente a um nó de trabalho. Se sua carga de trabalho é agendada, execute a redução vertical após a conclusão do trabalho do Hive.

Parar os trabalhos da Colmeia antes de dimensionar, ajuda a minimizar o número de arquivos de risco na pasta tmp (se houver).

#### <a name="manually-clean-up-hives-scratch-files"></a>Limpar manualmente os arquivos temporários do Hive

Se o Hive deixou arquivos temporários na pasta, você pode limpar manualmente esses arquivos antes de reduzir verticalmente, evitando o modo de segurança.

1. Verifique qual local está sendo usado para arquivos `hive.exec.scratchdir` temporários da Colmeia olhando a propriedade de configuração. Este parâmetro é `/etc/hive/conf/hive-site.xml`definido dentro de:

    ```xml
    <property>
        <name>hive.exec.scratchdir</name>
        <value>hdfs://mycluster/tmp/hive</value>
    </property>
    ```

1. Interrompa os serviços do Hive e verifique se todas as consultas e os trabalhos foram concluídos.
2. Liste o conteúdo do diretório `hdfs://mycluster/tmp/hive/` de risco encontrado acima, para ver se ele contém algum arquivo:

    ```bash
    hadoop fs -ls -R hdfs://mycluster/tmp/hive/hive
    ```

    Aqui está um exemplo de saída quando há arquivos:

    ```output
    sshuser@scalin:~$ hadoop fs -ls -R hdfs://mycluster/tmp/hive/hive
    drwx------   - hive hdfs          0 2017-07-06 13:40 hdfs://mycluster/tmp/hive/hive/4f3f4253-e6d0-42ac-88bc-90f0ea03602c
    drwx------   - hive hdfs          0 2017-07-06 13:40 hdfs://mycluster/tmp/hive/hive/4f3f4253-e6d0-42ac-88bc-90f0ea03602c/_tmp_space.db
    -rw-r--r--   3 hive hdfs         27 2017-07-06 13:40 hdfs://mycluster/tmp/hive/hive/4f3f4253-e6d0-42ac-88bc-90f0ea03602c/inuse.info
    -rw-r--r--   3 hive hdfs          0 2017-07-06 13:40 hdfs://mycluster/tmp/hive/hive/4f3f4253-e6d0-42ac-88bc-90f0ea03602c/inuse.lck
    drwx------   - hive hdfs          0 2017-07-06 20:30 hdfs://mycluster/tmp/hive/hive/c108f1c2-453e-400f-ac3e-e3a9b0d22699
    -rw-r--r--   3 hive hdfs         26 2017-07-06 20:30 hdfs://mycluster/tmp/hive/hive/c108f1c2-453e-400f-ac3e-e3a9b0d22699/inuse.info
    ```

3. Se souber que Hive terminou de usar esses arquivos, você poderá removê-los. Verifique se o Hive não tem nenhuma consulta em execução examinando a página de interface do usuário do Yarn ResourceManager.

    Linha de comando de exemplo para remover arquivos do HDFS:

    ```bash
    hadoop fs -rm -r -skipTrash hdfs://mycluster/tmp/hive/
    ```

#### <a name="scale-hdinsight-to-three-or-more-worker-nodes"></a>Dimensione o HDInsight para três ou mais nós de trabalhadores

Se seus clusters ficam presos no modo de segurança com freqüência ao reduzir para menos de três nós de trabalhadores, e as etapas anteriores não funcionarem, então você pode evitar que seu cluster entre no modo de segurança completamente mantendo pelo menos três nós de trabalhador.

Reter três nós de trabalhadoré mais caro do que reduzir para apenas um nó de trabalhador, mas impedirá que seu cluster fique preso no modo de segurança.

### <a name="scale-hdinsight-down-to-one-worker-node"></a>Dimensione o HDInsight para um nó de trabalhador

Mesmo quando o cluster é reduzido para 1 nó, o nó 0 do trabalhador ainda sobreviverá. O nó 0 do trabalhador nunca pode ser desativado.

#### <a name="run-the-command-to-leave-safe-mode"></a>Executar o comando para sair do modo de segurança

A opção final é executar o comando de modo de segurança de saída. Se você sabe que a razão para o HDFS entrar no modo de segurança é por causa da sub-replicação do arquivo Hive, você pode executar o seguinte comando para deixar o modo de segurança:

```bash
hdfs dfsadmin -D 'fs.default.name=hdfs://mycluster/' -safemode leave
```

### <a name="scale-down-an-apache-hbase-cluster"></a>Reduza um cluster Apache HBase

Os servidores da região são automaticamente equilibrados em poucos minutos após a conclusão de uma operação de dimensionamento. Para equilibrar manualmente os servidores da região, complete as seguintes etapas:

1. Conecte-se ao cluster HDInsight usando SSH. Para obter mais informações, consulte [Use SSH com HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

2. Inicie o shell do HBase:

    ```bash
    hbase shell
    ```

3. Use o seguinte comando para balancear manualmente os servidores de região:

    ```bash
    balancer
    ```

## <a name="next-steps"></a>Próximas etapas

* [Dimensionar automaticamente os clusters do Azure HDInsight](hdinsight-autoscale-clusters.md)
* [Introdução ao Azure HDInsight](hadoop/apache-hadoop-introduction.md)
