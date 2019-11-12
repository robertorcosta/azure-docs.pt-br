---
title: Elemento de criação da definição da interface do usuário do Azure| Microsoft Docs
description: Descreve os elementos a serem usados ao construir definições da interface do usuário para o Portal do Azure.
author: tfitzmac
ms.service: managed-applications
ms.topic: reference
ms.date: 11/11/2019
ms.author: tomfitz
ms.openlocfilehash: 9f952b8301f1d85d81fcc63e5d46dc57b1fb1106
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73931999"
---
# <a name="createuidefinition-elements"></a>Elementos CreateUiDefinition

Este artigo descreve o esquema e as propriedades de todos os elementos de um CreateUiDefinition com suporte. 

## <a name="schema"></a>ESQUEMA

O esquema para a maioria dos elementos é o seguinte:

```json
{
  "name": "element1",
  "type": "Microsoft.Common.TextBox",
  "label": "Some text box",
  "defaultValue": "my value",
  "toolTip": "Provide a descriptive name.",
  "constraints": {},
  "options": {},
  "visible": true
}
```

| Propriedade | obrigatórios | DESCRIÇÃO |
| -------- | -------- | ----------- |
| name | sim | Um identificador interno para fazer referência a uma instância específica de um elemento. O uso mais comum do nome do elemento está em `outputs`, onde os valores de saída de elementos especificados são mapeados para os parâmetros do modelo. Você também pode usá-lo para associar o valor de saída de um elemento ao `defaultValue` de outro elemento. |
| type | sim | O controle de interface do usuário a ser processado para o elemento. Para obter uma lista dos tipos com suporte, consulte [Elementos](#elements). |
| label | sim | O texto de exibição do elemento. Alguns tipos de elemento contêm vários rótulos e, portanto, o valor pode ser um objeto que contém várias cadeias de caracteres. |
| defaultValue | Não | O valor padrão do elemento. Alguns tipos de elemento dão suporte a valores padrão complexos e, portanto, o valor pode ser um objeto. |
| toolTip | Não | O texto exibido na dica de ferramenta do elemento. Semelhante a `label`, alguns elementos dão suporte a várias cadeias de caracteres de dica de ferramenta. Links embutidos podem ser inseridos usando a sintaxe de markdown.
| constraints | Não | Uma ou mais propriedades que são usadas para personalizar o comportamento de validação do elemento. As propriedades com suporte a restrições variam de acordo com o tipo de elemento. Alguns tipos de elemento não dão suporte à personalização do comportamento de validação e, portanto, não têm nenhuma propriedade de restrições. |
| options | Não | Propriedades adicionais que personalizam o comportamento do elemento. Semelhantemente a `constraints`, as propriedades com suporte variam de acordo com o tipo de elemento. |
| visible | Não | Indica se o elemento é exibido. Se `true`, o elemento e os elementos filho aplicáveis são exibidos. O valor padrão é `true`. Use [funções lógicas](create-uidefinition-functions.md#logical-functions) para controlar o valor da propriedade dinamicamente.

## <a name="elements"></a>Elementos

A documentação para cada elemento contém exemplo de interface do usuário, esquema, comentários sobre o comportamento do elemento (normalmente sobre a validação e a personalização com suporte) e saída de exemplo.

- [Microsoft.Common.DropDown](microsoft-common-dropdown.md)
- [Microsoft.Common.FileUpload](microsoft-common-fileupload.md)
- [Microsoft.Common.InfoBox](microsoft-common-infobox.md)
- [Microsoft.Common.OptionsGroup](microsoft-common-optionsgroup.md)
- [Microsoft.Common.PasswordBox](microsoft-common-passwordbox.md)
- [Microsoft.Common.Section](microsoft-common-section.md)
- [Microsoft. Common. TagsByResource](microsoft-common-tagsbyresource.md)
- [Microsoft.Common.TextBlock](microsoft-common-textblock.md)
- [Microsoft.Common.TextBox](microsoft-common-textbox.md)
- [Microsoft.Compute.CredentialsCombo](microsoft-compute-credentialscombo.md)
- [Microsoft.Compute.SizeSelector](microsoft-compute-sizeselector.md)
- [Microsoft.Compute.UserNameTextBox](microsoft-compute-usernametextbox.md)
- [Microsoft.Network.PublicIpAddressCombo](microsoft-network-publicipaddresscombo.md)
- [Microsoft.Network.VirtualNetworkCombo](microsoft-network-virtualnetworkcombo.md)
- [Microsoft.Storage.MultiStorageAccountCombo](microsoft-storage-multistorageaccountcombo.md)
- [Microsoft.Storage.StorageAccountSelector](microsoft-storage-storageaccountselector.md)

## <a name="next-steps"></a>Próximas etapas

Para obter uma introdução à criação de definições de interface do usuário, consulte [Introdução ao CreateUiDefinition](create-uidefinition-overview.md).
