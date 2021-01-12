---
title: Azure Monitor de solução de problemas de logs (versão prévia)
description: Use Azure Monitor para investigar rapidamente ou periodicamente problemas, solucionar problemas de código ou configuração ou atender a casos de suporte, que muitas vezes dependem da pesquisa de grandes volumes de dados para obter informações específicas.
author: osalzberg
ms.author: bwren
ms.reviewer: bwren
ms.subservice: logs
ms.topic: conceptual
ms.date: 12/29/2020
ms.openlocfilehash: b3d1ad2d327da60874d2d07ba697b8f5ab815189
ms.sourcegitcommit: 02b1179dff399c1aa3210b5b73bf805791d45ca2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/12/2021
ms.locfileid: "98127083"
---
# <a name="azure-monitor-troubleshooting-logs-preview"></a>Azure Monitor de solução de problemas de logs (versão prévia)
Use Azure Monitor para investigar rapidamente e/ou periodicamente problemas, solucionar problemas de código ou configuração ou atender a casos de suporte, que muitas vezes dependem da pesquisa de grandes volumes de dados para obter informações específicas.

>[!NOTE]
> * Os logs de solução de problemas estão no modo de visualização.
>* Entre em contato com a [equipe de log Analytics](mailto:orens@microsoft.com) com dúvidas ou para aplicar o recurso.
## <a name="troubleshoot-and-query-your-code-or-configuration-issues"></a>Solucionar problemas e consultar seu código ou problemas de configuração
Use Azure Monitor os logs de solução de problemas para buscar seus registros e investigar problemas e problemas de maneira mais simples e barata usando o KQL.
Os logs de solução de problemas decreesm seus encargos fornecendo recursos básicos para a solução de problemas.

> [!NOTE]
>* A decisão para o modo de solução de problemas é configurável.
>* Os logs de solução de problemas podem ser aplicados a tabelas específicas, atualmente em tabelas de "logs de contêiner" e "rastreamentos de aplicativo".
>* Há um período de retenção livre de 4 dias, pode ser estendido além do custo adicional.
>* Por padrão, as tabelas herdam a retenção do espaço de trabalho. Para evitar encargos adicionais, é recomendável alterar essas tabelas de retenção. [Clique aqui para saber como alterar a retenção da tabela](https://docs.microsoft.com//azure/azure-monitor/platform/manage-cost-storage).

## <a name="turn-on-troubleshooting-logs-on-your-tables"></a>Ativar os logs de solução de problemas em suas tabelas

Para ativar a solução de problemas de logs em seu espaço de trabalho, você precisa usar a chamada à API a seguir.
```http
PUT https://PortalURL/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}/tables/{tableName}

(With body in the form of a GET single table request response)

Response:

{
        "properties": {
          "retentionInDays": 40,
          "isTroubleshootingAllowed": true,
          "isTroubleshootEnabled": true
        },
        "id": "/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}/tables/{tableName}",
        "name": "{tableName}"
}
```
## <a name="check-if-the-troubleshooting-logs-feature-is-enabled-for-a-given-table"></a>Verificar se o recurso de logs de solução de problemas está habilitado para uma determinada tabela
Para verificar se o log de solução de problemas está habilitado para uma determinada tabela, você pode usar a seguinte chamada à API.

```http
GET https://PortalURL/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}/tables/{tableName}

Response: 
"properties": {
          "retentionInDays": 30,
          "isTroubleshootingAllowed": true,
          "isTroubleshootEnabled": true,
          "lastTroubleshootDate": "Thu, 19 Nov 2020 07:40:51 GMT"
        },
        "id": "/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/microsoft.operationalinsights/workspaces/{workspaceName}/tables/{tableName}",
        "name": " {tableName}"
                }

```
## <a name="check-if-the-troubleshooting-logs-feature-is-enabled-for-all-of-the-tables-in-a-workspace"></a>Verificar se o recurso de logs de solução de problemas está habilitado para todas as tabelas em um espaço de trabalho
Para verificar quais tabelas têm o log de solução de problemas habilitado, você pode usar a seguinte chamada à API.

```http
GET "https://PortalURL/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}/tables"

Response: 
{
          "properties": {
            "retentionInDays": 30,
            "isTroubleshootingAllowed": true,
            "isTroubleshootEnabled": true,
            "lastTroubleshootDate": "Thu, 19 Nov 2020 07:40:51 GMT"
          },
          "id": "/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/microsoft.operationalinsights/workspaces/{workspaceName}/tables/table1",
          "name": "table1"
 },
        {
          "properties": {
            "retentionInDays": 7,
            "isTroubleshootingAllowed": true
          },
          "id": "/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/microsoft.operationalinsights/workspaces/{workspaceName}/tables/table2",
          "name": "table2"
        },
        {
          "properties": {
            "retentionInDays": 7,
            "isTroubleshootingAllowed": false
          },
          "id": "/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/microsoft.operationalinsights/workspaces/{workspaceName}/tables/table3",
          "name": "table3"
        }
```
## <a name="turn-off-troubleshooting-logs-on-your-tables"></a>Desligar os logs de solução de problemas em suas tabelas

Para desativar os logs de solução de problemas em seu espaço de trabalho, você precisa usar a chamada à API a seguir.
```http
PUT https://PortalURL/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}/tables/{tableName}

(With body in the form of a GET single table request response)

Response:

{
        "properties": {
          "retentionInDays": 40,
          "isTroubleshootingAllowed": true,
          "isTroubleshootEnabled": false
        },
        "id": "/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}/tables/{tableName}",
        "name": "{tableName}"
}
```
>[!TIP]
>* Você pode usar qualquer ferramenta de API REST para executar os comandos. [Leia mais](https://docs.microsoft.com/rest/api/azure/)
>* Você precisa usar o token de portador para autenticação. [Leia mais](https://social.technet.microsoft.com/wiki/contents/articles/51140.azure-rest-management-api-the-quickest-way-to-get-your-bearer-token.aspx)

>[!NOTE]
>* O sinalizador "isTroubleshootingAllowed" – descreve se a tabela é permitida no serviço
>* O "isTroubleshootEnabled" indica se o recurso está habilitado para a tabela-pode ser ligado ou desligado (true ou false)
>* Ao desabilitar o sinalizador "isTroubleshootEnabled" para uma tabela específica, habilitá-la novamente é possível apenas uma semana após a data de habilitação anterior.
>* Atualmente, isso tem suporte apenas para tabelas em (alguns outros SKUs também terão suporte no futuro)- [Leia mais sobre preços](https://docs.microsoft.com/services-hub/health/azure_pricing).

## <a name="query-limitations-for-troubleshooting"></a>Limitações de consulta para solução de problemas
Há algumas limitações para uma tabela marcada como "logs de solução de problemas":
*   Obterá menos recursos de processamento e, portanto, não será adequado para painéis grandes, análises complexas ou muitas chamadas de API simultâneas.
*   As consultas são limitadas a um intervalo de tempo de dois dias.
* a limpeza não funcionará – [Leia mais sobre limpeza](https://docs.microsoft.com/rest/api/loganalytics/workspacepurge/purge).
* Não há suporte para alertas por meio deste serviço.
## <a name="next-steps"></a>Próximas etapas
* [Gravar consultas](https://docs.microsoft.com/azure/data-explorer/write-queries)


