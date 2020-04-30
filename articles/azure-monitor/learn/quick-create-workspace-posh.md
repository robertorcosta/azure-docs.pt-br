---
title: Criar um espaço de trabalho do Log Analytics usando o Azure PowerShell | Microsoft Docs
description: Saiba como criar um espaço de trabalho do Log Analytics para habilitar as soluções de gerenciamento e a coleta de dados da sua nuvem e dos ambientes locais com o Azure PowerShell.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/12/2019
ms.openlocfilehash: 3547062f100eeb4dfa741a0566d737a0eac776a0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81605105"
---
# <a name="create-a-log-analytics-workspace-with-azure-powershell"></a>Criar um espaço de trabalho do Log Analytics com o Azure PowerShell

O módulo do Azure PowerShell é usado para criar e gerenciar recursos do Azure da linha de comando do PowerShell ou em scripts. Este início rápido mostra como usar o módulo do Azure PowerShell para implantar um espaço de trabalho do Log Analytics no Azure Monitor. Um espaço de trabalho do Log Analytics é um ambiente exclusivo para dados de log do Azure Monitor. Cada workspace tem seu próprio repositório de dados e configuração. As fontes de dados e as soluções são configuradas para armazenar seus dados em um determinado workspace. Você precisa de um espaço de trabalho do Log Analytics se pretender coletar dados das seguintes fontes:

* Recursos do Azure em sua assinatura  
* Computadores locais monitorados pelo System Center Operations Manager  
* Coleções de dispositivos de Configuration Manager  
* Dados de diagnóstico ou de log do armazenamento do Azure  
 
Para outras fontes, como as VMs do Azure e as VMs do Windows ou do Linux em seu ambiente, veja os tópicos a seguir:

* [Coletar dados de máquinas virtuais do Azure](../learn/quick-collect-azurevm.md)
* [Coletar dados de um computador Linux híbrido](../learn/quick-collect-linux-computer.md)
* [Coletar dados de um computador Windows híbrido](quick-collect-windows-computer.md)

Se você não tiver uma assinatura do Azure, crie [uma conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se você optar por instalar e usar o PowerShell localmente, este tutorial exigirá o módulo Azure PowerShell AZ. Execute `Get-Module -ListAvailable Az` para encontrar a versão. Se você precisa fazer a atualização, confira [Instalar o módulo do Azure PowerShell](/powershell/azure/install-az-ps). Se você estiver executando o PowerShell localmente, também precisará executar o `Connect-AzAccount` para criar uma conexão com o Azure.

## <a name="create-a-workspace"></a>Criar um workspace
Crie um espaço de trabalho com [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment). O exemplo a seguir cria um espaço de trabalho no local *eastus* usando um modelo do Resource Manager do computador local. O modelo JSON está configurado para solicitar apenas o nome do workspace e especifica um valor padrão para os outros parâmetros que provavelmente seriam usados como uma configuração padrão em seu ambiente. 

Para obter informações sobre regiões com suporte, consulte [regiões log Analytics está disponível no](https://azure.microsoft.com/regions/services/) e procure por Azure monitor no campo **Pesquisar um produto** . 

Os parâmetros a seguir definem um valor padrão:

* local – assume por padrão o valor Leste dos EUA
* SKU – assume por padrão o novo tipo de preço por GB lançado no modelo de preço de abril de 2018

>[!WARNING]
>Se criar ou configurar um espaço de trabalho do Log Analytics em uma assinatura que tiver aceitado o novo modelo de preços de abril de 2018, o único tipo de preço válido do Log Analytics **PerGB2018**. 
>

### <a name="create-and-deploy-template"></a>Criar e implantar modelo

1. Copie e cole a seguinte sintaxe JSON em seu arquivo:

    ```json
    {
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceName": {
            "type": "String",
            "metadata": {
              "description": "Specifies the name of the workspace."
            }
        },
        "location": {
            "type": "String",
            "allowedValues": [
              "eastus",
              "westus"
            ],
            "defaultValue": "eastus",
            "metadata": {
              "description": "Specifies the location in which to create the workspace."
            }
        },
        "sku": {
            "type": "String",
            "allowedValues": [
              "Standalone",
              "PerNode",
              "PerGB2018"
            ],
            "defaultValue": "PerGB2018",
            "metadata": {
            "description": "Specifies the service tier of the workspace: Standalone, PerNode, Per-GB"
        }
          }
    },
    "resources": [
        {
            "type": "Microsoft.OperationalInsights/workspaces",
            "name": "[parameters('workspaceName')]",
            "apiVersion": "2015-11-01-preview",
            "location": "[parameters('location')]",
            "properties": {
                "sku": {
                    "Name": "[parameters('sku')]"
                },
                "features": {
                    "searchVersion": 1
                }
            }
          }
       ]
    }
    ```

2. Edite o modelo para atender às suas necessidades. Revisão de referência[Microsoft.OperationalInsights/workspaces](https://docs.microsoft.com/azure/templates/microsoft.operationalinsights/2015-11-01-preview/workspaces) para saber quais propriedades e os valores são suportados. 
3. Salve esse arquivo como **deploylaworkspacetemplate.json** para uma pasta local.   
4. Você está pronto para implantar o modelo. Use os comandos a seguir da pasta que contém o modelo. Quando for solicitado um nome de espaço de trabalho, forneça um nome que seja globalmente exclusivo em todas as assinaturas do Azure.

    ```powershell
        New-AzResourceGroupDeployment -Name <deployment-name> -ResourceGroupName <resource-group-name> -TemplateFile deploylaworkspacetemplate.json
    ```

A implantação pode levar alguns minutos para ser concluída. Quando ela for concluída, você verá uma mensagem semelhante que inclui o resultado:

![Resultados de exemplo, quando a implantação for concluída](media/quick-create-workspace-posh/template-output-01.png)

## <a name="next-steps"></a>Próximas etapas
Agora que já tem um workspace disponível, você pode configurar a coleta de monitoramento de telemetria, executar pesquisas de logs para analisar os dados e adicionar uma solução de gerenciamento para fornecer dados adicionais e informações analíticas.  

* Para habilitar a coleta de dados de recursos do Azure com o Diagnóstico do Azure ou com o armazenamento do Azure, confira [Coletar logs e as métricas do serviço do Azure para uso no Azure Monitor](../platform/collect-azure-metrics-logs.md).  
* Adicione o [System Center Operations Manager como uma fonte de dados](../platform/om-agents.md) para coletar dados de agentes que se reportam ao seu grupo de gerenciamento do Operations Manager e armazene-o em seu repositório de espaço de trabalho do Log Analytics.  
* Conecte-se ao [Configuration Manager](../platform/collect-sccm.md) para importar computadores que são membros de coleções na hierarquia.  
* Examine as [soluções de monitoramento](../insights/solutions.md) disponíveis e como adicionar ou remover uma solução do workspace.
