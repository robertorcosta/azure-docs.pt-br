---
title: Configure o tempo de tempo de marcha ocioso TCP do balanceador de carga no Azure
titleSuffix: Azure Load Balancer
description: Neste artigo, saiba como configurar o tempo de espera ocioso do Azure Load Balancer TCP.
services: load-balancer
documentationcenter: na
author: asudbring
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/09/2020
ms.author: allensu
ms.openlocfilehash: d0bb73b58aa23e5f7eb784772acf37b05df463ba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79456821"
---
# <a name="configure-tcp-idle-timeout-settings-for-azure-load-balancer"></a>Definir as configurações de tempo limite de ociosidade do TCP para o Azure Load Balancer

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se você optar por instalar e usar o PowerShell localmente, este artigo exigirá o módulo do Azure PowerShell versão 5.4.1 ou posterior. Execute `Get-Module -ListAvailable Az` para localizar a versão instalada. Se você precisa atualizar, consulte [Instalar o módulo do Azure PowerShell](/powershell/azure/install-Az-ps). Se você estiver executando o PowerShell localmente, também precisará executar o `Connect-AzAccount` para criar uma conexão com o Azure.

## <a name="tcp-idle-timeout"></a>Tempo de inatividade TCP
O Azure Load Balancer tem um ajuste de tempo de 4 minutos a 30 minutos. Por padrão, está definido como 4 minutos. Se um período de inatividade for maior que o valor de tempo limite, não haverá nenhuma garantia de que a sessão TCP ou HTTP seja mantida entre o cliente e o serviço de nuvem.

Quando a conexão é fechada, seu aplicativo cliente pode recebe a seguinte mensagem de erro: "A conexão subjacente foi fechada: uma conexão que deveria ser mantida ativa foi fechada pelo servidor."

Uma prática comum é usar um TCP keep alive. Essa prática mantém a conexão ativa por um período maior. Para obter mais informações, consulte estes [exemplos do .NET](https://msdn.microsoft.com/library/system.net.servicepoint.settcpkeepalive.aspx). Com o keep alive habilitado, os pacotes são enviados durante os períodos de inatividade na conexão. Pacotes keep-alive garantem que o valor do tempo de espera ocioso não seja atingido e que a conexão seja mantida por um longo período.

A configuração funciona apenas para conexões de entrada. Para evitar perder a conexão, configure o TCP keep-alive com um intervalo menor do que a configuração de tempo limite ocioso ou aumente o valor de tempo limite ocioso. Para suportar esses cenários, foi adicionado o suporte a um tempo de tempo inlocável de ociosidade.

TCP keep-alive funciona para cenários onde a duração da bateria não é uma restrição. Não é recomendado para aplicações móveis. Usar o TCP keep alive em um aplicativo móvel pode consumir a bateria do dispositivo mais rapidamente.

![Tempo limite de TCP](./media/load-balancer-tcp-idle-timeout/image1.png)

As seções a seguir descrevem como alterar as configurações de tempo de tempo parado público para recursos públicos de IP e balanceador de carga.

## <a name="configure-the-tcp-timeout-for-your-instance-level-public-ip-to-15-minutes"></a>Configure o tempo limite de TCP para o IP Público em Nível de Instância para 15 minutos

```azurepowershell-interactive
$publicIP = Get-AzPublicIpAddress -Name MyPublicIP -ResourceGroupName MyResourceGroup
$publicIP.IdleTimeoutInMinutes = "15"
Set-AzPublicIpAddress -PublicIpAddress $publicIP
```

`IdleTimeoutInMinutes` é opcional. Se não for definido, o tempo limite padrão será de quatro minutos. O intervalo de tempo limite aceitável é entre quatro e 30 minutos.

## <a name="set-the-tcp-timeout-on-a-load-balanced-rule-to-15-minutes"></a>Defina o tempo de intervalo do TCP em uma regra equilibrada de carga para 15 minutos

Para definir o tempo de espera ocioso para um balanceador de carga, o 'IdleTimeoutInMinutes' está definido na regra balanceada de carga. Por exemplo: 

```azurepowershell-interactive
$lb = Get-AzLoadBalancer -Name "MyLoadBalancer" -ResourceGroup "MyResourceGroup"
$lb | Set-AzLoadBalancerRuleConfig -Name myLBrule -IdleTimeoutInMinutes 15
```
## <a name="next-steps"></a>Próximas etapas

[Visão geral do balanceador de carga interno](load-balancer-internal-overview.md)

[Introdução à configuração de um balanceador de carga para a Internet](quickstart-create-standard-load-balancer-powershell.md)

[Configurar um modo de distribuição do balanceador de carga](load-balancer-distribution-mode.md)
