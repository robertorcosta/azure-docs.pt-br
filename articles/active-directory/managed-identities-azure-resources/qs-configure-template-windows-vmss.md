---
title: Configure modelo para usar identidades gerenciadas em conjuntos de escala de máquinas virtuais - Azure AD
description: Instruções passo a passo para configurar identidades gerenciadas para recursos do Azure em um conjunto de dimensionamento de máquinas virtuais, usando um modelo do Azure Resource Manager.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/20/2018
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2d5e324ea20b2ea82fac5b5132893d3558bd3b41
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77425554"
---
# <a name="configure-managed-identities-for-azure-resources-on-an-azure-virtual-machine-scale-using-a-template"></a>Configure identidades gerenciadas para recursos do Azure em uma escala de máquina virtual do Azure usando um modelo

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

As identidades gerenciadas dos recursos do Azure fornecem aos serviços do Azure uma identidade gerenciada automaticamente no Azure Active Directory. Você pode usar essa identidade para autenticar em qualquer serviço que dá suporte à autenticação do Azure AD, incluindo o Key Vault, sem ter as credenciais no seu código.

Neste artigo, você aprenderá como executar as seguintes identidades gerenciadas para operações de recursos do Azure em um conjunto de dimensionamento de máquinas virtuais do Azure, usando o modelo de implantação do Azure Resource Manager:
- Habilitar e desabilitar a identidade gerenciada atribuída ao sistema em um conjunto de dimensionamento de máquinas virtuais do Azure
- Adicionar e remover uma identidade gerenciada atribuída ao usuário em um conjunto de dimensionamento de máquinas virtuais do Azure

## <a name="prerequisites"></a>Pré-requisitos

