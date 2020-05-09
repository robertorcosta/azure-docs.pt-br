---
title: Usar modelos de Azure Resource Manager para criar uma conta de automação | Microsoft Docs
description: Você pode usar um modelo de Azure Resource Manager para criar uma conta de automação do Azure.
ms.service: automation
ms.subservice: update-management
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 04/24/2020
ms.openlocfilehash: 19aee9d5fdf3f4a3d74484bb7cb2e609bc2807b4
ms.sourcegitcommit: a6d477eb3cb9faebb15ed1bf7334ed0611c72053
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/08/2020
ms.locfileid: "82927847"
---
# <a name="create-an-automation-account-by-using-an-azure-resource-manager-template"></a>Criar uma conta de automação usando um modelo de Azure Resource Manager

Você pode usar [modelos de Azure Resource Manager](../azure-resource-manager/templates/template-syntax.md) para criar uma conta de automação do Azure em seu grupo de recursos. Este artigo fornece um modelo de exemplo que:

* Automatiza a criação de um Azure Monitor espaço de trabalho Log Analytics.
* Automatiza a criação de uma conta de automação do Azure.
* Vincula a conta de automação ao espaço de trabalho Log Analytics.

O modelo não automatiza a integração de máquinas virtuais ou soluções do Azure ou não Azure. 

>[!NOTE]
>Não há suporte para a criação da conta Executar como de automação quando você está usando um modelo de Azure Resource Manager. Para criar uma conta Executar como manualmente no portal ou com o PowerShell, consulte [gerenciar contas Executar como](manage-runas-account.md).

## <a name="api-versions"></a>Versões de API

A tabela a seguir lista a versão de API para os recursos usados neste exemplo.

| Recurso | Tipo de recurso | Versão da API |
|:---|:---|:---|
| Workspace | workspaces | 2017-03-15-preview |
| Conta de automação | automação | 31-10-2015 | 

## <a name="before-you-use-the-template"></a>Antes de usar o modelo

Se você optar por instalar e usar o PowerShell localmente, este artigo exigirá o módulo Azure PowerShell AZ. Execute `Get-Module -ListAvailable Az` para encontrar a versão. Se você precisa fazer a atualização, confira [Instalar o módulo do Azure PowerShell](/powershell/azure/install-az-ps). Se você estiver executando o PowerShell localmente, também precisará executar o `Connect-AzAccount` para criar uma conexão com o Azure. Com o PowerShell, a implantação usa [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment).

