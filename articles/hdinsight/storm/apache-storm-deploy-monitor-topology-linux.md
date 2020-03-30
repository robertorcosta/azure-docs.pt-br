---
title: Implantar e gerenciar topologias Apache Storm no Microsoft Azure HDInsight
description: Aprenda a implantar, monitorar e gerenciar topologias do Apache Storm usando o Storm Dashboard no HDInsight baseado em Linux. Use as ferramentas do Hadoop para Visual Studio.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/18/2019
ms.openlocfilehash: e890289230b3215bd102d8c5a78dca4f1b7b90f8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79271896"
---
# <a name="deploy-and-manage-apache-storm-topologies-on-azure-hdinsight"></a>Implantar e gerenciar topologias Apache Storm no Microsoft Azure HDInsight

Neste documento, conheça as noções básicas de gerenciamento e monitoramento de topologias [Apache Storm](https://storm.apache.org/) em execução no Storm em clusters HDInsight.

## <a name="prerequisites"></a>Pré-requisitos

* Um cluster do Apache Storm no HDInsight. Confira [Criar clusters Apache Hadoop usando o portal do Azure](../hdinsight-hadoop-create-linux-clusters-portal.md) e selecione **Storm** como **Tipo de cluster**.

* (Opcional) Familiaridade com O Shell Seguro (SSH) e Cópia Segura (SCP). Para saber mais, confira [Conectar-se ao HDInsight (Apache Hadoop) usando SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

* (Opcional) Visual Studio, Azure SDK 2.5.1 ou mais novo, e o Data Lake Tools for Visual Studio. Para obter mais informações, consulte [Apache Hadoop & Visual Studio Data Lake Tools](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).

## <a name="submit-a-topology-using-visual-studio"></a>Envie uma topologia usando o Visual Studio

Você pode usar as Ferramentas do Lago de Dados para o Visual Studio para enviar topologias C# ou híbridas para o seu cluster Storm. As etapas a seguir usam um aplicativo de exemplo. Para obter informações sobre a criação de topologia usando as Ferramentas do Lago de Dados, consulte [as topologias apache storm com Visual Studio e C#](apache-storm-develop-csharp-visual-studio-topology.md).

1. Se você ainda não instalou a versão mais recente das ferramentas Data Lake para o Visual Studio, consulte [Use Data Lake Tools for Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).

    > [!NOTE]  
    > As ferramentas Azure Data Lake e Stream Analytics foram anteriormente chamadas de HDInsight Tools for Visual Studio.
    >
    > Azure Data Lake e Stream Analytics Tools for Visual Studio estão incluídos na carga de trabalho de desenvolvimento do **Azure** para o Visual Studio 2019.

1. Inicie o Visual Studio.

1. Na janela **Iniciar**, selecione **Criar projeto**.

1. Na **Criação de uma nova** janela de projeto, selecione a caixa de pesquisa e digite `Storm`. Em seguida, escolha **A amostra** de tempestade na lista de resultados e selecione **Next**.

1. Na **Configure sua nova** janela de projeto, digite um **nome do Projeto**e vá ou crie um **Local** para salvar o novo projeto. Em seguida, **selecione Criar**.

    ![Configure sua nova janela de projeto, Visual Studio](./media/apache-storm-deploy-monitor-topology-linux/apache-storm-sample1.png)

1. No **Server Explorer**, clique com o botão direito do mouse no **Azure** e selecione **Conecte-se à Assinatura Do Microsoft Azure...** e complete o processo de login.

1. No **Solution Explorer**, clique com o botão direito do mouse no projeto e escolha Enviar para Tempestade no **HDInsight**.

    > [!NOTE]  
    > Se solicitado, insira as credenciais de logon para sua assinatura do Azure. Se você tiver mais de uma assinatura, entre naquela que contém seu cluster Storm no HDInsight.

1. Na caixa de diálogo **Enviar topologia,** na lista de paradas do **Storm Cluster,** escolha o cluster Tempestade no cluster HDInsight e, em seguida, **selecione Enviar**. Você pode monitorar se o envio é bem-sucedido visualizando o painel **Saída.**

## <a name="submit-a-topology-using-ssh-and-the-storm-command"></a>Envie uma topologia usando SSH e o comando Tempestade

1. Use [o comando ssh](../hdinsight-hadoop-linux-use-ssh-unix.md) para se conectar ao seu cluster. Edite o comando abaixo substituindo CLUSTERNAME pelo nome do seu cluster e, em seguida, digite o comando:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. A partir da sessão ssh, use o seguinte comando para iniciar a topologia do exemplo **do WordCount:**

    ```bash
    storm jar /usr/hdp/current/storm-client/contrib/storm-starter/storm-starter-topologies-*.jar org.apache.storm.starter.WordCountTopology WordCount
    ```

    Esse comando inicia a topologia de WordCount de exemplo no cluster. Essa topologia gera frases aleatoriamente e conta a ocorrência de cada palavra nas frases.

    > [!NOTE]  
    > Ao enviar topologia para o cluster, você deve primeiro copiar o `storm` arquivo .jar contendo o cluster antes de usar o comando. Para copiar o arquivo para o cluster, é possível usar o comando `scp`. Por exemplo, insira `scp FILENAME.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:FILENAME.jar`.
    >
    > O exemplo *do WordCount* e outros exemplos iniciais de `/usr/hdp/current/storm-client/contrib/storm-starter/`tempestade já estão incluídos no seu cluster em .

## <a name="submit-a-topology-programmatically"></a>Enviar uma topologia programáticamente

É possível implantar programaticamente uma topologia usando o serviço Nimbus. [https://github.com/Azure-Samples/hdinsight-java-deploy-storm-topology](https://github.com/Azure-Samples/hdinsight-java-deploy-storm-topology)fornece um aplicativo Java exemplo que demonstra como implantar e iniciar uma topologia através do serviço Nimbus.

## <a name="monitor-and-manage-a-topology-in-visual-studio"></a>Monitore e gerencie uma topologia no Visual Studio

Quando você envia uma topologia usando o Visual Studio, a janela **Storm Topologies View** é exibida. Selecione a topologia da lista para exibir informações sobre a topologia em execução.

![Topologia do monitor, prioridades de tempestade saem da janela, Visual Studio](./media/apache-storm-deploy-monitor-topology-linux/visual-studio-monitor.png)

> [!NOTE]  
> Você também pode exibir **topologias Storm** do **Gerenciador de Servidores**. Expanda **o Azure** > **HDInsight,** clique com o botão direito do mouse em uma tempestade no cluster HDInsight e, em seguida, selecione **Exibir Topoologias de Tempestade**.

Escolha a forma dos spouts ou bolts para exibir informações sobre esses componentes. Uma dica de ferramenta com informações do componente é exibida para o item selecionado.

### <a name="deactivate-and-reactivate-a-topology"></a>Desativar e reativar uma topologia

Desativar uma topologia pausa-a até que a topologia seja morta ou reativada. Para fazer essas operações, use os botões **Desativação** e **Reativação** na área **Ações** na parte superior da janela Exibição de **Topoologias** de Tempestade.

### <a name="rebalance-a-topology"></a>Reequilibrar uma topologia

Rebalancear uma topologia permite que o sistema revise o paralelismo da topologia. Por exemplo, se você redimensionou o cluster para adicionar mais notas, o reequilíbrio permite que uma topologia veja os novos nós.

Para reequilibrar uma topologia, use o botão **Reequilibrar** na área **Ações** da janela **Exibição de Topoologias** de Tempestade.

> [!WARNING]  
> Reequilibrar uma topologia desativa a topologia, redistribui os trabalhadores uniformemente através do cluster e, em seguida, devolve a topologia ao estado em que estava antes do reequilíbrio ocorrer. Se a topologia estava ativa, ela se torna ativa novamente. Se a topologia foi desativada, ela permanece desativada.

### <a name="kill-a-running-topology"></a>Eliminar uma topologia em execução

Topoologias de tempestade continuam funcionando até que sejam paradas ou o cluster seja excluído. Para parar uma topologia, use o botão **Matar** na área **Ações.**

## <a name="monitor-and-manage-a-topology-using-ssh-and-the-storm-command"></a>Monitore e gerencie uma topologia usando SSH e o comando Tempestade

O `storm` utilitário permite que você trabalhe com as topologias de execução na linha de comando. Use `storm -h` para uma lista completa de comandos.

### <a name="list-topologies"></a>Topologias de lista

Use o comando a seguir para listar todas as topologias em execução:

```shell
storm list
```

Esse comando retorna informações semelhantes ao seguinte texto:

```shell
Topology_name        Status     Num_tasks  Num_workers  Uptime_secs
-------------------------------------------------------------------
WordCount            ACTIVE     29         2            263
```

### <a name="deactivate-and-reactivate-a-topology"></a>Desativar e reativar uma topologia

Desativar uma topologia pausa-a até que a topologia seja morta ou reativada. Use os seguintes comandos para desativar ou reativar:

```shell
storm Deactivate TOPOLOGYNAME
```

```shell
storm Activate TOPOLOGYNAME
```

### <a name="kill-a-running-topology"></a>Eliminar uma topologia em execução

As topologias Storm, depois de iniciadas, continuarão em execução até serem interrompidas. Para interrompê-la, use o comando a seguir:

```shell
storm kill TOPOLOGYNAME
```

### <a name="rebalance-a-topology"></a>Reequilibrar uma topologia

Rebalancear uma topologia permite que o sistema revise o paralelismo da topologia. Por exemplo, se você redimensionou o cluster para adicionar mais notas, o reequilíbrio permite que uma topologia veja os novos nós.

> [!WARNING]  
> Reequilibrar uma topologia desativa a topologia, redistribui os trabalhadores uniformemente através do cluster e, em seguida, devolve a topologia ao estado em que estava antes do reequilíbrio ocorrer. Se a topologia estava ativa, ela se torna ativa novamente. Se ela foi desativada, ela permanecerá desativada.

```shell
storm rebalance TOPOLOGYNAME
```

## <a name="monitor-and-manage-a-topology-using-the-storm-ui"></a>Monitore e gerencie uma topologia usando a UI tempestade

A Interface do Tempestade fornece uma interface web para trabalhar com topologias em execução, e está incluída no cluster HDInsight. Para exibir a interface do Storm, use um navegador da Web para abrir `https://CLUSTERNAME.azurehdinsight.net/stormui`, onde *CLUSTERNAME* é o nome do cluster.

> [!NOTE]  
> Se você for solicitado a fornecer um nome de usuário e senha, digite o nome de usuário e a senha do administrador de cluster que você usou ao criar o cluster.

### <a name="storm-ui-main-page"></a>Página principal da Storm UI

A página principal da interface do usuário do Storm fornece as seguintes informações:

| Seção | Descrição |
| --- | --- |
| Resumo do cluster| Informações básicas sobre o cluster do Storm. |
| Resumo de Nimbus | Uma lista de informações básicas do Nimbus. |
| Resumo da topologia | Uma lista das topologias em execução. Para exibir mais informações sobre uma topologia específica, selecione seu link na coluna **Nome.** |
| Resumo do supervisor | Informações sobre o supervisor do Storm. Para ver os recursos do trabalhador associados a um supervisor específico, selecione seu link na coluna **Host** ou **Id.** |
| Configuração nimbus | Configuração do Nimbus para o cluster. |

A página principal da Storm UI é semelhante a esta página:

![Página principal, Storm UI, Topoologias da Tempestade Apache, Azure](./media/apache-storm-deploy-monitor-topology-linux/apache-storm-web-ui-main-page.png)

#### <a name="topology-summary"></a>Resumo da topologia

Selecionar um link na seção **Resumo da topologia** exibirá as seguintes informações sobre a topologia:

| Seção | Descrição |
| --- | --- |
| Resumo da topologia | Informações básicas sobre a topologia. |
| Ações de topologia| Ações de gestão que você pode fazer para a topologia. As ações disponíveis são descritas posteriormente nesta seção. |
| Estatísticas de topologia | Estatísticas sobre a topologia. Para definir o período de tempo para uma entrada nesta seção, selecione seu link na coluna **Janela.** |
| Bicos *(período de tempo)* | Os spouts usados pela topologia. Para exibir mais informações sobre um bico específico, selecione seu link na coluna **Id.** |
| Parafusos *(período de tempo)* | Os bolts usados pela topologia. Para visualizar mais informações sobre um parafuso específico, selecione seu link na coluna **Id.** |
| Recursos do trabalhador | Uma lista de recursos dos trabalhadores. Para exibir mais informações sobre um recurso específico do trabalhador, selecione seu link na coluna **Host.** |
| Visualização de topologia | Um botão **Mostrar visualização** que exibe uma visualização da topologia. |
| Configuração de topologia | A configuração da topologia selecionada. |

A página de resumo da topologia do Storm é semelhante a esta página da Web:

![Página de resumo de Topologia, Storm UI, Apache Storm, Azure](./media/apache-storm-deploy-monitor-topology-linux/apache-storm-web-ui-topology-summary.png)

Na seção **ações de Topologia,** você pode selecionar os seguintes botões para fazer uma ação:

| Botão | Descrição |
| --- | --- |
| Ativar | Retoma o processamento de uma topologia desativada. |
| Desativar | Pausa uma topologia em execução. |
| Rebalanceamento | Ajusta o paralelismo da topologia. Você deve reequilibrar topoologias em execução depois de alterar o número de nódulos no cluster. Esta operação permite que a topologia ajuste o paralelismo para compensar o número adicional ou reduzido de nódulos no cluster.<br/><br/>Para saber mais, consulte <a href="https://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html" target="_blank">Noções básicas sobre o paralelismo de uma topologia do Apache Storm</a>.
| Encerrar | Termina uma topologia do Storm após o tempo limite especificado. |
| Depurar | Começa uma sessão de depuração para a topologia em execução. |
| Parar de bug | Termina a sessão de depuração da topologia em execução. |
| Nível de log de alteração | Modifica o nível de registro de depuração. |

##### <a name="spout-and-bolt-summary"></a>Resumo de bico e parafuso

Selecionar um spout nas seções **Spouts** ou **Bolts** exibirá as seguintes informações sobre o item selecionado:

| Seção | Descrição |
| --- | --- |
| Resumo do componente | Informações básicas sobre o spout ou o bolt. |
| Ações componentes | **Botões Debug** e **Stop Debug.** |
| Estatísticas de bico ou estatísticas de parafusos | Estatísticas sobre o spout ou o bolt. Para definir o período de tempo para uma entrada nesta seção, selecione seu link na coluna **Janela.** |
| (Somente parafuso)<br/>Estatísticas de entrada *(período de tempo)* | Informações sobre os fluxos de entrada consumidos pelo bolt. |
| Estatísticas de saída *(período de tempo)* | Informações sobre os fluxos emitidos pelo spout ou pelo bolt. |
| Criação e depuração de perfil | Controles para traçar perfis e depurar os componentes nesta página. Você pode definir o valor **status / timeout (Minutos)** e você pode selecionar botões para **JStack**, **Restart Worker**e **Heap**. |
| Executores *(período de tempo)* | Informações sobre as instâncias do spout ou bolt. Para exibir um registro de informações de diagnóstico produzidas para este caso, selecione a entrada **Porta** para um executor específico. Você também pode ver os recursos do trabalhador associados a um executor específico selecionando seu link na coluna **Host.** |
| Errors | Qualquer informação de erro para o spout ou bolt. |

A página de resumo do parafuso tempestade é semelhante a esta página da Web:

![Página de resumo bolt, Storm UI, Apache Storm, Azure](./media/apache-storm-deploy-monitor-topology-linux/apache-storm-web-ui-bolt-summary.png)

## <a name="monitor-and-manage-the-topology-using-the-rest-api"></a>Monitore e gerencie a topologia usando a API REST

A ID tempestade é construída em cima da API REST, para que você possa fazer tarefas semelhantes de gerenciamento e monitoramento usando a API REST. Você pode usar a API REST para criar ferramentas personalizadas para o gerenciamento e o monitoramento de topologias Storm.

Para obter mais informações, veja [API REST da interface do usuário do Apache Storm](https://storm.apache.org/releases/current/STORM-UI-REST-API.html). As informações a seguir são específicas para o uso da API REST com Apache Storm no HDInsight.

> [!IMPORTANT]  
> A API Storm REST não está disponível publicamente pela internet. Ele deve ser acessado usando um túnel SSH para o nó de cabeça do cluster HDInsight. Para obter informações sobre como criar e usar um túnel SSH, consulte [Use o túnel SSH para acessar o Azure HDInsight](../hdinsight-linux-ambari-ssh-tunnel.md).

### <a name="base-uri"></a>URI de base

O URI base da API REST em clusters HDInsight `https://HEADNODEFQDN:8744/api/v1/`baseados em Linux está disponível no endereço URL, onde você substitui *o HEADNODEFQDN* pelo nó principal. O nome de domínio do nó de cabeça é gerado durante a criação do cluster e não é estático.

Você pode encontrar o nome de domínio totalmente qualificado (FQDN) para o nó de cabeça do cluster de várias maneiras:

| Método de descoberta do FQDN | Descrição |
| --- | --- |
| Sessão SSH | Use o comando `headnode -f` de uma sessão SSH para o cluster. |
| Ambari Web | Na página da Web do`https://CLUSTERNAME.azurehdinsight.net`cluster Ambari (), selecione **Serviços** na parte superior da página e selecione **Tempestade**. Na guia **Resumo** selecione **Servidor de IU do Storm**. O FQDN do nó que hospeda a interface do usuário do Storm e a API REST são exibidos na parte superior da página. |
| API REST do Ambari | Use o comando `curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/STORM/components/STORM_UI_SERVER"` para recuperar informações sobre o nó em que a interface do usuário do Storm e a API REST estão em execução. Substitua as duas instâncias de *CLUSTERNAME* pelo nome do cluster. Quando for solicitado, digite a senha da conta do usuário (admin). Na resposta, a entrada "host_name" da saída JSON contém o FQDN do nó. |

### <a name="authentication"></a>Autenticação

As solicitações para a API REST devem usar *autenticação básica,* então você tem que usar o nome do administrador e a senha para o cluster HDInsight.

> [!NOTE]  
> Como a autenticação básica é enviada usando texto não criptografado, você *sempre* deverá usar HTTPS para proteger as comunicações com o cluster.

### <a name="return-values"></a>Valores retornados

As informações retornadas pela API REST só podem ser usadas dentro do cluster. Por exemplo, o nome de domínio totalmente qualificado (FQDN) retornado para servidores [Apache ZooKeeper](https://zookeeper.apache.org/) não está acessível a partir da internet.

## <a name="next-steps"></a>Próximas etapas

A seguir, aprenda a [Desenvolver topologias baseadas em Java usando o Apache Maven](apache-storm-develop-java-topology.md).

Para obter uma lista de topologias mais exemplos, consulte [Exemplo de Topologias apache storm no Azure HDInsight](apache-storm-example-topology.md).
