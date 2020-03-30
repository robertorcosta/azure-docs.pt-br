---
title: Módulo PowerShell para serviços do Azure Lab | Microsoft Docs
description: Este artigo fornece informações sobre um módulo PowerShell que ajuda no gerenciamento de artefatos no Azure Lab Services.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/01/2019
ms.author: spelluru
ms.openlocfilehash: cc82355ee43f9fee4f9c2e1bb1bcc0481e4dcea3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73609392"
---
# <a name="azlabservices-powershell-module-preview"></a>Módulo Az.LabServices do PowerShell (versão prévia)
O Az.LabServices é um módulo PowerShell que simplifica o gerenciamento dos serviços do Azure Lab. Ele fornece funções compostáveis para criar, consultar, atualizar e excluir contas de laboratório, laboratórios, VMs e Imagens. Para obter mais informações sobre este módulo, consulte a página inicial do [Az.LabServices no GitHub](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Modules/Library).

> [!NOTE]
> Este módulo está em **pré-visualização**. 

## <a name="example-command"></a>Comando de exemplo
Aqui está um exemplo de uso de um comando PowerShell para parar todas as VMs em execução em todos os laboratórios.

```powershell
Get-AzLabAccount | Get-AzLab | Get-AzLabVm -Status Running | Stop-AzLabVm
```

## <a name="get-started"></a>Introdução
1. Instale [o Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) se ele não existir na sua máquina. 
2. Baixe [Az.LabServices.psm1](https://github.com/Azure/azure-devtestlab/blob/master/samples/ClassroomLabs/Modules/Library/Az.LabServices.psm1) para sua máquina.
3. Importar o módulo:

    ```powershell
    Import-Module .\Az.LabServices.psm1
    ```
4. Execute o seguinte comando para listar todos os laboratórios em sua assinatura.

    ```powershell
    Get-AzLabAccount | Get-AzLab
    ```

## <a name="next-steps"></a>Próximas etapas
Consulte a página inicial do [Az.LabServices no GitHub](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Modules/Library).
