---
title: Criar um IP público-portal do Azure
description: Saiba como criar um IP público no portal do Azure
services: virtual-network
documentationcenter: na
author: blehr
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/28/2020
ms.author: blehr
ms.openlocfilehash: add763b713b93604e089d7aec586876fecd2887c
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2020
ms.locfileid: "95895631"
---
# <a name="quickstart-create-a-public-ip-address-using-the-azure-portal"></a>Início rápido: criar um endereço IP público usando o portal do Azure

Este artigo mostra como criar um recurso de endereço IP público usando o portal do Azure. Para obter mais informações sobre a quais recursos isso pode ser associado, a diferença entre o SKU básico e Standard e outras informações relacionadas, consulte [endereços IP públicos](https://docs.microsoft.com/azure/virtual-network/public-ip-addresses).  Para este exemplo, nos concentraremos somente nos endereços IPv4; para obter mais informações sobre endereços IPv6, consulte [IPv6 para VNet do Azure](https://docs.microsoft.com/azure/virtual-network/ipv6-overview).

# <a name="standard-sku---using-zones"></a>[**SKU padrão-usando zonas**](#tab/option-create-public-ip-standard-zones)

Use as etapas a seguir para criar um endereço IP público com redundância de zona padrão chamado **myStandardZRPublicIP**.

1. Entre no [portal do Azure](https://portal.azure.com/).
2. Selecione **Criar um recurso**. 
3. Na caixa de pesquisa, digite *Endereço IP público*.
4. Nos resultados da pesquisa, selecione **Endereço IP público**. Em seguida, na página **Endereço IP público**, selecione **Criar**.
5. Na página **criar endereço IP público** , insira ou selecione as seguintes informações: 

    | Configuração                 | Valor                       |
    | ---                     | ---                         |
    | Versão IP              | Selecionar IPv4                 |    
    | SKU                     | Selecione **Standard**         |
    | Name                    | Insira *myStandardZRPublicIP*          |
    | Atribuição de endereço IP   | Observe que isso será bloqueado como "estático"                                        |
    | Tempo limite de ociosidade (minutos)  | Deixe o valor em 4        |
    | Rótulo do nome DNS          | Deixe o valor em branco    |
    | Subscription            | Selecione sua assinatura.   |
    | Resource group          | Selecione **criar novo** , insira MyResource e, em seguida, selecione **OK** |
    | Localização                | Selecione **leste dos EUA 2**      |
    | Zona de disponibilidade       | Selecione **com redundância de zona** ou selecione uma zona específica (veja a observação abaixo) |

Observe que essas são apenas seleções válidas em regiões com [zonas de disponibilidade](https://docs.microsoft.com/azure/availability-zones/az-overview?toc=/azure/virtual-network/toc.json#availability-zones).  (Você também pode selecionar uma zona específica nessas regiões, embora ela não seja resiliente a falhas zonais.)

# <a name="basic-sku"></a>[**SKU Básico**](#tab/option-create-public-ip-basic)

Use as etapas a seguir para criar um endereço IP público estático básico chamado **myBasicPublicIP**.  Os IPs públicos básicos não têm o conceito de zonas de disponibilidade.

1. Entre no [portal do Azure](https://portal.azure.com/).
2. Selecione **Criar um recurso**. 
3. Na caixa de pesquisa, digite *Endereço IP público*.
4. Nos resultados da pesquisa, selecione **Endereço IP público**. Em seguida, na página **Endereço IP público**, selecione **Criar**.
5. Na página **criar endereço IP público** , insira ou selecione as seguintes informações: 

    | Configuração                 | Valor                       |
    | ---                     | ---                         |
    | Versão IP              | Selecionar IPv4                 |    
    | SKU                     | Selecione **Standard**         |
    | Name                    | Insira *myBasicPublicIP*          |
    | Atribuição de endereço IP   | Escolha **estático** (veja a observação abaixo)                                     |
    | Tempo limite de ociosidade (minutos)  | Deixe o valor em 4        |
    | Rótulo do nome DNS          | Deixe o valor em branco    |
    | Subscription            | Selecione sua assinatura.   |
    | Resource group          | Selecione **criar novo** , insira MyResource e, em seguida, selecione **OK** |
    | Localização                | Selecione **leste dos EUA 2**      |

Se for aceitável que o endereço IP seja alterado ao longo do tempo, a atribuição de IP **dinâmico** poderá ser selecionada.

---

## <a name="additional-information"></a>Informações adicionais 

Para obter mais detalhes sobre os campos individuais listados acima, consulte [gerenciar endereços IP públicos](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address#create-a-public-ip-address).

## <a name="next-steps"></a>Próximas etapas
- Associar um [endereço IP público a uma máquina virtual](https://docs.microsoft.com/azure/virtual-network/associate-public-ip-address-vm#azure-portal)
- Saiba mais sobre os [endereços IP públicos](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses) no Azure.
- Saiba mais sobre todas as [configurações de endereço IP público](virtual-network-public-ip-address.md#create-a-public-ip-address).