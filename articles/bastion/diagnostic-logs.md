---
title: Habilite e trabalhe com registros de diagnóstico do Azure Bastion
description: Neste artigo, saiba como habilitar e trabalhar com registros de diagnóstico do Azure Bastion.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: cherylmc
ms.openlocfilehash: 2167a17d5d388c97ad357398c4ac2676e43be5a4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76989443"
---
# <a name="enable-and-work-with-bastion-diagnostic-logs"></a>Habilite e trabalhe com registros de diagnóstico bastion

À medida que os usuários se conectam a cargas de trabalho usando o Azure Bastion, o Bastion pode registrar diagnósticos das sessões remotas. Em seguida, você pode usar os diagnósticos para visualizar quais usuários conectados a quais cargas de trabalho, em que momento, de onde e outras informações de registro relevantes. Para usar os diagnósticos, você deve habilitar registros de diagnóstico no Azure Bastion. Este artigo ajuda você a habilitar registros de diagnósticoe, em seguida, visualizar os registros.

## <a name="enable-the-diagnostics-log"></a><a name="enable"></a>Habilite o registro de diagnóstico

1. No [portal Azure,](https://portal.azure.com)navegue até o recurso Do Azure Bastion e selecione **configurações** de Diagnóstico na página Azure Bastion.

   ![configurações de diagnóstico](./media/diagnostic-logs/1diagnostics-settings.png)
2. Selecione **Configurações de diagnóstico**e selecione **+Adicionar configuração de diagnóstico** para adicionar um destino para os logs.

   ![adicionar configuração de diagnóstico](./media/diagnostic-logs/2add-diagnostic-setting.png)
3. Na página **Configurações de Diagnóstico,** selecione o tipo de conta de armazenamento a ser usada para armazenar registros de diagnósticos.

   ![selecionar local de armazenamento](./media/diagnostic-logs/3add-storage-account.png)
4. Quando você concluir as configurações, ele será semelhante a este exemplo:

   ![configurações exemplo](./media/diagnostic-logs/4example-settings.png)

## <a name="view-diagnostics-log"></a><a name="view"></a>Exibir registro de diagnóstico

Para acessar seus registros de diagnóstico, você pode usar diretamente a conta de armazenamento que você especificou ao ativar as configurações de diagnóstico.

1. Navegue até o recurso da conta de armazenamento e, em seguida, para **Contêineres**. Você vê o **blob insights-logs-bastionauditlogs** criado no contêiner blob da sua conta de armazenamento.

   ![configurações de diagnóstico](./media/diagnostic-logs/1-navigate-to-logs.png)
2. Ao navegar para dentro do contêiner, você vê várias pastas em seu blog. Essas pastas indicam a hierarquia de recursos para o recurso Do Azure Bastion.

   ![adicionar configuração de diagnóstico](./media/diagnostic-logs/2-resource-h.png)
3. Navegue até a hierarquia completa do recurso Azure Bastion, cujos registros de diagnósticos você deseja acessar/visualizar. Os 'y=', 'm=', 'd=', 'h=' e 'm=' indicam o ano, mês, dia, hora e minuto, respectivamente, para os registros diagnósticos.

   ![selecionar local de armazenamento](./media/diagnostic-logs/3-resource-location.png)
4. Localize o arquivo json criado pelo Azure Bastion que contém os dados de registro de diagnóstico para o período de tempo navegado.

5. Baixe o arquivo json do seu recipiente de bolha de armazenamento. Uma entrada de exemplo do arquivo json é mostrada abaixo para referência:

   ```json
   { 
   "time":"2019-10-03T16:03:34.776Z",
   "resourceId":"/SUBSCRIPTIONS/<subscripionID>/RESOURCEGROUPS/MYBASTION/PROVIDERS/MICROSOFT.NETWORK/BASTIONHOSTS/MYBASTION-BASTION",
   "operationName":"Microsoft.Network/BastionHost/connect",
   "category":"BastionAuditLogs",
   "level":"Informational",
   "location":"eastus",
   "properties":{ 
      "userName":"<username>",
      "userAgent":"Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/77.0.3865.90 Safari/537.36",
      "clientIpAddress":"131.107.159.86",
      "clientPort":24039,
      "protocol":"ssh",
      "targetResourceId":"/SUBSCRIPTIONS/<subscripionID>/RESOURCEGROUPS/MYBASTION/PROVIDERS/MICROSOFT.COMPUTE/VIRTUALMACHINES/LINUX-KEY",
      "subscriptionId":"<subscripionID>",
      "message":"Successfully Connected.",
      "resourceType":"VM",
      "targetVMIPAddress":"172.16.1.5",
      "tunnelId":"<tunnelID>"
   },
   "FluentdIngestTimestamp":"2019-10-03T16:03:34.0000000Z",
   "Region":"eastus",
   "CustomerSubscriptionId":"<subscripionID>"
   }
   ```

## <a name="next-steps"></a>Próximas etapas

Leia o [Bastião FAQ](bastion-faq.md).
