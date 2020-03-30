---
title: Aplicativo gerenciado com identidade gerenciada
description: Configure o Aplicativo Gerenciado com identidade gerenciada para vincular aos recursos existentes, gerenciar os recursos do Azure e fornecer identidade operacional para o Registro de Atividades.
ms.topic: conceptual
ms.author: jobreen
author: jjbfour
ms.date: 05/13/2019
ms.openlocfilehash: dbf75262440474c5cb50a6d733ac7cba212b5f3f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75651651"
---
# <a name="azure-managed-application-with-managed-identity"></a>Aplicativo gerenciado do Azure com identidade gerenciada

> [!NOTE]
> O suporte de identidade gerenciada para aplicativos gerenciados está atualmente em pré-visualização. Use a versão api 2018-09-01-preview para utilizar a Identidade Gerenciada.

Saiba como configurar um aplicativo gerenciado para conter uma identidade gerenciada. A Identidade Gerenciada pode ser usada para permitir que o cliente conceda ao Aplicativo Gerenciado acesso a recursos adicionais existentes. A identidade é gerenciada pela plataforma do Azure e não exige provisionamento ou giro de nenhum segredo. Para obter mais informações sobre identidades gerenciadas no Azure Active Directory (AAD), consulte [Identidades gerenciadas para recursos do Azure](../../active-directory/managed-identities-azure-resources/overview.md).

Seu aplicativo pode receber dois tipos de identidades:

- Uma **identidade atribuída pelo sistema** é vinculada ao seu aplicativo e é excluída se o seu aplicativo for excluído. Um aplicativo só pode ter uma identidade atribuída pelo sistema.
- Uma **identidade atribuída pelo usuário** é um recurso autônomo do Azure que pode ser atribuído ao seu aplicativo. Um aplicativo pode ter várias identidades atribuídas pelo usuário.

## <a name="how-to-use-managed-identity"></a>Como usar a Identidade Gerenciada

A Identidade Gerenciada permite muitos cenários para aplicativos gerenciados. Alguns cenários comuns que podem ser resolvidos são:

- Implantação de um aplicativo gerenciado vinculado aos recursos existentes do Azure. Um exemplo é a implantação de uma máquina virtual Azure (VM) dentro do Aplicativo Gerenciado que está conectado a uma [interface de rede existente](../../virtual-network/virtual-network-network-interface-vm.md).
- Concedendo ao aplicativo gerenciado e ao acesso do editor aos recursos do Azure fora do **grupo de recursos gerenciados**.
- Fornecendo uma identidade operacional de aplicativos gerenciados para registro de atividades e outros serviços dentro do Azure.

## <a name="adding-managed-identity"></a>Adicionando identidade gerenciada

Criar um aplicativo gerenciado com uma identidade gerenciada requer que uma propriedade adicional seja definida no recurso Azure. O exemplo a seguir mostra uma propriedade **de identidade** de amostra:

```json
{
"identity": {
    "type": "SystemAssigned, UserAssigned",
    "userAssignedIdentities": {
        "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.ManagedIdentity/userassignedidentites/myuserassignedidentity": {}
    }
}
```

Existem duas maneiras comuns de criar um aplicativo gerenciado com **identidade**: [createUIDefinition.json](./create-uidefinition-overview.md) e [azure Resource Manager .](../templates/template-syntax.md) Para cenários simples de criação única, createUIDefinition deve ser usado para ativar a Identidade Gerenciada, porque proporciona uma experiência mais rica. No entanto, ao lidar com sistemas avançados ou complexos que requerem implantações automatizadas ou múltiplas de aplicativos gerenciados, os modelos podem ser usados.

### <a name="using-createuidefinition"></a>Usando createuiDefinition

