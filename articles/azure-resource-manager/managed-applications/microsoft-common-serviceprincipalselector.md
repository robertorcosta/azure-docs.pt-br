---
title: Elemento de interface do usuário ServicePrincipalSelector
description: Descreve o elemento de interface do usuário Microsoft. Common. ServicePrincipalSelector para portal do Azure. Fornece uma lista suspensa para escolher um identificador de aplicativo e uma caixa de texto para inserir uma senha ou impressão digital de certificado.
author: tfitzmac
ms.topic: conceptual
ms.date: 09/29/2020
ms.author: tomfitz
ms.openlocfilehash: 73b242754bfae53b6df5abd9c2c8dee33b973dad
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91575989"
---
# <a name="microsoftcommonserviceprincipalselector-ui-element"></a>Elemento de interface do usuário Microsoft. Common. ServicePrincipalSelector

Um controle que permite aos usuários selecionar uma entidade de serviço existente ou registrar uma nova. Ao selecionar **criar novo**, você passa pelas etapas para registrar um novo aplicativo. Quando você seleciona um aplicativo existente, o controle fornece uma caixa de texto para inserir uma senha ou impressão digital do certificado.

## <a name="ui-sample"></a>Exemplo de interface do usuário

A exibição padrão é determinada pelos valores na `defaultValue` propriedade. Se a `principalId` Propriedade contiver um GUID (identificador global exclusivo) válido, o controle pesquisará a ID de objeto do aplicativo. O valor padrão se aplicará se o usuário não fizer uma seleção no menu suspenso.

:::image type="content" source="./media/managed-application-elements/microsoft-common-serviceprincipal-initial.png" alt-text="Exibição inicial do Microsoft. Common. ServicePrincipalSelector":::

Quando você seleciona **criar novo** ou um identificador de aplicativo existente no menu suspenso, o **tipo de autenticação** é exibido para inserir uma senha ou impressão digital do certificado na caixa de texto.

:::image type="content" source="./media/managed-application-elements/microsoft-common-serviceprincipal-selection.png" alt-text="Exibição inicial do Microsoft. Common. ServicePrincipalSelector":::

## <a name="schema"></a>Esquema

```json
{
  "name": "ServicePrincipal",
  "type": "Microsoft.Common.ServicePrincipalSelector",
  "label": {
    "principalId": "App Id",
    "password": "Password",
    "certificateThumbprint": "Certificate thumbprint",
    "authenticationType": "Authentication Type",
    "sectionHeader": "Service Principal"
  },
  "toolTip": {
    "principalId": "App Id",
    "password": "Password",
    "certificateThumbprint": "Certificate thumbprint",
    "authenticationType": "Authentication Type"
  },
  "defaultValue": {
    "principalId": "<default guid>",
    "name": "(New) default App Id"
  },
  "constraints": {
    "required": true,
    "regex": "^[a-zA-Z0-9]{8,}$",
    "validationMessage": "Password must be at least 8 characters long, contain only numbers and letters"
  },
  "options": {
    "hideCertificate": false
  },
  "visible": true
}
```

## <a name="remarks"></a>Comentários

- As propriedades obrigatórias são:
  - `name`
  - `type`
  - `label`
  - `defaultValue`: Especifica o padrão `principalId` e `name` .

- As propriedades opcionais são:
  - `toolTip`: Anexa uma dica `infoBalloon` de ferramenta a cada rótulo.
  - `visible`: Oculta ou exibe o controle.
  - `options`: Especifica se a opção de impressão digital do certificado deve ser disponibilizada ou não.
  - `constraints`: Restrições Regex para validação de senha.

## <a name="example"></a>Exemplo

Veja a seguir um exemplo do `Microsoft.Common.ServicePrincipalSelector` controle. A `defaultValue` propriedade `principalId` é definida `<default guid>` como um espaço reservado para um GUID de identificador de aplicativo padrão.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [],
    "steps": [
      {
        "name": "SPNcontrol",
        "label": "SPNcontrol",
        "elements": [
          {
            "name": "ServicePrincipal",
            "type": "Microsoft.Common.ServicePrincipalSelector",
            "label": {
              "principalId": "App Id",
              "password": "Password",
              "certificateThumbprint": "Certificate thumbprint",
              "authenticationType": "Authentication Type",
              "sectionHeader": "Service Principal"
            },
            "toolTip": {
              "principalId": "App Id",
              "password": "Password",
              "certificateThumbprint": "Certificate thumbprint",
              "authenticationType": "Authentication Type"
            },
            "defaultValue": {
              "principalId": "<default guid>",
              "name": "(New) default App Id"
            },
            "constraints": {
              "required": true,
              "regex": "^[a-zA-Z0-9]{8,}$",
              "validationMessage": "Password must be at least 8 characters long, contain only numbers and letters"
            },
            "options": {
              "hideCertificate": false
            },
            "visible": true
          }
        ]
      }
    ],
    "outputs": {
      "appId": "[steps('SPNcontrol').ServicePrincipal.appId]",
      "objectId": "[steps('SPNcontrol').ServicePrincipal.objectId]",
      "password": "[steps('SPNcontrol').ServicePrincipal.password]",
      "certificateThumbprint": "[steps('SPNcontrol').ServicePrincipal.certificateThumbprint]",
      "newOrExisting": "[steps('SPNcontrol').ServicePrincipal.newOrExisting]",
      "authenticationType": "[steps('SPNcontrol').ServicePrincipal.authenticationType]"
    }
  }
}
```

## <a name="example-output"></a>Saída de exemplo

O `appId` é a ID do registro do aplicativo que você selecionou ou criou. O `objectId` é uma matriz de ObjectIDs para as entidades de serviço configuradas para o registro de aplicativo selecionado.

Quando nenhuma seleção é feita na lista suspensa, o `newOrExisting` valor da propriedade é **novo**:

```json
{
  "appId": {
    "value": "<default guid>"
  },
  "objectId": {
    "value": ["<default guid>"]
  },
  "password": {
    "value": "<password>"
  },
  "certificateThumbprint": {
    "value": ""
  },
  "newOrExisting": {
    "value": "new"
  },
  "authenticationType": {
    "value": "password"
  }
}
```

Quando **criar novo** ou um identificador de aplicativo existente é selecionado na lista suspensa, o `newOrExisting` valor da propriedade é **existente**:

```json
{
  "appId": {
    "value": "<guid>"
  },
  "objectId": {
    "value": ["<guid>"]
  },
  "password": {
    "value": "<password>"
  },
  "certificateThumbprint": {
    "value": ""
  },
  "newOrExisting": {
    "value": "existing"
  },
  "authenticationType": {
    "value": "password"
  }
}
```

## <a name="next-steps"></a>Próximas etapas

- Para obter uma introdução à criação de definições de interface do usuário, consulte [Introdução ao CreateUiDefinition](create-uidefinition-overview.md).
- Para obter uma descrição das propriedades comuns em elementos de interface do usuário, consulte [Elementos de CreateUiDefinition](create-uidefinition-elements.md).
