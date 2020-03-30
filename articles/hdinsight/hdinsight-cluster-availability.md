---
title: 'Monitoramento: Apache Ambari & Logs do Monitor Azure - Azure HDInsight'
description: Saiba como usar os registros do Monitor Ambari e Azure para monitorar a saúde e a disponibilidade do cluster.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 02/06/2020
ms.openlocfilehash: 383366fa3e436c79bed28a7c47f1e9daa5f0d9de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77060160"
---
# <a name="how-to-monitor-cluster-availability-with-apache-ambari-and-azure-monitor-logs"></a>Como monitorar a disponibilidade de clusters com registros do Apache Ambari e do Azure Monitor

Os clusters HDInsight incluem tanto o Apache Ambari, que fornece informações de saúde em um relance e alertas predefinidos, quanto a integração de logs do Azure Monitor, que fornece métricas e logs queryable, bem como alertas configuráveis.

Este doc mostra como usar essas ferramentas para monitorar seu cluster e percorre alguns exemplos para configurar um alerta Ambari, monitorar a taxa de disponibilidade de nós e criar um alerta do Monitor Azure que é acionado quando um batimento cardíaco não foi recebido de um ou mais nós em cinco horas.

## <a name="ambari"></a>Ambari

### <a name="dashboard"></a>Painel

O painel ambari pode ser acessado selecionando o link **inicial ambari** na seção **Cluster dashboards** do hdinsight Overview no portal Azure, conforme mostrado abaixo. Alternativamente, ele pode ser acessado `https://CLUSTERNAME.azurehdinsight.net` navegando em um navegador onde CLUSTERNAME é o nome do seu cluster.

![Visão do portal de recursos HDInsight](media/hdinsight-cluster-availability/azure-portal-dashboard-ambari.png)

Em seguida, você será solicitado para um nome de usuário e senha de login de cluster. Digite as credenciais escolhidas quando criou o cluster.

Em seguida, você será levado para o painel Ambari, que contém widgets que mostram um punhado de métricas para lhe dar uma visão geral rápida da saúde do seu cluster HDInsight. Esses widgets mostram métricas como o número de DataNodes (nó de trabalhador) e JournalNodes (nó de zootecna), NameNodes (nó de cabeça) de tempo de atividade, bem como métricas específicas para determinados tipos de cluster, como o tempo de atividade do YARN ResourceManager para clusters Spark e Hadoop.

![Apache Ambari usa tela de painel](media/hdinsight-cluster-availability/apache-ambari-dashboard.png)

### <a name="hosts--view-individual-node-status"></a>Hosts – visualize o status do nó individual

Você também pode visualizar informações de status para nós individuais. Selecione a guia **'Hosts'** para exibir uma lista de todos os nós do cluster e consulte informações básicas sobre cada nó. A verificação verde à esquerda de cada nome do nó indica que todos os componentes estão no nó. Se um componente estiver em baixo em um nó, você verá um triângulo de alerta vermelho em vez da verificação verde.

![HDInsight Apache Ambari hospeda visualização](media/hdinsight-cluster-availability/apache-ambari-hosts1.png)

Em seguida, você pode selecionar no **nome** de um nó para exibir métricas de host mais detalhadas para esse nó em particular. Esta exibição mostra o status/disponibilidade de cada componente individual.

![Apache Ambari hospeda exibição de nó único](media/hdinsight-cluster-availability/apache-ambari-hosts-node.png)

### <a name="ambari-alerts"></a>Alertas ambari

A Ambari também oferece vários alertas configuráveis que podem fornecer notificação de determinados eventos. Quando os alertas são acionados, eles são mostrados no canto superior esquerdo de Ambari em um distintivo vermelho contendo o número de alertas. A seleção deste crachá mostra uma lista de alertas atuais.

![Contagem de alertas atuais apache ambari](media/hdinsight-cluster-availability/apache-ambari-alerts.png)

Para exibir uma lista de definições de alerta e seus status, selecione a guia **Alertas,** conforme mostrado abaixo.