Um aplicativo gerenciado pode ser configurado com identidade gerenciada através do [CreateUIDefinition.json](./create-uidefinition-overview.md). Na [seção saídas,](./create-uidefinition-overview.md#outputs)a chave `managedIdentity` pode ser usada para substituir a propriedade de identidade do modelo Aplicativo Gerenciado. O abaixo da amostra habilitará a identidade **atribuída ao sistema** no aplicativo gerenciado. Objetos de identidade mais complexos podem ser formados usando elementos createUIDefinition para pedir entradas ao consumidor. Essas entradas podem ser usadas para construir aplicativos gerenciados com **identidade atribuída pelo usuário**.

```json
"outputs": {
    "managedIdentity": "[parse('{\"Type\":\"SystemAssigned\"}')]"
}
```

#### <a name="when-to-use-createuidefinition-for-managed-identity"></a>Quando usar CreateUIDefinition for Managed Identity

Abaixo estão algumas recomendações sobre quando usar CreateUIDefinition para habilitar identidade gerenciada em aplicativos gerenciados.

- A criação do Aplicativo Gerenciado passa pelo portal ou marketplace do Azure.
- A Identidade Gerenciada requer uma entrada complexa do consumidor.
- A Identidade Gerenciada é necessária na criação do Aplicativo Gerenciado.

#### <a name="systemassigned-createuidefinition"></a>SistemaAtribuído CreateUIDefinition

Uma CreateUIDefinition básica que permite a identidade atribuída ao Sistema Para o Aplicativo Gerenciado.

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
        ],
        "outputs": {
            "managedIdentity": "[parse('{\"Type\":\"SystemAssigned\"}')]"
        }
    }
}
```

#### <a name="userassigned-createuidefinition"></a>Criação atribuída pelo usuárioUIDefinition

Uma CreateUIDefinition básica que toma um recurso **de identidade atribuído pelo usuário** como entrada e permite a identidade atribuída ao Usuário para o aplicativo gerenciado.

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
                "name": "manageIdentity",
                "label": "Identity",
                "subLabel": {
                    "preValidation": "Manage Identities",
                    "postValidation": "Done"
                },
                "bladeTitle": "Identity",
                "elements": [
                    {
                        "name": "userAssignedText",
                        "type": "Microsoft.Common.TextBox",
                        "label": "User assigned managed identity",
                        "defaultValue": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.ManagedIdentity/userassignedidentites/myuserassignedidentity",
                        "visible": true
                    }
                ]
            }
        ],
        "outputs": {
            "managedIdentity": "[parse(concat('{\"Type\":\"UserAssigned\",\"UserAssignedIdentities\":{',string(steps('manageIdentity').userAssignedText),':{}}}'))]"
        }
    }
}
```

O CreateUIDefinition.json acima gera uma experiência de criação do usuário que tem uma caixa de texto para um consumidor inserir o ID de recurso azure **de identidade atribuído pelo usuário.** A experiência gerada se pareceria com:

![Exemplo de criação de identidade atribuída pelo usuárioUIDefinition](./media/publish-managed-identity/user-assigned-identity.png)

### <a name="using-azure-resource-manager-templates"></a>Usando modelos do Gerenciador de Recursos do Azure

> [!NOTE]
> Os modelos de aplicativos gerenciados pelo marketplace são gerados automaticamente para os clientes que passam pelo portal Azure criar experiência.
> Para esses cenários, a `managedIdentity` chave de saída no CreateUIDefinition deve ser usada para habilitar a identidade.

A Identidade Gerenciada também pode ser habilitada através de modelos do Azure Resource Manager. O abaixo da amostra habilitará a identidade **atribuída ao sistema** no aplicativo gerenciado. Objetos de identidade mais complexos podem ser formados usando parâmetros de modelo do Azure Resource Manager para fornecer entradas. Essas entradas podem ser usadas para construir aplicativos gerenciados com **identidade atribuída pelo usuário**.

#### <a name="when-to-use-azure-resource-manager-templates-for-managed-identity"></a>Quando usar os modelos do Azure Resource Manager para identidade gerenciada

Abaixo estão algumas recomendações sobre quando usar os modelos do Azure Resource Manager para habilitar identidade gerenciada em aplicativos gerenciados.

- Aplicativos gerenciados podem ser implantados programáticamente com base em um modelo.
- As atribuições de função personalizadas para a Identidade Gerenciada são necessárias para provisionar o aplicativo gerenciado.
- O Aplicativo Gerenciado não precisa do fluxo de criação do portal Azure e do marketplace.

#### <a name="systemassigned-template"></a>Modelo atribuído ao Sistema

Um modelo básico do Azure Resource Manager que implanta um aplicativo gerenciado com identidade **atribuída ao sistema.**

```json
"resources": [
    {
        "type": "Microsoft.Solutions/applications",
        "name": "[parameters('applicationName')]",
        "apiVersion": "2018-09-01-preview",
        "location": "[parameters('location')]",
        "identity": {
            "type": "SystemAssigned"
        },
        "properties": {
            "ManagedResourceGroupId": "[parameters('managedByResourceGroupId')]",
            "parameters": { }
        }
    }
]
```

### <a name="userassigned-template"></a>Modelo atribuído pelo usuário

Um modelo básico do Azure Resource Manager que implanta um aplicativo gerenciado com uma **identidade atribuída pelo usuário**.

