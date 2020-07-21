---
title: Baixar o modelo para uma VM do Azure
description: Baixe o modelo para uma VM usando o portal ou o PowerShell.
author: cynthn
manager: gwallace
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 11/17/2017
ms.author: cynthn
ms.openlocfilehash: 6a648fd7f2835ca23285b69d5fed7156507b2ace
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/20/2020
ms.locfileid: "86500558"
---
# <a name="download-the-template-for-a-vm"></a>Baixar o modelo para uma VM
Quando você cria uma VM no Azure usando o portal ou o PowerShell, um modelo do Resource Manager é criado automaticamente para você. Você pode usar este modelo para duplicar rapidamente uma implantação. O modelo contém informações sobre todos os recursos em um grupo de recursos. Para uma máquina virtual, isso significa que o modelo contém tudo o que é criado para dar suporte à VM desse grupo de recursos, incluindo os recursos de rede.

## <a name="download-the-template-using-the-portal"></a>Baixar o modelo usando o portal
1. Faça logon no [Portal do Azure](https://portal.azure.com/).
2. No menu à esquerda, selecione **Máquinas Virtuais**.
3. Selecione a máquina virtual na lista.
4. Selecione **Exportar modelo**.
5. Selecione **Baixar** no menu na parte superior e salve o arquivo .zip em seu computador local.
6. Abra o arquivo zip e extraia os arquivos para uma pasta. O arquivo .zip contém:
   
   * parameters.json
   * template.json

O arquivo template.json é o modelo.

## <a name="download-the-template-using-powershell"></a>Baixar o modelo usando o PowerShell
Você também pode baixar o arquivo de modelo .json usando o cmdlet [Export-AzResourceGroup](/powershell/module/az.resources/export-azresourcegroup). Você pode usar o parâmetro `-path` para fornecer o nome de arquivo e o caminho para o arquivo .json. Este exemplo mostra como baixar o modelo para o grupo de recursos denominado **myResourceGroup** para a pasta **C:\users\public\downloads** no computador local.

```powershell
    Export-AzResourceGroup -ResourceGroupName "myResourceGroup" -Path "C:\users\public\downloads"
```

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre como implantar recursos usando modelos, veja o [passo a passo do modelo do Resource Manager](../../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md).
