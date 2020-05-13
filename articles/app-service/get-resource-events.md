---
title: Obter eventos de recurso no serviço Azure App
description: Saiba como obter eventos de recurso por meio de logs de atividade e da grade de eventos em seu aplicativo do serviço de aplicativo.
ms.topic: article
ms.date: 04/24/2020
ms.author: msangapu
ms.openlocfilehash: 1fd283f95823a67319dc467a3a1d6251193182da
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83124730"
---
# <a name="get-resource-events-in-azure-app-service"></a>Obter eventos de recurso no serviço Azure App

Azure App serviço fornece ferramentas internas para monitorar o status e a integridade de seus recursos. Os eventos de recurso ajudam você a entender as alterações que foram feitas em seus recursos de aplicativo Web subjacentes e tomar medidas conforme necessário. Os exemplos de evento incluem: dimensionamento de instâncias, atualizações para configurações de aplicativo, reinicialização do aplicativo Web e muito mais. Neste artigo, você aprenderá a exibir [os logs de atividades do Azure](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view) e a habilitar a [grade de eventos](https://docs.microsoft.com/azure/event-grid/) para monitorar eventos de recursos relacionados ao seu aplicativo Web do serviço de aplicativo.

> [!NOTE]
> A integração do serviço de aplicativo com a grade de eventos está em versão **prévia**. [Exiba o comunicado para obter mais detalhes.](https://aka.ms/app-service-event-grid-announcement)
>

## <a name="view-azure-activity-logs"></a>Exibir logs de atividades do Azure
Os logs de atividades do Azure contêm eventos de recursos emitidos por operações executadas nos recursos em sua assinatura. As ações do usuário nos modelos portal do Azure e Azure Resource Manager contribuem para os eventos capturados pelo log de atividades. 

Logs de atividades do Azure para detalhes do serviço de aplicativo, como:
- quais operações foram executadas nos recursos (por exemplo: planos do serviço de aplicativo)
- quem iniciou a operação
- quando a operação ocorreu
- o status da operação
- valores de propriedade para ajudá-lo a Pesquisar a operação

### <a name="what-can-you-do-with-azure-activity-logs"></a>O que você pode fazer com os logs de atividades do Azure?

Os logs de atividades do Azure podem ser consultados usando o portal do Azure, o PowerShell, a API REST ou a CLI. Você pode enviar os logs para uma conta de armazenamento, Hub de eventos e Log Analytics. Você também pode analisá-los em Power BI ou criar alertas para permanecer atualizados em eventos de recursos.

[Exibir e recuperar eventos do log de atividades do Azure.](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view)

## <a name="ship-activity-logs-to-event-grid"></a>Enviar logs de atividade para a grade de eventos

Embora os logs de atividade sejam baseados no usuário, há uma nova integração da [grade de eventos](https://docs.microsoft.com/azure/event-grid/) com o serviço de aplicativo (versão prévia) que registra as ações do usuário e os eventos automatizados. Com a grade de eventos, você pode configurar um manipulador para reagir aos eventos citados. Por exemplo, use a Grade de Eventos para disparar instantaneamente uma função sem servidor para executar a análise de imagem sempre que uma nova foto é adicionada a um contêiner de armazenamento de blobs.

Como alternativa, você pode usar a Grade de Eventos com Aplicativos Lógicos para processar dados em qualquer lugar, sem escrever código. A Grade de Eventos conecta fontes de dados e manipuladores de eventos. Por exemplo, use a Grade de Eventos para disparar instantaneamente uma função sem servidor para executar a análise de imagem sempre que uma nova foto é adicionada a um contêiner de armazenamento de blobs.

### <a name="supported-event-types"></a>Tipos de eventos com suporte
| Tipo de evento |Descrição|
| -----------| ------------- |
| Microsoft.web/sites | Webapp |
| BackupOperationCompleted |O backup do webapp foi concluído com êxito|
| BackupOperationFailed | Falha no backup do webapp|
| RestoreOperationStarted |A restauração do backup foi iniciada|
| RestoreOperationCompleted |Restauração do backup concluída com êxito|
| RestoreOperationFailed |Falha na restauração do backup|
| SlotSwapStarted |A permuta do slot foi iniciada|
| SlotSwapCompleted |Permuta de slot concluída com êxito|
| SlotSwapFailed |Falha na permuta de slot|
| SlotSwapWithPreviewStarted |A permuta de slot com a visualização foi iniciada|
| SlotSwapWithPreviewCancelled |Falha na permuta de slot com visualização|
| AppUpdated | |
| Reiniciado | O webapp foi reiniciado |
| Parado | O webapp foi interrompido |
| ChangedAppSettings | As configurações do aplicativo no WebApp foram alteradas |
| - | - |
| Microsoft. Web/serverfarms | (Plano do serviço de aplicativo) |
| AspUpdated | O plano do serviço de aplicativo foi atualizado. O objeto de evento contém detalhes sobre as propriedades que foram alteradas. |
| Aumentar/reduzir | O plano do serviço de aplicativo foi dimensionado ou reduzido verticalmente. O objeto de evento contém a contagem de instâncias.|


## <a name="next-steps"></a><a name="nextsteps"></a> Próximas etapas
* [Logs de consulta com Azure Monitor](../azure-monitor/log-query/log-query-overview.md)
* [Como monitorar aplicativos no Serviço de Aplicativo do Azure](web-sites-monitor.md)
* [Solucionar problemas de Serviço de Aplicativo do Azure no Visual Studio](troubleshoot-dotnet-visual-studio.md)
* [Analisar logs de aplicativos no HDInsight](https://gallery.technet.microsoft.com/scriptcenter/Analyses-Windows-Azure-web-0b27d413)
