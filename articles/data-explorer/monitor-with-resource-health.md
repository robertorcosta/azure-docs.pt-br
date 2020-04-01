---
title: Monitore o Azure Data Explorer usando o Resource Health
description: Use o Azure Resource Health para monitorar os recursos do Azure Data Explorer.
author: orspod
ms.author: orspodek
ms.reviewer: prvavill
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/31/2020
ms.openlocfilehash: 9b8b1b40e972d7719ef2cf495ed12cb4ed3478fd
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/01/2020
ms.locfileid: "80479364"
---
# <a name="monitor-azure-data-explorer-using-resource-health-preview"></a>Monitore o Azure Data Explorer usando o Resource Health (Preview)

[O Resource Health](/azure/service-health/resource-health-overview) for Azure Data Explorer informa a saúde do seu recurso Azure Data Explorer e fornece recomendações acionáveis para solucionar problemas. A saúde dos recursos é atualizada a cada 1-2 minutos e relata a saúde atual e passada de seus recursos. 

A Resource Health determina a saúde do seu recurso Do Azure Data Explorer examinando várias verificações de status de saúde, tais como:
* Disponibilidade de recursos
* Falhas de consulta

## <a name="access-resource-health-reporting"></a>Relatórios de saúde de recursos de acesso

1. No [portal Azure,](https://portal.azure.com/)selecione **Monitor** na lista de serviços.
1. Selecione **o Serviço de Saúde** > **Recursos de Saúde**.
1. Na **iscada por assinatura,** selecione sua assinatura. No **tipo de redrop** down do tipo Recurso, selecione **Azure Data Explorer**.
1. A tabela resultante lista todos os recursos da assinatura escolhida. Cada recurso terá um ícone do estado de saúde indicando a saúde dos recursos.
1. Selecione seu recurso para visualizar seu status de saúde de [recursos](#resource-health-status) e recomendações.

    ![Visão geral](media/monitor-with-resource-health/resource-health-overview.png)

## <a name="resource-health-status"></a>Status de integridade de recurso

A saúde de um recurso é exibida com um dos seguintes status, disponível, indisponível e desconhecido.

### <a name="available"></a>Disponível

Um status de saúde do **Available** indica que o recurso do Azure Data Explorer é saudável e não tem problemas.

![Disponível](media/monitor-with-resource-health/available.png)

### <a name="unavailable"></a>Indisponível

Um status de saúde de **Indisponível** indica que há um problema em andamento com o recurso Do Azure Data Explorer que faz com que ele não esteja disponível para consultas e ingestão. Por exemplo, os nós no recurso Do Azure Data Explorer podem ter sido reiniciados inesperadamente. Se o recurso do Azure Data Explorer permanecer neste estado por um longo período de tempo, entre em contato [com o suporte]().

![Indisponível](media/monitor-with-resource-health/unavailable.png)

### <a name="unknown"></a>Unknown (desconhecido)

Um estado de saúde do **Unknown** indica que **a Resource Health** não recebeu informações sobre este recurso do Azure Data Explorer por mais de 10 minutos. Esse status não é uma indicação definitiva da saúde dos recursos do Azure Data Explorer, mas é um importante ponto de dados no processo de solução de problemas. Se o cluster Azure Data Explorer estiver funcionando como esperado, o status mudará para **Disponível** dentro de alguns minutos. O **estado de** saúde desconhecido pode sugerir que um evento na plataforma está afetando o recurso. 

> [!TIP]
> A saúde dos recursos do cluster Azure Data Explorer será **desconhecida** se estiver em estado parado.

![Unknown (desconhecido)](media/monitor-with-resource-health/unknown.png)

## <a name="historical-information"></a>Informações de histórico

No **painel saúde de recursos** > histórico de **saúde**, acesso a até quatro semanas de informações sobre o estado de saúde dos recursos. Selecione a seta para obter informações adicionais sobre os problemas de eventos de saúde relatados neste painel. 

![Histórico](media/monitor-with-resource-health/healthhistory.png)

## <a name="next-steps"></a>Próximas etapas

* [Configuração de alertas de saúde de recursos](https://docs.microsoft.com/azure/service-health/resource-health-alert-arm-template-guide)
* [Tutorial: Inserie e consulte dados de monitoramento no Azure Data Explorer](ingest-data-no-code.md)
* [Monitore o desempenho, a saúde e o uso do Azure Data Explorer com métricas](using-metrics.md)