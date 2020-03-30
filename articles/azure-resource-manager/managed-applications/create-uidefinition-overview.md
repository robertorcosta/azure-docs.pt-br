---
title: CreateUiDefinition.json arquivo para painel de portal
description: Descreve como criar definições de interface de usuário para o portal Azure. Usado na definição de aplicativos gerenciados do Azure.
author: tfitzmac
ms.topic: conceptual
ms.date: 08/06/2019
ms.author: tomfitz
ms.openlocfilehash: 2956c76f5bec353639b39228b982db21b6932deb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80294904"
---
# <a name="createuidefinitionjson-for-azure-managed-applications-create-experience"></a>CreateUiDefinition.json para a experiência de criação do aplicativo gerenciado do Azure

Este documento introduz os conceitos principais do arquivo **createUiDefinition.json** que o portal Azure usa para definir a interface do usuário ao criar um aplicativo gerenciado.

O modelo é o seguinte

```json
{
   "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
   "handler": "Microsoft.Azure.CreateUIDef",
   "version": "0.1.2-preview",
   "parameters": {
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

O manipulador deve `Microsoft.Azure.CreateUIDef`ser sempre , e `0.1.2-preview`a versão mais recente suportada é .

O esquema da propriedade parameters depende da combinação do manipulador e da versão especificados. Para aplicativos gerenciados, as propriedades com suporte são `basics`, `steps` e `outputs`. As propriedades basic e steps contêm os [elementos](create-uidefinition-elements.md), como caixas de texto e listas suspensas, que serão exibidos no portal do Azure. A propriedade outputs é usada para mapear os valores de saída de elementos especificados para os parâmetros do modelo de implantação do Azure Resource Manager.

A inclusão de `$schema` é opcional, mas recomendada. Se especificado, o valor de `version` deve corresponder à versão dentro do URI `$schema`.

Você pode usar um editor JSON para criar o createUiDefinition e testá-lo no [createUiDefinition Sandbox](https://portal.azure.com/?feature.customPortal=false&#blade/Microsoft_Azure_CreateUIDef/SandboxBlade) para visualizá-lo. Para obter mais informações sobre a caixa de areia, consulte [Teste sua interface de portal para aplicativos gerenciados do Azure](test-createuidefinition.md).

## <a name="basics"></a>Noções básicas

O básico é o primeiro passo gerado quando o portal Azure analisa o arquivo. Além de exibir os elementos especificados em `basics`, o portal injeta elementos para que os usuários escolham a assinatura, o grupo de recursos e o local da implantação. Quando possível, elementos que consultam parâmetros de implantação em toda a área, como o nome de um cluster ou credenciais de administrador, devem ir nesta etapa.

## <a name="steps"></a>Etapas

A propriedade steps pode conter uma ou mais etapas adicionais para exibir depois de basics, cada uma contendo um ou mais elementos. Considere adicionar etapas por função ou camada do aplicativo que está sendo implantado. Por exemplo, adicione um passo para entradas de nó mestre e um passo para os nós do trabalhador em um cluster.

## <a name="outputs"></a>outputs

O portal do Azure usa a propriedade `outputs` para mapear os elementos de `basics` e `steps` para os parâmetros do modelo de implantação do Azure Resource Manager. As chaves do dicionário são os nomes dos parâmetros do modelo, e os valores são propriedades dos objetos da saída dos elementos referenciados.

Para definir o nome de recurso do aplicativo gerenciado, inclua um valor chamado `applicationResourceName` na propriedade outputs. Se você não definir esse valor, o aplicativo atribui um GUID para o nome. Você pode incluir uma caixa de texto na interface do usuário que solicita um nome de usuário.

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

Para filtrar os locais disponíveis apenas para os locais que suportam os tipos de recursos a serem implantados, forneça uma matriz dos tipos de recursos. Se você fornecer mais de um tipo de recurso, apenas os locais que suportam todos os tipos de recursos serão devolvidos. Essa propriedade é opcional.

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

CreateUiDefinition fornece [funções](create-uidefinition-functions.md) para trabalhar com entradas e saídas de elementos e recursos como condicionalidades. Essas funções são semelhantes tanto na sintaxe quanto na funcionalidade das funções de modelo do Azure Resource Manager.

## <a name="next-steps"></a>Próximas etapas

O próprio arquivo createUiDefinition.json tem um esquema simples. A profundidade real dele é proveniente de todos os elementos e funções com suporte. Esses itens são descritos mais detalhadamente em:

- [Elementos](create-uidefinition-elements.md)
- [Funções](create-uidefinition-functions.md)

Um esquema JSON atual para createUiDefinition está disponível aqui: `https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json`.

Para ver um arquivo de interface do usuário de exemplo, consulte [createUiDefinition.json](https://github.com/Azure/azure-managedapp-samples/blob/master/Managed%20Application%20Sample%20Packages/201-managed-app-using-existing-vnet/createUiDefinition.json).