Se você optar por instalar e usar o CLI do Azure localmente, este artigo exigirá que você esteja executando a versão 2.1.0 ou posterior. Execute `az --version` para encontrar a versão. Se você precisar instalar ou atualizar, confira [Instalar a CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest). Com o CLI do Azure, essa implantação usa [AZ Group Deployment Create](https://docs.microsoft.com/cli/azure/group/deployment?view=azure-cli-latest#az-group-deployment-create). 

O modelo JSON está configurado para solicitar:

* O nome do workspace.
* A região na qual criar o espaço de trabalho.
* O nome da conta de automação.
* A região na qual criar a conta.

Os seguintes parâmetros no modelo são definidos com um valor padrão para o espaço de trabalho Log Analytics:

* o *SKU* usa como padrão o tipo de preço por GB lançado no modelo de preços de abril de 2018.
* o padrão *Retention* é de 30 dias.
* *capacityReservationLevel* usa 100 GB como padrão.

>[!WARNING]
>Se você quiser criar ou configurar um espaço de trabalho Log Analytics em uma assinatura que tenha optado pelo modelo de preços de abril de 2018, o único tipo de preço válido de Log Analytics é *PerGB2018*.
>

O modelo JSON especifica um valor padrão para os outros parâmetros que provavelmente seriam usados como uma configuração padrão em seu ambiente. Você pode armazenar o modelo em uma conta de armazenamento do Azure para acesso compartilhado em sua organização. Para obter mais informações sobre como trabalhar com modelos, consulte [implantar recursos com modelos do Resource Manager e o CLI do Azure](../azure-resource-manager/templates/deploy-cli.md).

Se você for novo na automação do Azure e Azure Monitor, é importante entender os detalhes de configuração a seguir. Eles podem ajudá-lo a evitar erros ao tentar criar, configurar e usar um espaço de trabalho Log Analytics vinculado à sua nova conta de automação. 

* Examine os [detalhes adicionais](../azure-monitor/platform/template-workspace-configuration.md#create-a-log-analytics-workspace) para entender totalmente as opções de configuração do espaço de trabalho, como modo de controle de acesso, tipo de preço, retenção e nível de reserva de capacidade.

* Revise os [mapeamentos de espaço de trabalho](how-to/region-mappings.md) para especificar as regiões com suporte embutidas ou em um arquivo de parâmetro. Somente determinadas regiões têm suporte para vincular um espaço de trabalho Log Analytics e uma conta de automação em sua assinatura.

* Se você for novo no Azure Monitor logs e ainda não tiver implantado um espaço de trabalho, examine as [diretrizes de design do espaço de trabalho](../azure-monitor/platform/design-logs-deployment.md). Ele ajudará você a aprender sobre o controle de acesso e compreender as estratégias de implementação de design que recomendamos para sua organização.

## <a name="deploy-the-template"></a>Implantar o modelo

1. Copie e cole a seguinte sintaxe JSON em seu arquivo:

    ```json
    {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceName": {
            "type": "string",
            "metadata": {
                "description": "Workspace name"
            }
        },
        "sku": {
            "type": "string",
            "allowedValues": [
                "pergb2018",
                "Free",
                "Standalone",
                "PerNode",
                "Standard",
                "Premium"
            ],
            "defaultValue": "pergb2018",
            "metadata": {
                "description": "Pricing tier: perGB2018 or legacy tiers (Free, Standalone, PerNode, Standard or Premium), which are not available to all customers."
            }
        },
        "dataRetention": {
            "type": "int",
            "defaultValue": 30,
            "minValue": 7,
            "maxValue": 730,
            "metadata": {
                "description": "Number of days of retention. Workspaces in the legacy Free pricing tier can have only 7 days."
            }
        },
        "immediatePurgeDataOn30Days": {
            "type": "bool",
            "defaultValue": "[bool('false')]",
            "metadata": {
                "description": "If set to true when changing retention to 30 days, older data will be immediately deleted. Use this with extreme caution. This applies only when retention is being set to 30 days."
            }
        },
        "location": {
            "type": "string",
            "metadata": {
                "description": "Specifies the location in which to create the workspace."
            }
        },
        "automationAccountName": {
            "type": "string",
            "metadata": {
                "description": "Automation account name"
            }
        },
        "automationAccountLocation": {
            "type": "string",
            "metadata": {
                "description": "Specify the location in which to create the Automation account."
            }
        },
        "sampleGraphicalRunbookName": {
                "type": "String",
                "defaultValue": "AzureAutomationTutorial"
            },
            "sampleGraphicalRunbookDescription": {
                "type": "String",
                "defaultValue": " An example runbook that gets all the Resource Manager resources by using the Run As account (service principal)."
            },
            "sampleGraphicalRunbookContentUri": {
                "type": "String",
                "defaultValue": "https://eus2oaasibizamarketprod1.blob.core.windows.net/marketplace-runbooks/AzureAutomationTutorial.graphrunbook"
            },
            "samplePowerShellRunbookName": {
                "type": "String",
                "defaultValue": "AzureAutomationTutorialScript"
            },
            "samplePowerShellRunbookDescription": {
                "type": "String",
                "defaultValue": " An example runbook that gets all the Resource Manager resources by using the Run As account (service principal)."
            },
            "samplePowerShellRunbookContentUri": {
                "type": "String",
                "defaultValue": "https://eus2oaasibizamarketprod1.blob.core.windows.net/marketplace-runbooks/AzureAutomationTutorial.ps1"
            },
            "samplePython2RunbookName": {
                "type": "String",
                "defaultValue": "AzureAutomationTutorialPython2"
            },
            "samplePython2RunbookDescription": {
                "type": "String",
                "defaultValue": " An example runbook that gets all the Resource Manager resources by using the Run As account (service principal)."
            },
            "samplePython2RunbookContentUri": {
                "type": "String",
                "defaultValue": "https://eus2oaasibizamarketprod1.blob.core.windows.net/marketplace-runbooks/AzureAutomationTutorialPython2.py"
            }
    },
    "resources": [
        {
        "type": "Microsoft.OperationalInsights/workspaces",
            "name": "[parameters('workspaceName')]",
            "apiVersion": "2017-03-15-preview",
            "location": "[parameters('location')]",
            "properties": {
                "sku": {
                    "Name": "[parameters('sku')]",
                    "name": "CapacityReservation",
                    "capacityReservationLevel": 100
                },
                "retentionInDays": "[parameters('dataRetention')]",
                "features": {
                    "searchVersion": 1,
                    "legacy": 0,
                    "enableLogAccessUsingOnlyResourcePermissions": true
                }
            },
        "resources": [
        {
            "type": "Microsoft.Automation/automationAccounts",
            "apiVersion": "2015-01-01-preview",
            "name": "[parameters('automationAccountName')]",
            "location": "[parameters('automationAccountLocation')]",
            "dependsOn": [
                 "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]",
            ],
            "tags": {},
            "properties": {
                "sku": {
                    "name": "Basic"
                }
            },
            "resources": [
                    {
                        "type": "runbooks",
                        "apiVersion": "2015-01-01-preview",
                        "name": "[parameters('sampleGraphicalRunbookName')]",
                        "location": "[parameters('automationAccountLocation')]",
                        "dependsOn": [
                            "[concat('Microsoft.Automation/automationAccounts/', parameters('automationAccountName'))]"
                        ],
                        "tags": {},
                        "properties": {
                            "runbookType": "GraphPowerShell",
                            "logProgress": "false",
                            "logVerbose": "false",
                            "description": "[parameters('sampleGraphicalRunbookDescription')]",
                            "publishContentLink": {
                                "uri": "[parameters('sampleGraphicalRunbookContentUri')]",
                                "version": "1.0.0.0"
                            }
                        }
                    },
                    {
                        "type": "runbooks",
                        "apiVersion": "2015-01-01-preview",
                        "name": "[parameters('samplePowerShellRunbookName')]",
                        "location": "[parameters('automationAccountLocation')]",
                        "dependsOn": [
                            "[concat('Microsoft.Automation/automationAccounts/', parameters('automationAccountName'))]"
                        ],
                        "tags": {},
                        "properties": {
                            "runbookType": "PowerShell",
                            "logProgress": "false",
                            "logVerbose": "false",
                            "description": "[parameters('samplePowerShellRunbookDescription')]",
                            "publishContentLink": {
                                "uri": "[parameters('samplePowerShellRunbookContentUri')]",
                                "version": "1.0.0.0"
                            }
                        }
                    },
                    {
                        "type": "runbooks",
                        "apiVersion": "2015-01-01-preview",
                        "name": "[parameters('samplePython2RunbookName')]",
                        "location": "[parameters('automationAccountLocation')]",
                        "dependsOn": [
                            "[concat('Microsoft.Automation/automationAccounts/', parameters('automationAccountName'))]"
                        ],
                        "tags": {},
                        "properties": {
                            "runbookType": "Python2",
                            "logProgress": "false",
                            "logVerbose": "false",
                            "description": "[parameters('samplePython2RunbookDescription')]",
                            "publishContentLink": {
                                "uri": "[parameters('samplePython2RunbookContentUri')]",
                                "version": "1.0.0.0"
                            }
                        }
                    }
                ]
        },
        {
            "apiVersion": "2015-11-01-preview",
            "type": "Microsoft.OperationalInsights/workspaces/linkedServices",
            "name": "[concat(parameters('workspaceName'), '/' , 'Automation')]",
            "location": "[resourceGroup().location]",
            "dependsOn": [
                "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]",
                "[concat('Microsoft.Automation/automationAccounts/', parameters('automationAccountName'))]"
            ],
            "properties": {
                "resourceId": "[resourceId('Microsoft.Automation/automationAccounts/', parameters('automationAccountName'))]"
            }
        }
       ]
      }
     ]
    }
    ```

2. Edite o modelo para atender às suas necessidades. Considere criar um [arquivo de parâmetro do Resource Manager](../azure-resource-manager/templates/parameter-files.md) em vez de passar parâmetros como valores embutidos.

3. Salve esse arquivo como deployAzAutomationAccttemplate. JSON em uma pasta local.

4. Você está pronto para implantar esse modelo. Você pode usar o PowerShell ou o CLI do Azure. Quando for solicitado um nome de espaço de trabalho e de conta de automação, forneça um nome que seja globalmente exclusivo em todas as suas assinaturas do Azure.

    **PowerShell**

    ```powershell
    New-AzResourceGroupDeployment -Name <deployment-name> -ResourceGroupName <resource-group-name> -TemplateFile deployAzAutomationAccttemplate.json
    ```

    **CLI do Azure**

    ```cli
    az group deployment create --resource-group <my-resource-group> --name <my-deployment-name> --template-file deployAzAutomationAccttemplate.json
    ```

    A implantação pode levar alguns minutos para ser concluída. Quando isso ocorrer, você verá uma mensagem semelhante à seguinte que inclui o resultado.

    ![Resultados de exemplo, quando a implantação for concluída](media/automation-create-account-template/template-output.png)

## <a name="next-steps"></a>Próximas etapas

Agora que você tem uma conta de automação, pode criar runbooks e automatizar processos manuais.

* Para começar a usar os runbooks do PowerShell, confira [Criar um runbook do PowerShell](automation-first-runbook-textual-powershell.md).
* Para começar a usar runbooks de fluxo de trabalho do PowerShell, consulte [criar um runbook de fluxo de trabalho do PowerShell](automation-first-runbook-textual.md).
* Para começar a usar runbooks do Python 2, consulte [criar um runbook do Python](automation-first-runbook-textual-python2.md).