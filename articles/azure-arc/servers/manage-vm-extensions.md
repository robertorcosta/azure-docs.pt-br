---
title: Gerenciamento de extensão de VM com servidores habilitados para Arc do Azure
description: Os servidores habilitados para Arc do Azure podem gerenciar a implantação de extensões de máquina virtual que fornecem tarefas de automação e configuração pós-implantação com VMs não Azure.
ms.date: 09/23/2020
ms.topic: conceptual
ms.openlocfilehash: 1c3d50f407f4412a14201dfe669334dbb083d323
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91329067"
---
# <a name="virtual-machine-extension-management-with-azure-arc-enabled-servers"></a>Gerenciamento de extensão de máquina virtual com servidores habilitados para Arc do Azure

As extensões de VM (máquina virtual) são pequenos aplicativos que fornecem tarefas de configuração e automação de pós-implantação em VMs do Azure. Por exemplo, se uma máquina virtual exigir instalação de software, proteção antivírus ou executar um script dentro dela, uma extensão de VM poderá ser usada.

Os servidores habilitados para Arc do Azure permitem implantar extensões de VM do Azure em VMs não Azure Windows e Linux, simplificando o gerenciamento de seu computador híbrido local, Edge e outros ambientes de nuvem por meio de seu ciclo de vida.

## <a name="key-benefits"></a>Principais benefícios

O suporte à extensão de VM dos servidores habilitados para Arc do Azure oferece os seguintes benefícios principais:

* Use a [configuração de estado da automação do Azure](../../automation/automation-dsc-overview.md) para armazenar configurações centralmente e manter o estado desejado de computadores conectados híbridos habilitados por meio da extensão de VM de DSC.

* Coletar dados de log para análise com [logs no Azure monitor](../../azure-monitor/platform/data-platform-logs.md) habilitado por meio da extensão de VM do agente log Analytics. Isso é útil para executar análises complexas em dados de uma variedade de fontes.

* Com o [Azure monitor para VMs](../../azure-monitor/insights/vminsights-overview.md), o analisa o desempenho de suas VMs do Windows e do Linux e monitora seus processos e dependências em outros recursos e processos externos. Isso é obtido por meio da habilitação do agente de Log Analytics e das extensões de VM do agente de dependência.

* Baixe e execute scripts em computadores conectados híbridos usando a extensão de script personalizado. Essa extensão é útil para a configuração de pós-implantação, instalação de software ou qualquer outra configuração ou tarefas de gerenciamento.

## <a name="availability"></a>Disponibilidade