- Se você não estiver familiarizado com identidades gerenciadas para recursos do Azure, confira a [seção de visão geral](overview.md). **Revise a [diferença entre uma identidade gerenciada atribuída ao sistema e atribuída ao usuário](overview.md#how-does-the-managed-identities-for-azure-resources-work)**.
- Se você ainda não tiver uma conta do Azure, [inscreva-se em uma conta gratuita](https://azure.microsoft.com/free/) antes de continuar.
- Para realizar as operações de gerenciamento deste artigo, a conta precisa das seguintes atribuições de controle de acesso baseado em função do Azure:

    > [!NOTE]
    > Não são necessárias atribuições de função do diretório adicionais do Azure AD.

    - [Colaborador da Máquina Virtual](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) para criar um conjunto de dimensionamento de máquinas virtuais e habilitar e remover identidade gerenciada atribuída ao usuário e/ou ao sistema de um conjunto de dimensionamento de máquinas virtuais.
    - [Função de Contratada de Identidade Gerenciada](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) para criar uma identidade gerenciada atribuída pelo usuário.
    - [Papel de Operador de Identidade Gerenciado](/azure/role-based-access-control/built-in-roles#managed-identity-operator) para atribuir e remover uma identidade gerenciada atribuída pelo usuário de e para um conjunto de dimensionamento de máquina virtual.

## <a name="azure-resource-manager-templates"></a>Modelos do Azure Resource Manager

Assim como com o Portal do Azure e o script, os modelos do [Azure Resource Manager](../../azure-resource-manager/management/overview.md) permitem implantar recursos novos ou modificados definidos por um grupo de recursos do Azure. Há várias opções disponíveis para a edição e a implantação do modelo, tanto locais quanto baseadas em portal, incluindo:

   - Usando um [modelo personalizado do Azure Marketplace](../../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template), que permite criar um modelo do zero ou baseá-lo em um modelo comum ou [rápido](https://azure.microsoft.com/documentation/templates/)existente.
   - Derivar de um grupo de recursos existente, exportando um modelo da [implantação original](../../azure-resource-manager/templates/export-template-portal.md) ou do [estado atual da implantação](../../azure-resource-manager/templates/export-template-portal.md).
   - Usar um [editor JSON local (por exemplo, VS Code)](../../azure-resource-manager/resource-manager-create-first-template.md), depois carregar e implantar usando o PowerShell ou a CLI.
   - Usar o [projeto do Grupo de Recursos do Azure](../../azure-resource-manager/templates/create-visual-studio-deployment-project.md) do Visual Studio para criar e implantar um modelo.  

Independentemente da opção escolhido, a sintaxe do modelo será a mesma durante a implantação inicial e a reimplantação. A habilitação de identidades gerenciadas para recursos do Azure em uma VM nova ou existente é feita da mesma maneira. Além disso, por padrão, o Azure Resource Manager faz uma [atualização incremental](../../azure-resource-manager/templates/deployment-modes.md) para implantações.

## <a name="system-assigned-managed-identity"></a>Identidade gerenciada atribuída pelo sistema

Nesta seção, você habilitará e desabilitará a identidade gerenciada atribuída ao sistema usando um modelo do Azure Resource Manager.

### <a name="enable-system-assigned-managed-identity-during-creation-the-creation-of-a-virtual-machines-scale-set-or-an-existing-virtual-machine-scale-set"></a>Habilite a identidade gerenciada atribuída ao sistema durante a criação de um conjunto de escala de máquinas virtuais ou de um conjunto de escala de máquinavirtual existente

1. Se você entrar no Azure localmente ou por meio do Portal do Azure, use uma conta que esteja associada à assinatura do Azure que contém o conjunto de dimensionamento de máquinas virtuais.
2. Para habilitar a identidade gerenciada atribuída ao sistema, carregue o modelo em um editor, localize o recurso `Microsoft.Compute/virtualMachinesScaleSets` de interesse dentro da seção recursos e adicione a propriedade `identity` no mesmo nível que a propriedade `"type": "Microsoft.Compute/virtualMachinesScaleSets"`. Use a seguinte sintaxe:

   ```JSON
   "identity": {
       "type": "SystemAssigned"
   }
   ```

> [!NOTE]
> Você pode opcionalmente provisionar as identidades gerenciadas para a extensão de `extensionProfile` configuração de escala de máquina virtual de recursos do Azure, especificando-a no elemento do modelo. Essa etapa é opcional, pois você pode usar o ponto de extremidade de identidade do Serviço de Metadados da Instância do Azure (IMDS) para recuperar também os tokens.  Para obter mais informações, consulte [Migrar da extensão VM para o Azure IMDS para autenticação](howto-migrate-vm-extension.md).


4. Quando tiver concluído, as seções a seguir deverão ser adicionadas à seção recurso do modelo e serem semelhantes ao seguinte:

   ```json
    "resources": [
        {
            //other resource provider properties...
            "apiVersion": "2018-06-01",
            "type": "Microsoft.Compute/virtualMachineScaleSets",
            "name": "[variables('vmssName')]",
            "location": "[resourceGroup().location]",
            "identity": {
                "type": "SystemAssigned",
            },
           "properties": {
                //other resource provider properties...
                "virtualMachineProfile": {
                    //other virtual machine profile properties...
                    //The following appears only if you provisioned the optional virtual machine scale set extension (to be deprecated)
                    "extensionProfile": {
                        "extensions": [
                            {
                                "name": "ManagedIdentityWindowsExtension",
                                "properties": {
                                  "publisher": "Microsoft.ManagedIdentity",
                                  "type": "ManagedIdentityExtensionForWindows",
                                  "typeHandlerVersion": "1.0",
                                  "autoUpgradeMinorVersion": true,
                                  "settings": {
                                      "port": 50342
                                  }
                                }
                            }
                        ]
                    }
                }
            }
        }
    ]
   ```

### <a name="disable-a-system-assigned-managed-identity-from-an-azure-virtual-machine-scale-set"></a>Desabilitar uma identidade gerenciada atribuída ao sistema de um conjunto de dimensionamento de máquinas virtuais do Azure

Se você tiver um conjunto de dimensionamento de máquinas virtuais que não precise mais de uma identidade gerenciada atribuída ao sistema:

1. Se você entrar no Azure localmente ou por meio do Portal do Azure, use uma conta que esteja associada à assinatura do Azure que contém o conjunto de dimensionamento de máquinas virtuais.

2. Carregue o modelo em um [editor](#azure-resource-manager-templates) e localize o recurso `Microsoft.Compute/virtualMachineScaleSets` de interesse na seção `resources`. Se você tiver uma VM que tenha apenas a identidade gerenciada atribuída ao sistema, poderá desabilitá-la alterando o tipo de identidade para `None`.

   **Microsoft.Compute/virtualMachineScaleSets API versão 01-06-2018**

   Se apiVersion for `2018-06-01` e a VM tiver ambas as identidades gerenciadas atribuídas ao usuário e ao sistema, remova `SystemAssigned` do tipo de identidade e mantenha `UserAssigned` com os valores de dicionário userAssignedIdentities.

   **Microsoft.Compute/virtualMachineScaleSets API versão 01-06-2018**

   Se apiVersion for `2017-12-01` e o conjunto de dimensionamento de máquinas virtuais tiver ambas as identidades gerenciadas atribuídas ao usuário e ao sistema, remova `SystemAssigned` do tipo de identidade e mantenha `UserAssigned` juntamente com a matriz `identityIds` das identidades gerenciadas atribuídas ao usuário.



   O exemplo a seguir mostra como remover uma identidade gerenciada atribuída ao sistema de um conjunto de dimensionamento de máquinas virtuais sem identidades gerenciadas atribuídas ao usuário:

   ```json
   {
       "name": "[variables('vmssName')]",
       "apiVersion": "2018-06-01",
       "location": "[parameters(Location')]",
       "identity": {
           "type": "None"
        }

   }
   ```

## <a name="user-assigned-managed-identity"></a>Identidade gerenciada atribuída pelo usuário

Nesta seção, você atribui uma identidade gerenciada atribuída ao usuário a um conjunto de dimensionamento de máquinas virtuais usando o modelo do Azure Resource Manager.

> [!Note]
> Para criar uma identidade gerenciada atribuída ao usuário usando um modelo do Azure Resource Manager, consulte [Criar uma identidade gerenciada atribuída ao usuário](how-to-manage-ua-identity-arm.md#create-a-user-assigned-managed-identity).

### <a name="assign-a-user-assigned-managed-identity-to-a-virtual-machine-scale-set"></a>Atribuir uma identidade gerenciada atribuída pelo usuário a um conjunto de dimensionamento de máquinas virtuais

1. No elemento `resources`, adicione a seguinte entrada para atribuir uma identidade gerenciada atribuída ao usuário ao conjunto de dimensionamento de máquinas virtuais.  Certifique-se de substituir `<USERASSIGNEDIDENTITY>` pelo nome da identidade gerenciada atribuída ao usuário que você criou.

   **Microsoft.Compute/virtualMachineScaleSets API versão 01-06-2018**

   Se a apiVersion for `2018-06-01`, as identidades gerenciadas atribuídas ao usuário serão armazenadas no formato de dicionário `userAssignedIdentities` e o valor `<USERASSIGNEDIDENTITYNAME>` deverá ser armazenado em uma variável definida na seção `variables` do modelo.

   ```json
   {
       "name": "[variables('vmssName')]",
       "apiVersion": "2018-06-01",
       "location": "[parameters(Location')]",
       "identity": {
           "type": "userAssigned",
           "userAssignedIdentities": {
               "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',variables('<USERASSIGNEDIDENTITYNAME>'))]": {}
           }
       }

   }
   ```   

   **Microsoft.Compute/virtualMachineScaleSets API versão 01-12-2017**

   Se `apiVersion` for `2017-12-01` ou anterior, as identidades gerenciadas atribuídas ao usuário serão armazenadas na matriz `identityIds` e o valor `<USERASSIGNEDIDENTITYNAME>` deverá ser armazenado em uma seção de variáveis definida na seção do modelo.

   ```json
   {
       "name": "[variables('vmssName')]",
       "apiVersion": "2017-03-30",
       "location": "[parameters(Location')]",
       "identity": {
           "type": "userAssigned",
           "identityIds": [
               "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',variables('<USERASSIGNEDIDENTITY>'))]"
           ]
       }

   }
   ```
> [!NOTE]
> Você pode opcionalmente provisionar as identidades gerenciadas para a extensão de `extensionProfile` configuração de escala de máquina virtual de recursos do Azure, especificando-a no elemento do modelo. Essa etapa é opcional, pois você pode usar o ponto de extremidade de identidade do Serviço de Metadados da Instância do Azure (IMDS) para recuperar também os tokens.  Para obter mais informações, consulte [Migrar da extensão VM para o Azure IMDS para autenticação](howto-migrate-vm-extension.md).

3. Quando terminar, seu modelo deverá ser semelhante ao seguinte:

   **Microsoft.Compute/virtualMachineScaleSets API versão 01-06-2018**   

   ```json
   "resources": [
        {
            //other resource provider properties...
            "apiVersion": "2018-06-01",
            "type": "Microsoft.Compute/virtualMachineScaleSets",
            "name": "[variables('vmssName')]",
            "location": "[resourceGroup().location]",
            "identity": {
                "type": "UserAssigned",
                "userAssignedIdentities": {
                    "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',variables('<USERASSIGNEDIDENTITYNAME>'))]": {}
                }
            },
           "properties": {
                //other virtual machine properties...
                "virtualMachineProfile": {
                    //other virtual machine profile properties...
                    //The following appears only if you provisioned the optional virtual machine scale set extension (to be deprecated)
                    "extensionProfile": {
                        "extensions": [
                            {
                                "name": "ManagedIdentityWindowsExtension",
                                "properties": {
                                  "publisher": "Microsoft.ManagedIdentity",
                                  "type": "ManagedIdentityExtensionForWindows",
                                  "typeHandlerVersion": "1.0",
                                  "autoUpgradeMinorVersion": true,
                                  "settings": {
                                      "port": 50342
                                  }
                                }
                            }
                        ]
                    }
                }
            }
        }
    ]
   ```

   **Microsoft.Compute/virtualMachines API versão 2017-12-01**

   ```json
   "resources": [
        {
            //other resource provider properties...
            "apiVersion": "2017-12-01",
            "type": "Microsoft.Compute/virtualMachineScaleSets",
            "name": "[variables('vmssName')]",
            "location": "[resourceGroup().location]",
            "identity": {
                "type": "UserAssigned",
                "identityIds": [
                    "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',variables('<USERASSIGNEDIDENTITYNAME>'))]"
                ]
            },
           "properties": {
                //other virtual machine properties...
                "virtualMachineProfile": {
                    //other virtual machine profile properties...
                    //The following appears only if you provisioned the optional virtual machine scale set extension (to be deprecated)    
                    "extensionProfile": {
                        "extensions": [
                            {
                                "name": "ManagedIdentityWindowsExtension",
                                "properties": {
                                  "publisher": "Microsoft.ManagedIdentity",
                                  "type": "ManagedIdentityExtensionForWindows",
                                  "typeHandlerVersion": "1.0",
                                  "autoUpgradeMinorVersion": true,
                                  "settings": {
                                      "port": 50342
                                  }
                                }
                            }
                        ]
                    }
                }
            }
        }
    ]
   ```
   ### <a name="remove-user-assigned-managed-identity-from-an-azure-virtual-machine-scale-set"></a>Remover a identidade gerenciada atribuída ao usuário de um conjunto de dimensionamento de máquinas virtuais do Azure

Se você tiver um conjunto de dimensionamento de máquinas virtuais que não precise mais de uma identidade gerenciada atribuída ao usuário:

1. Se você entrar no Azure localmente ou por meio do Portal do Azure, use uma conta que esteja associada à assinatura do Azure que contém o conjunto de dimensionamento de máquinas virtuais.

2. Carregue o modelo em um [editor](#azure-resource-manager-templates) e localize o recurso `Microsoft.Compute/virtualMachineScaleSets` de interesse na seção `resources`. Caso tenha um conjunto de dimensionamento de máquinas virtuais que tenha apenas a identidade gerenciada atribuída ao usuário, desabilite-a alterando o tipo de identidade para `None`.

   O exemplo a seguir mostra como remover todas as identidades gerenciadas atribuídas ao usuário de uma VM sem identidades gerenciadas atribuídas ao sistema:

   ```json
   {
       "name": "[variables('vmssName')]",
       "apiVersion": "2018-06-01",
       "location": "[parameters(Location')]",
       "identity": {
           "type": "None"
        }
   }
   ```

   **Microsoft.Compute/virtualMachineScaleSets API versão 01-06-2018**

   Para remover uma única identidade gerenciada atribuída pelo usuário de um conjunto de dimensionamento de máquinas virtuais, remova-a do dicionário `userAssignedIdentities`.

   Se você tiver uma identidade atribuída ao sistema, mantenha-a no valor `type` no valor `identity`.

   **Microsoft.Compute/virtualMachineScaleSets API versão 01-12-2017**

   Para remover uma única identidade gerenciada atribuída ao usuário de um conjunto de dimensionamento de máquinas virtuais, remova-a da matriz `identityIds`.

   Se você tiver uma identidade gerenciada atribuída ao sistema, mantenha-a no valor `type` no valor `identity`.

## <a name="next-steps"></a>Próximas etapas

- [Identidades gerenciadas para visão geral dos recursos do Azure](overview.md).
