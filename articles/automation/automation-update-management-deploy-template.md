---
title: Habilitar o Gerenciamento de Atualizações usando o modelo do Azure Resource Manager | Microsoft Docs
description: Este artigo diz como usar um modelo do Azure Resource Manager to habilitar o Gerenciamento de Atualizações.
ms.service: automation
ms.subservice: update-management
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 06/10/2020
ms.openlocfilehash: feb1cc132bf5463550a2e7921f347c8f2f48260e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84667991"
---
# <a name="enable-update-management-using-azure-resource-manager-template"></a>Habilitar o Gerenciamento de Atualizações usando o modelo do Azure Resource Manager

Você pode usar um [modelo do Azure Resource Manager](../azure-resource-manager/templates/template-syntax.md) para habilitar o recurso de Gerenciamento de Atualizações da Automação do Azure em seu grupo de recursos. Este artigo fornece um modelo de exemplo que automatiza o seguinte:

* Criação de um workspace do Log Analytics do Azure Monitor.
* Criação de uma conta da Automação do Azure.
* Vinculação da conta da Automação ao workspace do Log Analytics se ela ainda não estiver vinculada.
* Habilitação do Gerenciamento de Atualizações.

O modelo não automatiza a habilitação de Gerenciamento de Atualizações em uma ou mais VMs do Azure ou não Azure.

Se você já tiver um workspace do Log Analytics e uma conta da Automação implantada em uma região com suporte em sua assinatura, eles não serão vinculados. O uso desse modelo cria com êxito o link e implanta Gerenciamento de Atualizações.

## <a name="api-versions"></a>Versões de API

A tabela a seguir lista as versões de API para os recursos usados neste modelo.

| Recurso | Tipo de recurso | Versão da API |
|:---|:---|:---|
| Workspace | workspaces | 2020-03-01-visualização |
| Conta de automação | automação | 2018-06-30 | 
| Solução | solutions | 2015-11-01-preview |

## <a name="before-using-the-template"></a>Antes de usar o modelo

Se você optar por instalar e usar o PowerShell localmente, este artigo exigirá o módulo do Azure PowerShell Az. Execute `Get-Module -ListAvailable Az` para encontrar a versão. Se você precisa fazer a atualização, confira [Instalar o módulo do Azure PowerShell](/powershell/azure/install-az-ps). Se você estiver executando o PowerShell localmente, também precisará executar o [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-3.7.0) para criar uma conexão com o Azure. Com o Azure PowerShell, a implantação usa [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment).

