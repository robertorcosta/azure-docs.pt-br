---
title: Criar redes virtuais com recursos de gateway da NAT
titleSuffix: Azure Virtual Network NAT
description: Saiba como criar redes virtuais com recursos de gateway da NAT.
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
ms.service: virtual-network
Customer intent: As an IT administrator, I want to learn more about how to design virtual networks with NAT gateway resources.
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/04/2020
ms.author: allensu
ms.openlocfilehash: d920bde856521f1e662536c1187881e143612039
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2020
ms.locfileid: "78359104"
---
# <a name="designing-virtual-networks-with-nat-gateway-resources-public-preview"></a>Criar redes virtuais com recursos de gateway da NAT (versão prévia pública)

Recursos de gateway da NAT são parte da [NAT de Rede Virtual](nat-overview.md) e fornecem conectividade com a Internet de saída para uma ou mais sub-redes de uma rede virtual. A sub-rede da rede virtual declara qual gateway da NAT será usado. A NAT fornece a SNAT (conversão de endereços de rede de origem) para uma sub-rede.  Os recursos do gateway da NAT especificam quais máquinas virtuais de endereço IP estático usar ao criar fluxos de saída. Endereços IP estáticos vêm de recursos de endereço IP público, recursos de prefixo IP público ou de ambos. Um recurso de gateway da NAT pode usar até 16 endereços IP estáticos, de qualquer desses tipos.


<p align="center">
  <img src="media/nat-overview/flow-direction1.svg" width="256" title="NAT de Rede Virtual para saída à Internet">
</p>

*Figura: NAT de Rede Virtual para saída à Internet*


