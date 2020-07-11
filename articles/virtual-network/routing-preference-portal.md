---
title: Configurar a preferência de roteamento para um endereço IP público - Portal do Azure
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
ms.openlocfilehash: 7d41893d734037e466ad786641fed98c461adcfc
ms.sourcegitcommit: f7e160c820c1e2eb57dc480b2a8fd6bef7053e91
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/10/2020
ms.locfileid: "86231804"
---
# <a name="configure-routing-preference-for-a-public-ip-address-using-the-azure-portal"></a>Configurar a preferência de roteamento para um endereço IP público usando o portal do Azure

Este artigo mostra como configurar a [preferência de roteamento](https://docs.microsoft.com/azure/virtual-network/routing-preference-overview) por meio da rede de ISP (opção da**Internet** ) para um endereço IP público. Depois de criar o endereço IP público, você pode associá-lo aos seguintes recursos do Azure para os tráfegos de entrada e saída para a Internet:

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

## <a name="register-the-feature-for-your-subscription"></a>Registrar recurso para sua assinatura
O recurso Preferência de Roteamento está atualmente na versão prévia. Registre o recurso para sua assinatura usando o Azure PowerShell da seguinte maneira:
```azurepowershell
Register-AzProviderFeature -FeatureName AllowRoutingPreferenceFeature -ProviderNamespace Microsoft.Network
```

## <a name="create-a-public-ip-address-with-a-routing-preference"></a>Criar um endereço IP público com uma preferência de roteamento
1. Entre no [portal do Azure](https://preview.portal.azure.com/).
2. Selecione **Criar um recurso**. 
3. Na caixa de pesquisa, digite *Endereço IP público*.
3. Nos resultados da pesquisa, selecione **Endereço IP público**. Em seguida, na página **Endereço IP público**, selecione **Criar**.
3. Nas opções **Preferência de roteamento**, selecione **Internet**.

      ![Criar um endereço IP público](./media/routing-preference-portal/pip-new.png)

    > [!NOTE]
    > Os endereços IP públicos são criados com um endereço IPv4 ou IPv6. Contudo, a preferência de roteamento só dá suporte ao IPV4 no momento.

Você pode associar o endereço IP público criado acima a uma máquina virtual [Windows](../virtual-machines/windows/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ou [Linux](../virtual-machines/linux/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Use a seção CLI na página do tutorial: [Associe um endereço IP público a uma máquina virtual](associate-public-ip-address-vm.md#azure-cli) para associar o IP público à sua VM. Você também pode associar o endereço IP público criado acima a um [Azure Load Balancer](../load-balancer/load-balancer-overview.md), atribuindo-o à configuração **front-end** do balanceador de carga. O endereço IP público serve como um endereço IP virtual de balanceamento de carga (VIP).

## <a name="next-steps"></a>Próximas etapas
- Saiba mais sobre o [IP público com a preferência de roteamento](routing-preference-overview.md).
- [Configure a preferência de roteamento para uma VM](tutorial-routing-preference-virtual-machine-portal.md).
- [Configure a preferência de roteamento para um endereço IP público usando o PowerShell](routing-preference-powershell.md).
- Saiba mais sobre os [endereços IP públicos](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses) no Azure.
- Saiba mais sobre todas as [configurações de endereço IP público](virtual-network-public-ip-address.md#create-a-public-ip-address).
