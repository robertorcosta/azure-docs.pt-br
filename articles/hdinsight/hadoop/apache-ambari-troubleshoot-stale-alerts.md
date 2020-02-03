---
title: Alertas obsoletos do Apache Ambari no Azure HDInsight
description: Discussão e análise de possíveis razões e soluções para alertas obsoletos do Apache Ambari no HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/22/2020
ms.openlocfilehash: f19d499b5e50fbb5030a0f396296eed46fc6eee3
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/24/2020
ms.locfileid: "76722805"
---
# <a name="scenario-apache-ambari-stale-alerts-in-azure-hdinsight"></a>Cenário: alertas obsoletos do Apache Ambari no Azure HDInsight

Este artigo descreve as etapas de solução de problemas e as possíveis resoluções para problemas ao interagir com clusters do Azure HDInsight.

## <a name="issue"></a>Problema

Na interface do usuário do Apache Ambari, você poderá ver um alerta semelhante à imagem a seguir:

![Exemplo de alerta do Apache Ambari obsoleto](./media/apache-ambari-troubleshoot-stale-alerts/ambari-stale-alerts-example.png)

## <a name="cause"></a>Causa

Os agentes do Ambari executam continuamente verificações de integridade para monitorar a integridade de muitos recursos. Cada alerta é configurado para ser executado em intervalos de tempo predefinidos. Após a execução de cada alerta, os agentes do Ambari relatam o status para o servidor Ambari. Neste ponto, se o Ambari Server detectar que qualquer um dos alertas não foi executado oportunamente, ele disparará um "Ambari Server Alerts". Há várias razões pelas quais uma verificação de integridade pode não ser executada em seu intervalo definido:

* Quando os hosts estão sob utilização pesada (alta CPU), há a possibilidade de que o agente do Ambari não consiga obter recursos suficientes do sistema para executar os alertas de maneira oportuna.

* O cluster está ocupado executando muitos trabalhos/serviços durante carga pesada.

* Alguns hosts no cluster podem hospedar muitos componentes e, portanto, serão necessários para executar muitos alertas. Se o número de componentes for grande, é possível que os trabalhos de alerta possam perder seus intervalos agendados

## <a name="resolution"></a>Resolução

### <a name="increase-alert-interval-time"></a>Aumentar a hora do intervalo de alerta

Você pode optar por aumentar o valor de um intervalo de alerta individual com base no tempo de resposta do cluster e sua carga.

1. Na interface do usuário do Apache Ambari, selecione a guia **alertas** .
1. Selecione o nome de definição de alerta desejado.
1. Na definição, selecione **Editar**.
1. Modifique o valor do **intervalo de verificação** conforme desejado e, em seguida, selecione **salvar**.

### <a name="increase-alert-interval-time-for-ambari-server-alerts"></a>Aumentar o tempo de intervalo de alerta para alertas do servidor Ambari

1. Na interface do usuário do Apache Ambari, selecione a guia **alertas** .
1. Na lista suspensa **grupos** , selecione **AMBARI padrão**.
1. Selecione **alertas do servidor**de alertas Ambari.
1. Na definição, selecione **Editar**.
1. Modifique o valor do **intervalo de verificação** conforme desejado.
1. Modifique o valor **multiplicador de intervalo** conforme desejado e, em seguida, selecione **salvar**.

### <a name="disable-and-enable-the-alert"></a>Desabilitar e habilitar o alerta

Você pode desabilitar e, em seguida, habilitar novamente o alerta para descartar alertas obsoletos.

1. Na interface do usuário do Apache Ambari, selecione a guia **alertas** .
1. Selecione o nome de definição de alerta desejado.
1. Na definição, selecione **habilitado** localizado na extrema direita.
1. No pop-up de **confirmação** , selecione **confirmar desabilitar**.
1. Aguarde alguns segundos para que todas as "instâncias" de alerta mostradas na página sejam limpas.
1. Na definição, selecione **desabilitado** localizado na extrema direita.
1. No pop-up de **confirmação** , selecione **confirmar habilitar**.

### <a name="increase-alert-grace-time"></a>Aumentar o tempo de carência do alerta

Antes que o agente Ambari relate que um alerta configurado perdeu sua agenda, há um tempo de carência aplicado. Mesmo que o alerta tenha perdido seu horário agendado, mas foi disparado dentro do tempo de carência do alerta, o alerta obsoleto não será acionado.

O valor padrão de `alert_grace_period` é 5 segundos. Essa configuração de `alert_grace_period` é configurável no `/etc/ambari-agent/conf/ambari-agent.ini`. Para os hosts dos quais os alertas obsoletos são acionados em intervalos regulares, tente aumentar para um valor de 10. Em seguida, reinicie o agente Ambari

## <a name="next-steps"></a>{1&gt;{2&gt;Próximas etapas&lt;2}&lt;1}

Se você não encontrou seu problema ou não conseguiu resolver seu problema, visite um dos seguintes canais para obter mais suporte:

* Obtenha respostas de especialistas do Azure por meio do [suporte da Comunidade do Azure](https://azure.microsoft.com/support/community/).

* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport) -a conta de Microsoft Azure oficial para melhorar a experiência do cliente. Conectando a Comunidade do Azure aos recursos certos: respostas, suporte e especialistas.

* Se precisar de mais ajuda, você poderá enviar uma solicitação de suporte do [portal do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selecione **suporte** na barra de menus ou abra o Hub **ajuda + suporte** . Para obter informações mais detalhadas, consulte [como criar uma solicitação de suporte do Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). O acesso ao gerenciamento de assinaturas e ao suporte de cobrança está incluído na sua assinatura do Microsoft Azure, e o suporte técnico é fornecido por meio de um dos [planos de suporte do Azure](https://azure.microsoft.com/support/plans/).
