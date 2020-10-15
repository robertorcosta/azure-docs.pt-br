---
title: Desativação de API Azure Monitor
description: Descreve a desativação de versões mais antigas da API do provedor de recursos OperationalInsights.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/02/2020
ms.openlocfilehash: a03b7bd63385b7cc16e4dafb5cd12ccd212dc959
ms.sourcegitcommit: 30505c01d43ef71dac08138a960903c2b53f2499
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/15/2020
ms.locfileid: "92089376"
---
# <a name="operationalinsights-api-version-retirement"></a>Desativação da versão da API do OperationalInsights
A Microsoft fornece notificações com pelo menos 12 meses de antecedência para desativar uma API a fim de suavizar a transição para uma versão mais recente/com suporte. Lançamos uma nova versão (2020-08-01) para as APIs do provedor de recursos **OperationalInsights** e desativaremos todas as versões anteriores da API em 31 de outubro de 2023.

Incentivamos você a começar a usar a versão 2020-08-01 agora para obter os benefícios da nova funcionalidade, como [cluster dedicado](https://docs.microsoft.com/azure/azure-monitor/log-query/logs-dedicated-clusters), [chaves gerenciadas pelo cliente](https://docs.microsoft.com/azure/azure-monitor/platform/customer-managed-keys), [link privado](https://docs.microsoft.com/azure/azure-monitor/platform/private-link-security) e [exportação de dados](https://docs.microsoft.com/azure/azure-monitor/platform/logs-data-export). Além disso, novos recursos e funcionalidades e otimizações são adicionados somente à API atual.

Após 31 de outubro de 2023 Azure Monitor não dará mais suporte a versões anteriores de APIs do que 2020-08-01. Se você preferir não atualizar, as solicitações enviadas de versões anteriores continuarão sendo servidas pelo serviço de Azure Monitor até 31 de outubro de 2023.

## <a name="migration-steps"></a>Etapas da migração
Dependendo do método de configuração usado, você deve atualizar a nova versão em solicitações **REST** e modelos do **Resource Manager**. Siga os exemplos abaixo para atualizar a versão da API:

1. As solicitações da API REST usam a versão da API na URL da solicitação. Substitua essa versão pela versão mais recente (2020-08-01), conforme mostrado no exemplo a seguir.

    ```rest
    https://management.azure.com/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}?api-version=2020-08-01
    ```

2. Os modelos de Azure Resource Manager usam a versão da API na propriedade **apiVersion** do recurso. Substitua essa versão pela versão mais recente (2020-08-01), conforme mostrado no exemplo a seguir.


    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2019-08-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "workspaceName": {
                "type": "string",
                "metadata": {
                "description": "Name of the workspace."
                }
            },
            "resources": [
            {
                "type": "Microsoft.OperationalInsights/workspaces",
                "name": "[parameters('workspaceName')]",
                "apiVersion": "2020-08-01",
                "location": "westus",
                "properties": {
                    "sku": {
                        "name": "pergb2018"
                    },
                    "retentionInDays": 30,
                    "features": {
                        "searchVersion": 1,
                        "legacy": 0,
                        "enableLogAccessUsingOnlyResourcePermissions": true
                    }
                }
            }
        ]
    }
    }
    ```


## <a name="next-steps"></a>Próximas etapas

- Consulte a [referência para a API do espaço de trabalho OperationalInsights](https://docs.microsoft.com/rest/api/loganalytics/workspaces).
