---
title: Elemento de interface do usuário SizeSelector do Azure | Microsoft Docs
description: Descreve o elemento de interface do usuário Microsoft.Compute.SizeSelector para o Portal do Azure. Use para selecionar o tamanho de uma máquina virtual.
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
ms.date: 06/27/2018
ms.author: tomfitz
ms.openlocfilehash: 288ea7e887a170c8560b0126fa53c9132da35db6
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72332662"
---
# <a name="microsoftcomputesizeselector-ui-element"></a>Elemento de interface do usuário Microsoft.Compute.SizeSelector
Um controle para selecionar um tamanho de uma ou mais instâncias de máquina virtual.

## <a name="ui-sample"></a>Exemplo de interface do usuário

O usuário vê um seletor com valores padrão da definição de elemento.

![Microsoft.Compute.SizeSelector](./media/managed-application-elements/microsoft.compute.sizeselector.png)

Depois de selecionar o controle, o usuário vê uma exibição expandida dos tamanhos disponíveis.

![Microsoft.Compute.SizeSelector expandido](./media/managed-application-elements/microsoft.compute.sizeselector-expanded.png)

## <a name="schema"></a>Esquema
```json
{
  "name": "element1",
  "type": "Microsoft.Compute.SizeSelector",
  "label": "Size",
  "toolTip": "",
  "recommendedSizes": [
    "Standard_D1",
    "Standard_D2",
    "Standard_D3"
  ],
  "constraints": {
    "allowedSizes": [],
    "excludedSizes": [],
    "numAvailabilityZonesRequired": 3,
    "zone": "3"
  },
  "options": {
    "hideDiskTypeFilter": false
  },
  "osPlatform": "Windows",
  "imageReference": {
    "publisher": "MicrosoftWindowsServer",
    "offer": "WindowsServer",
    "sku": "2012-R2-Datacenter"
  },
  "count": 2,
  "visible": true
}
```

## <a name="remarks"></a>Comentários
- `recommendedSizes` deve ter pelo menos um tamanho. O primeiro tamanho recomendado é usado como padrão. A lista de tamanhos disponíveis não é classificada pelo estado recomendado. O usuário pode selecionar uma coluna para classificar por estado recomendado.
- Se um tamanho recomendado não estiver disponível no local selecionado, o tamanho será ignorado automaticamente. O próximo tamanho recomendado será usado.
- `constraints.allowedSizes` e `constraints.excludedSizes` são opcionais, mas não podem ser usados simultaneamente. A lista de tamanhos disponíveis pode ser determinada chamando [Listar tamanhos de máquinas virtuais disponíveis para uma assinatura](/rest/api/compute/virtualmachines/virtualmachines-list-sizes-region). Os tamanhos não especificados em `constraints.allowedSizes` ficam ocultos e os tamanhos não especificados em `constraints.excludedSizes` são mostrados.
- `osPlatform` deve ser especificada e pode ser **Windows** ou **Linux**. Ela é usada para determinar os custos de hardware das máquinas virtuais.
- `imageReference` é omitida para imagens próprias, mas fornecida para imagens de terceiros. Ela é usada para determinar os custos de software das máquinas virtuais.
- `count` é usado para definir o multiplicador apropriado para o elemento. Ele dá suporte a um valor estático, como **2**, ou a um valor dinâmico de outro elemento, como `[steps('step1').vmCount]`. O valor padrão é **1**.
- O `numAvailabilityZonesRequired` pode ser 1, 2 ou 3.
- Por padrão, `hideDiskTypeFilter` é **falso**. O filtro de tipo de disco permite que o usuário veja todos os tipos de disco ou somente o SSD.

## <a name="sample-output"></a>Saída de exemplo
```json
"Standard_D1"
```

## <a name="next-steps"></a>Próximos passos
* Para obter uma introdução à criação de definições de interface do usuário, consulte [Introdução ao CreateUiDefinition](create-uidefinition-overview.md).
* Para obter uma descrição das propriedades comuns em elementos de interface do usuário, consulte [Elementos de CreateUiDefinition](create-uidefinition-elements.md).
