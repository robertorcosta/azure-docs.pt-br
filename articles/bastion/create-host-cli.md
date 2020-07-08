---
title: Criar um host bastião usando CLI do Azure | Bastiões do Azure
description: Neste artigo, saiba como criar e excluir um host de bastiões
services: bastion
author: mialdrid
ms.service: bastion
ms.topic: how-to
ms.date: 03/26/2020
ms.author: mialdrid
ms.openlocfilehash: e7f80bb7f9be2e01aa24090d7305b1a5d882da04
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85255505"
---
# <a name="create-an-azure-bastion-host-using-azure-cli"></a>Criar um host de bastiões do Azure usando CLI do Azure

Este artigo mostra como criar um host de bastiões do Azure usando CLI do Azure. Depois de provisionar o serviço de bastiões do Azure em sua rede virtual, a experiência ininterrupta de RDP/SSH estará disponível para todas as VMs na mesma rede virtual. A implantação do Azure Bastion é feita por rede virtual, não por assinatura/conta ou máquina virtual.

Opcionalmente, você pode criar um host de bastiões do Azure usando o [portal do Azure](bastion-create-host-portal.md)ou usando [Azure PowerShell](bastion-create-host-powershell.md).

## <a name="before-you-begin"></a>Antes de começar

Verifique se você tem uma assinatura do Azure. Se ainda não tiver uma assinatura do Azure, você poderá ativar os [Benefícios do assinante do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) ou inscrever-se para obter uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial).

[!INCLUDE [cloudshell cli](../../includes/vpn-gateway-cloud-shell-cli.md)]

## <a name="create-a-bastion-host"></a><a name="createhost"></a>Criar um bastion host

Esta seção ajuda você a criar um novo recurso de bastiões do Azure usando o CLI do Azure.

1. Crie uma rede virtual e uma sub-rede de bastiões do Azure. Você deve criar a sub-rede de bastiões do Azure usando o valor de nome **AzureBastionSubnet**. Esse valor permite que o Azure saiba em qual sub-rede os recursos do Bastion serão implantados. Isso é diferente de uma sub-rede de gateway. Você deve usar uma sub-rede de pelo menos/27 ou uma sub-rede maior (/27,/26 e assim por diante). Crie o **AzureBastionSubnet** sem nenhuma tabela ou delegação de rota. Se você usar grupos de segurança de rede no **AzureBastionSubnet**, consulte o artigo [trabalhar com NSGs](bastion-nsg.md) .

   ```azurecli-interactive
   az network vnet create -g MyResourceGroup -n MyVnet --address-prefix 10.0.0.0/16 --subnet-name AzureBastionSubnet  --subnet-prefix 10.0.0.0/24
   ```

2. Crie um endereço IP público para a bastiões do Azure. O IP público é o endereço IP público no qual o recurso de bastiões em que o RDP/SSH será acessado (pela porta 443). O endereço IP público precisa estar na mesma região que o recurso do Bastion que você está criando.

   ```azurecli-interactive
   az network public-ip create -g MyResourceGroup -n MyIp --sku Standard
   ```

3. Crie um novo recurso de bastiões do Azure no AzureBastionSubnet de sua rede virtual. Leva cerca de 5 minutos para que o recurso de bastiões seja criado e implantado.

   ```azurecli-interactive
   az network bastion create --name $name--public-ip-address $publicip--resource-group $RgName --vnet-name $VNetName --location $location
                           
   ```

## <a name="next-steps"></a>Próximas etapas

* Leia as [perguntas frequentes sobre bastiões](bastion-faq.md) para obter informações adicionais.

* Para usar grupos de segurança de rede com a sub-rede do Azure Bastion, confira [Trabalhar com NSGs](bastion-nsg.md).
