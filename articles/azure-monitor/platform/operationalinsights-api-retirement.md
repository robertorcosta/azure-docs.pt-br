---
title: Desativação de API Azure Monitor
description: Descreve a desativação de versões mais antigas da API do provedor de recursos OperationalInsights.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/29/2020
ms.openlocfilehash: e2b12d7a2206ab369328563af438c6ef1ea39327
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/26/2020
ms.locfileid: "96184103"
---
# <a name="operationalinsights-api-version-retirement"></a>Desativação da versão da API do OperationalInsights
A Microsoft fornece notificações com pelo menos 12 meses de antecedência para desativar uma API a fim de suavizar a transição para uma versão mais recente/com suporte. Lançamos uma nova versão (2020-08-01) para as APIs do provedor de recursos **OperationalInsights** e desativaremos todas as versões anteriores da API em 29 de fevereiro de 2024.

Incentivamos você a começar a usar a versão 2020-08-01 agora para obter os benefícios da nova funcionalidade, como [cluster dedicado](../log-query/logs-dedicated-clusters.md), [chaves gerenciadas pelo cliente](./customer-managed-keys.md), [link privado](./private-link-security.md) e [exportação de dados](./logs-data-export.md). Além disso, novos recursos e funcionalidades e otimizações são adicionados somente à API atual.

Após 29 de fevereiro de 2024 Azure Monitor não dará mais suporte a versões anteriores de APIs do que 2020-08-01. Se você preferir não atualizar, as solicitações enviadas de versões anteriores continuarão sendo servidas pelo serviço de Azure Monitor até 29 de fevereiro de 2024.

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

- Consulte a [referência para a API do espaço de trabalho OperationalInsights](/rest/api/loganalytics/workspaces).