```json
"resources": [
    {
      "type": "Microsoft.ManagedIdentity/userAssignedIdentities",
      "name": "[parameters('managedIdentityName')]",
      "apiVersion": "2018-11-30",
      "location": "[parameters('location')]"
    },
    {
        "type": "Microsoft.Solutions/applications",
        "name": "[parameters('applicationName')]",
        "apiVersion": "2018-09-01-preview",
        "location": "[parameters('location')]",
        "identity": {
            "type": "UserAssigned",
            "userAssignedIdentities": {
                "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',parameters('managedIdentityName'))]": {}
            }
        },
        "properties": {
            "ManagedResourceGroupId": "[parameters('managedByResourceGroupId')]",
            "parameters": { }
        }
    }
]
```

## <a name="granting-access-to-azure-resources"></a>Concessão de acesso aos recursos do Azure

Uma vez que um aplicativo gerenciado é concedido uma identidade, ele pode ser concedido acesso aos recursos azure existentes. Esse processo pode ser feito através da interface de controle de acesso (IAM) no portal Azure. O nome do aplicativo gerenciado ou **da identidade atribuída pelo usuário** pode ser pesquisado para adicionar uma atribuição de função.

![Adicionar atribuição de função para aplicativo gerenciado](./media/publish-managed-identity/identity-role-assignment.png)

## <a name="linking-existing-azure-resources"></a>Vinculando os recursos existentes do Azure

> [!NOTE]
> Uma **identidade atribuída pelo usuário** deve ser [configurada](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md) antes de implantar o Aplicativo Gerenciado. Além disso, a implantação de recursos vinculados de Aplicativos Gerenciados só é suportada para o tipo **de marketplace.**

A Identidade Gerenciada também pode ser usada para implantar um aplicativo gerenciado que requer acesso aos recursos existentes durante sua implantação. Quando o aplicativo gerenciado é provisionado pelo cliente, **as identidades atribuídas pelo usuário** podem ser adicionadas para fornecer autorizações adicionais à implantação **principalTemplate.**

### <a name="authoring-the-createuidefinition-with-a-linked-resource"></a>Criação do CreateUIDefinition com um recurso vinculado

Ao vincular a implantação do Aplicativo Gerenciado aos recursos existentes, tanto o recurso Azure existente quanto uma **identidade atribuída pelo usuário** com a atribuição de função aplicável nesse recurso devem ser fornecidos.

 Uma amostra CreateUIDefinition que requer duas entradas: um ID de recurso de interface de rede e um id de recurso de identidade atribuído pelo usuário.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
    "handler": "Microsoft.Compute.MultiVm",
    "version": "0.1.2-preview",
    "parameters": {
        "basics": [
            {}
        ],
        "steps": [
            {
                "name": "managedApplicationSetting",
                "label": "Managed Application Settings",
                "subLabel": {
                    "preValidation": "Managed Application Settings",
                    "postValidation": "Done"
                },
                "bladeTitle": "Managed Application Settings",
                "elements": [
                    {
                        "name": "networkInterfaceId",
                        "type": "Microsoft.Common.TextBox",
                        "label": "network interface resource id",
                        "defaultValue": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Network/networkInterfaces/existingnetworkinterface",
                        "toolTip": "Must represent the identity as an Azure Resource Manager resource identifer format ex. /subscriptions/sub1/resourcegroups/myGroup/providers/Microsoft.Network/networkInterfaces/networkinterface1",
                        "visible": true
                    },
                    {
                        "name": "userAssignedId",
                        "type": "Microsoft.Common.TextBox",
                        "label": "user assigned identity resource id",
                        "defaultValue": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.ManagedIdentity/userassignedidentites/myuserassignedidentity",
                        "toolTip": "Must represent the identity as an Azure Resource Manager resource identifer format ex. /subscriptions/sub1/resourcegroups/myGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/identity1",
                        "visible": true
                    }
                ]
            }
        ],
        "outputs": {
            "existingNetworkInterfaceId": "[steps('managedApplicationSetting').networkInterfaceId]",
            "managedIdentity": "[parse(concat('{\"Type\":\"UserAssigned\",\"UserAssignedIdentities\":{',string(steps('managedApplicationSetting').userAssignedId),':{}}}'))]"
        }
    }
}
```

Este CreateUIDefinition.json gera uma experiência de criação do usuário que tem dois campos. O primeiro campo permite que o usuário insira o ID de recurso do Azure para que o recurso seja vinculado à implantação do Aplicativo Gerenciado. A segunda é para que um consumidor insira o ID de recurso do Azure **de identidade atribuído pelo usuário,** que tem acesso ao recurso vinculado do Azure. A experiência gerada se pareceria com:

![Sample CreateUIDefinition com duas entradas: um ID de recurso de interface de rede e um ID de recurso de identidade atribuído pelo usuário](./media/publish-managed-identity/network-interface-cuid.png)

### <a name="authoring-the-maintemplate-with-a-linked-resource"></a>Autor do mainTemplate com um recurso vinculado

Além de atualizar o CreateUIDefinition, o modelo principal também precisa ser atualizado para aceitar o id de recurso vinculado. O modelo principal pode ser atualizado para aceitar a nova saída adicionando um novo parâmetro. Uma `managedIdentity` vez que a saída substitui o valor no modelo de aplicativo gerenciado gerado, ele não é passado para o modelo principal e não deve ser incluído na seção parâmetros.

Um modelo principal de exemplo que define o perfil de rede para uma interface de rede existente fornecida pela CreateUIDefinition.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "existingNetworkInterfaceId": { "type": "string" }
    },
    "variables": {
    },
    "resources": [
        {
            "apiVersion": "2016-04-30-preview",
            "type": "Microsoft.Compute/virtualMachines",
            "name": "myLinkedResourceVM",
            "location": "[resourceGroup().location]",
            "properties": {
                …,
                "networkProfile": {
                    "networkInterfaces": [
                        {
                            "id": "[parameters('existingNetworkInterfaceId')]"
                        }
                    ]
                }
            }
        }
    ]
}
```

