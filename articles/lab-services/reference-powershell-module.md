---
title: Módulo do PowerShell para Azure Lab Services | Microsoft Docs
description: Este artigo fornece informações sobre um módulo do PowerShell que ajuda no gerenciamento de artefatos no Azure Lab Services.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: d9c184f3917be378eb77d1bf4096bfebb5ee1884
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85445586"
---
# <a name="azlabservices-powershell-module-preview"></a>Módulo Az.LabServices do PowerShell (versão prévia)
AZ. LabServices é um módulo do PowerShell que simplifica o gerenciamento dos serviços de laboratório do Azure. Ele fornece funções combináveis para criar, consultar, atualizar e excluir contas de laboratório, laboratórios, VMs e imagens. Para obter mais informações sobre esse módulo, consulte [AZ. LabServices Home Page no GitHub](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Modules/Library).

> [!NOTE]
> Este módulo está em versão **prévia**. 

## <a name="example-command"></a>Comando de exemplo
Aqui está um exemplo de como usar um comando do PowerShell para interromper todas as VMs em execução em todos os laboratórios.

```powershell
Get-AzLabAccount | Get-AzLab | Get-AzLabVm -Status Running | Stop-AzLabVm
```

## <a name="get-started"></a>Introdução
1. Instale [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) se ele não existir em seu computador. 
2. Baixe [AZ. LabServices. psm1](https://github.com/Azure/azure-devtestlab/blob/master/samples/ClassroomLabs/Modules/Library/Az.LabServices.psm1) em seu computador.
3. Importe o módulo:

    ```powershell
    Import-Module .\Az.LabServices.psm1
    ```
4. Execute o comando a seguir para listar todos os laboratórios em sua assinatura.

    ```powershell
    Get-AzLabAccount | Get-AzLab
    ```

## <a name="next-steps"></a>Próximas etapas
Consulte o [Home Page AZ. LabServices no GitHub](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Modules/Library).
