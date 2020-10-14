---
title: Criar um host bastião usando Azure PowerShell | Microsoft Docs
description: Neste artigo, saiba como criar um host de bastiões do Azure
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: how-to
ms.date: 10/13/2020
ms.author: cherylmc
ms.openlocfilehash: f0e7a66ef7d6947306f1b2ffec54b8e9d12737d7
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/14/2020
ms.locfileid: "92018586"
---
# <a name="create-an-azure-bastion-host-using-azure-powershell"></a>Criar um host de bastiões do Azure usando Azure PowerShell

Este artigo mostra como criar um host de bastiões do Azure usando o PowerShell. Depois de implantar a bastiões, você pode se conectar à VM por meio de seu endereço IP privado por meio do navegador usando o portal do Azure. Sua VM não precisa de um endereço IP público, um cliente adicional ou um software especial. A implantação do Azure Bastion é feita por rede virtual, não por assinatura/conta ou máquina virtual. A experiência ininterrupta de RDP/SSH está disponível para todas as VMs na mesma rede virtual.

Estas instruções são para a implantação do PowerShell. Você também pode criar um host de bastiões do Azure usando o [portal do Azure](tutorial-create-host-portal.md) ou [CLI do Azure](create-host-cli.md).

## <a name="before-you-begin"></a>Antes de começar

Verifique se você tem uma assinatura do Azure. Se ainda não tiver uma assinatura do Azure, você poderá ativar os [Benefícios do assinante do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) ou inscrever-se para obter uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial).

[!INCLUDE [PowerShell](../../includes/vpn-gateway-cloud-shell-powershell-about.md)]

## <a name="create-a-bastion-host"></a><a name="createhost"></a>Criar um bastion host

Esta seção ajuda você a criar um novo recurso de bastiões do Azure para sua rede virtual usando Azure PowerShell.

1. Crie uma rede virtual e uma sub-rede de bastiões do Azure. Você deve criar a sub-rede de bastiões do Azure usando o valor de nome **AzureBastionSubnet**. Esse valor permite que o Azure saiba em qual sub-rede os recursos do Bastion serão implantados. Isso é diferente de uma sub-rede de gateway. Você deve usar uma sub-rede de pelo menos/27 ou uma sub-rede maior (/27,/26 e assim por diante). Crie o **AzureBastionSubnet** sem nenhuma tabela ou delegação de rota. Se você usar grupos de segurança de rede no **AzureBastionSubnet**, consulte o artigo [trabalhar com NSGs](bastion-nsg.md) .

   ```azurepowershell-interactive
   $subnetName = "AzureBastionSubnet"
   $subnet = New-AzVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24
   $vnet = New-AzVirtualNetwork -Name "myVnet" -ResourceGroupName "myBastionRG" -Location "westeurope" -AddressPrefix 10.0.0.0/16 -Subnet $subnet
   ```

2. Crie um endereço IP público para a bastiões do Azure. O IP público é o endereço IP público no qual o recurso de bastiões em que o RDP/SSH será acessado (pela porta 443). O endereço IP público precisa estar na mesma região que o recurso do Bastion que você está criando.

   ```azurepowershell-interactive
   $publicip = New-AzPublicIpAddress -ResourceGroupName "myBastionRG" -name "myPublicIP" -location "westeurope" -AllocationMethod Static -Sku Standard
   ```

3. Crie um novo recurso de bastiões do Azure no AzureBastionSubnet de sua rede virtual. Leva cerca de 5 minutos para que o recurso de bastiões seja criado e implantado.

   ```azurepowershell-interactive
   $bastion = New-AzBastion -ResourceGroupName "myBastionRG" -Name "myBastion" -PublicIpAddress $publicip -VirtualNetwork $vnet
   ```

## <a name="next-steps"></a>Próximas etapas

* Conecte-se a uma máquina virtual.
   * [VM Linux](bastion-connect-vm-ssh.md)
   * [VM Windows](bastion-connect-vm-rdp.md)
