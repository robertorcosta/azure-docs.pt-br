---
author: cherylmc
ms.date: 12/06/2019
ms.service: expressroute
ms.topic: include
ms.author: cherylmc
ms.openlocfilehash: f04861d55c9cea3c79f4983f7be2e1f5a3c6d864
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "74926238"
---
Instale as versões mais recentes dos módulos PowerShell do SM (Gerenciamento de Serviços) do Azure e do módulo ExpressRoute. Você não pode usar o ambiente Azure CloudShell para executar módulos SM.

1. Use as instruções no artigo [do módulo Instalar o módulo de gerenciamento de serviços](/powershell/azure/servicemanagement/install-azure-ps) para instalar o Módulo de Gerenciamento de Serviços do Azure. Se você tiver o módulo Az ou RM já instalado, certifique-se de usar '-AllowClobber'.
2. Importe os módulos instalados. Ao usar o exemplo a seguir, ajuste o caminho para refletir a localização e a versão dos módulos PowerShell instalados.

   ```powershell
   Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.3.0\Azure.psd1'
   Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.3.0\ExpressRoute\ExpressRoute.psd1'
   ```
3. Para entrar na sua conta do Azure, abra seu console PowerShell com direitos elevados e conecte-se à sua conta. Use o seguinte exemplo para ajudá-lo a se conectar usando o módulo gerenciamento de serviços:

   ```powershell
   Add-AzureAccount
   ```