>[!NOTE] 
>A NAT de Rede Virtual está disponível atualmente como versão prévia pública. No momento, ela só está disponível em um conjunto limitado de [regiões](nat-overview.md#region-availability). Essa visualização é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Veja os [Termos de Uso Adicionais para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms) para obter detalhes.

## <a name="how-to-deploy-nat"></a>Como implantar a NAT

Configurar e usar o gateway da NAT é intencionalmente simples:  

Recurso de gateway da NAT:
- crie um recurso de gateway da NAT regional ou zonal (isolado em zona),
- atribua endereços IP,
- modifique o tempo limite de ociosidade (opcional).

Rede virtual:
- Configure a sub-rede da rede virtual para usar um gateway da NAT.

Rotas definidas pelo usuário não são necessárias.

## <a name="resource"></a>Recurso

O recurso é projetado para ser simples, conforme você pode ver no exemplo a seguir do Azure Resource Manager, em um formato semelhante ao de um modelo.  Esse formato semelhante ao de um modelo é mostrado aqui para ilustrar os conceitos e a estrutura.  Modifique o exemplo segundo as suas necessidades.  Este documento não é destinado a servir como um tutorial.

O diagrama a seguir mostra as referências graváveis entre os diferentes recursos do Azure Resource Manager.  A seta indica a direção da referência, partindo no local em que ela é gravável. Revisão 

<p align="center">
  <img src="media/nat-overview/flow-map.svg" width="256" title="Modelo de objeto da NAT de Rede Virtual">
</p>

*Figura: Modelo de objeto da NAT de Rede Virtual*

A NAT é recomendada para a maioria das cargas de trabalho, a menos que você tenha uma dependência específica de [conectividade de saída do Load Balancer baseada em pool](../load-balancer/load-balancer-outbound-connections.md).  

Você pode migrar de cenários de um Standard Load Balancer, incluindo [regras de saída](../load-balancer/load-balancer-outbound-rules-overview.md), para um gateway da NAT. Para migrar, mova os recursos de prefixo de IP público e prefixo de IP público dos front-ends do balanceador de carga para o gateway da NAT. Não são necessários novos endereços IP para o gateway da NAT. O IP público e o prefixo Standard podem ser reutilizados, desde que o total não exceda 16 endereços IP. Planeje a migração considerando a interrupção do serviço durante a transição.  Automatizando o processo, você pode minimizar a interrupção. Teste a migração primeiro em um ambiente de preparo.  Durante a transição, fluxos originados em entrada não são afetados.

O exemplo a seguir criaria um recurso de gateway da NAT chamado _myNATGateway_ na região _Leste dos EUA 2, AZ 1_ com um tempo limite de ociosidade equivalente a _quatro minutos_. Os endereços IP de saída fornecidos são:
- Um conjunto de recursos de endereço IP público _myIP1_ e _myIP2_ e 
- Um conjunto de recursos de prefixo de IP público _myPrefix1_ e _myPrefix2_. 

O número total de endereços IP fornecidos por todos os quatro recursos de endereço IP não pode exceder 16. Qualquer número de endereços IP entre 1 e 16 é permitido.

```json
{
"name": "myNATGateway",
   "type": "Microsoft.Network/natGateways",
   "apiVersion": "2018-11-01",
   "location": "East US 2",
   "sku": { "name": "Standard" },
   "zones": [ "1" ],
   "properties": {
      "idleTimeoutInMinutes": 4, 
      "publicIPPrefixes": [
         {
            "id": "ref to myPrefix1"
         },
         {
            "id": "ref to myPrefix2"
         }
      ],
      "publicIPAddresses": [
         {
            "id": "ref to myIP1"
         },
         {
            "id": "ref to myIP2"
         }
      ]
   }
}
```

Quando o recurso de gateway da NAT tiver sido criado, ele poderá ser usado em uma ou mais sub-redes de uma rede virtual. Especifique quais sub-redes usam esse recurso de gateway da NAT. Um gateway da NAT não é pode abranger mais de uma rede virtual. Não é necessário atribuir o mesmo gateway da NAT para todas as sub-redes de uma rede virtual. Sub-redes individuais podem ser configuradas com diferentes recursos de gateway da NAT.

Os cenários que não usam zonas de disponibilidade serão regionais (sem que zonas sejam especificadas). Se você estiver usando zonas de disponibilidade, poderá especificar uma zona para isolar a NAT em uma zona específica. O uso de redundância de zona não é compatível. Examine as [zonas de disponibilidade](#availability-zones) da NAT.


```json
{
   "name": "myVNet",
   "apiVersion": "2018-11-01",
   "type": "Microsoft.Network/virtualNetworks",
   "location": "myRegion", 
   "properties": {
      "addressSpace": {
          "addressPrefixes": [
           "192.168.0.0/16"
          ]
      },
      "subnets": [
         {
            "name": "mySubnet1",
            "properties": {
               "addressPrefix": "192.168.0.0/24",
               "natGateway": {
                  "id": "ref to myNATGateway"
               }
            }
         } 
      ]
   }
}
```
Os gateways da NAT são definidos com uma propriedade em uma sub-rede, que por sua vez é parte de uma rede virtual. Fluxos criados por máquinas virtuais na sub-rede _mySubnet1_ da rede virtual _myVNet_ usarão o gateway da NAT. Toda a conectividade de saída usará os endereços IP associados com _myNatGateway_ como o endereço IP de origem.


## <a name="design-guidance"></a>Diretrizes de design

Examine esta seção para familiarizar-se com as considerações pertinentes à criação de redes virtuais com NAT.  

1. [Otimização de custos](#cost-optimization)
1. [Coexistência de entrada e saída](#coexistence-of-inbound-and-outbound)
2. [Gerenciar recursos Básicos](#managing-basic-resources)
3. [Zonas de Disponibilidade](#availability-zones)

### <a name="cost-optimization"></a>Otimização de custos

[Pontos de extremidade de serviço](virtual-network-service-endpoints-overview.md) e [link privado](../private-link/private-link-overview.md) são duas opções a considerar para otimizar custos quando não há necessidade de uso da NAT.  Qualquer tráfego direcionado a pontos de extremidade de serviço ou a um link privado não é processado pela NAT de Rede Virtual.  

Os pontos de extremidade de serviço associam os recursos de serviços do Azure à rede virtual e controlam o acesso aos recursos de serviço do Azure. Por exemplo, quando você acessa o Armazenamento do Azure, use um ponto de extremidade de serviço como armazenamento para evitar encargos da NAT referentes a dados processados. Os pontos de extremidade de serviço são gratuitos.

Um link privado expõe o serviço de PaaS do Azure (ou outros serviços hospedados com um link privado) como um ponto de extremidade privado dentro de uma rede virtual.  Um link privado é cobrado com base na duração e nos dados processados.

Avalie se é uma boa opção adotar ambas ou uma dessas abordagens em seu cenário e use-as conforme necessário.

### <a name="coexistence-of-inbound-and-outbound"></a>Coexistência de entrada e saída

O gateway da NAT é compatível com:

 - Standard Load Balancer
 - IP público Standard
 - Prefixo de IP público Standard

Ao desenvolver uma nova implantação, comece com SKUs padrão.

<p align="center">
  <img src="media/nat-overview/flow-direction1.svg" width="256" title="NAT de Rede Virtual para saída à Internet">
</p>

*Figura: NAT de Rede Virtual para saída à Internet*

O cenário de somente saída pela Internet fornecido pelo gateway da NAT pode ser expandido com a funcionalidade de entrada da Internet. Cada recurso está ciente da direção em que um fluxo é originado. Em uma sub-rede em um gateway da NAT, toda a saída para cenários de Internet é substituída pelo gateway da NAT. Cenários de entrada da Internet são fornecidos pelo respectivo recurso.

#### <a name="nat-and-vm-with-instance-level-public-ip"></a>NAT e VM com IP público no nível da instância

<p align="center">
  <img src="media/nat-overview/flow-direction2.svg" width="300" title="NAT e VM da Rede Virtual com IP público no nível da instância">
</p>

*Figura: NAT e VM da Rede Virtual com IP público no nível da instância*

| Direção | Recurso |
|:---:|:---:|
| Entrada | VM com IP público no nível da instância |
| Saída | Gateway da NAT |

A VM usará o gateway da NAT para saída.  A entrada originada não é afetada.

#### <a name="nat-and-vm-with-public-load-balancer"></a>NAT e VM com Load Balancer público

<p align="center">
  <img src="media/nat-overview/flow-direction3.svg" width="350" title="NAT e VM da Rede Virtual com Load Balancer público">
</p>

*Figura: NAT e VM da Rede Virtual com Load Balancer público*

| Direção | Recurso |
|:---:|:---:|
| Entrada | Load Balancer público |
| Saída | Gateway da NAT |

Qualquer configuração de saída de uma regra para balanceamento de carga ou regras de saída é substituída pelo gateway da NAT.  A entrada originada não é afetada.

#### <a name="nat-and-vm-with-instance-level-public-ip-and-public-load-balancer"></a>NAT e VM com Load Balancer público e IP público no nível da instância

<p align="center">
  <img src="media/nat-overview/flow-direction4.svg" width="425" title="NAT e VM com IP da Rede Virtual com Load Balancer público e IP público no nível da instância">
</p>

*Figura: NAT e VM com IP da Rede Virtual com Load Balancer público e IP público no nível da instância*

| Direção | Recurso |
|:---:|:---:|
| Entrada | VM com Load Balancer público e IP público no nível da instância |
| Saída | Gateway da NAT |

Qualquer configuração de saída de uma regra para balanceamento de carga ou regras de saída é substituída pelo gateway da NAT.  A VM também usará o gateway da NAT para saída.  A entrada originada não é afetada.

### <a name="managing-basic-resources"></a>Gerenciar recursos Básicos

O Standard Load Balancer, o IP público e o prefixo de IP público são compatíveis com o gateway da NAT. Gateways da NAT operam no escopo de uma sub-rede. O SKU básico desses serviços precisa ser implantado em uma sub-rede sem um gateway da NAT. Essa separação permite que as duas variantes do SKU coexistam na mesma rede virtual.

Gateways da NAT têm precedência sobre cenários de saída da sub-rede. O IP público ou o Load Balancer Básico (e qualquer serviço gerenciado criado com eles) não pode ser ajustado com as conversões corretas. O gateway da NAT assume o controle do tráfego de saída para a Internet em uma sub-rede. O tráfego de entrada para o Load Balancer Básico e o IP público não está disponível. O tráfego de entrada para o Load Balancer Básico e/ou um IP público configurado em uma VM não estará disponível.

### <a name="availability-zones"></a>Zonas de Disponibilidades

Mesmo sem zonas de disponibilidade, a NAT é resiliente e pode resistir a múltiplas falhas de componentes de infraestrutura. Quando o seu cenário inclui zonas de disponibilidade disponíveis, você deve configurar a NAT para uma zona específica.  As operações do painel de controle e o plano de dados ficam restritos à zona especificada. Espera-se que uma eventual falha em uma zona diferente daquela em que o seu cenário reside não tenha impacto sobre a NAT. Tráfego de saída de máquinas virtuais na mesma zona resultará em falha, devido ao isolamento de zona.

<p align="center">
  <img src="media/nat-overview/az-directions.svg" width="425" title="NAT de Rede Virtual com zonas de disponibilidade">
</p>

*Figura: NAT de Rede Virtual com zonas de disponibilidade*

Um gateway da NAT isolado em zona requer que os endereços IP correspondam à zona do gateway da NAT. Recursos de gateway da NAT com endereços IP de uma zona diferente ou que não estejam em uma zona não são compatíveis.

As redes virtuais e sub-redes são regionais e não são alinhadas de maneira zonal. Uma VM precisa estar na mesma zona que o gateway da NAT para que haja garantia de uma distribuição zonal de conexões de saída. O isolamento em zona é formado criando-se uma "pilha" zonal em cada zona de disponibilidade. Não haverá garantia de distribuição zonal ao atravessar zonas de um gateway da NAT zonal nem ao usar um gateway da NAT regional com VMs zonais.

Quando você implantar conjuntos de dimensionamento de máquinas virtuais para uso com NAT, você implantará um conjunto de dimensionamento zonal na respectiva sub-rede e anexará o gateway da NAT da zona correspondente a essa sub-rede. Se você usar conjuntos de dimensionamento que transcendam uma zona (um conjunto de dimensionamento em duas ou mais zonas), a NAT não oferecerá garantia de distribuição zonal.  A NAT não dá suporte à redundância de zona.  Ela dá suporte somente ao isolamento regional ou em zonas.

<p align="center">
  <img src="media/nat-overview/az-directions2.svg" width="425" title="NAT de Rede Virtual que transcende uma zona">
</p>

*Figura: NAT de Rede Virtual que transcende uma zona*

A propriedade zones não é mutável.  Reimplante o recurso de gateway da NAT com a preferência de zona ou regional pretendida.

>[!NOTE] 
>Endereços IP, por si sós, não apresentam redundância de zona se nenhuma zona é especificada.  O front-end de um [Standard Load Balancer apresenta redundância de zona](../load-balancer/load-balancer-standard-availability-zones.md#frontend) se um endereço IP não é criado em uma zona específica.  Isso não se aplica à NAT.  Ela dá suporte somente ao isolamento regional ou em zonas.

## <a name="source-network-address-translation"></a>Conversão de endereços de rede de origem

A SNAT (conversão de endereços de rede de origem) reescreve a origem de um fluxo, passando-a para um endereço IP diferente.  Os recursos de gateway da NAT usam uma variante da SNAT, comumente referida como PAT (conversão de endereços de porta). A PAT reescreve o endereço de origem e a porta de origem. Com a SNAT, não há nenhuma relação fixa entre o número de endereços privados e os respectivos endereços públicos convertidos.  

### <a name="fundamentals"></a>Conceitos básicos

Vejamos um exemplo de quatro fluxos para explicar o conceito básico.  O gateway de NAT está usando o recurso de endereço IP público 65.52.0.2.

| Flow | Tupla de origem | Tupla de destino |
|:---:|:---:|:---:|
| 1 | 192.168.0.16:4283 | 65.52.0.1:80 |
| 2 | 192.168.0.16:4284 | 65.52.0.1:80 |
| 3 | 192.168.0.17.5768 | 65.52.0.1:80 |
| 4 | 192.168.0.16:4285 | 65.52.0.2:80 |

Esses fluxos podem ter aparência semelhante à seguinte após a realização da PAT:

| Flow | Tupla de origem | Dupla de origem em que a SNAT foi realizada | Tupla de destino | 
|:---:|:---:|:---:|:---:|
| 1 | 192.168.0.16:4283 | 65.52.0.2:234 | 65.52.0.1:80 |
| 2 | 192.168.0.16:4284 | 65.52.0.2:235 | 65.52.0.1:80 |
| 3 | 192.168.0.17.5768 | 65.52.0.2:236 | 65.52.0.1:80 |
| 4 | 192.168.0.16:4285 | 65.52.0.2:237 | 65.52.0.2:80 |

O destino verá a origem do fluxo como 65.52.0.2 (tupla de origem de SNAT) com a porta atribuída mostrada.  A PAT, conforme mostrada na tabela anterior, também é chamada de SNAT mascaradora de portas.  Várias fontes privadas ficam mascaradas por trás de um IP e uma porta.

Não assuma uma dependência no modo específico como as portas do destino são atribuídas.  A ilustração anterior mostra apenas o conceito fundamental.

A SNAT fornecida pela NAT é diferente do [Load Balancer](../load-balancer/load-balancer-outbound-connections.md) em diversos aspectos.

### <a name="on-demand"></a>Sob demanda

A NAT fornece portas de SNAT sob demanda para novos fluxos de tráfego de saída. Todas as portas de SNAT disponíveis no inventário são usadas por qualquer máquina virtual em sub-redes configuradas com NAT. 

<p align="center">
  <img src="media/nat-overview/lb-vnnat-chart.svg" width="550" title="NAT de Rede Virtual/SNAT de saída sob demanda">
</p>

*Figura: NAT de Rede Virtual/SNAT de saída sob demanda*

Qualquer configuração de IP de uma máquina virtual pode criar fluxos de saída sob demanda conforme necessário.  Antes da alocação, não é necessário um planejamento por instância que inclua sobreprovisionamento por instância para o pior cenário.  

<p align="center">
  <img src="media/nat-overview/exhaustion-threshold.svg" width="550" title="Diferenças em cenários de esgotamento">
</p>

*Figura: Diferenças em cenários de esgotamento*

Após a liberação de uma porta de SNAT, ela fica disponível para uso por qualquer máquina virtual em sub-redes configuradas com NAT.  A alocação sob demanda permite que cargas de trabalho dinâmicas e divergentes em sub-redes usem portas de SNAT como precisarem.  Desde que haja inventário de portas de SNAT disponível, os fluxos da SNAT terão êxito. Já os pontos de acesso de portas de SNAT se beneficiam do inventário maior. As portas de SNAT não são deixadas sem uso para máquinas virtuais que não precisem ativamente dessas portas.

### <a name="scaling"></a>Scaling

A NAT precisa de um inventário de portas de SNAT suficiente para o cenário de saída completo. O dimensionamento de NAT é, primariamente, uma função de gerenciamento do inventário de portas de SNAT disponíveis e compartilhadas. É necessário que exista um inventário suficiente para atender ao fluxo de saída de pico para todas as sub-redes anexadas a um recurso de gateway da NAT.

A SNAT mapeia vários endereços privados para um endereço público e usa vários IPs públicos para dimensionar.

Um recurso de gateway da NAT usará 64.000 portas (portas de SNAT) de um endereço IP público.  Essas portas de SNAT se tornarão o inventário disponível para o mapeamento de fluxo de privado para público. Além disso, adicionar mais endereços IP públicos aumenta o inventário de portas de SNAT disponíveis. Os recursos de gateway da NAT podem ser aumentados para até 16 endereços IP e um milhão de portas de SNAT.  TCP e UDP são inventários de portas de SNAT separados e não relacionados.

Os recursos de gateway da NAT reutilizam oportunamente as portas de origem. Para fins de dimensionamento, você deve presumir que cada fluxo requer uma nova porta de SNAT e dimensionar o número total de endereços IP disponíveis para o tráfego de saída.

### <a name="protocols"></a>Protocolos

Os recursos de gateway da NAT interagem com IPs e cabeçalhos de transporte de IPs de fluxos UDP e TCP e independe dos conteúdos das camadas dos aplicativos.  Outros protocolos IP não são compatíveis.

### <a name="timers"></a>Temporizadores

O tempo limite de ociosidade pode ser ajustado de quatro minutos (padrão) para 120 minutos (duas horas) para todos os fluxos.  Adicionalmente, você pode redefinir o temporizador de ociosidade com o tráfego no fluxo.  Um padrão recomendado para atualizar conexões ociosas por longos períodos e a detecção de atividade de pontos de extremidade é o uso de keepalives TCP.  Os keepalives TCP aparecem como ACKs duplicados para os pontos de extremidade, representam pouca sobrecarga e são invisíveis para a camada do aplicativo.

Os seguintes temporizadores são usados para a liberação de portas de SNAT:

| Timer | Valor |
|---|---|
| TCP FIN | 60 segundos |
| TCP RST | 10 segundos |
| TCP semiaberto | 30 segundos |

Uma porta de SNAT está disponível para reutilização para o mesmo endereço IP de destino e a mesma porta de destino após 5 segundos.

>[!NOTE] 
>Essas configurações de temporizador estão sujeitas a alterações. Os valores são fornecidos para auxiliar a solucionar problemas e você não deverá assumir uma dependência de temporizadores específicos nesse momento.

## <a name="limitations"></a>Limitações

- A NAT é compatível com recursos de prefixo de IP público, IP público de SKU e balanceador de carga de nível Standard.   Os recursos Básicos (por exemplo, um balanceador de carga básico) e produtos derivados deles não são compatíveis com a NAT.  Os recursos básicos precisam ser colocados em uma sub-rede não configurada com NAT.
- A família de endereços IPv4 é compatível.  A NAT não interage com a família de endereços IPv6.  A NAT não pode ser implantada em uma sub-rede com o prefixo IPv6.
- O registro em log de fluxo do NSG não é compatível com o uso de NAT.
- A NAT não pode abranger várias redes virtuais.

## <a name="preview-participation"></a>Participação de versão prévia

Siga as [instruções para habilitar sua assinatura](nat-overview.md#public-preview-participation).

## <a name="feedback"></a>Comentários

Desejamos saber como podemos aprimorar o serviço. Compartilhe conosco seus [comentários sobre a versão prévia pública](https://aka.ms/natfeedback).  Você também pode propor e votar em quais itens deveríamos criar em seguida, no [UserVoice para a NAT](https://aka.ms/natuservoice).

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre a [NAT da rede virtual](nat-overview.md).
* Saiba mais sobre [métricas e alertas para recursos do Gateway da NAT](nat-metrics.md).
* Saiba mais sobre a [solução de problemas de recursos do Gateway da NAT](troubleshoot-nat.md).
* [Diga-nos o que criar em seguida para a NAT de Rede Virtual no UserVoice](https://aka.ms/natuservoice).
* [Forneça comentários sobre a versão prévia pública](https://aka.ms/natfeedback).
* Tutorial para validar um gateway da NAT
  - [CLI do Azure](tutorial-create-validate-nat-gateway-cli.md),
  - [PowerShell](tutorial-create-validate-nat-gateway-cli.md),
  - [Portal](tutorial-create-validate-nat-gateway-cli.md)
* Início rápido para implantar o recurso de gateway da NAT
  - [CLI do Azure](./quickstart-create-nat-gateway-cli.md),
  - [PowerShell](./quickstart-create-nat-gateway-powershell.md),
  - [Portal](./quickstart-create-nat-gateway-portal.md).
* Saiba mais sobre a API do recurso do Gateway da NAT
  - [API REST](https://docs.microsoft.com/rest/api/virtualnetwork/natgateways),
  - [CLI do Azure](https://docs.microsoft.com/cli/azure/network/nat/gateway?view=azure-cli-latest),
  - [PowerShell](https://docs.microsoft.com/powershell/module/az.network/new-aznatgateway).
* Saiba mais sobre as [zonas de disponibilidade](../availability-zones/az-overview.md).
* Saiba mais sobre o [Standard Load Balancer](../load-balancer/load-balancer-standard-overview.md).
* Saiba mais sobre as [zonas de disponibilidade e o Standard Load Balancer](../load-balancer/load-balancer-standard-availability-zones.md).


