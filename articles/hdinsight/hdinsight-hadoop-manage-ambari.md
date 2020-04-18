---
title: Monitorar e gerenciar o Azure HDInsight usando a IU da Web do Ambari
description: Aprenda a usar a UI Apache Ambari para monitorar e gerenciar clusters HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 04/16/2020
ms.openlocfilehash: 2ed3d6b1088315b580ab8ddc4f12d8d61434ec53
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/17/2020
ms.locfileid: "81606549"
---
# <a name="manage-hdinsight-clusters-by-using-the-apache-ambari-web-ui"></a>Gerenciar clusters HDInsight usando a interface do usuário da Web do Apache Ambari

[!INCLUDE [ambari-selector](../../includes/hdinsight-ambari-selector.md)]

Apache Ambari simplifica o gerenciamento e o monitoramento de um aglomerado Apache Hadoop. Essa simplificação é feita fornecendo uma Interface do Web fácil de usar e a API REST. O Ambari está incluído nos clusters HDInsight e é usado para monitorar o cluster e fazer alterações de configuração.

Neste documento, você aprenderá a usar a interface de usuário do Ambari Web com um cluster HDInsight.

## <a name="what-is-apache-ambari"></a><a id="whatis"></a>O que é o Apache Storm?

O [Apache Ambari](https://ambari.apache.org) simplifica o gerenciamento do Hadoop, fornecendo uma interface do usuário da Web fácil de usar. Você pode usar o Ambari para gerenciar e monitorar clusters do Hadoop. Os desenvolvedores podem integrar essas funcionalidades em seus aplicativos usando as [APIs REST do Ambari](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).

## <a name="connectivity"></a>Conectividade

A UI da Ambari Web está `https://CLUSTERNAME.azurehdinsight.net`disponível `CLUSTERNAME` no cluster HDInsight em , onde está o nome do seu cluster.

> [!IMPORTANT]  
> Conectar-se ao Ambari no HDInsight requer HTTPS. Quando a autenticação for solicitada, use o nome e a senha da conta do administrador que você forneceu quando o cluster foi criado. Se você não for solicitado para credenciais, verifique as configurações da rede para confirmar que não há problema de conectividade entre o cliente e os Clusters Azure HDInsight.

## <a name="ssh-tunnel-proxy"></a>Túnel SSH (proxy)

Embora o Ambari para o seu cluster esteja acessível diretamente pela Internet, alguns links da Interface do Usuário da Ambari Web (como o JobTracker) não são expostos na internet. Para acessar esses serviços, você deve criar um túnel SSH. Para obter mais informações, consulte [Usar túnel SSH com o HDInsight](hdinsight-linux-ambari-ssh-tunnel.md).

## <a name="ambari-web-ui"></a>Interface do usuário da Ambari Web

> [!WARNING]  
> Nem todos os recursos da interface do usuário da Web do Ambari têm suporte no HDInsight. Para obter mais informações, consulte a seção [Operações sem suporte](#unsupported-operations) deste documento.

Ao se conectar à UI da Web Ambari, você é solicitado a autenticar a página. Use a senha e o usuário administrador de cluster (Admin padrão) usados durante a criação do cluster.

Quando a página se abrir, observe a barra na parte superior. Essa barra contém as seguintes informações e controles:

![Visão geral do painel apache ambari](./media/hdinsight-hadoop-manage-ambari/apache-ambari-dashboard.png)

|Item |Descrição |
|---|---|
|Logotipo ambari|Abre o painel, que pode ser usado para monitorar o cluster.|
|Nome do cluster # ops|Exibe o número de operações ambari em andamento. A seleção do nome do cluster ou **Nº operações** exibe uma lista de operações em segundo plano.|
|Alertas|Exibe avisos ou alertas críticos, se houver, para o cluster.|
|Painel|Exibe o painel de instrumentos.|
|Serviços|Configurações de informações e configuração dos serviços no cluster.|
|Hosts|Configurações de informações e configurações para os nós no cluster.|
|Alertas|Um registro de informações, avisos e alertas críticos.|
|Admin|Pilha/serviços de software instalados no cluster, informações da conta de serviço e segurança Kerberos.|
|Botão de admin|Gerenciamento de Ambari, configurações do usuário e saída.|

## <a name="monitoring"></a>Monitoramento

### <a name="alerts"></a>Alertas

A lista a seguir contém os status de alerta comuns usados pelo Ambari:

* **OK**
* **Aviso**
* **Crítico**
* **DESCONHECIDO**

Os alertas diferentes de **OK** fazem com que a entrada **nº alertas** na parte superior da página exiba o número de alertas. A seleção dessa entrada exibe os alertas e seus status.

Os alertas estão organizados em vários grupos padrão, que podem ser exibidos na página **Alertas** .

![Apache Ambari alerta resumo da página](./media/hdinsight-hadoop-manage-ambari/hdinsight-alerts-page.png)

Você pode gerenciar os grupos usando o menu **Ações** e selecionando **Gerenciar Grupos de Alerta**.

![Apache Ambari gerencia grupos de alerta](./media/hdinsight-hadoop-manage-ambari/ambari-manage-alerts.png)

Você gerencia métodos de alerta e cria notificações de alerta do menu **Ações** selecionando __Gerenciar notificações__. Quaisquer notificações atuais são exibidas. Crie notificações a partir daqui. As notificações podem ser enviadas por **EMAIL** ou **SNMP** quando ocorrerem combinações específicas de alerta/gravidade. Por exemplo, você pode enviar uma mensagem de email quando qualquer um dos alertas no grupo **YARN Padrão** está definido para **Crítico**.

![Apache Ambari cria notificação de alerta](./media/hdinsight-hadoop-manage-ambari/create-alert-notification.png)

Por fim, selecionar __Gerenciar Definições de Alerta__ do menu __Ações__ permite que você defina o número de vezes que um alerta deve ocorrer antes do envio de uma notificação. Essa configuração pode ser usada para evitar notificações de erros transitórios.

Para obter um tutorial de uma notificação de alerta usando uma [conta sendgrid](https://docs.microsoft.com/azure/sendgrid-dotnet-how-to-send-email)gratuita, consulte [Configure notificações de e-mail apache Ambari no Azure HDInsight](./apache-ambari-email.md).

### <a name="cluster"></a>Cluster

A guia **Métricas** do painel contém uma série de widgets que facilitam monitorar o status do cluster em um relance. Vários widgets, tais como **Uso de CPU**, fornecem informações adicionais quando são clicados.

![Painel Apache Ambari com métricas](./media/hdinsight-hadoop-manage-ambari/hdi-metrics-dashboard.png)

A guia **Mapa de Dados** exibe as métricas na forma de mapas de dados coloridos, que vão do verde ao vermelho.

![Painel Apache Ambari com mapas de calor](./media/hdinsight-hadoop-manage-ambari/hdi-heatmap-dashboard.png)

Para obter mais informações sobre os nós no cluster, selecione **Hosts**. Em seguida, selecione o nó específico em que você está interessado.

![Detalhes do resumo do host Apache Ambari](./media/hdinsight-hadoop-manage-ambari/ambari-host-details1.png)

### <a name="services"></a>Serviços

A barra lateral **Serviços** no painel oferece uma visão rápida do status dos serviços em execução no cluster. Ícones diversos são usados para indicar o status ou ações que devem ser realizadas. Por exemplo, um símbolo amarelo de reciclagem será exibido se um serviço precisar ser reciclado.

![Barra lateral de serviços Apache Ambari](./media/hdinsight-hadoop-manage-ambari/apache-ambari-service-bar.png)

> [!NOTE]  
> Os serviços exibidos variam de acordo com versões e tipos de cluster HDInsight. Os serviços exibidos aqui podem ser diferentes dos serviços exibidos para o cluster.

A seleção de um serviço exibirá informações mais detalhadas sobre o serviço.

![Informações de resumo do serviço Apache Ambari](./media/hdinsight-hadoop-manage-ambari/ambari-service-details.png)

#### <a name="quick-links"></a>Links rápidos

Alguns serviços exibem um link **Links Rápidos** na parte superior da página. Este link pode ser usado para acessar uis web específicas de serviço, tais como:

* **Histórico de Trabalhos** : histórico de trabalhos do MapReduce.
* **Gerenciador de recursos** - YARN Resource Manager UI.
* **NameNode** : IU NameNode no HDFS (Sistema de Arquivos Distribuído do Hadoop).
* **IU da Web do Oozie** : IU do Oozie.

A seleção de qualquer um desses links abrirá uma nova guia em seu navegador, que exibirá a página selecionada.

> [!NOTE]  
> Selecionar a entrada **Links Rápidos** para um serviço pode retornar um erro de "servidor não encontrado". Se você encontrar esse erro, você deverá usar um túnel SSH ao usar a entrada **Links rápidos** para esse serviço. Para obter informações, consulte [Usar túnel SSH com o HDInsight](hdinsight-linux-ambari-ssh-tunnel.md)

## <a name="management"></a>Gerenciamento

### <a name="ambari-users-groups-and-permissions"></a>Usuários, grupos e permissões do Ambari

O trabalho com usuários, grupos e permissões tem suporte ao usar um cluster HDInsight [ingressado no domínio](./domain-joined/hdinsight-security-overview.md). Para obter informações sobre o uso da IU de Gerenciamento do Ambari em um cluster ingressado no domínio, consulte [Gerenciar clusters HDInsight ingressados no domínio](./domain-joined/hdinsight-security-overview.md).

> [!WARNING]  
> Não altere a senha do watchdog Ambari (hdinsightwatchdog) no seu cluster HDInsight baseado em Linux. A alteração da senha interrompe a capacidade de usar as ações de script ou executar operações de dimensionamento com o cluster.

### <a name="hosts"></a>Hosts

A página **Hosts** lista todos os hosts no cluster. Para gerenciar hosts, siga estas etapas.

![Apache Ambari hospeda visão geral da página](./media/hdinsight-hadoop-manage-ambari/hdinsight-hosts-page.png)

> [!NOTE]  
> As ações de adicionar, encerrar e reativar um host não devem ser realizadas com clusters HDInsight.

1. Selecione o host que você deseja gerenciar.

2. Use o menu **Ações** para selecionar a ação que deseja fazer:

    |Item |Descrição |
    |---|---|
    |Inicie todos os componentes|Inicie todos os componentes no host.|
    |Pare todos os componentes|Pare todos os componentes do host.|
    |Reiniciar todos os componentes|Pare e inicie todos os componentes do host.|
    |Ativar modo de manutenção|Suprime alertas para o hospedeiro. Este modo deve ser ativado se você estiver fazendo ações que geram alertas. Por exemplo, parar e iniciar um serviço.|
    |Desligue o modo de manutenção|Retorna o host para alerta normal.|
    |Stop|Interrompe datanode ou nodeManagers no host.|
    |Iniciar|Inicia DataNode ou NodeManagers no host.|
    |Reiniciar|Pára e inicia DataNode ou NodeManagers no host.|
    |Desprogramar|Remove um host do cluster. **Não use essa ação em clusters HDInsight.**|
    |Recomissão|Adiciona um host desativado anteriormente ao cluster. **Não use essa ação em clusters HDInsight.**|

### <a name="services"></a><a id="service"></a>Serviços

Na página **Painel** ou **Serviços**, use o botão **Ações** na parte inferior da lista de serviços para interromper e iniciar todos os serviços.

![Lista de ações de serviço apache ambari](./media/hdinsight-hadoop-manage-ambari/ambari-service-actions.png)

> [!WARNING]  
> Enquanto **Adicionar Serviço** estiver listado nesse menu, ele não deverá ser usado para adicionar serviços ao cluster HDInsight. Novos serviços devem ser adicionados usando uma Ação de Script durante o provisionamento do cluster. Para saber mais sobre o uso de ações de script, consulte [Personalizar clusters HDInsight usando ações de script](hdinsight-hadoop-customize-cluster-linux.md).

Embora o botão **Ações** possa reiniciar todos os serviços, muitas vezes convém iniciar, parar ou reiniciar um serviço específico. Use as seguintes etapas para fazer ações em um serviço individual:

1. Na página **Painel** ou **Serviços**, selecione um serviço.

2. Na parte superior da guia **Resumo**, use o botão **Ações de Serviço** e selecione a ação a tomar. Esta ação reinicia o serviço em todos os nós.

    ![Ações de serviço individual apache ambari](./media/hdinsight-hadoop-manage-ambari/individual-service-actions.png)

   > [!NOTE]  
   > Reiniciar alguns serviços enquanto o cluster estiver em execução pode gerar alertas. Para evitar alertas, você pode usar o botão **Ações de Serviço** para habilitar o **Modo de manutenção** para o serviço antes de executar a reinicialização.

3. Depois que uma ação é selecionada, a entrada **Nº operações** na parte superior da página aumenta para mostrar que está ocorrendo uma operação em segundo plano. Se a exibição estiver configurada, a lista de operações em segundo plano será exibida.

   > [!NOTE]  
   > Se você habilitou o **modo Manutenção** para o serviço, lembre-se de desabilitá-lo usando o botão **Ações de Serviço** quando a operação for concluída.

Para configurar um serviço, use as seguintes etapas:

1. Na página **Painel** ou **Serviços**, selecione um serviço.

2. Selecione a guia **Configs.** A configuração atual é exibida. Uma lista das configurações anteriores também é exibida.

    ![Configuração do serviço Apache Ambari](./media/hdinsight-hadoop-manage-ambari/ambari-service-configs.png)

3. Use os campos exibidos para modificar a configuração e selecione **Salvar**. Ou selecione uma configuração anterior e clique em **Tornar atual** para reverter para as configurações anteriores.

## <a name="ambari-views"></a>Exibições do Ambari

As Exibições do Ambari permitem que os desenvolvedores conectem elementos de interface do usuário à interface do usuário da Web do Apache Ambari usando a [Estrutura de modos de exibição do Ambari](https://cwiki.apache.org/confluence/display/AMBARI/Views). O HDInsight fornece as seguintes exibições com tipos de cluster do Hadoop:

* Exibição do Hive: a Exibição do Hive permite executar consultas de Hive diretamente do seu navegador da Web. Você pode salvar consultas, exibir os resultados, salvar os resultados no armazenamento de cluster ou baixar os resultados no sistema local. Para obter mais informações sobre como usar Exibições do Hive, consulte [Usar Exibições do Apache Hive com HDInsight](hadoop/apache-hadoop-use-hive-ambari-view.md).

* Exibição Tez: a exibição Tez permite que você compreenda melhor e otimize os trabalhos. Você pode exibir informações sobre como os trabalhos do Tez são executados e quais recursos são usados.

## <a name="unsupported-operations"></a>Operações sem suporte

As seguintes operações ambari não são suportadas no HDInsight:

* __Mover o serviço do Coletor de métricas__. Ao exibir as informações sobre o serviço do Coletor de métricas, uma das ações disponíveis no menu de Ações do serviço é __Mover o Coletor de métricas__. Esta ação não é suportada com o HDInsight.

## <a name="next-steps"></a>Próximas etapas

* [API Apache Ambari REST](hdinsight-hadoop-manage-ambari-rest-api.md) com HDInsight.
* [Use o Apache Ambari para otimizar as configurações de cluster do HDInsight](./hdinsight-changing-configs-via-ambari.md)
* [Scale Azure HDInsight clusters](./hdinsight-scaling-best-practices.md)
