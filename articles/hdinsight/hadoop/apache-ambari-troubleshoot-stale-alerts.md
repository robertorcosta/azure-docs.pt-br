---
title: Alertas obsoletos do Apache Ambari no Azure HDInsight
description: Discussão e análise de possíveis razões e soluções para alertas obsoletos apache ambari no HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/22/2020
ms.openlocfilehash: f9dfcb930e3fe4f862f9f51ff00270d0eb0c66ca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77539103"
---
# <a name="scenario-apache-ambari-stale-alerts-in-azure-hdinsight"></a>Cenário: Alertas obsoletos do Apache Ambari no Azure HDInsight

Este artigo descreve etapas de solução de problemas e possíveis resoluções para problemas ao interagir com clusters Azure HDInsight.

## <a name="issue"></a>Problema

Na UI Apache Ambari, você pode ver um alerta como este:

![Exemplo de alerta obsoleto Apache Ambari](./media/apache-ambari-troubleshoot-stale-alerts/ambari-stale-alerts-example.png)

## <a name="cause"></a>Causa

Os agentes ambari monitoram continuamente a saúde de muitos recursos. *Os alertas* podem ser configurados para notificá-lo se as propriedades específicas do cluster estão dentro de limites predeterminados. Depois que cada verificação de recursos é executada, se a condição de alerta for atendida, os agentes ambari relatam o status de volta ao servidor Ambari e acionam um alerta. Se um alerta não for verificado de acordo com o intervalo em seu Perfil de Alerta, o servidor acionará um alerta *de alerta soletano do servidor Ambari.*

Existem várias razões pelas quais uma verificação de saúde pode não ser executada no seu intervalo definido:

* Os hosts estão uso pesado (alto uso de CPU), de modo que o agente Ambari não pode obter recursos suficientes do sistema para executar os alertas a tempo.

* O cluster está ocupado executando muitos trabalhos ou serviços durante um período de carga pesada.

* Um pequeno número de hosts no cluster está hospedando muitos componentes e, portanto, são necessários para executar muitos alertas. Se o número de componentes for grande, os trabalhos de alerta podem perder seus intervalos programados.

## <a name="resolution"></a>Resolução

Experimente os seguintes métodos para resolver problemas com alertas obsoletos da Ambari.

### <a name="increase-the-alert-interval-time"></a>Aumente o tempo de intervalo de alerta

Você pode aumentar o valor de um intervalo de alerta individual, com base no tempo de resposta e carga do seu cluster:

1. Na ui Apache Ambari, selecione a guia **Alertas.**
1. Selecione o nome de definição de alerta que você deseja.
1. Na definição, selecione **Editar**.
1. Aumente o valor do **intervalo de verificação** e, em seguida, **selecione Salvar**.

### <a name="increase-the-alert-interval-time-for-ambari-server-alerts"></a>Aumente o tempo de intervalo de alerta para alertas do servidor Ambari

1. Na ui Apache Ambari, selecione a guia **Alertas.**
1. Na lista de paradas **de grupos,** selecione **PADRÃO AMBARI**.
1. Selecione o **alerta de alertas do servidor Ambari.**
1. Na definição, selecione **Editar**.
1. Aumente o valor do **intervalo de verificação.**
1. Aumente o valor **do multiplicador de intervalo** e selecione **Salvar**.

### <a name="disable-and-reenable-the-alert"></a>Desativar e reativar o alerta

Para descartar um alerta obsoleto, desabilite-o e, em seguida, rehabilite-o:

1. Na ui Apache Ambari, selecione a guia **Alertas.**
1. Selecione o nome de definição de alerta que você deseja.
1. A partir da definição, **selecione Ativado** na parte mais à direita da interface do iu.
1. Na janela pop-up **Confirmação,** **selecione Confirmar desativar**.
1. Aguarde alguns segundos para que todas as "instâncias" de alerta mostradas na página sejam apagadas.
1. A partir da definição, **selecione Desativado** na parte mais à direita da interface do iu.
1. Na janela pop-up **Confirmação,** **selecione Confirmar ativar**.

### <a name="increase-the-alert-grace-period"></a>Aumente o período de carência de alerta

Há um período de carência antes que um agente ambari informe que um alerta configurado perdeu sua programação. Se o alerta perdeu o horário programado, mas foi publicado dentro do período de carência, o alerta obsoleto não é gerado.

O `alert_grace_period` valor padrão é de 5 segundos. Você pode configurar essa configuração em /etc/ambari-agent/conf/ambari-agent.ini. Para hosts em que alertas obsoletos ocorrem em intervalos regulares, tente aumentar o valor para 10. Então, reinicie o agente Ambari.

## <a name="next-steps"></a>Próximas etapas

Se o seu problema não foi mencionado aqui ou você não é capaz de resolvê-lo, visite um dos seguintes canais para obter mais suporte:

* Obtenha respostas dos especialistas do Azure no [Azure Community Support](https://azure.microsoft.com/support/community/).

* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport) no Twitter. Esta é a conta oficial do Microsoft Azure para melhorar a experiência do cliente. Ele conecta a comunidade Azure aos recursos certos: respostas, suporte e especialistas.

* Se você precisar de mais ajuda, envie uma solicitação de suporte do [portal Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Para chegar lá, selecione Ajuda **(?**) no menu do portal ou abra o painel **de suporte Ajuda +.** Para obter mais informações, consulte [Como criar uma solicitação de suporte do Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). 

  O suporte para gerenciamento e faturamento de assinaturas está incluído na assinatura do Microsoft Azure. O suporte técnico está disponível através dos [Planos de Suporte do Azure.](https://azure.microsoft.com/support/plans/)
