---
title: incluir arquivo
description: incluir arquivo
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/01/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 1aca39a7ff162aa3c42fdb3ca5999c71091ec02e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "67171655"
---
 Se estiver usando o Azure Cloud Shell, você entrará em sua conta do Azure automaticamente depois de clicar em ' experimentar '. Para entrar localmente, abra o console do PowerShell com privilégios elevados e execute o cmdlet para se conectar.

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