---
title: Extensão de sub-rede no Azure | Microsoft Docs
description: Saiba mais sobre a extensão da sub-rede no Azure.
services: virtual-network
documentationcenter: na
author: anupam-p
manager: narayan
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/31/2019
ms.author: anupand
ms.openlocfilehash: f718471c3f79e9a33b0e03b088f8c8d2ae0231d3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73587505"
---
# <a name="subnet-extension"></a>Extensão de sub-rede
A migração da carga de trabalho para a nuvem pública requer um planejamento e coordenação cuidadosos. Uma das principais considerações pode ser a capacidade de reter seus endereços IP. O que pode ser importante especialmente se seus aplicativos tiverem dependência de endereço IP ou você tiver requisitos de conformidade para usar endereços IP específicos. A Rede Virtual do Azure resolve esse problema para você, permitindo que você crie VNet e Subnets usando um intervalo de endereçoIP de sua escolha.

As migrações podem ficar um pouco desafiadoras quando o requisito acima é associado a um requisito adicional para manter alguns aplicativos no local. Em tal situação, você terá que dividir os aplicativos entre o Azure e os locais, sem renumerar os endereços IP em ambos os lados. Além disso, você terá que permitir que os aplicativos se comuniquem como se estivessem na mesma rede.

Uma solução para o problema acima é a extensão da sub-rede. A extensão de uma rede permite que os aplicativos conversem sobre o mesmo domínio de transmissão quando eles existem em diferentes locais físicos, removendo a necessidade de reprojetar sua topologia de rede. 

Embora estender sua rede não seja uma boa prática em geral, casos de uso abaixo podem torná-lo necessário.

- **Migração em fases**: O cenário mais comum é que você deseja fazer a fase de sua migração. Você deseja trazer alguns aplicativos primeiro e ao longo do tempo migrar o resto dos aplicativos para o Azure.
- **Latência**: Os requisitos de baixa latência podem ser outra razão para você manter alguns aplicativos no local para garantir que eles estejam o mais próximos possível do seu data center.
- **Conformidade**: Outro caso de uso é que você pode ter requisitos de conformidade para manter alguns de seus aplicativos no local.
 
> [!NOTE] 
> Você não deve estender suas sub-redes a menos que seja necessário. Nos casos em que você estende suas sub-redes, você deve tentar torná-lo um passo intermediário. Com o tempo, você deve tentar renumerar aplicativos em sua rede local e migrar para o Azure.

Na próxima seção, discutiremos como você pode estender suas sub-redes para o Azure.


## <a name="extend-your-subnet-to-azure"></a>Estenda sua sub-rede para o Azure
 Você pode estender suas sub-redes no local para o Azure usando uma solução baseada em rede de sobreposição de camada 3. A maioria das soluções usa uma tecnologia de sobreposição, como a VXLAN, para estender a rede de camada 2 usando uma rede de sobreposição de camada 3. O diagrama abaixo mostra uma solução generalizada. Nesta solução, a mesma sub-rede existe em ambos os lados, ou seja, Azure e on-premises. 

![Exemplo de extensão de sub-rede](./media/subnet-extension/subnet-extension.png)

Os endereços IP da sub-rede são atribuídos às VMs no Azure e no local. Tanto o Azure quanto o local têm um NVA inserido em suas redes. Quando uma VM no Azure tenta falar com uma VM em rede local, a NVA do Azure captura o pacote, encapsula-o e envia-o através da VPN/Express Route para a rede local. A NVA no local recebe o pacote, descapitula-o e encaminha-o para o destinatário pretendido em sua rede. O tráfego de retorno usa um caminho e lógica semelhantes.

No exemplo acima, o Azure NVA e o NVA no local se comunicam e aprendem sobre endereços IP atrás um do outro. Redes mais complexas também podem ter um serviço de mapeamento, que mantém o mapeamento entre os NVAs e os endereços IP por trás deles. Quando um NVA recebe um pacote, ele consulta o serviço de mapeamento para descobrir o endereço do NVA que tem o endereço IP de destino por trás dele.

Na próxima seção, você encontrará detalhes sobre as soluções de extensão de sub-rede que testamos no Azure.

## <a name="next-steps"></a>Próximas etapas 
[Amplie sua sub-rede para o Azure usando soluções de fornecedores.](https://github.com/microsoft/Azure-LISP)