### <a name="consuming-the-managed-application-with-a-linked-resource"></a>Consumir o Aplicativo Gerenciado com um recurso vinculado

Uma vez criado o pacote de aplicativo gerenciado, o aplicativo gerenciado pode ser consumido através do portal Azure. Antes de ser consumido, existem várias etapas pré-requisitos.

- Uma instância do recurso Azure vinculado necessário deve ser criada.
- A **identidade atribuída pelo usuário** deve ser criada e [dada atribuições de função](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md) ao recurso vinculado.
- O ID de recurso vinculado existente e o ID **de identidade atribuído pelo usuário** são fornecidos à CreateUIDefinition.

## <a name="accessing-the-managed-identity-token"></a>Acessando o token de identidade gerenciada

O token do Aplicativo Gerenciado agora pode `listTokens` ser acessado através da api do inquilino do editor. Um pedido de exemplo pode parecer:

``` HTTP
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Solutions/applications/{applicationName}/listTokens?api-version=2018-09-01-preview HTTP/1.1

{
    "authorizationAudience": "https://management.azure.com/",
    "userAssignedIdentities": [
        "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{userAssignedIdentityName}"
    ]
}
```

Solicitar parâmetros do corpo:

Parâmetro | Obrigatório | Descrição
---|---|---
autorizaçãoAudiência de audiência | *Não* | O App ID URI do recurso de destino. É também `aud` a reivindicação (audiência) do token emitido. O valor padrãohttps://management.azure.com/é " "
userAssignedIdentities | *Não* | A lista de identidades gerenciadas atribuídas pelo usuário para recuperar um token para. Se não for `listTokens` especificado, retornará o token para a identidade gerenciada atribuída pelo sistema.


Uma resposta de amostra pode parecer:

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json

{
    "value": [
        {
            "access_token": "eyJ0eXAi…",
            "expires_in": "2…",
            "expires_on": "1557…",
            "not_before": "1557…",
            "authorizationAudience": "https://management.azure.com/",
            "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Solutions/applications/{applicationName}",
            "token_type": "Bearer"
        }
    ]
}
```

A resposta conterá uma matriz de `value` tokens a propriedade:

Parâmetro | Descrição
---|---
access_token | O token de acesso solicitado.
expires_in | O número de segundos que o token de acesso será válido.
expires_on | O período de expiração do token de acesso. Isso é representado como o número de segundos da época.
not_before | O tempo quando o token de acesso faz efeito. Isso é representado como o número de segundos da época.
autorizaçãoAudiência de audiência | O `aud` (público) o token de acesso foi solicitado. Isso é o mesmo que foi `listTokens` fornecido no pedido.
resourceId | O ID de recurso do Azure para o token emitido. Este é o ID do aplicativo gerenciado ou o ID de identidade atribuído pelo usuário.
token_type | O tipo do símbolo.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Como configurar um aplicativo gerenciado com um provedor personalizado](../custom-providers/overview.md)
