---
title: O que é a NAT de Rede Virtual do Azure?
description: Visão geral de recursos, da arquitetura e da implementação da NAT de Rede Virtual. Saiba como funciona a NAT de Rede Virtual e como usar recursos de gateway da NAT na nuvem.
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
ms.service: virtual-network
Customer intent: As an IT administrator, I want to learn more about Virtual Network NAT, its NAT gateway resources, and what I can use them for.
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/18/2020
ms.author: allensu
ms.openlocfilehash: 98d77f43c990dd00dd5e5d616b2fdee44fb8a2f6
ms.sourcegitcommit: dfa543fad47cb2df5a574931ba57d40d6a47daef
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/18/2020
ms.locfileid: "77431410"
---
# <a name="what-is-virtual-network-nat-public-preview"></a>O que é NAT de Rede Virtual (versão prévia pública)?

A NAT de Rede Virtual (conversão de endereços de rede) simplifica a conectividade de Internet somente de saída para redes virtuais. Quando configurado em uma sub-rede, toda a conectividade de saída usa seus endereços IP públicos estáticos especificados.  A conectividade de saída é possível sem endereços IP públicos ou de balanceador de carga conectados diretamente a máquinas virtuais. A NAT é totalmente gerenciada e altamente resiliente.

<!-- 
<img src="./media/nat-overview/flow-map.svg" width="270" align="center">
![Virtual Network NAT](./media/nat-overview/flow-map.svg)
-->

<p align="center">
  <img src="./media/nat-overview/flow-map.svg" width="256" title="NAT de Rede Virtual">
</p>



*Figura: NAT de Rede Virtual*


