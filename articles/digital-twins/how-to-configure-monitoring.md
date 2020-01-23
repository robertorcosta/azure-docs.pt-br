---
title: Como configurar o monitoramento – gêmeos digital do Azure | Microsoft Docs
description: Saiba como configurar opções de monitoramento e log para o gêmeos digital do Azure.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 01/21/2020
ms.custom: seodec18
ms.openlocfilehash: e35e18be20af3bd9f6fdc9541f9abfe857a6b87c
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/22/2020
ms.locfileid: "76511851"
---
# <a name="how-to-configure-monitoring-in-azure-digital-twins"></a>Como configurar o monitoramento nos Gêmeos Digitais do Azure

Os Gêmeos Digitais do Azure dão suporte a registro em log, monitoramento e análise robustos. As soluções que os desenvolvedores podem usar Azure Monitor logs, logs de diagnóstico, log de atividades e outros serviços para dar suporte às necessidades complexas de monitoramento de um aplicativo de IoT. As opções de registro em log podem ser combinadas para consultar ou exibir registros em vários serviços e para fornecer cobertura de registro em log granular para muitos serviços.

Este artigo resume as opções de registro em log e monitoramento e como combiná-las de maneiras específicas para os Gêmeos Digitais do Azure.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="review-activity-logs"></a>Revisar logs de atividade

Os [logs de atividade](../azure-monitor/platform/platform-logs-overview.md) do Azure fornecem insights rápidos em históricos de operações e eventos de nível de assinatura para cada instância de serviço do Azure.

Os eventos de nível de assinatura incluem:

* Criação de recursos
* Remoção de recursos
* Criação de segredos do aplicativo
* Integração com outros serviços

O log de atividades dos Gêmeos Digitais do Azure é habilitado por padrão e pode ser encontrado no portal do Azure ao:

