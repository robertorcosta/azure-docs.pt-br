---
title: Configurar a preferência de roteamento para um endereço IP público usando a CLI do Azure
titlesuffix: Azure Virtual Network
description: Saiba como criar um IP público com uma preferência de roteamento de tráfego da Internet
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/18/2020
ms.author: mnayak
ms.custom: devx-track-azurecli
ms.openlocfilehash: 64284b198fc76c219ffe0dfbc57461b587b23130
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/31/2020
ms.locfileid: "87504595"
---
# <a name="configure-routing-preference-for-a-public-ip-address-using-azure-cli"></a>Configurar a preferência de roteamento para um endereço IP público usando a CLI do Azure

Este artigo mostra como configurar a preferência de roteamento via rede ISP (opção **Internet**) para um endereço IP público usando a CLI do Azure. Depois de criar o endereço IP público, você pode associá-lo aos seguintes recursos do Azure para os tráfegos de entrada e saída para a Internet:

* Máquina virtual
* Conjunto de escala de máquina virtual
* AKS (Serviço de Kubernetes do Azure)
* Balanceador de carga voltado para a Internet
* Gateway de Aplicativo
* Firewall do Azure

Por padrão, a preferência de roteamento para o endereço IP público é definida para a rede global da Microsoft para todos os serviços do Azure e pode ser associada a qualquer serviço do Azure.

> [!IMPORTANT]
> A preferência de roteamento está atualmente em versão prévia pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Caso não tenha uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) agora.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]
Se você optar por instalar e usar a CLI do Azure localmente, este início rápido exigirá a versão 2.0.49 ou posterior da CLI do Azure. Execute `az --version` para localizar a versão instalada. Para informações sobre como instalar ou atualizar, confira [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

## <a name="register-the-feature-for-your-subscription"></a>Registrar recurso para sua assinatura
O recurso Preferência de Roteamento está atualmente na versão prévia. Registrar recurso para sua assinatura como a seguir:
```azurecli
az feature register --namespace Microsoft.Network --name AllowRoutingPreferenceFeature
```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos
Crie um grupo de recursos com o comando [az group create](/cli/azure/group#az-group-create). O exemplo a seguir cria um grupo de recursos na região **Leste dos EUA** do Azure:

```azurecli
  az group create --name myResourceGroup --location eastus
```
## <a name="create-a-public-ip-address"></a>Criar um endereço IP público

Crie um Endereço IP Público com preferência de roteamento do tipo "Internet" usando o comando [az network public-ip create](/cli/azure/network/public-ip?view=azure-cli-latest#az-network-public-ip-create), com o formato mostrado abaixo.

O comando a seguir cria um novo IP público com a preferência de roteamento **Internet** na região **Leste dos EUA** do Azure.

```azurecli
az network public-ip create \
--name MyRoutingPrefIP \
--resource-group MyResourceGroup \
--location eastus \
--ip-tags 'RoutingPreference=Internet' \
--sku STANDARD \
--allocation-method static \
--version IPv4
```

> [!NOTE]
>  Atualmente, a preferência de roteamento dá suporte apenas a endereços IP públicos IPV4.

Você pode associar o endereço IP público criado acima a uma máquina virtual [Windows](../virtual-machines/windows/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ou [Linux](../virtual-machines/linux/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Use a seção CLI na página do tutorial: [Associe um endereço IP público a uma máquina virtual](associate-public-ip-address-vm.md#azure-cli) para associar o IP Público à sua VM. Você também pode associar o endereço IP público criado acima a um [Azure Load Balancer](../load-balancer/load-balancer-overview.md), atribuindo-o à configuração **front-end** do balanceador de carga. O endereço IP público serve como um endereço IP virtual de balanceamento de carga (VIP).

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre a [preferência de roteamento em endereços IP públicos](routing-preference-overview.md). 
- [Configure a preferência de roteamento para uma VM usando a CLI do Azure](configure-routing-preference-virtual-machine-cli.md).

