---
title: Crie um host Bastião usando o Azure CLI | Bastião Azure
description: Neste artigo, saiba como criar e excluir um host Bastion
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 03/26/2020
ms.author: cherylmc
ms.openlocfilehash: 9e216bf2bb8e2e605723256f0511b9bdb63083b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80337565"
---
# <a name="create-an-azure-bastion-host-using-azure-cli"></a>Crie um host Azure Bastion usando o Azure CLI

Este artigo mostra como criar um host Azure Bastion usando o Azure CLI. Uma vez que você disponibilize o serviço Azure Bastion em sua rede virtual, a experiência RDP/SSH perfeita está disponível para todas as VMs na mesma rede virtual. A implantação do Azure Bastion é feita por rede virtual, não por assinatura/conta ou máquina virtual.

Opcionalmente, você pode criar um host Azure Bastion usando o [portal Azure](bastion-create-host-portal.md)ou usando [o Azure PowerShell](bastion-create-host-powershell.md).

## <a name="before-you-begin"></a>Antes de começar

Verifique se você tem uma assinatura do Azure. Se ainda não tiver uma assinatura do Azure, você poderá ativar os [Benefícios do assinante do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) ou inscrever-se para obter uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial).

[!INCLUDE [cloudshell cli](../../includes/vpn-gateway-cloud-shell-cli.md)]

## <a name="create-a-bastion-host"></a><a name="createhost"></a>Crie um host bastião

Esta seção ajuda você a criar um novo recurso do Azure Bastion usando o Azure CLI.

1. Crie uma rede virtual e uma sub-rede Azure Bastion. Você deve criar a sub-rede Azure Bastion usando o valor de nome **AzureBastionSubnet**. Esse valor permite que o Azure saiba para qual sub-rede implantar os recursos do Bastion. Isto é diferente de uma sub-rede gateway. Você deve usar uma sub-rede de pelo menos 27 ou maior sub-rede (/27, /26, e assim por diante). Crie **a AzureBastionSubnet** sem quaisquer tabelas de rota ou delegações. Se você usar Grupos de Segurança de Rede na **AzureBastionSubnet,** consulte o artigo [Work with NSGs.](bastion-nsg.md)

   ```azurecli-interactive
   az network vnet create -g MyResourceGroup -n MyVnet --address-prefix 10.0.0.0/16 --subnet-name AzureBastionSubnet  --subnet-prefix 10.0.0.0/24
   ```

2. Crie um endereço IP público para o Azure Bastion. O IP público é o endereço IP público do recurso Bastion no qual o RDP/SSH será acessado (sobre a porta 443). O endereço IP público deve estar na mesma região que o recurso Bastion que você está criando.

   ```azurecli-interactive
   az network public-ip create -g MyResourceGroup -n MyIp
   ```

3. Crie um novo recurso do Azure Bastion na AzureBastionSubnet da sua rede virtual. Leva cerca de 5 minutos para o recurso Bastion criar e implantar.

   ```azurecli-interactive
   az network bastion create --name $name--public-ip-address $publicip--resource-group $RgName --vnet-name $VNetName --location $location
                           
   ```

## <a name="next-steps"></a>Próximas etapas

* Leia o [Bastion FAQ](bastion-faq.md) para obter informações adicionais.

* Para usar grupos de segurança de rede com a sub-rede Azure Bastion, consulte [Trabalhar com NSGs](bastion-nsg.md).