1. Selecionar sua instância de Gêmeos Digitais do Azure.
1. Escolher o **log de atividades** para exibir o painel de exibição:

    [log de atividades ![](media/how-to-configure-monitoring/activity-log.png)](media/how-to-configure-monitoring/activity-log.png#lightbox)

Para o registro de log de atividades avançadas:

1. Selecione a opção **Logs** para exibir a **Visão geral da Análise do Log de Atividades**:

    [Seleção de ![](media/how-to-configure-monitoring/activity-log-select.png)](media/how-to-configure-monitoring/activity-log-select.png#lightbox)

1. A **Visão geral da Análise do Log de Atividades** resume dados de log de atividade essenciais:

    [Visão geral do ![log Analytics]( media/how-to-configure-monitoring/log-analytics-overview.png)]( media/how-to-configure-monitoring/log-analytics-overview.png#lightbox)

>[!TIP]
>Use **logs de atividade** para insights rápidos de eventos no nível da assinatura.

## <a name="enable-customer-diagnostic-logs"></a>Habilite logs de diagnóstico do cliente

As [Configurações de diagnóstico](../azure-monitor/platform/platform-logs-overview.md) do Azure podem ser definidas para cada instância do Azure para complementar o log de atividades. Enquanto os logs de atividade pertencem aos eventos de nível de assinatura, o registro de log de diagnóstico fornece insights sobre o histórico operacional dos próprios recursos.

Exemplos de registro de log de diagnóstico incluem:

* O tempo de execução para uma função definida pelo usuário
* O código de status de resposta de uma solicitação de API bem-sucedida
* Recuperar uma chave do aplicativo de um cofre

Para habilitar logs de diagnóstico, siga estas etapas:

1. Abra os recursos no portal do Azure.
1. Selecione **configurações de diagnóstico**:

    [![configurações de diagnóstico um](media/how-to-configure-monitoring/diagnostic-settings-one.png)](media/how-to-configure-monitoring/diagnostic-settings-one.png#lightbox)

1. Selecione **Ativar diagnóstico** para coletar dados (se não tiver sido habilitado anteriormente).
1. Preencha os campos solicitados e selecione como e onde os dados serão salvos:

    [![configurações de diagnóstico duas](media/how-to-configure-monitoring/diagnostic-settings-two.png)](media/how-to-configure-monitoring/diagnostic-settings-two.png#lightbox)

    Os logs de diagnóstico são frequentemente salvos usando o [armazenamento de arquivos do Azure](../storage/files/storage-files-deployment-guide.md) e compartilhados com [logs de Azure monitor](../azure-monitor/log-query/get-started-portal.md). Ambas as opções podem ser selecionadas.

>[!TIP]
>Use **logs de diagnóstico** para obter insights sobre operações de recursos.

## <a name="azure-monitor-and-log-analytics"></a>Log Analytics e Azure Monitor

Aplicativos de IoT unem diferentes recursos, dispositivos, locais e dados em um só local. O registro em log refinado fornece informações detalhadas sobre cada parte, serviço ou componente específicos da arquitetura geral do aplicativo, mas uma visão geral unificada costuma ser necessária para manutenção e depuração.

Azure Monitor inclui o poderoso serviço log Analytics, que permite que as fontes de log sejam exibidas e analisadas em um único local. O Azure Monitor, portanto, é muito útil para analisar logs de dentro de aplicativos sofisticados de IoT.

Os exemplos de uso incluem:

* Consulta a várias históricos de log de diagnóstico
* Visualizar os logs para várias funções definidas pelo usuário
* Exibir os logs para dois ou mais serviços dentro de um período de tempo específico

A consulta de log completa é fornecida por meio de [logs de Azure monitor](../azure-monitor/log-query/log-query-overview.md). Para configurar esses recursos avançados:

1. No portal do Microsoft Azure, pesquise **Log Analytics**.
1. As instâncias do **espaço de trabalho log Analytics** disponíveis serão exibidas. Para consultar, escolha um e selecione **Logs**:

    [![log Analytics](media/how-to-configure-monitoring/log-analytics.png)](media/how-to-configure-monitoring/log-analytics.png#lightbox)

1. Se você ainda não tiver uma instância de **espaço de trabalho log Analytics** , poderá criar um espaço de trabalho selecionando o botão **Adicionar** :

    [![criar OMS](media/how-to-configure-monitoring/log-analytics-oms.png)](media/how-to-configure-monitoring/log-analytics-oms.png#lightbox)

Depois que a instância do **espaço de trabalho do log Analytics** for provisionada, você poderá usar consultas poderosas para localizar entradas em vários logs ou Pesquisar usando critérios específicos usando o gerenciamento de **log**:

   [gerenciamento de log do ![](media/how-to-configure-monitoring/log-analytics-management.png)](media/how-to-configure-monitoring/log-analytics-management.png#lightbox)

Para obter mais informações sobre operações de consulta avançadas, leia [introdução às consultas](../azure-monitor/log-query/get-started-queries.md).

> [!NOTE]
> Você pode experimentar um atraso de 5 minutos ao enviar eventos para **log Analytics espaço de trabalho** pela primeira vez.

Os logs de Azure Monitor também fornecem serviços de notificação de erro e alerta avançados, que podem ser exibidos selecionando **diagnosticar e resolver problemas**:

   [![alertas e notificações de erro](media/how-to-configure-monitoring/log-analytics-notifications.png)](media/how-to-configure-monitoring/log-analytics-notifications.png#lightbox)

>[!TIP]
>Use **log Analytics espaço de trabalho** para consultar históricos de log para várias funcionalidades, assinaturas ou serviços do aplicativo.

## <a name="other-options"></a>Outras opções

Os Gêmeos Digitais do Azure também dão suporte a registro em log específico de aplicativo e auditoria de segurança. Para obter uma visão geral completa de todas as opções de log do Azure disponíveis para sua instância do gêmeos digital do Azure, leia o artigo [auditoria de log do Azure](../security/fundamentals/log-audit.md) .

## <a name="next-steps"></a>Próximos passos

- Saiba mais sobre o [Log de Atividades](../azure-monitor/platform/platform-logs-overview.md) do Azure.

- Aprofunde-se nas configurações de diagnóstico do Azure lendo uma [Visão geral dos logs de diagnóstico](../azure-monitor/platform/platform-logs-overview.md).

- Leia mais sobre [logs de Azure monitor](../azure-monitor/log-query/get-started-portal.md).