A funcionalidade de extensão de VM está disponível somente na lista de [regiões com suporte](overview.md#supported-regions). Certifique-se de carregar seu computador em uma dessas regiões.

## <a name="extensions"></a>Extensões

Nesta versão, damos suporte às seguintes extensões de VM em computadores Windows e Linux.

|Extensão |Sistema operacional |Publicador |Informações adicionais |
|----------|---|----------|-----------------------|
|CustomScriptExtension |Windows |Microsoft.Compute |[Extensão de script personalizado do Windows](../../virtual-machines/extensions/custom-script-windows.md)|
|DSC |Windows |Microsoft. PowerShell|[Extensão DSC do Windows PowerShell](../../virtual-machines/extensions/dsc-windows.md)|
|Agente do Log Analytics |Windows |Microsoft.EnterpriseCloud.Monitoring |[Log Analytics extensão de VM para Windows](../../virtual-machines/extensions/oms-windows.md)|
|Microsoft Dependency Agent | Windows |Microsoft.Compute | [Extensão da máquina virtual do agente de dependência para Windows](../../virtual-machines/extensions/agent-dependency-windows.md)|
|CustomScript|Linux |Microsoft. Azure. Extension |[Extensão de script personalizado do Linux versão 2](../../virtual-machines/extensions/custom-script-linux.md) |
|DSC |Linux |Microsoft.OSTCExtensions |[Extensão de DSC do PowerShell para Linux](../../virtual-machines/extensions/dsc-linux.md) |
|Agente do Log Analytics |Linux |Microsoft.EnterpriseCloud.Monitoring |[Log Analytics extensão de VM para Linux](../../virtual-machines/extensions/oms-linux.md) |
|Microsoft Dependency Agent | Linux |Microsoft.Compute | [Extensão da máquina virtual do agente de dependência para Linux](../../virtual-machines/extensions/agent-dependency-linux.md) |

As extensões de VM podem ser executadas com modelos de Azure Resource Manager, da portal do Azure ou Azure PowerShell em servidores híbridos gerenciados por servidores habilitados para Arc.

Para saber mais sobre o pacote do agente do computador conectado do Azure e detalhes sobre o componente do agente de extensão, consulte [visão geral do agente](agent-overview.md#agent-component-details).

## <a name="prerequisites"></a>Pré-requisitos

Esse recurso depende dos seguintes provedores de recursos do Azure em sua assinatura:

* **Microsoft.HybridCompute**
* **Microsoft.GuestConfiguration**

Se eles ainda não estiverem registrados, siga as etapas em [registrar provedores de recursos do Azure](agent-overview.md#register-azure-resource-providers).

A extensão de VM do agente de Log Analytics para Linux requer o Python 2. x instalado no computador de destino.

### <a name="connected-machine-agent"></a>Agente do Connected Machine

Verifique se seu computador corresponde às [versões com suporte](agent-overview.md#supported-operating-systems) do sistema operacional Windows e Linux para o agente do computador conectado do Azure.

A versão mínima do agente do computador conectado que tem suporte com esse recurso no Windows e no Linux é a versão 1,0.

Para atualizar seu computador para a versão do agente necessária, consulte [Atualizar agente](manage-agent.md#upgrading-agent).

## <a name="enable-extensions-from-the-portal"></a>Habilitar extensões do portal

As extensões de VM podem ser aplicadas ao seu arco para o computador gerenciado pelo servidor por meio do portal do Azure.

1. No navegador, acesse o [portal do Azure](https://portal.azure.com).

2. No portal, navegue até **servidores – Azure Arc** e selecione seu computador híbrido na lista.

3. Escolha **extensões**e, em seguida, selecione **Adicionar**. Escolha a extensão desejada na lista de extensões disponíveis e siga as instruções no assistente. Neste exemplo, iremos implantar a extensão de VM Log Analytics.

    ![Selecionar a extensão de VM para o computador selecionado](./media/manage-vm-extensions/add-vm-extensions.png)

    O exemplo a seguir mostra a instalação do Log Analytics extensão de VM do portal do Azure:

    ![Instalar Log Analytics extensão de VM](./media/manage-vm-extensions/mma-extension-config.png)

    Para concluir a instalação, é necessário fornecer a ID do espaço de trabalho e a chave primária. Se você não estiver familiarizado com a localização dessas informações, consulte [obter ID e chave do espaço de trabalho](../../azure-monitor/platform/log-analytics-agent.md#workspace-id-and-key).

4. Depois de confirmar as informações necessárias fornecidas, selecione **criar**. Um resumo da implantação é exibido e você pode revisar o status da implantação.

>[!NOTE]
>Embora várias extensões possam ser agrupadas e processadas em lote, elas são instaladas em série. Depois que a primeira instalação da extensão for concluída, a instalação da próxima extensão será tentada.

## <a name="azure-resource-manager-templates"></a>Modelos do Azure Resource Manager

É possível adicionar extensões de VM a um modelo do Azure Resource Manager e executá-las com a implantação do modelo. Com as extensões de VM com suporte de servidores habilitados para Arc, você pode implantar a extensão de VM com suporte em computadores Linux ou Windows usando o Azure PowerShell. Cada exemplo a seguir inclui um arquivo de modelo e um arquivo de parâmetros com valores de exemplo para fornecer ao modelo.

>[!NOTE]
>Embora várias extensões possam ser agrupadas e processadas em lote, elas são instaladas em série. Depois que a primeira instalação da extensão for concluída, a instalação da próxima extensão será tentada.

### <a name="deploy-the-log-analytics-vm-extension"></a>Implantar a extensão de VM Log Analytics

Para implantar facilmente o agente de Log Analytics, o exemplo a seguir é fornecido para instalar o agente no Windows ou no Linux.

#### <a name="template-file-for-linux"></a>Arquivo de modelo para Linux

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "workspaceId": {
            "type": "string"
        },
        "workspaceKey": {
            "type": "string"
        }
    },
    "resources": [
        {
            "name": "[concat(parameters('vmName'),'/OMSAgentForLinux')]",
            "type": "Microsoft.HybridCompute/machines/extensions",
            "location": "[parameters('location')]",
            "apiVersion": "2019-08-02-preview",
            "properties": {
                "publisher": "Microsoft.EnterpriseCloud.Monitoring",
                "type": "OmsAgentForLinux",
                "settings": {
                    "workspaceId": "[parameters('workspaceId')]"
                },
                "protectedSettings": {
                    "workspaceKey": "[parameters('workspaceKey')]"
                }
            }
        }
    ]
}
```

#### <a name="template-file-for-windows"></a>Arquivo de modelo para Windows

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "workspaceId": {
            "type": "string"
        },
        "workspaceKey": {
            "type": "string"
        }
    },
    "resources": [
        {
            "name": "[concat(parameters('vmName'),'/MicrosoftMonitoringAgent')]",
            "type": "Microsoft.HybridCompute/machines/extensions",
            "location": "[parameters('location')]",
            "apiVersion": "2019-08-02-preview",
            "properties": {
                "publisher": "Microsoft.EnterpriseCloud.Monitoring",
                "type": "MicrosoftMonitoringAgent",
                "autoUpgradeMinorVersion": true,
                "settings": {
                    "workspaceId": "[parameters('workspaceId')]"
                },
                "protectedSettings": {
                    "workspaceKey": "[parameters('workspaceKey')]"
                }
            }
        }
    ]
}
```

#### <a name="parameter-file"></a>Arquivo de parâmetro.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "value": "<vmName>"
        },
        "location": {
            "value": "<region>"
        },
        "workspaceId": {
            "value": "<MyWorkspaceID>"
        },
        "workspaceKey": {
            "value": "<MyWorkspaceKey>"
        }
    }
}
```

Salve o modelo e os arquivos de parâmetro no disco e edite o arquivo de parâmetro com os valores apropriados para sua implantação. Em seguida, você pode instalar a extensão em todos os computadores conectados em um grupo de recursos com o comando a seguir. O comando usa o parâmetro *TemplateFile* para especificar o modelo e o parâmetro *TemplateParameterFile* para especificar um arquivo que contém parâmetros e valores de parâmetro.

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "ContosoEngineering" -TemplateFile "D:\Azure\Templates\LogAnalyticsAgentWin.json" -TemplateParameterFile "D:\Azure\Templates\LogAnalyticsAgentWinParms.json"
```

### <a name="deploy-the-custom-script-extension"></a>Implantar a extensão de script personalizado

Para usar a extensão de script personalizado, o exemplo a seguir é fornecido para execução no Windows e no Linux. Se você não estiver familiarizado com a extensão de script personalizado, consulte [extensão de script personalizado para Windows](../../virtual-machines/extensions/custom-script-windows.md) ou [extensão de script personalizado para Linux](../../virtual-machines/extensions/custom-script-linux.md). Há algumas características diferentes que você deve entender ao usar essa extensão com computadores híbridos:

* A lista de sistemas operacionais com suporte com a extensão de script personalizado da VM do Azure não é aplicável aos servidores habilitados para Arc do Azure. A lista de OSs com suporte para servidores habilitados para Arc pode ser encontrada [aqui](agent-overview.md#supported-operating-systems).

* Os detalhes de configuração sobre conjuntos de dimensionamento de máquinas virtuais do Azure ou VMs clássicas não são aplicáveis.

* Se seus computadores precisarem baixar um script externamente e só puderem se comunicar por meio de um servidor proxy, você precisará [Configurar o agente do computador conectado](manage-agent.md#update-or-remove-proxy-settings) para definir a variável de ambiente de servidor proxy.

A configuração de extensão de script personalizado especifica itens como localização de script e o comando a ser executado. Essa configuração é especificada em um modelo de Azure Resource Manager, fornecido abaixo para máquinas híbridas do Linux e do Windows.

#### <a name="template-file-for-linux"></a>Arquivo de modelo para Linux

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "vmName": {
      "type": "string"
    },
    "location": {
      "type": "string"
    },
    "fileUris": {
      "type": "array"
    },
    "commandToExecute": {
      "type": "securestring"
    }
  },
  "resources": [
    {
      "name": "[concat(parameters('vmName'),'/CustomScript')]",
      "type": "Microsoft.HybridCompute/machines/extensions",
      "location": "[parameters('location')]",
      "apiVersion": "2019-08-02-preview",
      "properties": {
        "publisher": "Microsoft.Azure.Extensions",
        "type": "CustomScript",
        "autoUpgradeMinorVersion": true,
        "settings": {},
        "protectedSettings": {
          "commandToExecute": "[parameters('commandToExecute')]",
          "fileUris": "[parameters('fileUris')]"
        }
      }
    }
  ]
}
```

#### <a name="template-file-for-windows"></a>Arquivo de modelo para Windows

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "fileUris": {
            "type": "string"
        },
        "arguments": {
            "type": "securestring",
            "defaultValue": " "
        }
    },
    "variables": {
        "UriFileNamePieces": "[split(parameters('fileUris'), '/')]",
        "firstFileNameString": "[variables('UriFileNamePieces')[sub(length(variables('UriFileNamePieces')), 1)]]",
        "firstFileNameBreakString": "[split(variables('firstFileNameString'), '?')]",
        "firstFileName": "[variables('firstFileNameBreakString')[0]]"
    },
    "resources": [
        {
            "name": "[concat(parameters('vmName'),'/CustomScriptExtension')]",
            "type": "Microsoft.HybridCompute/machines/extensions",
            "location": "[parameters('location')]",
            "apiVersion": "2019-08-02-preview",
            "properties": {
                "publisher": "Microsoft.Compute",
                "type": "CustomScriptExtension",
                "autoUpgradeMinorVersion": true,
                "settings": {
                    "fileUris": "[split(parameters('fileUris'), ' ')]"
                },
                "protectedSettings": {
                    "commandToExecute": "[concat ('powershell -ExecutionPolicy Unrestricted -File ', variables('firstFileName'), ' ', parameters('arguments'))]"
                }
            }
        }
    ]
}
```

#### <a name="parameter-file"></a>Arquivo de parâmetro.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {}
    ],
    "steps": [
      {
        "name": "customScriptExt",
        "label": "Add Custom Script Extension",
        "elements": [
          {
            "name": "fileUris",
            "type": "Microsoft.Common.FileUpload",
            "label": "Script files",
            "toolTip": "The script files that will be downloaded to the virtual machine.",
            "constraints": {
              "required": false
            },
            "options": {
              "multiple": true,
              "uploadMode": "url"
            },
            "visible": true
          },
          {
            "name": "commandToExecute",
            "type": "Microsoft.Common.TextBox",
            "label": "Command",
            "defaultValue": "sh script.sh",
            "toolTip": "The command to execute, for example: sh script.sh",
            "constraints": {
              "required": true
            },
            "visible": true
          }
        ]
      }
    ],
    "outputs": {
      "vmName": "[vmName()]",
      "location": "[location()]",
      "fileUris": "[steps('customScriptExt').fileUris]",
      "commandToExecute": "[steps('customScriptExt').commandToExecute]"
    }
  }
}
```

### <a name="deploy-the-powershell-dsc-extension"></a>Implantar a extensão de DSC do PowerShell

Para usar a extensão de DSC do PowerShell, o exemplo a seguir é fornecido para execução no Windows e no Linux. Se você não estiver familiarizado com a extensão de DSC do PowerShell, consulte [visão geral do manipulador de extensão de DSC](../../virtual-machines/extensions/dsc-overview.md). Há algumas características diferentes que você deve entender ao usar essa extensão com computadores híbridos:

* A lista de sistemas operacionais com suporte com a extensão de DSC do PowerShell de VM do Azure não é aplicável aos servidores habilitados para Arc do Azure. A lista de OSs com suporte para servidores habilitados para Arc pode ser encontrada [aqui](agent-overview.md#supported-operating-systems).

* Se seus computadores precisarem baixar um script externamente e só puderem se comunicar por meio de um servidor proxy, você precisará [Configurar o agente do computador conectado](manage-agent.md#update-or-remove-proxy-settings) para definir a variável de ambiente de servidor proxy.

#### <a name="template-file-for-linux"></a>Arquivo de modelo para Linux

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "type": "string",
            "metadata": {
                "description": "Name of the vm, will be used as DNS Name for the Public IP used to access the Virtual Machine."
            }
        },
        "location": {
            "type": "string",
            "metadata": {
                "description": "Location for all resources."
            }
        },
        "mode": {
            "type": "string",
            "defaultValue": "Push",
            "metadata": {
                "description": "The functional mode, push MOF configuration (Push), distribute MOF configuration (Pull), install custom DSC module (Install)"
            },
            "allowedValues": [
                "Push",
                "Pull",
                "Install",
                "Register"
            ]
        },
        "fileUri": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The uri of the MOF file/Meta MOF file/resource ZIP file"
            }
        },
        "registrationUrl": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The URL of the Azure Automation account"
            }
        },
        "registrationKey": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The access key of the Azure Automation account"
            }
        }
    },
    "resources": [
        {
            "name": "[concat(parameters('vmName'),'/DSCForLinux')]",
            "type": "Microsoft.HybridCompute/machines/extensions",
            "location": "[parameters('location')]",
            "apiVersion": "2019-08-02-preview",
            "properties": {
                "publisher": "Microsoft.OSTCExtensions",
                "type": "DSCForLinux",
                "settings": {
                    "Mode": "[parameters('mode')]",
                    "FileUri": "[parameters('fileUri')]"
                },
                "protectedSettings": {
                    "RegistrationUrl": "[parameters('registrationUrl')]",
                    "RegistrationKey": "[parameters('registrationKey')]"
                }
            }
        }
    ]
}
```

