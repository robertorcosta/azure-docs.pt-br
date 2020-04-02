---
title: Criar um host Bastião usando o Azure Powershell | Microsoft Docs
description: Neste artigo, saiba como criar um host Azure Bastion
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: cherylmc
ms.openlocfilehash: 43d834f0c834696cd4a836466c9663fe7c31a392
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/01/2020
ms.locfileid: "80520509"
---
# <a name="create-an-azure-bastion-host-using-azure-powershell"></a>Crie um host Azure Bastion usando o Azure PowerShell

Este artigo mostra como criar um host Azure Bastion usando o PowerShell. Uma vez que você disponibilize o serviço Azure Bastion em sua rede virtual, a experiência RDP/SSH perfeita está disponível para todas as VMs na mesma rede virtual. A implantação do Azure Bastion é feita por rede virtual, não por assinatura/conta ou máquina virtual.

Opcionalmente, você pode criar um host Azure Bastion usando o [portal Azure](bastion-create-host-portal.md).

## <a name="before-you-begin"></a>Antes de começar

Verifique se você tem uma assinatura do Azure. Se ainda não tiver uma assinatura do Azure, você poderá ativar os [Benefícios do assinante do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) ou inscrever-se para obter uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial).

[!INCLUDE [powershell](../../includes/vpn-gateway-cloud-shell-powershell-about.md)]

## <a name="create-a-bastion-host"></a><a name="createhost"></a>Crie um host bastião

Esta seção ajuda você a criar um novo recurso do Azure Bastion usando o Azure PowerShell.

1. Crie uma rede virtual e uma sub-rede Azure Bastion. Você deve criar a sub-rede Azure Bastion usando o valor de nome **AzureBastionSubnet**. Esse valor permite que o Azure saiba para qual sub-rede implantar os recursos do Bastion. Isto é diferente de uma sub-rede gateway. Você deve usar uma sub-rede de pelo menos 27 ou maior sub-rede (/27, /26, e assim por diante). Crie **a AzureBastionSubnet** sem quaisquer tabelas de rota ou delegações. Se você usar Grupos de Segurança de Rede na **AzureBastionSubnet,** consulte o artigo [Work with NSGs.](bastion-nsg.md)

   ```azurepowershell-interactive
   $subnetName = "AzureBastionSubnet"
   $subnet = New-AzVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24
   $vnet = New-AzVirtualNetwork -Name "myVnet" -ResourceGroupName "myBastionRG" -Location "westeurope" -AddressPrefix 10.0.0.0/16 -Subnet $subnet
   ```

2. Crie um endereço IP público para o Azure Bastion. O IP público é o endereço IP público do recurso Bastion no qual o RDP/SSH será acessado (sobre a porta 443). O endereço IP público deve estar na mesma região que o recurso Bastion que você está criando.

   ```azurepowershell-interactive
   $publicip = New-AzPublicIpAddress -ResourceGroupName "myBastionRG" -name "myPublicIP" -location "westeurope" -AllocationMethod Static -Sku Standard
   ```

3. Crie um novo recurso do Azure Bastion na AzureBastionSubnet da sua rede virtual. Leva cerca de 5 minutos para o recurso Bastion criar e implantar.

   ```azurepowershell-interactive
   $bastion = New-AzBastion -ResourceGroupName "myBastionRG" -Name "myBastion" -PublicIpAddress $publicip -VirtualNetwork $vnet
   ```

## <a name="next-steps"></a>Próximas etapas

* Leia o [Bastion FAQ](bastion-faq.md) para obter informações adicionais.

* Para usar grupos de segurança de rede com a sub-rede Azure Bastion, consulte [Trabalhar com NSGs](bastion-nsg.md).
