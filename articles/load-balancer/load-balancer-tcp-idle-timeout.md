---
title: Configurar o tempo limite de TCP ocioso do balanceador de carga no Azure
titleSuffix: Azure Load Balancer
description: Neste artigo, saiba como configurar Azure Load Balancer tempo limite de ociosidade de TCP.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "79456821"
---
# <a name="configure-tcp-idle-timeout-settings-for-azure-load-balancer"></a>Definir as configurações de tempo limite de ociosidade do TCP para o Azure Load Balancer

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se você optar por instalar e usar o PowerShell localmente, este artigo exigirá o módulo do Azure PowerShell versão 5.4.1 ou posterior. Execute `Get-Module -ListAvailable Az` para localizar a versão instalada. Se você precisa atualizar, consulte [Instalar o módulo do Azure PowerShell](/powershell/azure/install-Az-ps). Se você estiver executando o PowerShell localmente, também precisará executar o `Connect-AzAccount` para criar uma conexão com o Azure.

## <a name="tcp-idle-timeout"></a>Tempo limite de ociosidade de TCP
Azure Load Balancer tem uma configuração de tempo limite de ociosidade de 4 minutos a 30 minutos. Por padrão, ele é definido como 4 minutos. Se um período de inatividade for maior que o valor de tempo limite, não haverá nenhuma garantia de que a sessão TCP ou HTTP seja mantida entre o cliente e o serviço de nuvem.

Quando a conexão é fechada, seu aplicativo cliente pode recebe a seguinte mensagem de erro: "A conexão subjacente foi fechada: uma conexão que deveria ser mantida ativa foi fechada pelo servidor."

Uma prática comum é usar um TCP keep alive. Essa prática mantém a conexão ativa por um período maior. Para obter mais informações, consulte estes [exemplos do .NET](https://msdn.microsoft.com/library/system.net.servicepoint.settcpkeepalive.aspx). Com o keep alive habilitado, os pacotes são enviados durante os períodos de inatividade na conexão. Os pacotes Keep-Alive garantem que o valor de tempo limite de ociosidade não seja atingido e a conexão seja mantida por um longo período.

A configuração funciona apenas para conexões de entrada. Para evitar a perda da conexão, configure o TCP Keep-Alive com um intervalo menor que a configuração de tempo limite de ociosidade ou aumente o valor de tempo limite de ociosidade. Para dar suporte a esses cenários, o suporte a um tempo limite de ociosidade configurável foi adicionado.

O TCP Keep-Alive funciona para cenários em que a vida útil da bateria não é uma restrição. Não é recomendável para aplicativos móveis. Usar o TCP keep alive em um aplicativo móvel pode consumir a bateria do dispositivo mais rapidamente.

![Tempo limite de TCP](./media/load-balancer-tcp-idle-timeout/image1.png)

As seções a seguir descrevem como alterar as configurações de tempo limite de ociosidade para os recursos de IP público e de balanceador de carga.

## <a name="configure-the-tcp-timeout-for-your-instance-level-public-ip-to-15-minutes"></a>Configure o tempo limite de TCP para o IP Público em Nível de Instância para 15 minutos

```azurepowershell-interactive
$publicIP = Get-AzPublicIpAddress -Name MyPublicIP -ResourceGroupName MyResourceGroup
$publicIP.IdleTimeoutInMinutes = "15"
Set-AzPublicIpAddress -PublicIpAddress $publicIP
```

`IdleTimeoutInMinutes` é opcional. Se não for definido, o tempo limite padrão será de quatro minutos. O intervalo de tempo limite aceitável é entre quatro e 30 minutos.

## <a name="set-the-tcp-timeout-on-a-load-balanced-rule-to-15-minutes"></a>Definir o tempo limite de TCP em uma regra de balanceamento de carga para 15 minutos

Para definir o tempo limite de ociosidade para um balanceador de carga, o ' IdleTimeoutInMinutes ' é definido na regra de balanceamento de carga. Por exemplo:

```azurepowershell-interactive
$lb = Get-AzLoadBalancer -Name "MyLoadBalancer" -ResourceGroup "MyResourceGroup"
$lb | Set-AzLoadBalancerRuleConfig -Name myLBrule -IdleTimeoutInMinutes 15
```
## <a name="next-steps"></a>Próximas etapas

[Visão geral do balanceador de carga interno](load-balancer-internal-overview.md)

[Introdução à configuração de um balanceador de carga para a Internet](quickstart-create-standard-load-balancer-powershell.md)

[Configurar um modo de distribuição do balanceador de carga](load-balancer-distribution-mode.md)
