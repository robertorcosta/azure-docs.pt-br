---
title: Standard Load Balancer e Zonas de Disponibilidade do Azure
titleSuffix: Azure Load Balancer
description: Com este caminho de aprendizado, comece com o Azure Standard Load Balancer e zonas de disponibilidade.
services: load-balancer
documentationcenter: na
author: asudbring
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/07/2019
ms.author: allensu
ms.openlocfilehash: 5a65982c5c13eb4e4273efcfd8d14910b0f35572
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78197140"
---
# <a name="standard-load-balancer-and-availability-zones"></a>Load Balancer Standard e Zonas de Disponibilidade

O Azure Standard Load Balancer suporta cenários [de zonas de disponibilidade.](../availability-zones/az-overview.md) Você pode usar o Standard Load Balancer para otimizar a disponibilidade em seu cenário de ponta a ponta, alinhando recursos com regiões e distribuindo-os entre regiões.  Revise [as zonas de disponibilidade](../availability-zones/az-overview.md) para obter orientações sobre quais zonas de disponibilidade são, quais regiões atualmente suportam zonas de disponibilidade e outros conceitos e produtos relacionados. As zonas de disponibilidade em combinação com o Standard Load Balancer é um conjunto de recursos expansivo e flexível que pode criar muitos cenários diferentes.  Analise este documento para entender esses [conceitos](#concepts) e as [diretrizes de design](#design) de cenário fundamentais.

## <a name="availability-zones-concepts-applied-to-load-balancer"></a><a name="concepts"></a> Conceitos de Zonas de Disponibilidade aplicados ao Load Balancer

Um recurso do Load Balancer em si é regional e nunca zonal. A granularidade do que você pode configurar é limitada por cada configuração de definição de pool frontend, regra e backend.
No contexto das zonas de disponibilidade, o comportamento e as propriedades de uma regra do Balanceador de carga são descritos como zona-redundante ou zonal.  Zonal ou com redundância de zona descreve a zonalidade de uma propriedade.  No contexto do Balanceador de Carga, a zona-redundante significa sempre *várias zonas* e zonal significa isolar o serviço para uma *única região*.
O balanceador de carga público e o interno são compatíveis com redundância de zona e com os cenários zonais, e ambos podem direcionar o tráfego entre as zonas, conforme necessário (*balanceamento de carga entre zonas*). 

### <a name="frontend"></a>Front-end

Um frontend do Load Balancer é uma configuração IP frontend que faz referência a um recurso de endereço IP público ou a um endereço IP privado dentro da sub-rede de um recurso de rede virtual.  Ele forma o ponto de extremidade com balanceamento de carga onde o serviço está exposto.
Um recurso load balancer pode conter regras com frontends zonais e redundantes de zona simultaneamente. Quando um recurso IP público ou um endereço IP privado foi garantido a uma região, a zonalidade (ou falta dele) não é mutável.  Se você deseja alterar ou omitir a zonalidade de um frontend de endereço IP público ou privado, você precisa recriar o IP público na região apropriada.  As zonas de disponibilidade não alteram as restrições para vários frontend, revisam [várias frontends para Load Balancer](load-balancer-multivip-overview.md) para obter detalhes sobre essa capacidade.

#### <a name="zone-redundant"></a>Redundância de zona 

Em uma região com zonas de disponibilidade, um frontend Standard Load Balancer pode ser redundante em zona.  A zona redundante significa que todos os fluxos de entrada ou saída são servidos por várias zonas de disponibilidade em uma região simultaneamente usando um único endereço IP. Esquemas de redundância de DNS não são necessários. Um único endereço IP frontend pode sobreviver à falha da zona e pode ser usado para alcançar todos os membros do pool de backend (não impactados) independentemente da região. Uma ou mais zonas de disponibilidade podem falhar e o caminho de dados sobrevive enquanto uma zona na região permanecer saudável. O endereço IP único do frontend é servido simultaneamente por várias implantações independentes de infra-estrutura em várias zonas de disponibilidade.  Isso não significa caminho de dados sem impacto, mas qualquer repetição ou restabelecimento terá sucesso em outras regiões não impactadas pela falha da região.   

#### <a name="optional-zone-isolation"></a>Isolamento opcional da zona

Você pode optar por ter um front-end garantido para uma única zona, conhecido como um *front-end zonal*.  Isso significa que qualquer fluxo de entrada ou saída é atendido por uma única zona em uma região.  O front-end compartilha o destino com a integridade da zona.  O caminho de dados não é afetado por falhas em outras zonas além de onde foi garantido. É possível utilizar front-ends zonais para expor um endereço IP por Zona de Disponibilidade.  

Além disso, você pode consumir frontends zonais diretamente para carregar pontos finais equilibrados dentro de cada região. Você também pode usar isso para expor pontos de extremidade com balanceamento de carga por zona para monitorar individualmente cada zona.  Ou para pontos finais públicos, você pode integrá-los com um produto de balanceamento de carga DNS como [o Traffic Manager](../traffic-manager/traffic-manager-overview.md) e usar um único nome DNS. Em seguida, o cliente resolverá este nome DNS para vários endereços IP zonal.  

Se você deseja mesclar esses conceitos (zonal e com redundância de zona para o mesmo back-end), analise [vários front-ends para Azure Load Balancer](load-balancer-multivip-overview.md).

Para um frontend público do Balancer de carga, você adiciona um parâmetro *de zonas* ao recurso IP público referenciado pela configuração de IP frontend usada pela respectiva regra.

Para um front-end público do Load Balancer, você inclui um parâmetro *zonas* para o IP público referenciado pela configuração de IP de front-end. O front-end zonal faz com que o Load Balancer garanta um endereço IP em uma sub-rede para uma zona específica.

### <a name="cross-zone-load-balancing"></a>Balanceamento de carga entre zonas

O balanceamento de carga entre zonas é a capacidade do balanceador de carga de alcançar um ponto de extremidade de back-end em qualquer zona e é independente do front-end e da zonalidade.  Qualquer regra de balanceamento de carga pode direcionar a instância backend em qualquer zona de disponibilidade ou instâncias regionais.

Você precisa tomar cuidado para construir seu cenário de uma maneira que expressasse uma noção de zonas de disponibilidade. Por exemplo, você precisa garantir a implantação da sua máquina virtual dentro de uma única região ou várias regiões e alinhar os recursos de frontend zonal e backend zonal para a mesma região.  Se você cruzar zonas de disponibilidade com apenas recursos zonais, o cenário funcionará, mas pode não ter um modo de falha claro em relação às zonas de disponibilidade. 

### <a name="backend"></a>Back-end

O Balancer de carga funciona com instâncias de máquinas virtuais.  Estes podem ser autônomos, conjuntos de disponibilidade ou conjuntos de escala de máquinas virtuais.  Qualquer instância de máquina virtual em uma única rede virtual pode fazer parte do pool de backend, independentemente de ter ou não sido garantido a uma região ou a qual região foi garantida.

Se você deseja alinhar e garantir seu frontend e backend com uma única região, coloque apenas máquinas virtuais dentro da mesma zona no respectivo pool de backend.

Se você deseja abordar máquinas virtuais em várias zonas, basta colocar máquinas virtuais de várias zonas no mesmo pool de back-end.  Ao utilizar conjuntos de dimensionamento de máquinas virtuais, é possível colocar um ou mais conjuntos de dimensionamento de máquinas virtuais no mesmo pool de back-end.  E cada um desses conjuntos de dimensionamento de máquinas virtuais pode estar em uma única ou em várias zonas.

### <a name="outbound-connections"></a>Conexões de saída

As mesmas propriedades zone-redundantes e zonais aplicam-se [às conexões de saída](load-balancer-outbound-connections.md).  Um endereço IP público redundante da zona usado para conexões de saída é servido por todas as regiões. Um endereço IP público zonal é servido apenas pela zona em que é garantido.  As alocações de porta SNAT de conexão de saída sobrevivem a falhas na zona e seu cenário continuará a fornecer conectividade SNAT de saída se não for impactada por falha na região.  Isso pode exigir transmissões ou para que as conexões sejam restabelecidas para cenários redundantes de zona se um fluxo for atendido por uma zona impactada.  Os fluxos em zonas diferentes das zonas impactadas não são afetados.

O algoritmo de pré-alocação da porta SNAT é o mesmo com ou sem zonas de disponibilidade.

### <a name="health-probes"></a>Investigações de integridade

As definições existentes do teste de saúde permanecem como estão sem zonas de disponibilidade.  No entanto, expandimos o modelo de saúde em nível de infraestrutura. 

Ao usar frontends redundantes de zona, o Load Balancer expande seu modelo de saúde interna para sondar independentemente a capacidade de alcance de uma máquina virtual de cada zona de disponibilidade e desligar caminhos em regiões que podem ter falhado sem a intervenção do cliente.  Se um determinado caminho não estiver disponível da infra-estrutura Load Balancer de uma região para uma máquina virtual em outra região, o Balancer de carga pode detectar e evitar essa falha. Outras zonas que podem alcançar essa VM podem continuar a atender a VM dos respectivos front-ends.  Como resultado, é possível que durante os eventos de falha, cada região possa ter distribuições ligeiramente diferentes de novos fluxos, protegendo a saúde geral do seu serviço de ponta a ponta.

## <a name="design-considerations"></a><a name="design"></a>Considerações de design

O Balanceador de Carga é propositalmente flexível no contexto das zonas de disponibilidade. Você pode optar por alinhar-se às zonas ou pode optar por ser redundante de zona para cada regra.  O aumento da disponibilidade pode resultar no aumento da complexidade e você deve projetar a disponibilidade para um desempenho ideal.  Vejamos algumas considerações importantes sobre o design.

### <a name="automatic-zone-redundancy"></a>Redundância de zona automática

O Load Balancer torna simples ter um único IP como um front-end com redundância de zona. Um endereço IP com redundância de zona pode seguramente atender um recurso zonal em qualquer zona e sobreviver a uma ou mais falhas de zona, desde que uma zona permaneça íntegra dentro da região. Por outro lado, um front-end zonal é uma redução do serviço para uma única zona e compartilha o destino com a respectiva zona.

Com redundância de zona não implica plano de controle ou caminho de dados sem ocorrência; é expressamente plano de dados. Fluxos com redundância de zona podem usar qualquer zona e os fluxos de um cliente usarão todas as zonas íntegras em uma região. No caso de falha de zona, os fluxos de tráfego usando zonas íntegras naquele momento não serão afetados.  Os fluxos de tráfego usando uma zona no momento da falha de fuso podem ser afetados, mas os aplicativos podem se recuperar. Esses fluxos podem continuar nas zonas saudáveis remanescentes dentro da região após a retransmissão ou restabelecimento, uma vez que o Azure convergiu em torno da falha da zona.

### <a name="cross-zone-boundaries"></a><a name="xzonedesign"></a> Limites entre zonas

É importante entender que sempre que um serviço de ponta a ponta cruza as zonas, você compartilha o destino não com uma zona, mas potencialmente várias zonas.  Como resultado, o serviço de ponta a ponta pode não ter obtido nenhuma disponibilidade em implantações não zonais.

Evite introduzir dependências de zonas cruzadas não intencionais, o que anulará os ganhos de disponibilidade ao usar zonas de disponibilidade.  Quando o aplicativo consiste em vários componentes e você deseja ser resiliente a falhas de zona, é necessário ter cuidado para garantir a sobrevivência de componentes críticos suficientes no caso de falha de uma zona.  Por exemplo, um único componente crítico para o aplicativo pode afetar todo o aplicativo se ele existir em uma zona diferente da(s) zona(s) sobrevivente(s).  Além disso, considere também a restauração de zona e como o aplicativo irá convergir. Você precisa entender como suas razões de aplicação em relação a falhas de partes dele. Vamos revisar alguns pontos principais e utilizá-los como inspiração para perguntas ao refletir sobre um cenário específico.

- Se o aplicativo tiver dois componentes como um endereço IP e uma máquina virtual com disco gerenciado, e eles estiverem garantidos na zona 1 e na zona 2, quando a zona 1 falhar, seu serviço de ponta a ponta não sobreviverá quando a zona 1 falhar.  Não cruze zonas com cenários zonais, a menos que você entenda completamente que está criando um modo de falha potencialmente perigoso.  Este cenário é permitido para proporcionar flexibilidade.

- Se o aplicativo tiver dois componentes como um endereço IP e uma máquina virtual com disco gerenciado, e eles tiverem a garantia de serem redundantes de zona e a zona 1, respectivamente, seu serviço de ponta a ponta sobreviverá à falha na zona 2, na zona 3 ou ambos, a menos que a zona 1 falhe.  No entanto, alguma capacidade de avaliar sobre a integridade do serviço será perdida, se tudo o que estiver observando for a acessibilidade do front-end.  Considere desenvolver um modelo de capacidade e integridade mais abrangente.  Você pode usar os conceitos com redundância de zona e zonal juntos para expandir a análise e a capacidade de gerenciamento.

- Se o aplicativo tiver dois componentes, como um front-end do balanceador de carga com redundância de zona e um conjunto de dimensionamento de máquinas virtuais entre regiões em três zonas, os recursos nas zonas não afetadas por falha estarão disponíveis, mas a capacidade do serviço de ponta a ponta poderá ser degradada durante a falha de zona. Do ponto de vista de infraestrutura, a implantação pode sobreviver a uma ou mais falhas de zona e isso suscita as seguintes perguntas:
  - É possível entender como o aplicativo avalia essas falhas e a capacidade degradada?
  - Você precisará de garantias no serviço para forçar um failover em um par de regiões, se necessário?
  - Como você monitorará, detectará e mitigará esse cenário? É possível utilizar os diagnósticos do Load Balancer Standard para aumentar o monitoramento do desempenho do serviço de ponta a ponta. Considere o que está disponível e o que pode ser necessário ampliar para uma imagem completa.

- Zonas podem tornar falhas mais fáceis de serem compreendidas e contidas.  No entanto, a falha de zona não é diferente de outras falhas quando se trata de conceitos como tempos limites, repetições e algoritmos de retirada. Mesmo que o Azure Load Balancer forneça caminhos com redundância de zona e tente recuperá-los rapidamente, em um nível de pacote em tempo real, retransmissões ou reestabelecimentos poderão ocorrer durante o início de uma falha e é importante entender como o aplicativo lida com falhas. O esquema de balanceamento de carga será mantido, mas será necessário planejar o seguinte:
  - Quando uma zona falha, o serviço de ponta a ponta entende isso e, se o estado for perdido, como você irá recuperar?
  - Quando uma zona retorna, o aplicativo é capaz de convergir com segurança?

Revise os padrões de design de nuvem do [Azure](https://docs.microsoft.com/azure/architecture/patterns/) para melhorar a resiliência do seu aplicativo a cenários de falha.

## <a name="next-steps"></a>Próximas etapas
- Saiba mais sobre [zonas de disponibilidade](../availability-zones/az-overview.md)
- Saiba mais sobre [o Balanceador de Carga Padrão](load-balancer-standard-overview.md)
- Saiba como [balancear carga de VMs em uma zona usando um Load Balancer Standard com um front-end zonal](load-balancer-standard-public-zonal-cli.md)
- Saiba como [balancear carga de VMs entre zonas usando um Load Balancer Standard com um front-end com redundância de zona](load-balancer-standard-public-zone-redundant-cli.md)
- Conheça [os padrões de design de nuvem do Azure](https://docs.microsoft.com/azure/architecture/patterns/) para melhorar a resiliência do seu aplicativo a cenários de falha.