Se você optar por instalar e usar a CLI localmente, este artigo exigirá que esteja em execução a versão 2.1.0 ou posterior da CLI do Azure. Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest). Com a CLI do Azure, essa implantação usa a [criação da implantação de grupo AZ](https://docs.microsoft.com/cli/azure/group/deployment?view=azure-cli-latest#az-group-deployment-create). 

O modelo JSON está configurado para solicitar:

* O nome do workspace.
* A região na qual criar o espaço de trabalho.
* Para habilitar permissões de recurso ou espaço de trabalho.
* O nome da conta de Automação.
* A região na qual criar a conta.

O modelo JSON especifica um valor padrão para os outros parâmetros que provavelmente serão usados para uma configuração padrão em seu ambiente. Você pode armazenar o modelo em uma conta de armazenamento do Azure para acesso compartilhado na sua organização. Para obter mais informações sobre como trabalhar com modelos, veja [Implantar recursos com modelos do Resource Manager e a CLI do Azure](../azure-resource-manager/templates/deploy-cli.md).

Os seguintes parâmetros no modelo são definidos com um valor padrão para o espaço de trabalho Log Analytics:

* SKU – assume por padrão o novo tipo de preço por GB lançado no modelo de preço de abril de 2018
* retenção de dados - usa como padrão trinta dias

>[!WARNING]
>Se criar ou configurar um espaço de trabalho do Log Analytics em uma assinatura que tiver aceitado o novo modelo de preços de abril de 2018, o único tipo de preço válido do Log Analytics **PerGB2018**.
>

O modelo JSON especifica um valor padrão para os outros parâmetros que provavelmente seriam usados como uma configuração padrão em seu ambiente. Você pode armazenar o modelo em uma conta de armazenamento do Azure para acesso compartilhado na sua organização. Para obter mais informações sobre como trabalhar com modelos, veja [Implantar recursos com modelos do Resource Manager e a CLI do Azure](../azure-resource-manager/templates/deploy-cli.md).

É importante entender os seguintes detalhes de configuração se você for novo na Automação do Azure e no Azure Monitor, a fim de evitar erros ao tentar criar, configurar e usar um workspace do Log Analytics vinculado à sua nova conta da Automação.

* Confira [detalhes adicionais](../azure-monitor/platform/template-workspace-configuration.md#create-a-log-analytics-workspace) para entender totalmente as opções de configuração do espaço de trabalho, como modo de controle de acesso, tipo de preço, retenção e nível de reserva de capacidade.

* Como apenas determinadas regiões têm suporte para vincular um workspace do Log Analytics e uma conta da Automação em sua assinatura, confira os [mapeamentos de workspace](how-to/region-mappings.md) para especificar as regiões com suporte embutidas ou em um arquivo de parâmetros.

* Se você for novo nos logs do Azure Monitor e ainda não tiver implantado um workspace, confira as diretrizes sobre [design de workspace](../azure-monitor/platform/design-logs-deployment.md) para saber mais sobre o controle de acesso e entender as estratégias de implementação de design recomendadas para sua organização.

## <a name="deploy-template"></a>Implantar modelo

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
                "description": "Pricing tier: perGB2018 or legacy tiers (Free, Standalone, PerNode, Standard or Premium) which are not available to all customers."
            }
        },
        "dataRetention": {
            "type": "int",
            "defaultValue": 30,
            "minValue": 7,
            "maxValue": 730,
            "metadata": {
                "description": "Number of days of retention. Workspaces in the legacy Free pricing tier can only have 7 days."
            }
        },
        "location": {
            "type": "string",
            "metadata": {
                "description": "Specifies the location in which to create the workspace."
            }
        },
        "resourcePermissions": {
              "type": "bool",
              "metadata": {
                "description": "true to use resource or workspace permissions. false to require workspace permissions."
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
        }
    },
    "variables": {
        "Updates": {
            "name": "[concat('Updates', '(', parameters('workspaceName'), ')')]",
            "galleryName": "Updates"
        }
    },
    "resources": [
        {
        "type": "Microsoft.OperationalInsights/workspaces",
            "name": "[parameters('workspaceName')]",
            "apiVersion": "2020-03-01-preview",
            "location": "[parameters('location')]",
            "properties": {
                "sku": {
                    "name": "[parameters('sku')]",
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
                    "apiVersion": "2015-11-01-preview",
                    "location": "[parameters('location')]",
                    "name": "[variables('Updates').name]",
                    "type": "Microsoft.OperationsManagement/solutions",
                    "id": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.OperationsManagement/solutions/', variables('Updates').name)]",
                    "dependsOn": [
                        "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
                    ],
                    "properties": {
                        "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
                    },
                    "plan": {
                        "name": "[variables('Updates').name]",
                        "publisher": "Microsoft",
                        "promotionCode": "",
                        "product": "[concat('OMSGallery/', variables('Updates').galleryName)]"
                    }
                }
            ]
        },
        {
            "type": "Microsoft.Automation/automationAccounts",
            "apiVersion": "2018-06-30",
            "name": "[parameters('automationAccountName')]",
            "location": "[parameters('automationAccountLocation')]",
            "dependsOn": [],
            "tags": {},
            "properties": {
                "sku": {
                    "name": "Basic"
                }
            },
        },
        {
            "apiVersion": "2020-03-01-preview",
            "type": "Microsoft.OperationalInsights/workspaces/linkedServices",
            "name": "[concat(parameters('workspaceName'), '/' , 'Automation')]",
            "location": "[parameters('location')]",
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
    ```

2. Edite o modelo para atender às suas necessidades. Considere a criação de um [arquivo de parâmetros do Resource Manager](../azure-resource-manager/templates/parameter-files.md) em vez de passar parâmetros como valores embutidos.

3. Salve esse arquivo em uma pasta local como **deployUMSolutiontemplate.json**.

4. Você está pronto para implantar o modelo. Você pode usar a CLI do Azure ou o PowerShell. Quando for solicitado a informar um nome de workspace ou conta da Automação, forneça um nome que seja globalmente exclusivo em todas as assinaturas do Azure.

    **PowerShell**

    ```powershell
    New-AzResourceGroupDeployment -Name <deployment-name> -ResourceGroupName <resource-group-name> -TemplateFile deployUMSolutiontemplate.json
    ```

    **CLI do Azure**

    ```cli
    az group deployment create --resource-group <my-resource-group> --name <my-deployment-name> --template-file deployUMSolutiontemplate.json
    ```

    A implantação pode levar alguns minutos para ser concluída. Quando ela for concluída, você verá uma mensagem semelhante que inclui o resultado:

    ![Resultados de exemplo, quando a implantação for concluída](media/automation-update-management-deploy-template/template-output.png)

## <a name="next-steps"></a>Próximas etapas

* Para usar o Gerenciamento de Atualizações para VMs, confira [Gerenciar atualizações e patches para suas VMs do Azure](automation-tutorial-update-management.md).

* Se você não precisar mais do workspace do Log Analytics, confira as instruções em [Desvincular workspace da conta da Automação para Gerenciamento de Atualizações](automation-unlink-workspace-update-management.md).

* Para excluir VMs do Gerenciamento de Atualizações, confira [Remover VMs do Gerenciamento de Atualizações](automation-remove-vms-from-update-management.md).
