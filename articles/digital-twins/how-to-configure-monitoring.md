---
title: Como configurar o monitoramento - Azure Digital Twins | Microsoft Docs
description: Saiba como configurar opções de monitoramento e registro para Azure Digital Twins.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 01/21/2020
ms.custom: seodec18
ms.openlocfilehash: e35e18be20af3bd9f6fdc9541f9abfe857a6b87c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76511851"
---
# <a name="how-to-configure-monitoring-in-azure-digital-twins"></a>Como configurar o monitoramento nos Gêmeos Digitais do Azure

Os Gêmeos Digitais do Azure dão suporte a registro em log, monitoramento e análise robustos. Os desenvolvedores de soluções podem usar registros do Monitor Do Azure, registros de diagnóstico, registro de atividades e outros serviços para suportar as complexas necessidades de monitoramento de um aplicativo IoT. As opções de registro em log podem ser combinadas para consultar ou exibir registros em vários serviços e para fornecer cobertura de registro em log granular para muitos serviços.

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

    [![Registro de atividades](media/how-to-configure-monitoring/activity-log.png)](media/how-to-configure-monitoring/activity-log.png#lightbox)

Para o registro de log de atividades avançadas:

1. Selecione a opção **Logs** para exibir a **Visão geral da Análise do Log de Atividades**:

    [![Seleção](media/how-to-configure-monitoring/activity-log-select.png)](media/how-to-configure-monitoring/activity-log-select.png#lightbox)

1. A **Visão geral da Análise do Log de Atividades** resume dados de log de atividade essenciais:

    [![Visão geral do Log Analytics de Atividade]( media/how-to-configure-monitoring/log-analytics-overview.png)]( media/how-to-configure-monitoring/log-analytics-overview.png#lightbox)

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
1. Selecione **as configurações de diagnóstico:**

    [![Configurações de Diagnóstico um](media/how-to-configure-monitoring/diagnostic-settings-one.png)](media/how-to-configure-monitoring/diagnostic-settings-one.png#lightbox)

1. Selecione **Ativar diagnósticos** para coletar dados (se não estiver previamente habilitado).
1. Preencha os campos solicitados e selecione como e onde os dados serão salvos:

    [![Configurações de Diagnóstico dois](media/how-to-configure-monitoring/diagnostic-settings-two.png)](media/how-to-configure-monitoring/diagnostic-settings-two.png#lightbox)

    Os registros de diagnóstico são frequentemente salvos usando [o Armazenamento de Arquivos Do Azure](../storage/files/storage-files-deployment-guide.md) e compartilhados com os registros do Monitor do [Azure](../azure-monitor/log-query/get-started-portal.md). Ambas as opções podem ser selecionadas.

>[!TIP]
>Use **logs de diagnóstico** para obter insights sobre operações de recursos.

## <a name="azure-monitor-and-log-analytics"></a>Log Analytics e Azure Monitor

Aplicativos de IoT unem diferentes recursos, dispositivos, locais e dados em um só local. O registro em log refinado fornece informações detalhadas sobre cada parte, serviço ou componente específicos da arquitetura geral do aplicativo, mas uma visão geral unificada costuma ser necessária para manutenção e depuração.

O Azure Monitor inclui o poderoso serviço de análise de log, que permite que as fontes de registro sejam visualizadas e analisadas em um único local. O Azure Monitor, portanto, é muito útil para analisar logs de dentro de aplicativos sofisticados de IoT.

Os exemplos de uso incluem:

* Consulta a várias históricos de log de diagnóstico
* Visualizar os logs para várias funções definidas pelo usuário
* Exibir os logs para dois ou mais serviços dentro de um período de tempo específico

A consulta completa de log é fornecida através [de logs do Monitor Do Azure](../azure-monitor/log-query/log-query-overview.md). Para configurar esses recursos avançados:

1. No portal do Microsoft Azure, pesquise **Log Analytics**.
1. As **instâncias** disponíveis do espaço de trabalho do Log Analytics serão exibidas. Para consultar, escolha um e selecione **Logs**:

    [![Log Analytics](media/how-to-configure-monitoring/log-analytics.png)](media/how-to-configure-monitoring/log-analytics.png#lightbox)

1. Se você ainda não tiver uma instância **de espaço de trabalho do Log Analytics,** você pode criar um espaço de trabalho selecionando o botão **Adicionar:**

    [![Criar OMS](media/how-to-configure-monitoring/log-analytics-oms.png)](media/how-to-configure-monitoring/log-analytics-oms.png#lightbox)

Uma vez que a instância **do espaço de trabalho do Log Analytics** seja provisionada, você pode usar consultas poderosas para encontrar entradas em registros de múltiplos ou pesquisar usando critérios específicos usando o Log **Management**:

   [![Gerenciamento de Log](media/how-to-configure-monitoring/log-analytics-management.png)](media/how-to-configure-monitoring/log-analytics-management.png#lightbox)

Para obter mais informações sobre operações de consulta poderosas, leia [como começar com as consultas](../azure-monitor/log-query/get-started-queries.md).

> [!NOTE]
> Você pode sofrer um atraso de 5 minutos ao enviar eventos para o **espaço de trabalho do Log Analytics** pela primeira vez.

Os registros do Monitor do Azure também fornecem poderosos serviços de notificação de erros e alertas, que podem ser visualizados selecionando **Diagnóstico e resolver problemas**:

   [![Notificações de alerta e erro](media/how-to-configure-monitoring/log-analytics-notifications.png)](media/how-to-configure-monitoring/log-analytics-notifications.png#lightbox)

>[!TIP]
>Use **o espaço de trabalho do Log Analytics** para consultar históricos de log de consulta para várias funcionalidades, assinaturas ou serviços do aplicativo.

## <a name="other-options"></a>Outras opções

Os Gêmeos Digitais do Azure também dão suporte a registro em log específico de aplicativo e auditoria de segurança. Para obter uma visão geral completa de todas as opções de registro do Azure disponíveis na instância do Azure Digital Twins, leia o artigo de auditoria de log do [Azure.](../security/fundamentals/log-audit.md)

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre o [Log de Atividades](../azure-monitor/platform/platform-logs-overview.md) do Azure.

- Aprofunde-se nas configurações de diagnóstico do Azure lendo uma [Visão geral dos logs de diagnóstico](../azure-monitor/platform/platform-logs-overview.md).

- Leia mais sobre [os registros do Azure Monitor](../azure-monitor/log-query/get-started-portal.md).
