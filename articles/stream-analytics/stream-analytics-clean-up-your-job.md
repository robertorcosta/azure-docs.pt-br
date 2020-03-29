---
title: Limpar o trabalho do Azure Stream Analytics
description: Este artigo mostra diferentes métodos para excluir trabalhos do Azure Stream Analytics.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.custom: seodec18
ms.openlocfilehash: d99920417f20034da1001a821c02376ac19274d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75426477"
---
# <a name="stop-or-delete-your-azure-stream-analytics-job"></a>Pare ou exclua seu trabalho do Azure Stream Analytics

Os trabalhos do Azure Stream Analytics podem ser facilmente interrompidos ou excluídos através do portal Azure, Azure PowerShell, Azure SDK para .Net ou Rest API. Um trabalho do Stream Analytics não pode ser recuperado depois de excluído.

>[!NOTE] 
>Quando o trabalho do Azure Stream Analytics é interrompido, os dados persistem somente no armazenamento de entrada e saída, como no Hubs de Eventos ou no Banco de Dados SQL do Azure. Se for necessário para remover dados do Azure, lembre-se de seguir o processo de remoção para os recursos de entrada e saída do seu trabalho do Stream Analytics.

## <a name="stop-a-job-in-azure-portal"></a>Parar um trabalho no portal do Azure

Quando você para um trabalho, os recursos são desprovisionados e ele pára de processar eventos. As acusações relacionadas a este trabalho também estão paradas. No entanto, toda a sua configuração é mantida e você pode reiniciar o trabalho mais tarde 

1. Faça login no [portal Azure](https://portal.azure.com). 

2. Localize o trabalho em execução do Stream Analytics e selecione-o.

3. Na página do trabalho no Stream Analytics, selecione **Parar** para interrompê-lo. 

   ![Interromper um trabalho do Azure Stream Analytics](./media/stream-analytics-clean-up-your-job/stop-stream-analytics-job.png)


## <a name="delete-a-job-in-azure-portal"></a>Excluir um trabalho no portal do Azure

>[!WARNING] 
>Um trabalho do Stream Analytics não pode ser recuperado depois de excluído.

1. Entre no portal do Azure. 

2. Localize o trabalho existente do Stream Analytics e selecione-o.

3. Na página do trabalho no Stream Analytics, selecione **Excluir** para excluí-lo. 

   ![Excluir um trabalho do Azure Stream Analytics](./media/stream-analytics-clean-up-your-job/delete-stream-analytics-job.png)


## <a name="stop-or-delete-a-job-using-powershell"></a>Parar ou excluir um trabalho usando o PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Para impedir um trabalho usando o PowerShell, use o [cmdlet Stop-AzStreamAnalyticsJob.](https://docs.microsoft.com/powershell/module/az.streamanalytics/stop-azstreamanalyticsjob) Para excluir um trabalho usando o PowerShell, use o [cmdlet Remove-AzStreamAnalyticsJob.](https://docs.microsoft.com/powershell/module/az.streamanalytics/Remove-azStreamAnalyticsJob)

## <a name="stop-or-delete-a-job-using-azure-sdk-for-net"></a>Parar ou excluir um trabalho usando o SDK do Azure para .NET

Para interromper um trabalho usando o SDK do Azure para .NET, use o método [StreamingJobsOperationsExtensions.BeginStop](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.streamanalytics.streamingjobsoperationsextensions.beginstop?view=azure-dotnet). Para excluir um trabalho usando o SDK do Azure para .NET, use o método [StreamingJobsOperationsExtensions.BeginDelete](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.streamanalytics.streamingjobsoperationsextensions.begindelete?view=azure-dotnet).

## <a name="stop-or-delete-a-job-using-rest-api"></a>Parar ou excluir um trabalho usando a API REST

Para interromper um trabalho usando a API REST, confira o método [Parar](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-job#stop). Para excluir um trabalho usando a API REST, confira o método [Excluir](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-job#delete).
