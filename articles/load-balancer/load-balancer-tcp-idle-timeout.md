---
title: Configurar a redefinição de TCP do balanceador de carga e o tempo limite de ociosidade no Azure
titleSuffix: Azure Load Balancer
description: Neste artigo, saiba como configurar o tempo limite de ociosidade do TCP do Azure Load Balancer.
services: load-balancer
documentationcenter: na
author: asudbring
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/09/2020
ms.author: allensu
ms.openlocfilehash: 26c4c01aaf6abe6b9c9ac6daf6836d7b660ba21e
ms.sourcegitcommit: b4f303f59bb04e3bae0739761a0eb7e974745bb7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/02/2020
ms.locfileid: "91649807"
---
# <a name="configure-tcp-idle-timeout-for-azure-load-balancer"></a>Configurar o tempo limite de ociosidade de TCP para Azure Load Balancer

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se você optar por instalar e usar o PowerShell localmente, este artigo exigirá o módulo do Azure PowerShell versão 5.4.1 ou posterior. Execute `Get-Module -ListAvailable Az` para localizar a versão instalada. Se você precisa atualizar, consulte [Instalar o módulo do Azure PowerShell](/powershell/azure/install-Az-ps). Se você estiver executando o PowerShell localmente, também precisará executar o `Connect-AzAccount` para criar uma conexão com o Azure.

Azure Load Balancer tem uma configuração de tempo limite de ociosidade de 4 minutos a 120 minutos. Por padrão, é definido como 4 minutos. Se um período de inatividade for maior que o valor de tempo limite, não haverá nenhuma garantia de que a sessão TCP ou HTTP seja mantida entre o cliente e o serviço de nuvem. Saiba mais sobre o [tempo limite de ociosidade de TCP](load-balancer-tcp-reset.md).

As seções a seguir descrevem como mudar as configurações de tempo limite de ociosidade para os recursos de IP público e de balanceador de carga.


## <a name="configure-the-tcp-idle-timeout-for-your-public-ip"></a>Configurar o tempo limite de ociosidade de TCP para seu IP público

```azurepowershell-interactive
$publicIP = Get-AzPublicIpAddress -Name MyPublicIP -ResourceGroupName MyResourceGroup
$publicIP.IdleTimeoutInMinutes = "15"
Set-AzPublicIpAddress -PublicIpAddress $publicIP
```

`IdleTimeoutInMinutes` é opcional. Se não for definido, o tempo limite padrão será de quatro minutos. O intervalo de tempo limite aceitável é de 4 a 120 minutos.

## <a name="set-the-tcp-idle-timeout-on-rules"></a>Definir o tempo limite de ociosidade de TCP nas regras

Para definir o tempo limite de ociosidade para um balanceador de carga, 'IdleTimeoutInMinutes' é definido na regra de balanceamento de carga. Por exemplo:

```azurepowershell-interactive
$lb = Get-AzLoadBalancer -Name "MyLoadBalancer" -ResourceGroup "MyResourceGroup"
$lb | Set-AzLoadBalancerRuleConfig -Name myLBrule -IdleTimeoutInMinutes 15
```

## <a name="next-steps"></a>Próximas etapas

[Visão geral do balanceador de carga interno](load-balancer-internal-overview.md)

[Introdução à configuração de um balanceador de carga para a Internet](quickstart-load-balancer-standard-public-powershell.md)

[Configurar um modo de distribuição do balanceador de carga](load-balancer-distribution-mode.md)
