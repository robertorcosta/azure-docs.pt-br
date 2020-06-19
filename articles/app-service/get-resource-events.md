---
title: Obter eventos de recurso no Serviço de Aplicativo do Azure
description: Saiba como obter eventos de recurso por meio de Logs de Atividades e da Grade de Eventos em seu Serviço de Aplicativo.
ms.topic: article
ms.date: 04/24/2020
ms.author: msangapu
ms.openlocfilehash: 7075e3eacc85198e22a9aa0e53f67a22416b2678
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83649061"
---
# <a name="get-resource-events-in-azure-app-service"></a>Obter eventos de recurso no Serviço de Aplicativo do Azure

O Serviço de Aplicativo do Azure fornece ferramentas internas para monitorar o status e a integridade de seus recursos. Os eventos de recurso ajudam você a entender as alterações que foram feitas em seus recursos de aplicativo Web subjacentes e a tomar medidas, conforme o necessário. Os exemplos de evento incluem: dimensionamento de instâncias, atualizações para configurações de aplicativo, reinicialização do aplicativo Web e muito mais. Neste artigo, você aprenderá como exibir [Logs de Atividades do Azure](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view) e habilitar a [Grade de Eventos](https://docs.microsoft.com/azure/event-grid/) para monitorar eventos de recursos relacionados a seu aplicativo Web do Serviço de Aplicativo.

> [!NOTE]
> A integração do Serviço de Aplicativo à Grade de Eventos está na **versão prévia**. [Exiba o comunicado para obter mais detalhes.](https://aka.ms/app-service-event-grid-announcement)
>

## <a name="view-azure-activity-logs"></a>Exibir Logs de Atividades do Azure
Os Logs de Atividades do Azure contêm eventos de recursos emitidos por operações executadas nos recursos em sua assinatura. As ações do usuário nos modelos do Azure Resource Manager e do portal do Azure contribuem para os eventos capturados pelo Log de Atividades. 

Detalhes do Serviço de Aplicativo do Log de Atividades do Azure, como:
- quais operações foram executadas nos recursos (por exemplo: Planos do Serviço de Aplicativo)
- quem iniciou a operação
- quando a operação ocorreu
- o status da operação
- valores de propriedade para ajudar a pesquisar a operação

### <a name="what-can-you-do-with-azure-activity-logs"></a>O que você pode fazer com os Logs de Atividades do Azure?

Os Logs de Atividades do Azure podem ser consultados usando o portal do Azure, o PowerShell, a API REST ou a CLI. Você pode enviar os logs para uma conta de armazenamento, um Hub de eventos e para o Log Analytics. Você também pode analisá-los no Power BI ou criar alertas para se manter atualizado sobre eventos de recursos.

[Exibir e recuperar eventos do Log de atividades do Azure.](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view)

## <a name="ship-activity-logs-to-event-grid"></a>Enviar Logs de Atividades para a Grade de Eventos

Embora os Logs de Atividades sejam baseados no usuário, há uma nova integração da [Grade de Eventos](https://docs.microsoft.com/azure/event-grid/) com o Serviço de Aplicativo (versão prévia) que registra as ações do usuário e os eventos automatizados. Com a Grade de Eventos, você pode configurar um manipulador para reagir a esses eventos. Por exemplo, use a Grade de Eventos para disparar instantaneamente uma função sem servidor para executar a análise de imagem sempre que uma nova foto é adicionada a um contêiner de armazenamento de blobs.

Como alternativa, você pode usar a Grade de Eventos com Aplicativos Lógicos para processar dados em qualquer lugar, sem escrever código. A Grade de Eventos conecta fontes de dados e manipuladores de eventos. Por exemplo, use a Grade de Eventos para disparar instantaneamente uma função sem servidor para executar a análise de imagem sempre que uma nova foto é adicionada a um contêiner de armazenamento de blobs.

[Exibir as propriedades e o esquema dos Eventos do Serviço de Aplicativo do Azure.](https://docs.microsoft.com/azure/event-grid/event-schema-app-service)

## <a name="next-steps"></a><a name="nextsteps"></a> Próximas etapas
* [Consultar logs com o Azure Monitor](../azure-monitor/log-query/log-query-overview.md)
* [Como monitorar aplicativos no Serviço de Aplicativo do Azure](web-sites-monitor.md)
* [Solucionar problemas de Serviço de Aplicativo do Azure no Visual Studio](troubleshoot-dotnet-visual-studio.md)
* [Analisar logs de aplicativos no HDInsight](https://gallery.technet.microsoft.com/scriptcenter/Analyses-Windows-Azure-web-0b27d413)
