---
title: Standard Load Balancer e Zonas de Disponibilidade do Azure
titleSuffix: Azure Load Balancer
description: Com este roteiro de aprendizagem, comece a usar o Azure Standard Load Balancer e Zonas de Disponibilidade.
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
ms.openlocfilehash: 63a7db6e7078df978f47a6d53ea82df83c22c800
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74076980"
---
# <a name="standard-load-balancer-and-availability-zones"></a>Load Balancer Standard e Zonas de Disponibilidade

O Azure Standard Load Balancer dá suporte a cenários de [zonas de disponibilidade](../availability-zones/az-overview.md) . Você pode usar Standard Load Balancer para otimizar a disponibilidade em seu cenário de ponta a ponta alinhando recursos com zonas e distribuindo-os entre zonas.  Examine as [zonas de disponibilidade](../availability-zones/az-overview.md) para obter diretrizes sobre quais zonas de disponibilidade são, quais regiões oferecem suporte atualmente a zonas de disponibilidade e outros conceitos e produtos relacionados. as zonas de disponibilidade em combinação com o Standard Load Balancer é um conjunto de recursos extenso e flexível que pode criar vários cenários diferentes.  Analise este documento para entender esses [conceitos](#concepts) e as [diretrizes de design](#design) de cenário fundamentais.

>[!IMPORTANT]
>Examine [zonas de disponibilidade](../availability-zones/az-overview.md) para obter tópicos relacionados, incluindo qualquer informação específica da região.

## <a name="concepts"></a> Conceitos de Zonas de Disponibilidade aplicados ao Load Balancer

Não há relação direta entre os recursos do balanceador de carga e a infraestrutura real. A criação de um balanceador de carga não cria uma instância. Os recursos do Load Balancer são objetos nos quais é possível expressar como o Azure deve programar sua infraestrutura multilocatária pré-compilada para alcançar o cenário que você deseja criar.  Isso é significativo no contexto de zonas de disponibilidade porque um único recurso de Load Balancer pode controlar a programação de infraestrutura em várias zonas de disponibilidade, enquanto um serviço com redundância de zona aparece como um recurso do ponto de vista do cliente.  

Um recurso do Load Balancer em si é regional e nunca zonal.  E uma VNet e sub-rede são sempre regionais e nunca zonais. A granularidade do que você pode configurar é restrita por cada configuração de front-end, regra e definição de pool de back-end.

No contexto de zonas de disponibilidade, o comportamento e as propriedades de uma regra de Load Balancer são descritos como com redundância de zona ou zonas.  Zonal ou com redundância de zona descreve a zonalidade de uma propriedade.  No contexto de Load Balancer, com redundância de zona, sempre significa *várias zonas* e zonal significa isolar o serviço em uma *única zona*.

O balanceador de carga público e o interno são compatíveis com redundância de zona e com os cenários zonais, e ambos podem direcionar o tráfego entre as zonas, conforme necessário (*balanceamento de carga entre zonas*). 

### <a name="frontend"></a>Front-end

Um front-end Load Balancer é uma configuração de IP de front-end referenciando um recurso de endereço IP público ou um endereço IP privado dentro da sub-rede de um recurso de rede virtual.  Ele forma o ponto de extremidade com balanceamento de carga onde o serviço está exposto.

Um recurso de Load Balancer pode conter regras com front-ends zonais e com redundância de zonas simultaneamente. 

Quando um recurso IP público ou um endereço IP privado tiver sido garantido para uma zona, o zonalidade (ou a falta dele) não é mutável.  Se você quiser alterar ou omitir o zonalidade de um IP público ou front-end de endereço IP privado, será necessário recriar o IP público na zona apropriada.  As zonas de disponibilidade não alteram as restrições para vários front-ends, examinam [vários front-ends para Load Balancer](load-balancer-multivip-overview.md) para obter detalhes sobre essa capacidade.

#### <a name="zone-redundant-by-default"></a>Com redundância de zona por padrão

Em uma região com zonas de disponibilidade, um Standard Load Balancer front-end é com redundância de zona por padrão.  Com redundância de zona significa que todos os fluxos de entrada ou saída são servidos por várias zonas de disponibilidade em uma região simultaneamente usando um único endereço IP. Esquemas de redundância de DNS não são necessários. Um único endereço IP de front-end pode sobreviver à falha de zona e pode ser usado para atingir todos os membros (não afetados) do pool de back-end independentemente da zona. Uma ou mais zonas de disponibilidade podem falhar e o caminho de dados sobreviver, desde que uma zona na região permaneça íntegra. O endereço IP único de front-end é servido simultaneamente por várias implantações de infraestrutura independentes em várias zonas de disponibilidade.  Isso não significa implica de dados de caminho, mas qualquer nova tentativa ou reestabelecimento terá êxito em outras zonas não afetadas pela falha de zona.   

O trecho a seguir é uma ilustração de como definir um endereço IP público com redundância de zona para usar com seu Standard Load Balancer público. Se você estiver usando modelos do Resource Manager existentes em sua configuração, adicione a seção **sku** a esses modelos.

```json
            "apiVersion": "2017-08-01",
            "type": "Microsoft.Network/publicIPAddresses",
            "name": "public_ip_standard",
            "location": "region",
            "sku":
            {
                "name": "Standard"
            },
```

O trecho a seguir é uma ilustração de como definir um endereço IP de front-end com redundância de zona para seu Standard Load Balancer interno. Se você estiver usando modelos do Resource Manager existentes em sua configuração, adicione a seção **sku** a esses modelos.

```json
            "apiVersion": "2017-08-01",
            "type": "Microsoft.Network/loadBalancers",
            "name": "load_balancer_standard",
            "location": "region",
            "sku":
            {
                "name": "Standard"
            },
            "properties": {
                "frontendIPConfigurations": [
                    {
                        "name": "zone_redundant_frontend",
                        "properties": {
                            "subnet": {
                                "Id": "[variables('subnetRef')]"
                            },
                            "privateIPAddress": "10.0.0.6",
                            "privateIPAllocationMethod": "Static"
                        }
                    },
                ],
```

Os trechos anteriores não são modelos completos, mas pretendem mostrar como expressar as propriedades das zonas de disponibilidade.  Você precisa incorporar essas instruções em seus modelos.

#### <a name="optional-zone-isolation"></a>Isolamento de zona opcional

Você pode optar por ter um front-end garantido para uma única zona, conhecido como um *front-end zonal*.  Isso significa que qualquer fluxo de entrada ou saída é atendido por uma única zona em uma região.  O front-end compartilha o destino com a integridade da zona.  O caminho de dados não é afetado por falhas em outras zonas além de onde foi garantido. É possível utilizar front-ends zonais para expor um endereço IP por Zona de Disponibilidade.  

Além disso, você pode consumir front-ends zonais diretamente para pontos de extremidade com balanceamento de carga em cada zona. Você também pode usar isso para expor pontos de extremidade com balanceamento de carga por zona para monitorar individualmente cada zona.  Ou para pontos de extremidade públicos, você pode integrá-los a um produto de balanceamento de carga de DNS como o [Gerenciador de tráfego](../traffic-manager/traffic-manager-overview.md) e usar um único nome DNS. Em seguida, o cliente então será resolvido para esse nome DNS para vários endereços IP zonais.  

Se você deseja mesclar esses conceitos (zonal e com redundância de zona para o mesmo back-end), analise [vários front-ends para Azure Load Balancer](load-balancer-multivip-overview.md).

Para um front-end público Load Balancer, você adiciona um parâmetro de *zonas* ao recurso de IP público referenciado pela configuração de IP de front-end usada pela respectiva regra.

Para um front-end público do Load Balancer, você inclui um parâmetro *zonas* para o IP público referenciado pela configuração de IP de front-end. O front-end zonal faz com que o Load Balancer garanta um endereço IP em uma sub-rede para uma zona específica.

O trecho a seguir é uma ilustração de como definir um endereço IP público padrão de zona em Zona 1 de disponibilidade. Se você estiver usando modelos do Resource Manager existentes em sua configuração, adicione a seção **sku** a esses modelos.

```json
            "apiVersion": "2017-08-01",
            "type": "Microsoft.Network/publicIPAddresses",
            "name": "public_ip_standard",
            "location": "region",
            "zones": [ "1" ],
            "sku":
            {
                "name": "Standard"
            },
```

O trecho a seguir é uma ilustração de como definir um front-end de Standard Load Balancer interno no Zona 1 de disponibilidade. Se você estiver usando modelos do Resource Manager existentes em sua configuração, adicione a seção **sku** a esses modelos. Além disso, defina a propriedade **zonas** na configuração de IP de front-end para o recurso filho.

```json
            "apiVersion": "2017-08-01",
            "type": "Microsoft.Network/loadBalancers",
            "name": "load_balancer_standard",
            "location": "region",
            "sku":
            {
                "name": "Standard"
            },
            "properties": {
                "frontendIPConfigurations": [
                    {
                        "name": "zonal_frontend_in_az1",
                        "zones": [ "1" ],
                        "properties": {
                            "subnet": {
                                "Id": "[variables('subnetRef')]"
                            },
                            "privateIPAddress": "10.0.0.6",
                            "privateIPAllocationMethod": "Static"
                        }
                    },
                ],
```

Os trechos anteriores não são modelos completos, mas pretendem mostrar como expressar as propriedades das zonas de disponibilidade.  Você precisa incorporar essas instruções em seus modelos.

### <a name="cross-zone-load-balancing"></a>Balanceamento de carga entre zonas

O balanceamento de carga entre zonas é a capacidade do balanceador de carga de alcançar um ponto de extremidade de back-end em qualquer zona e é independente do front-end e da zonalidade.  Qualquer regra de balanceamento de carga pode direcionar a instância de back-end em qualquer zona de disponibilidade ou instâncias regionais.

Você precisa tomar cuidado para construir seu cenário de uma maneira que expressa uma noção de zonas de disponibilidade. Por exemplo, você precisa garantir a implantação de sua máquina virtual em uma única zona ou em várias zonas e alinhar os recursos de back-end zonal e frontend zonal para a mesma região.  Se você cruzar zonas de disponibilidade com apenas recursos de zona, o cenário funcionará, mas talvez não tenha um modo de falha clara em relação às zonas de disponibilidade. 

### <a name="backend"></a>Back-end

Load Balancer funciona com instâncias de máquinas virtuais.  Eles podem ser autônomos, conjuntos de disponibilidade ou conjuntos de dimensionamento de máquinas virtuais.  Qualquer instância de máquina virtual em uma única rede virtual pode fazer parte do pool de back-end, independentemente de ser ou não garantida a uma zona ou a qual zona foi garantida.

Se você quiser alinhar e garantir o frontend e o back-end com uma única zona, só coloque as máquinas virtuais na mesma zona no respectivo pool de back-end.

Se você quiser endereçar máquinas virtuais entre várias zonas, simplesmente coloque as máquinas virtuais de várias zonas no mesmo pool de back-end.  Ao utilizar conjuntos de dimensionamento de máquinas virtuais, é possível colocar um ou mais conjuntos de dimensionamento de máquinas virtuais no mesmo pool de back-end.  E cada um desses conjuntos de dimensionamento de máquinas virtuais pode estar em uma única ou em várias zonas.

### <a name="outbound-connections"></a>Conexões de saída

As mesmas propriedades com redundância de zona e zonas se aplicam a [conexões de saída](load-balancer-outbound-connections.md).  Um endereço IP público com redundância de zona usado para conexões de saída é servido por todas as zonas. Um endereço IP público zonal é servido somente pela zona em que ele está garantido.  As alocações de porta SNAT de conexão de saída sobrevivem a falhas de zona e seu cenário continuará a fornecer conectividade SNAT de saída se não for afetado por falha de zona.  Isso pode exigir transmissões ou para que as conexões sejam restabelecidas para cenários com redundância de zona se um fluxo foi servido por uma zona afetada.  Os fluxos em zonas diferentes das zonas afetadas não são afetados.

O algoritmo de prealocação de porta SNAT é o mesmo com ou sem zonas de disponibilidade.

### <a name="health-probes"></a>Investigações de integridade

Suas definições de investigação de integridade existentes permanecem como estão sem zonas de disponibilidade.  No entanto, expandimos o modelo de integridade em um nível de infraestrutura. 

Ao usar front-ends com redundância de zona, o Load Balancer expande seu modelo de integridade interno para investigar de forma independente a acessibilidade de uma máquina virtual de cada zona de disponibilidade e desligar os caminhos entre zonas que podem ter falhado sem intervenção do cliente.  Se um determinado caminho não estiver disponível na infraestrutura de Load Balancer de uma zona para uma máquina virtual em outra zona, Load Balancer poderá detectar e evitar essa falha. Outras zonas que podem alcançar essa VM podem continuar a atender a VM dos respectivos front-ends.  Como resultado, é possível que, durante os eventos de falha, cada zona possa ter distribuições ligeiramente diferentes dos novos fluxos, ao mesmo tempo em que protege a integridade geral de seu serviço de ponta a ponta.

## <a name="design"></a> Considerações sobre o Design

A Load Balancer é intencionalmente flexível no contexto de zonas de disponibilidade. Você pode optar por se alinhar às zonas ou pode optar por ser com redundância de zona para cada regra.  O aumento da disponibilidade pode resultar no aumento da complexidade e você deve projetar a disponibilidade para um desempenho ideal.  Vejamos algumas considerações importantes sobre o design.

### <a name="automatic-zone-redundancy"></a>Redundância de zona automática

O Load Balancer torna simples ter um único IP como um front-end com redundância de zona. Um endereço IP com redundância de zona pode seguramente atender um recurso zonal em qualquer zona e sobreviver a uma ou mais falhas de zona, desde que uma zona permaneça íntegra dentro da região. Por outro lado, um front-end zonal é uma redução do serviço para uma única zona e compartilha o destino com a respectiva zona.

Com redundância de zona não implica plano de controle ou caminho de dados sem ocorrência; é expressamente plano de dados. Fluxos com redundância de zona podem usar qualquer zona e os fluxos de um cliente usarão todas as zonas íntegras em uma região. No caso de falha de zona, os fluxos de tráfego usando zonas íntegras naquele momento não serão afetados.  Os fluxos de tráfego usando uma zona no momento da falha de zona podem ser afetados, mas os aplicativos podem ser recuperados. Esses fluxos podem continuar nas zonas de integridade restantes dentro da região após a retransmissão ou o restabelecimento, uma vez que o Azure convergiu em redor da falha de zona.

### <a name="xzonedesign"></a> Limites entre zonas

É importante entender que sempre que um serviço de ponta a ponta cruza as zonas, você compartilha o destino não com uma zona, mas potencialmente várias zonas.  Como resultado, o serviço de ponta a ponta pode não ter obtido nenhuma disponibilidade em implantações não zonais.

Evite introduzir dependências entre zonas não pretendidas, o que anulará os ganhos de disponibilidade ao usar zonas de disponibilidade.  Quando o aplicativo consiste em vários componentes e você deseja ser resiliente a falhas de zona, é necessário ter cuidado para garantir a sobrevivência de componentes críticos suficientes no caso de falha de uma zona.  Por exemplo, um único componente crítico para o aplicativo pode afetar todo o aplicativo se ele existir em uma zona diferente da(s) zona(s) sobrevivente(s).  Além disso, considere também a restauração de zona e como o aplicativo irá convergir. Você precisa entender como os seus aplicativos têm relação a falhas de partes dele. Vamos revisar alguns pontos principais e utilizá-los como inspiração para perguntas ao refletir sobre um cenário específico.

- Se seu aplicativo tiver dois componentes, como um endereço IP e uma máquina virtual com disco gerenciado, e eles estiverem garantidos na zona 1 e zona 2, quando a zona 1 falhar, o serviço de ponta a ponta não permanecerá quando a zona 1 falhar.  Não entre zonas com cenários de zona, a menos que você entenda totalmente que está criando um modo de falha potencialmente nocivo.  Esse cenário tem permissão para fornecer flexibilidade.

- Se seu aplicativo tiver dois componentes, como um endereço IP e uma máquina virtual com disco gerenciado, e for garantido que estejam com redundância de zona e a zona 1, respectivamente, o serviço de ponta a ponta sobreviver a falhas de zona da zona 2, zona 3 ou ambas, a menos que a zona 1 tenha falhado.  No entanto, alguma capacidade de avaliar sobre a integridade do serviço será perdida, se tudo o que estiver observando for a acessibilidade do front-end.  Considere desenvolver um modelo de capacidade e integridade mais abrangente.  Você pode usar os conceitos com redundância de zona e zonal juntos para expandir a análise e a capacidade de gerenciamento.

- Se o aplicativo tiver dois componentes, como um front-end do balanceador de carga com redundância de zona e um conjunto de dimensionamento de máquinas virtuais entre regiões em três zonas, os recursos nas zonas não afetadas por falha estarão disponíveis, mas a capacidade do serviço de ponta a ponta poderá ser degradada durante a falha de zona. Do ponto de vista de infraestrutura, a implantação pode sobreviver a uma ou mais falhas de zona e isso suscita as seguintes perguntas:
  - É possível entender como o aplicativo avalia essas falhas e a capacidade degradada?
  - Você precisará de garantias no serviço para forçar um failover em um par de regiões, se necessário?
  - Como você monitorará, detectará e mitigará esse cenário? É possível utilizar os diagnósticos do Load Balancer Standard para aumentar o monitoramento do desempenho do serviço de ponta a ponta. Considere o que está disponível e o que pode ser necessário ampliar para uma imagem completa.

- Zonas podem tornar falhas mais fáceis de serem compreendidas e contidas.  No entanto, a falha de zona não é diferente de outras falhas quando se trata de conceitos como tempos limites, repetições e algoritmos de retirada. Mesmo que o Azure Load Balancer forneça caminhos com redundância de zona e tente recuperá-los rapidamente, em um nível de pacote em tempo real, retransmissões ou reestabelecimentos poderão ocorrer durante o início de uma falha e é importante entender como o aplicativo lida com falhas. O esquema de balanceamento de carga será mantido, mas será necessário planejar o seguinte:
  - Quando uma zona falha, o serviço de ponta a ponta entende isso e, se o estado for perdido, como você irá recuperar?
  - Quando uma zona retorna, o aplicativo é capaz de convergir com segurança?

Examine os [padrões de design de nuvem do Azure](https://docs.microsoft.com/azure/architecture/patterns/) para melhorar a resiliência do seu aplicativo para cenários de falha.

### <a name="zonalityguidance"></a> Com redundância de zona versus zonal

Com redundância de zona, é possível fornecer uma simplicidade com uma opção independente de zona e, ao mesmo tempo, a opção resiliente com um único endereço IP para o serviço.  Por sua vez, pode reduzir a complexidade.  Com redundância de zona também tem mobilidade entre zonas e pode ser utilizada com segurança em recursos em qualquer zona.  Além disso, é uma prova futura em regiões sem zonas de disponibilidade, o que pode limitar as alterações necessárias quando uma região tiver zonas de disponibilidade.  A sintaxe de configuração para um endereço IP com redundância de zona ou front-end é realizada com sucesso em qualquer região, incluindo aquelas sem zonas de disponibilidade: uma zona não é especificada dentro da propriedade zonas: do recurso.

As zonas podem fornecer uma garantia explícita para uma zona, compartilhando explicitamente destino com a integridade da zona. A criação de uma regra de Load Balancer com um endereço IP de zona frontend ou um front-end Load Balancer interno de frontend pode ser desejável especialmente se o recurso anexado for uma máquina virtual zonal na mesma zona.  Ou talvez seu aplicativo exija conhecimento explícito sobre a zona em que um recurso está localizado antecipadamente e você deseja ponderar a disponibilidade em zonas separadas explicitamente.  É possível escolher expor vários front-ends zonais para um serviço de ponta a ponta distribuído entre zonas (ou seja, front-ends zonais por zona para vários conjuntos de dimensionamento de máquinas virtuais zonais).  E se os front-ends zonais forem endereços IP públicos, será possível utilizar esses vários front-ends zonais para expor o serviço com o [Gerenciador de Tráfego](../traffic-manager/traffic-manager-overview.md).  Ou poderá utilizar vários front-ends zonais para obter análises de desempenho e integridade por zona por meio de soluções de monitoramento de terceiros e expor o serviço geral com um front-end com redundância de zona. Você deve atender apenas recursos zonais com front-ends zonais alinhados à mesma zona e evitar cenários entre zonas potencialmente prejudiciais para recursos zonais.  Os recursos zonais existem somente em regiões em que existem zonas de disponibilidade.

Não há nenhuma diretriz geral informando que uma escolha é melhor do que a outra, sem conhecer a arquitetura do serviço.  Examine os [padrões de design de nuvem do Azure](https://docs.microsoft.com/azure/architecture/patterns/) para melhorar a resiliência do seu aplicativo para cenários de falha.

## <a name="limitations"></a>Limitações

- Enquanto o plano de dados tiver completamente com redundância de zonas (a menos que a garantia zonal tenha sido especificada), as operações do plano de controle não terão total redundância entre zonas.

## <a name="next-steps"></a>Próximas etapas
- Saiba mais sobre as [Zonas de disponibilidade](../availability-zones/az-overview.md)
- Saiba mais sobre o [Load Balancer Standard](load-balancer-standard-overview.md)
- Saiba como [balancear carga de VMs em uma zona usando um Load Balancer Standard com um front-end zonal](load-balancer-standard-public-zonal-cli.md)
- Saiba como [balancear carga de VMs entre zonas usando um Load Balancer Standard com um front-end com redundância de zona](load-balancer-standard-public-zone-redundant-cli.md)
- Saiba mais sobre os [padrões de design de nuvem do Azure](https://docs.microsoft.com/azure/architecture/patterns/) para melhorar a resiliência do seu aplicativo para cenários de falha.
