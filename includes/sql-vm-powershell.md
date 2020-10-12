---
author: MikeRayMSFT
ms.service: virtual-machines-sql
ms.topic: include
ms.date: 11/25/2018
ms.author: mikeray
ms.openlocfilehash: c6666f4417cde9e0f77cc965ded1d6bdb5dced34
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "67171880"
---
## <a name="start-your-powershell-session"></a>Iniciar sua sessão do PowerShell
 

Execute o cmdlet [**Connect-Az Account**](https://docs.microsoft.com/powershell/module/Az.Accounts/Connect-AzAccount) e você verá uma tela de entrada para inserir suas credenciais. Use as mesmas credenciais que você usa para entrar no Portal do Azure.

```powershell
Connect-AzAccount
```

Se você tiver várias assinaturas, use o cmdlet [**Set-AzContext**](https://docs.microsoft.com/powershell/module/az.accounts/set-azcontext) para selecionar qual assinatura sua sessão do PowerShell deve usar. Para ver qual assinatura a sessão atual do PowerShell está usando, execute [**Get-AzContext**](https://docs.microsoft.com/powershell/module/az.accounts/get-azcontext). Para ver todas as suas assinaturas, execute [**Get-AzSubscription**](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription).

```powershell
Set-AzContext -SubscriptionId '4cac86b0-1e56-bbbb-aaaa-000000000000'
```