#### <a name="template-file-for-windows"></a>Arquivo de modelo para Windows

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "modulesUrl": {
            "type": "string"
        },
        "configurationFunction": {
            "type": "string"
        },
        "properties": {
            "type": "string",
            "defaultValue": ""
        },
        "dataBlobUri": {
            "type": "string",
            "defaultValue": ""
        },
        "wmfVersion": {
            "type": "string",
            "defaultValue": "latest",
            "allowedValues": [
                "4.0",
                "5.0",
                "5.1",
                "latest"
            ]
        },
        "privacy": {
            "type": "string",
            "defaultValue": ""
        },
        "autoUpdate": {
            "type": "bool",
            "defaultValue": false
        }
    },
    "resources": [
        {
            "name": "[concat(parameters('vmName'),'/Microsoft.Powershell.DSC')]",
            "type": "Microsoft.HybridCompute/machines/extensions",
            "location": "[parameters('location')]",
            "apiVersion": "2019-08-02-preview",
            "properties": {
                "publisher": "Microsoft.Powershell",
                "type": "DSC",
                "autoUpgradeMinorVersion": "[parameters('autoUpdate')]",
                "settings": {
                    "ModulesUrl": "[parameters('modulesUrl')]",
                    "ConfigurationFunction": "[parameters('configurationFunction')]",
                    "Properties": "[parameters('properties')]",
                    "WmfVersion": "[parameters('wmfVersion')]",
                    "Privacy": {
                        "DataCollection": "[parameters('privacy')]"
                    }
                },
                "protectedSettings": {
                    "DataBlobUri": "[parameters('dataBlobUri')]"
                }
            }
        }
    ]
}
```

#### <a name="parameter-file"></a>Arquivo de parâmetro.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {}
    ],
    "steps": [
      {
        "name": "dscExtension",
        "label": "Add DSC Extension",
        "elements": [
          {
            "name": "Mode",
            "type": "Microsoft.Common.OptionsGroup",
            "label": "Mode",
            "defaultValue": 0,
            "toolTip": "The functional mode, push MOF configuration (Push), distribute MOF configuration (Pull), install custom DSC module (Install)",
            "constraints": {
              "allowedValues": [
                {
                  "label": "Push",
                  "value": "Push"
                },
                {
                  "label": "Pull",
                  "value": "Pull"
                },
                {
                  "label": "Install",
                  "value": "Install"
                },
                {
                  "label": "Register",
                  "value": "Register"
                }
              ]
            },
            "visible": true
          },
          {
            "name": "FileUri",
            "type": "Microsoft.Common.FileUpload",
            "label": "File URI",
            "toolTip": "The uri of the MOF file/Meta MOF file/resource ZIP file",
            "constraints": {
              "required": false,
              "accept": ".psd1"
            },
            "options": {
              "multiple": false,
              "uploadMode": "url",
              "openMode": "binary",
              "encoding": "UTF-8"
            }
          },
          {
            "name": "RegistrationUrl",
            "type": "Microsoft.Common.TextBox",
            "label": "Registration URL",
            "toolTip": "The URL of the Azure Automation account",
            "constraints": {
              "required": false
            }
          },
          {
            "name": "RegistrationKey",
            "type": "Microsoft.Common.TextBox",
            "label": "Registration key",
            "toolTip": "The access key of the Azure Automation account",
            "constraints": {
              "required": false
            }
          }
        ]
      }
    ],
    "outputs": {
      "vmName": "[vmName()]",
      "location": "[location()]",
      "mode": "[steps('dscExtension').Mode]",
      "fileUri": "[steps('dscExtension').FileUri]",
      "registrationUrl": "[steps('dscExtension').RegistrationUrl]",
      "registrationKey": "[steps('dscExtension').RegistrationKey]"
    }
  }
}
```