![Ambari alerta definiçãos de visualização](media/hdinsight-cluster-availability/ambari-alerts-definitions.png)

A Ambari oferece muitos alertas predefinidos relacionados à disponibilidade, incluindo:

| Nome do alerta                        | Descrição   |
|---|---|
| Resumo da Saúde do DataNode           | Este alerta de nível de serviço é acionado se houver DataNodes insalubres|
| NameNode Alta Disponibilidade Saúde | Este alerta de nível de serviço é acionado se o Active NameNode ou o Standby NameNode não estiverem em execução.|
| Porcentagem de diários disponíveis    | Este alerta é acionado se o número de JournalNodes abaixo no cluster for maior do que o limiar crítico configurado. Ele agrega os resultados das verificações do processo JournalNode. |
| Porcentagem de datanodes disponíveis       | Este alerta é acionado se o número de DataNodes abaixo no cluster for maior do que o limiar crítico configurado. Ele agrega os resultados das verificações do processo DataNode.|

Uma lista completa de alertas Ambari que ajudam a monitorar a disponibilidade de um cluster pode ser encontrada [aqui,](https://docs.microsoft.com/azure/hdinsight/hdinsight-high-availability-linux#ambari-web-ui)

Para visualizar detalhes para um alerta ou modificar critérios, selecione o **nome** do alerta. Tome o **DataNode Health Summary** como exemplo. Você pode ver uma descrição do alerta, bem como os critérios específicos que desencadearão um alerta de 'aviso' ou 'crítico' e o intervalo de verificação para os critérios. Para editar a configuração, selecione o botão **Editar** no canto superior direito da caixa Configuração.

![Configuração de alerta Apache Ambari](media/hdinsight-cluster-availability/ambari-alert-configuration.png)

Aqui, você pode editar a descrição e, mais importante, o intervalo de verificação e os limites para avisos ou alertas críticos.

![Configurações de alerta Ambari editam exibição](media/hdinsight-cluster-availability/ambari-alert-configuration-edit.png)

Neste exemplo, você pode fazer com que 2 DataNodes insalubres acionem um alerta crítico e 1 DataNode insalubre apenas acione um aviso. Selecione **Salvar** quando terminar a edição.

### <a name="email-notifications"></a>Notificações por email

Você também pode configurar notificações de e-mail opcionalmente para alertas Ambari. Para fazer isso, quando estiver na guia **Alertas,** clique no botão **Ações** no canto superior esquerdo e, em seguida, **gerencie notificações.**

![Ambari gerenciar ações de notificações](media/hdinsight-cluster-availability/ambari-manage-notifications.png)

Uma caixa de diálogo para o gerenciamento de notificações de alerta será aberta. Selecione **+** na parte inferior da caixa de diálogo e preencha os campos necessários para fornecer à Ambari detalhes do servidor de e-mail a partir do qual enviar e-mails.

> [!TIP]
> Configurar notificações de e-mail ambari pode ser uma boa maneira de receber alertas em um só lugar ao gerenciar muitos clusters HDInsight.

## <a name="azure-monitor-logs-integration"></a>Integração de logs do Azure Monitor

Os logs do Azure Monitor permitem que os dados gerados por vários recursos, como clusters HDInsight, sejam coletados e agregados em um só lugar para obter uma experiência de monitoramento unificada.

Como pré-requisito, você precisará de um Espaço de Trabalho do Log Analytics para armazenar os dados coletados. Se você ainda não criou um, você pode seguir as instruções aqui: [Crie um Espaço de Trabalho do Log Analytics](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace).

### <a name="enable-hdinsight-azure-monitor-logs-integration"></a>Habilite a integração de logs do HDInsight Azure Monitor

Na página de recursos do cluster HDInsight no portal, selecione **O Monitor Azure**. Em seguida, **selecione ativar** e selecionar o espaço de trabalho do Log Analytics a partir do drop-down.

![HDInsight Operations Management Suite](media/hdinsight-cluster-availability/azure-portal-monitoring.png)

### <a name="query-metrics-and-logs-tables"></a>Tabelas de métricas e logs de consulta

Uma vez que a integração de log do Azure Monitor esteja ativada (isso pode levar alguns minutos), navegue até o recurso **do Espaço de Trabalho do Log Analytics** e selecione **Logs**.

![Logs de espaço de trabalho do Log Analytics](media/hdinsight-cluster-availability/hdinsight-portal-logs.png)

Os logs listam uma série de consultas de amostra, tais como:

| Nome da consulta                      | Descrição                                                               |
|---------------------------------|---------------------------------------------------------------------------|
| Disponibilidade de computadores hoje    | Mapeie o número de computadores que enviam logs, a cada hora                     |
| Listar batimentos cardíacos                 | Liste todos os batimentos cardíacos do computador da última hora                           |
| Último batimento cardíaco de cada computador | Mostre os últimos batimentos cardíacos enviados por cada computador                             |
| Computadores indisponíveis           | Liste todos os computadores conhecidos que não enviaram um batimento cardíaco nas últimas 5 horas |
| Taxa de disponibilidade               | Calcule a taxa de disponibilidade de cada computador conectado                |

Como exemplo, execute a consulta de amostra **de taxa de disponibilidade** selecionando **Executar** nessa consulta, conforme mostrado na captura de tela acima. Isso mostrará a taxa de disponibilidade de cada nó em seu cluster como uma porcentagem. Se você habilitou vários clusters HDInsight para enviar métricas para o mesmo espaço de trabalho do Log Analytics, você verá a taxa de disponibilidade para todos os nós nesses clusters exibidos.

![Log Analytics registra consulta de amostra 'taxa de disponibilidade' do Log Analytics](media/hdinsight-cluster-availability/portal-availability-rate.png)

> [!NOTE]  
> A taxa de disponibilidade é medida durante um período de 24 horas, de modo que seu cluster precisará funcionar por pelo menos 24 horas antes de ver as taxas de disponibilidade precisas.

Você pode fixar esta tabela em um painel compartilhado clicando em **Pin** no canto superior direito. Se você não tiver nenhum painel compartilhado gravável, você pode ver como criar um aqui: [Criar e compartilhar dashboards no portal Azure](https://docs.microsoft.com/azure/azure-portal/azure-portal-dashboards#publish-and-share-a-dashboard).

### <a name="azure-monitor-alerts"></a>Alertas do Monitor Do Azure

Você também pode configurar alertas do Azure Monitor que serão acionados quando o valor de uma métrica ou os resultados de uma consulta atenderem a determinadas condições. Como exemplo, vamos criar um alerta para enviar um e-mail quando um ou mais nós não enviarem um batimento cardíaco em 5 horas (ou seja, presume-se que não estejam disponíveis).

Em **Logs,** execute a consulta de amostra **de computadores indisponíveis** selecionando **Executar** nessa consulta, conforme mostrado abaixo.

![Log Analytics logs 'indisponíveis computadores' amostra](media/hdinsight-cluster-availability/portal-unavailable-computers.png)

Se todos os nós estiverem disponíveis, esta consulta deve retornar zero resultados por enquanto. Clique **em Nova regra de alerta** para começar a configurar seu alerta para esta consulta.

![Nova regra de alerta do espaço de trabalho do Log Analytics](media/hdinsight-cluster-availability/portal-logs-new-alert-rule.png)

Há três componentes para um alerta: o *recurso* para criar a regra (o espaço de trabalho do Log Analytics neste caso), a *condição* para acionar o alerta e os *grupos de ação* que determinam o que acontecerá quando o alerta for acionado.
Clique no título da **condição,** como mostrado abaixo, para terminar de configurar a lógica do sinal.

![Alerta do portal cria condição de regra](media/hdinsight-cluster-availability/portal-condition-title.png)

Isso abrirá **Configurar a lógica do sinal**.

Defina a seção **lógica Alerta** da seguinte forma:

*Com base em: Número de resultados, Condição: Maior que, Limiar: 0.*

Uma vez que esta consulta só retorna os nós indisponíveis como resultados, se o número de resultados for cada vez maior que 0, o alerta deve disparar.

Na **seção Avaliado,** defina o **período** e a **frequência** com base na frequência que você deseja verificar se há nódulos indisponíveis.

Para o propósito deste alerta, você deseja ter certeza **de Period=Frequency.** Mais informações sobre período, frequência e outros parâmetros de alerta podem ser encontradas [aqui.](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-unified-log#log-search-alert-rule---definition-and-types)

Selecione **Concluído** quando terminar de configurar a lógica do sinal.

![Regra de alerta configura lógica de sinal](media/hdinsight-cluster-availability/portal-configure-signal-logic.png)

Se você ainda não tiver um grupo de ação existente, clique em **Criar novo** na seção Grupos **de ação.**

![Regra de alerta cria novo grupo de ação](media/hdinsight-cluster-availability/portal-create-new-action-group.png)

Isso abrirá **adicionar grupo de ação**. Escolha um **nome do grupo Ação,** **Nome curto,** **Assinatura**e grupo **de recursos.** Na seção **Ações,** escolha um **Nome de Ação** e selecione **E-mail/SMS/Push/Voice** como o **Tipo de Ação.**

> [!NOTE]
> Existem várias outras ações que um alerta pode desencadear além de um E-mail/SMS/Push/Voice, como uma função Azure, LogicApp, Webhook, ITSM e Automation Runbook. [Saiba Mais.](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups#action-specific-information)

Isso abrirá **E-mail/SMS/Push/Voice**. Escolha um **nome** para o destinatário, **marque** a caixa **de e-mail** e digite um endereço de e-mail para o qual deseja que o alerta seja enviado. Selecione **OK** em **E-mail/SMS/Push/Voice,** em seguida, em **Adicionar grupo de ação** para terminar a configuração do seu grupo de ação.

![Regra de alerta cria grupo de ação de adicionar](media/hdinsight-cluster-availability/portal-add-action-group.png)

Depois que essas lâminas fecharem, você deve ver seu grupo de ação listado na seção **Grupos de ação.** Por fim, complete a seção **Detalhes de alerta** digitando um Nome e **descrição** da regra de **alerta** e escolhendo uma **Gravidade**. Clique **em Criar regra de alerta** para finalizar.

![Portal cria acabamento de regra de alerta](media/hdinsight-cluster-availability/portal-create-alert-rule-finish.png)

> [!TIP]
> A capacidade de especificar **severidade** é uma ferramenta poderosa que pode ser usada ao criar vários alertas. Por exemplo, você pode criar um alerta para levantar um aviso (Sev 1) se um único nó de cabeça cair e outro alerta que levanta Crítico (Sev 0) no caso improvável de que ambos os nós de cabeça caiam.

Quando a condição para este alerta for atendida, o alerta será acionado e você receberá um e-mail com os detalhes de alerta como este:

![Exemplo de e-mail de alerta do Azure Monitor](media/hdinsight-cluster-availability/portal-oms-alert-email.png)

Você também pode visualizar todos os alertas que foram disparados, agrupados por gravidade, indo para **Alertas** em seu **Espaço de Trabalho log analytics**.

![Alertas de espaço de trabalho do Log Analytics](media/hdinsight-cluster-availability/hdi-portal-oms-alerts.png)

A seleção em um agrupamento de gravidade (ou **seja, Sev 1,** como destacado acima) mostrará registros de todos os alertas dessa gravidade que dispararam como abaixo:

![Log Analytics espaço de trabalho sev um alerta](media/hdinsight-cluster-availability/portal-oms-alerts-sev1.png)

## <a name="next-steps"></a>Próximas etapas

- [Disponibilidade e confiabilidade dos clusters Apache Hadoop em HDInsight](hdinsight-high-availability-linux.md)
