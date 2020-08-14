---
title: CreateUiDefinition.jsno arquivo para o painel do portal
description: Descreve como criar definições de interface do usuário para o portal do Azure. Usado ao definir aplicativos gerenciados do Azure.
author: tfitzmac
ms.topic: conceptual
ms.date: 07/14/2020
ms.author: tomfitz
ms.openlocfilehash: 0e2aee194d3c97655dd4ec5aaeea46fb607c4c5e
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/14/2020
ms.locfileid: "88210957"
---
# <a name="createuidefinitionjson-for-azure-managed-applications-create-experience"></a>CreateUiDefinition.json para a experiência de criação do aplicativo gerenciado do Azure

Este documento apresenta os principais conceitos do **createUiDefinition.jsno** arquivo. O portal do Azure usa esse arquivo para definir a interface do usuário ao criar um aplicativo gerenciado.

O modelo é o seguinte

```json
{
    "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
    "handler": "Microsoft.Azure.CreateUIDef",
    "version": "0.1.2-preview",
    "parameters": {
        "config": {
            "basics": { }
        },
        "basics": [ ],
        "steps": [ ],
        "outputs": { },
        "resourceTypes": [ ]
    }
}
```

Um CreateUiDefinition sempre contém três propriedades: 

* handler
* version
* parâmetros

O manipulador deve sempre ser `Microsoft.Azure.CreateUIDef` , e a versão mais recente com suporte é `0.1.2-preview` .

O esquema da propriedade parameters depende da combinação do manipulador e da versão especificados. Para aplicativos gerenciados, as propriedades com suporte são `basics` ,, `steps` `outputs` e `config` . As propriedades basic e steps contêm os [elementos](create-uidefinition-elements.md), como caixas de texto e listas suspensas, que serão exibidos no portal do Azure. A propriedade Outputs é usada para mapear os valores de saída dos elementos especificados para os parâmetros do modelo de Azure Resource Manager. Você `config` só usa quando precisa substituir o comportamento padrão da `basics` etapa.

A inclusão de `$schema` é opcional, mas recomendada. Se especificado, o valor de `version` deve corresponder à versão dentro do URI `$schema`.