### <a name="dependency-agent"></a>Agente de dependência

Para usar a extensão do agente de dependência Azure Monitor, o exemplo a seguir é fornecido para execução no Windows e no Linux. Se você não estiver familiarizado com o Dependency Agent, consulte [visão geral dos agentes de Azure monitor](../../azure-monitor/platform/agents-overview.md#dependency-agent).

#### <a name="template-file-for-linux"></a>Arquivo de modelo para Linux

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
  "parameters": {
    "vmName": {
      "type": "string",
      "metadata": {
        "description": "The name of existing Linux machine."
      }
    }
  },
  "variables": {
    "vmExtensionsApiVersion": "2017-03-30"
  },
  "resources": [
    {
      "type": "Microsoft.HybridCompute/machines/extensions",
      "name": "[concat(parameters('vmName'),'/DAExtension')]",
      "apiVersion": "[variables('vmExtensionsApiVersion')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
      ],
      "properties": {
        "publisher": "Microsoft.Azure.Monitoring.DependencyAgent",
        "type": "DependencyAgentLinux",
        "typeHandlerVersion": "9.5",
        "autoUpgradeMinorVersion": true
      }
    }
  ],
    "outputs": {
    }
}
```

#### <a name="template-file-for-windows"></a>Arquivo de modelo para Windows

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
  "parameters": {
    "vmName": {
      "type": "string",
      "metadata": {
        "description": "The name of existing Windows machine."
      }
    }
  },
  "variables": {
    "vmExtensionsApiVersion": "2017-03-30"
  },
  "resources": [
    {
      "type": "Microsoft.HybridCompute/machines/extensions",
      "name": "[concat(parameters('vmName'),'/DAExtension')]",
      "apiVersion": "[variables('vmExtensionsApiVersion')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
      ],
      "properties": {
        "publisher": "Microsoft.Azure.Monitoring.DependencyAgent",
        "type": "DependencyAgentWindows",
        "typeHandlerVersion": "9.5",
        "autoUpgradeMinorVersion": true
      }
    }
  ],
    "outputs": {
    }
}
```

