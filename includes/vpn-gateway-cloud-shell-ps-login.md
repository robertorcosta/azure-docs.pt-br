---
title: arquivo de inclusão
description: arquivo de inclusão
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/10/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 50ce8530aca40eed07741f35be1a57bbd7cc1868
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "77133587"
---
Abra o console do PowerShell com privilégios elevados.

Se você estiver executando o Microsoft Azure PowerShell localmente, conecte-se à sua conta do Azure. O cmdlet *Connect-AzAccount* solicita as credenciais. Depois de entrar, ele baixa as configurações da conta para que estejam disponíveis para o Microsoft Azure PowerShell. Se você estiver usando Azure Cloud Shell em vez disso, não será necessário executar *Connect-AzAccount*. Azure Cloud Shell se conecta automaticamente à sua conta do Azure.

```azurepowershell
Connect-AzAccount
```

Se você tiver mais de uma assinatura, obtenha uma lista das assinaturas do Azure.

```azurepowershell-interactive
Get-AzSubscription
```

Especifique a assinatura que você deseja usar.

```azurepowershell-interactive
Select-AzSubscription -SubscriptionName "Name of subscription"
```