>[!NOTE] 
>A NAT de Rede Virtual está disponível atualmente como versão prévia pública. No momento, ela só está disponível em um conjunto limitado de [regiões](#region-availability). Essa visualização é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Veja os [Termos de Uso Adicionais para Visualizações do Microsoft Azure](https://azure.microsoft.comsupport/legal/preview-supplemental-terms) para obter detalhes.

## <a name="static-ip-addresses-for-outbound-only"></a>Endereços IP estáticos para somente saída

A conectividade de saída pode ser definida para cada sub-rede com a NAT.  Várias sub-redes dentro da mesma rede virtual podem ter NATs diferentes. Uma sub-rede é configurada especificando qual [recurso de gateway da NAT](./nat-gateway-resource.md) usar. Todos os fluxos de saída UDP e TCP de qualquer instância de máquina virtual usarão a NAT. 

A NAT é compatível com [recursos de endereço IP público](./virtual-network-ip-addresses-overview-arm.md#standard) ou [recursos de prefixo IP público](./public-ip-address-prefix.md) do SKU padrão ou uma combinação de ambos.  Você pode usar um prefixo IP público diretamente ou distribuir os endereços IP públicos do prefixo entre vários recursos do gateway da NAT. A NAT limpará todo o tráfego para o intervalo de endereços IP do prefixo.  Agora é fácil incluir o IP na lista de permissões de suas implantações.

Todo o tráfego de saída da sub-rede é processado pela NAT automaticamente sem nenhuma configuração do cliente.  Rotas definidas pelo usuário não são necessárias. A NAT tem precedência sobre outros [cenários de saída](../load-balancer/load-balancer-outbound-connections.md) e substitui o destino de Internet padrão de uma sub-rede.

## <a name="on-demand-snat-with-multiple-ip-addresses-for-scale"></a>SNAT sob demanda com vários endereços IP para escala

A NAT usa a PNAT ou PAT (“conversão de endereços de rede de porta”) e é recomendável para a maioria das cargas de trabalho. Cargas de trabalho dinâmicas ou divergentes podem ser facilmente acomodadas com alocação de fluxo de saída sob demanda. Deve-se evitar o pré-planejamento, a pré-alocação e, em último caso, o excesso de provisionamento extensivos de recursos de saída. Os recursos de porta SNAT são compartilhados e estão disponíveis em todas as sub-redes usando um recurso de gateway NAT específico e são fornecidos quando necessário.

Um endereço IP público anexado ao NAT fornece até 64 mil fluxos simultâneos para UDP e TCP. Você pode começar com um endereço IP e expandir para até 16 endereços IP públicos.

A NAT permite que os fluxos sejam criados com base na rede virtual para a Internet. O tráfego de retorno da Internet só é permitido em resposta a um fluxo ativo.

Diferentemente do SNAT de saída do balanceador de carga, a NAT não tem restrições sobre qual IP privado de uma instância de máquina virtual pode realizar conexões de saída.  As configurações de IP secundário podem criar uma conexão de Internet de saída com a NAT.

## <a name="coexistence-of-inbound-and-outbound"></a>Coexistência de entrada e saída

A NAT é compatível com os seguintes recursos de SKU padrão:

- [Balanceador de carga](../load-balancer/load-balancer-overview.md)
- [Endereço IP público](../virtual-network/virtual-network-ip-addresses-overview-arm.md#public-ip-addresses)
- [Prefixo IP público](../virtual-network/public-ip-address-prefix.md)

Quando usados em conjunto com a NAT, esses recursos fornecem conectividade de Internet de entrada para suas sub-redes. A NAT fornece toda a conectividade da Internet de saída de suas sub-redes.

A NAT e os recursos de SKU Standard compatíveis reconhecem a direção em que o fluxo foi iniciado. Cenários de entrada e saída podem coexistir. Esses cenários receberão as conversões de endereço de rede corretas, pois esses recursos reconhecem a direção do fluxo. 

<!-- 
<img src="./media/nat-overview/flow-direction4.svg" width="500" align="center">
![Virtual Network NAT flow direction](./media/nat-overview/flow-direction4.svg)
-->
<p align="center">
  <img src="./media/nat-overview/flow-direction4.svg" width="512" title="Direção do fluxo da NAT de Rede Virtual">
</p>

*Figura: Direção do fluxo da NAT de Rede Virtual*

## <a name="fully-managed-highly-resilient"></a>Totalmente gerenciado, altamente resiliente

A NAT é totalmente expandido desde o início. Não há nenhuma operação de aumento ou expansão necessária.  O Azure gerencia a operação da NAT para você.  A NAT sempre tem vários domínios de falha e pode manter várias falhas sem interrupção do serviço.

## <a name="tcp-reset-for-unrecognized-flows"></a>Redefinição de TCP para fluxos não reconhecidos

O lado privado da NAT envia pacotes de Redefinição de TCP para tentativas de se comunicar em uma conexão TCP que não existe. Um exemplo são conexões cujo tempo limite de ociosidade foi atingido. O próximo pacote recebido retornará uma Redefinição de TCP para o endereço IP privado a fim de sinalizar e forçar o fechamento da conexão.

O lado público da NAT não gera pacotes de Redefinição de TCP nem nenhum outro tráfego.  Somente o tráfego produzido pela rede virtual do cliente é emitido.

## <a name="configurable-idle-timeout"></a>Tempo limite de ociosidade configurável

Um tempo limite de ociosidade padrão de 4 minutos é usado e pode ser aumentado para até 120 minutos. Qualquer atividade em um fluxo também pode redefinir o temporizador de ociosidade, incluindo keepalives TCP.

## <a name="regional-or-zone-isolation-with-availability-zones"></a>Isolamento regional ou de zona com zonas de disponibilidade

A NAT é regional por padrão. Ao criar cenários de [zonas de disponibilidade](../availability-zones/az-overview.md), a NAT pode ser isolada em uma zona específica (implantação de zona).

<!-- 
<img src="./media/nat-overview/az-directions.svg" width="500" align="center">
![Virtual Network NAT with availability zones](./media/nat-overview/az-directions.svg)
-->

<p align="center">
  <img src="./media/nat-overview/az-directions.svg" width="512" title="NAT de Rede Virtual com zonas de disponibilidade">
</p>

*Figura: NAT de Rede Virtual com zonas de disponibilidade*

## <a name="multi-dimensional-metrics-for-observability"></a>Métricas multidimensionais para observabilidade

Você pode monitorar a operação da NAT por meio de métricas multidimensionais expostas no Azure Monitor. Essas métricas podem ser usadas para observar o uso e para solucionar problemas.  Os recursos do gateway da NAT expõem as seguintes métricas:
- Bytes
- Pacotes
- Pacotes descartados
- Total de conexões SNAT
- Transições de estado de conexão SNAT por intervalo.

<!-- "ADD when PM is done" Learn more about [NAT gateway metrics](./nat-gateway-metrics.md) -->

## <a name="sla"></a>Contrato de Nível de Serviço

Em disponibilidade geral, o caminho de dados da NAT tem pelo menos 99,9% de disponibilidade.

## <a name = "region-availability"></a>Disponibilidade de região

A NAT está disponível atualmente nestas regiões:

- Europa Ocidental
- Leste do Japão
- Leste dos EUA 2
- Oeste dos EUA
- Oeste dos EUA 2
- Centro-Oeste dos EUA

## <a name = "enable-preview"></a>Participação da versão prévia pública

As assinaturas devem ser registradas para permitir a participação na versão prévia pública.  A participação requer um processo em duas etapas e as instruções são fornecidas abaixo para a CLI do Azure e o Azure PowerShell.  A ativação pode levar vários minutos para ser concluída.

> [!IMPORTANT]
> Depois que a [versão prévia da NAT de Rede Virtual estiver habilitada](./nat-overview.md#enable-preview) na assinatura, use https://aka.ms/natportal para acessar o portal.

### <a name="azure-cli"></a>CLI do Azure

1. registrar a assinatura para a versão prévia pública

    ```azurecli-interactive
      az feature register --namespace Microsoft.Network --name AllowNatGateway
    ```

2. ativar o registro

    ```azurecli-interactive
      az provider register --namespace Microsoft.Network
    ```

### <a name="azure-powershell"></a>Azure PowerShell

1. registrar a assinatura para a versão prévia pública

    ```azurepowershell-interactive
      Register-AzProviderFeature -ProviderNamespace Microsoft.Network -FeatureName AllowNatGateway
    ```

2. ativar o registro

    ```azurepowershell-interactive
      Register-AzProviderFeature -ProviderNamespace Microsoft.Network
    ```

## <a name="pricing"></a>Preços

O gateway da NAT é cobrado com dois medidores separados:

| Medidor | Tarifa |
| --- | --- |
| Horas do recurso | US$ 0,045/hora |
| Dados processados | US$ 0,045/GB |

As horas do recurso representam o tempo durante o qual um recurso do gateway da NAT existe.
Os dados processados representam todo o tráfego processado por um recurso do gateway da NAT.

Durante a versão prévia pública, há um desconto de preço de 50%.

## <a name="support"></a>Suporte

Há suporte para a NAT por meio de canais de suporte normais.

## <a name="feedback"></a>Comentários

Desejamos saber como podemos aprimorar o serviço. Compartilhe conosco seus [comentários sobre a versão prévia pública](https://aka.ms/natfeedback).  Você também pode propor e votar em quais itens deveríamos criar em seguida, no [UserVoice para a NAT](https://aka.ms/natuservoice).

## <a name="limitations"></a>Limitações

- A NAT é compatível com recursos de prefixo de IP público, IP público de SKU e balanceador de carga de nível Standard.   Os recursos Básicos (por exemplo, um balanceador de carga básico) e produtos derivados deles não são compatíveis com a NAT.  Os recursos básicos precisam ser colocados em uma sub-rede não configurada com NAT.
- A família de endereços IPv4 é compatível.  A NAT não interage com a família de endereços IPv6.
- O NSG em uma sub-rede ou adaptador de rede não é respeitado para fluxos de saída para pontos de extremidade públicos usando a NAT.
- O registro em log de fluxo do NSG não é compatível com o uso de NAT.
- Quando uma rede virtual tem várias sub-redes, cada uma delas pode ter uma NAT diferente configurada.
- A NAT não pode abranger várias redes virtuais.

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre o [recurso de gateway da NAT](./nat-gateway-resource.md).
- [Com o UserVoice, diga-nos o que criar em seguida](https://aka/natuservoice).
- [Forneça comentários sobre a versão prévia pública](https://aka.ms/natfeedback).