## <a name="uninstall-extension"></a>Extensão de desinstalação

A remoção de uma ou mais extensões de um servidor habilitado para Arc só pode ser executada no portal do Azure. Execute as etapas a seguir para remover uma extensão.

1. No navegador, acesse o [portal do Azure](https://portal.azure.com).

2. No portal, navegue até **servidores – Azure Arc** e selecione seu computador híbrido na lista.

3. Escolha **extensões**e, em seguida, selecione uma extensão na lista de extensões instaladas.

4. Selecione **desinstalar** e, quando for solicitado a verificar, selecione **Sim** para continuar.

## <a name="next-steps"></a>Próximas etapas

* As informações de solução de problemas podem ser encontradas no [guia solucionar problemas de extensões de VM](troubleshoot-vm-extensions.md).

* Saiba como gerenciar seu computador usando o [Azure Policy](../../governance/policy/overview.md) para itens como [configurar convidados](../../governance/policy/concepts/guest-configuration.md) de VM, verificar se o computador está relatando ao workspace do Log Analytics esperado, habilitar o monitoramento com o [Azure Monitor em VMs](../../azure-monitor/insights/vminsights-enable-policy.md) e muito mais.

* Saiba mais sobre o [Agente do Log Analytics](../../azure-monitor/platform/log-analytics-agent.md). O agente Log Analytics para Windows e Linux é necessário quando você deseja coletar dados de monitoramento do sistema operacional e da carga de trabalho, gerenciá-los usando runbooks de automação ou recursos como Gerenciamento de Atualizações ou usar outros serviços do Azure, como a [central de segurança do Azure](../../security-center/security-center-intro.md).