Você pode usar um editor de JSON para criar seu createUiDefinition e testá-lo na [área restrita do createUiDefinition](https://portal.azure.com/?feature.customPortal=false&#blade/Microsoft_Azure_CreateUIDef/SandboxBlade) para visualizá-lo. Para obter mais informações sobre a área restrita, consulte [testar sua interface do portal para aplicativos gerenciados do Azure](test-createuidefinition.md).

## <a name="basics"></a>Básico

A etapa **básico** é a primeira etapa gerada quando o portal do Azure analisa o arquivo. Por padrão, a etapa básico permite que os usuários escolham a assinatura, o grupo de recursos e o local para implantação.

:::image type="content" source="./media/create-uidefinition-overview/basics.png" alt-text="Noções básicas padrão":::

Você pode adicionar mais elementos nesta seção. Quando possível, adicione elementos que consultam parâmetros de toda a implantação, como o nome de um cluster ou credenciais de administrador.

O exemplo a seguir mostra uma caixa de texto que foi adicionada aos elementos padrão.

```json
"basics": [
    {
        "name": "textBox1",
        "type": "Microsoft.Common.TextBox",
        "label": "Textbox on basics",
        "defaultValue": "my text value",
        "toolTip": "",
        "visible": true
    }
]
```

## <a name="config"></a>Config

Você especifica o elemento config quando precisa substituir o comportamento padrão para as etapas básicas. O exemplo a seguir mostra as propriedades disponíveis.

```json
"config": {
    "basics": {
        "description": "Customized description with **markdown**, see [more](https://www.microsoft.com).",
        "subscription": {
            "constraints": {
                "validations": [
                    {
                        "isValid": "[expression for checking]",
                        "message": "Please select a valid subscription."
                    },
                    {
                        "permission": "<Resource Provider>/<Action>",
                        "message": "Must have correct permission to complete this step."
                    }
                ]
            },
            "resourceProviders": [
                "<Resource Provider>"
            ]
        },
        "resourceGroup": {
            "constraints": {
                "validations": [
                    {
                        "isValid": "[expression for checking]",
                        "message": "Please select a valid resource group."
                    }
                ]
            },
            "allowExisting": true
        },
        "location": {
            "label": "Custom label for location",
            "toolTip": "provide a useful tooltip",
            "resourceTypes": [
                "Microsoft.Compute/virtualMachines"
            ],
            "allowedValues": [
                "eastus",
                "westus2"
            ],
            "visible": true
        }
    }
},
```

Para `description` o, forneça uma cadeia de caracteres habilitada para fins de redução que descreve seu recurso. Há suporte para o formato e os links de várias linhas.

Para `location` , especifique as propriedades para o controle de localização que você deseja substituir. Todas as propriedades não substituídas são definidas com seus valores padrão. `resourceTypes` aceita uma matriz de cadeias de caracteres que contêm nomes de tipo de recurso totalmente qualificados. As opções de local são restritas a apenas regiões que dão suporte aos tipos de recursos.  `allowedValues`   aceita uma matriz de cadeias de caracteres de região. Somente essas regiões aparecem na lista suspensa.Você pode definir `allowedValues`   e  `resourceTypes` . O resultado é a interseção de ambas as listas. Por fim, a `visible` propriedade pode ser usada para desabilitar condicionalmente ou completamente a lista suspensa local.  

Os `subscription` `resourceGroup` elementos e permitem que você especifique validações adicionais. A sintaxe para especificar validações é idêntica à validação personalizada para a [caixa de texto](microsoft-common-textbox.md). Você também pode especificar `permission` validações na assinatura ou no grupo de recursos.  

O controle de assinatura aceita uma lista de namespaces do provedor de recursos. Por exemplo, você pode especificar **Microsoft. Compute**. Ele mostra uma mensagem de erro quando o usuário seleciona uma assinatura que não dá suporte ao provedor de recursos. O erro ocorre quando o provedor de recursos não está registrado nessa assinatura e o usuário não tem permissão para registrar o provedor de recursos.  

O controle de grupo de recursos tem uma opção para `allowExisting` . Quando `true` , os usuários podem selecionar grupos de recursos que já têm recursos. Esse sinalizador é mais aplicável aos modelos de solução, em que o comportamento padrão exige que os usuários selecionem um grupo de recursos novo ou vazio. Na maioria dos outros cenários, a especificação dessa propriedade não é necessária.  

## <a name="steps"></a>Etapas

A propriedade Steps contém zero ou mais etapas adicionais a serem exibidas após noções básicas. Cada etapa contém um ou mais elementos. Considere adicionar etapas por função ou camada do aplicativo que está sendo implantado. Por exemplo, adicione uma etapa para entradas de nó mestre e uma etapa para os nós de trabalho em um cluster.

```json
"steps": [
    {
        "name": "demoConfig",
        "label": "Configuration settings",
        "elements": [
          ui-elements-needed-to-create-the-instance
        ]
    }
]
```

## <a name="outputs"></a>Saídas

O portal do Azure usa a propriedade `outputs` para mapear os elementos de `basics` e `steps` para os parâmetros do modelo de implantação do Azure Resource Manager. As chaves do dicionário são os nomes dos parâmetros do modelo, e os valores são propriedades dos objetos da saída dos elementos referenciados.

Para definir o nome de recurso do aplicativo gerenciado, inclua um valor chamado `applicationResourceName` na propriedade outputs. Se você não definir esse valor, o aplicativo atribuirá um GUID para o nome. Você pode incluir uma caixa de texto na interface do usuário que solicita um nome de usuário.

```json
"outputs": {
    "vmName": "[steps('appSettings').vmName]",
    "trialOrProduction": "[steps('appSettings').trialOrProd]",
    "userName": "[steps('vmCredentials').adminUsername]",
    "pwd": "[steps('vmCredentials').vmPwd.password]",
    "applicationResourceName": "[steps('appSettings').vmName]"
}
```

## <a name="resource-types"></a>Tipos de recurso

Para filtrar os locais disponíveis somente para os locais que dão suporte aos tipos de recursos a serem implantados, forneça uma matriz dos tipos de recursos. Se você fornecer mais de um tipo de recurso, somente os locais que dão suporte a todos os tipos de recursos serão retornados. Essa propriedade é opcional.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
    "handler": "Microsoft.Azure.CreateUIDef",
    "version": "0.1.2-preview",
    "parameters": {
        "resourceTypes": ["Microsoft.Compute/disks"],
        "basics": [
          ...
```  

## <a name="functions"></a>Funções

O CreateUiDefinition fornece [funções](create-uidefinition-functions.md) para trabalhar com entradas e saídas de elementos e recursos como condicionais. Essas funções são semelhantes na sintaxe e na funcionalidade para Azure Resource Manager funções de modelo.

## <a name="next-steps"></a>Próximas etapas

O próprio arquivo createUiDefinition.json tem um esquema simples. A profundidade real dele é proveniente de todos os elementos e funções com suporte. Esses itens são descritos mais detalhadamente em:

- [Elementos](create-uidefinition-elements.md)
- [Funções](create-uidefinition-functions.md)

Um esquema JSON atual para createUiDefinition está disponível aqui: `https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json`.

Para ver um arquivo de interface do usuário de exemplo, consulte [createUiDefinition.json](https://github.com/Azure/azure-managedapp-samples/blob/master/Managed%20Application%20Sample%20Packages/201-managed-app-using-existing-vnet/createUiDefinition.json).
