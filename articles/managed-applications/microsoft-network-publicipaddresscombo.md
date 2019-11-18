---
title: Elemento de interface do usuário PublicIpAddressCombo do Azure | Microsoft Docs
description: Descreve o elemento de interface do usuário Microsoft.Network.PublicIpAddressCombo para o Portal do Azure.
services: managed-applications
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: managed-applications
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/28/2018
ms.author: tomfitz
ms.openlocfilehash: 069a8ee1f019d1b21be996084e4902f94076fbf7
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/17/2019
ms.locfileid: "74151495"
---
# <a name="microsoftnetworkpublicipaddresscombo-ui-element"></a>Elemento de interface do usuário Microsoft.Network.PublicIpAddressCombo

Um grupo de controles para selecionar um endereço IP público novo ou existente.

## <a name="ui-sample"></a>Exemplo de interface do usuário

![Microsoft.Network.PublicIpAddressCombo](./media/managed-application-elements/microsoft.network.publicipaddresscombo.png)

- Se o usuário selecionar 'Nenhum' como endereço IP público, a caixa de texto de rótulo do nome de domínio ficará oculta.
- Se o usuário selecionar um endereço IP público existente, a caixa de texto de rótulo do nome de domínio ficará desabilitada. Seu valor é o rótulo de nome de domínio do endereço IP selecionado.
- O sufixo do nome de domínio (por exemplo, westus.cloudapp.azure.com) é atualizado automaticamente com base no local selecionado.

## <a name="schema"></a>ESQUEMA

```json
{
  "name": "element1",
  "type": "Microsoft.Network.PublicIpAddressCombo",
  "label": {
    "publicIpAddress": "Public IP address",
    "domainNameLabel": "Domain name label"
  },
  "toolTip": {
    "publicIpAddress": "",
    "domainNameLabel": ""
  },
  "defaultValue": {
    "publicIpAddressName": "ip01",
    "domainNameLabel": "mydomain"
  },
  "constraints": {
    "required": {
      "domainNameLabel": true
    }
  },
  "options": {
    "hideNone": false,
    "hideDomainNameLabel": false,
    "hideExisting": false,
    "zone": 3
  },
  "visible": true
}
```

## <a name="sample-output"></a>Saída de exemplo

Se o usuário não seleciona nenhum endereço IP público, o controle retorna a seguinte saída:

```json
{
  "newOrExistingOrNone": "none"
}
```

Se o usuário não seleciona um endereço IP novo ou existente, o controle retorna a seguinte saída:

```json
{
  "name": "ip01",
  "resourceGroup": "rg01",
  "domainNameLabel": "mydomain",
  "publicIPAllocationMethod": "Dynamic",
  "sku": "Basic",
  "newOrExistingOrNone": "new"
}
```

- Quando `options.hideNone` for especificado como **true**, `newOrExistingOrNone` terá somente o valor **new** ou **existing**.
- Quando `options.hideDomainNameLabel` for especificado como **true**, `domainNameLabel` não será declarado.

## <a name="remarks"></a>Comentários

- Se `constraints.required.domainNameLabel` é definido como **true**, o usuário deve fornecer um rótulo de nome de domínio ao criar um novo endereço IP público. Endereços IP públicos existentes sem um rótulo não ficam disponíveis para seleção.
- Se `options.hideNone` é definido como **true**, a opção de selecionar **Nenhum** para o endereço IP público fica oculta. O valor padrão é **false**.
- Se `options.hideDomainNameLabel` é definido como **true**, a caixa de texto do rótulo de nome de domínio fica oculta. O valor padrão é **false**.
- Se `options.hideExisting` é true, o usuário não pode escolher um endereço IP público existente. O valor padrão é **false**.
- Para `zone`, os endereços IP públicos estão disponíveis somente para endereços IP públicos para a zona especificada ou zona resiliente.

## <a name="next-steps"></a>Próximas etapas

* Para obter uma introdução à criação de definições de interface do usuário, consulte [Introdução ao CreateUiDefinition](create-uidefinition-overview.md).
* Para obter uma descrição das propriedades comuns em elementos de interface do usuário, consulte [Elementos de CreateUiDefinition](create-uidefinition-elements.md).
