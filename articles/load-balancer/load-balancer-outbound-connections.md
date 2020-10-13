---
title: Conexões de saída
titleSuffix: Azure Load Balancer
description: Este artigo explica como o Azure permite que as VMs comuniquem-se com serviços de Internet públicos.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.custom: contperfq1
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/30/2020
ms.author: allensu
ms.openlocfilehash: 6b9f454c75a10644e86931dc86ebd9514e5431d3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91649789"
---
# <a name="outbound-connections"></a>Conexões de saída

O Azure Load Balancer fornece conectividade de saída por meio de mecanismos diferentes. Este artigo descreve os cenários e como gerenciá-los. 


## <a name="scenarios"></a>Cenários

* Máquina virtual com IP público.
* Máquina virtual sem IP público.
* Máquina virtual sem IP público e sem o balanceador de carga padrão.

### <a name="virtual-machine-with-public-ip"></a><a name="scenario1"></a>Máquina virtual com IP público

| Associações | Método | Protocolos IP |
| ---------- | ------ | ------------ |
| Balanceador de carga público ou autônomo | [SNAT (conversão de endereços de rede de origem)](#snat) </br> [Pat (mascaramento de porta)](#pat) não usado. | TCP (protocolo de controle de transmissão) </br> UDP (protocolo de datagrama do usuário) </br> ICMP (protocolo de mensagens de controle da Internet) </br> ESP (encapsulamento de carga de segurança) |

#### <a name="description"></a>Descrição

O Azure usa o IP público atribuído à configuração de IP da NIC da instância para todos os fluxos de saída. A instância possui todas as portas efêmeras disponíveis. Não importa se a VM tem balanceamento de carga ou não. Esse cenário tem precedência sobre os outros. 

Um IP público atribuído a uma VM é uma relação 1:1 (em vez de 1:muitos) e implementado como sem estado 1:1 NAT.

### <a name="virtual-machine-without-public-ip"></a><a name="scenario2"></a>Máquina virtual sem IP público

| Associações | Método | Protocolos IP |
| ------------ | ------ | ------------ |
| Balanceador de carga público | Uso do front-end do balanceador de carga para [SNAT](#snat) com [Pat (mascaramento de porta)](#pat).| TCP </br> UDP |

#### <a name="description"></a>Descrição

O recurso de balanceador de carga está configurado com uma regra de balanceador de carga. Essa regra é usada para criar um vínculo entre o front-end IP público com o pool de back-end. 

Se você não concluir essa configuração de regra, o comportamento será conforme descrito no cenário 3. 

Uma regra com um ouvinte não é necessária para que a investigação de integridade tenha sucesso.

Quando uma VM cria um fluxo de saída, o Azure converte o endereço IP de origem para o endereço IP público do front-end do Load Balancer público. Essa conversão é feita via [SNAT](#snat). 

As portas efêmeras do endereço IP público de front-end do balanceador de carga são usadas para distinguir fluxos individuais originados pela VM. A SNAT usa dinamicamente [ortas efêmeras pré-alocadas](#preallocatedports) quando os fluxos de saída são criados. 

Neste contexto, as portas efêmeras usadas para SNAT são chamadas de portas SNAT. As portas SNAT são previamente alocadas conforme descrito na [tabela de alocação de portas SNAT padrão](#snatporttable).

### <a name="virtual-machine-without-public-ip-and-without-standard-load-balancer"></a><a name="scenario3"></a>Máquina virtual sem IP público e sem o balanceador de carga padrão

| Associações | Método | Protocolos IP |
| ------------ | ------ | ------------ |
|Nenhum </br> Balanceador de carga básico | [SNAT](#snat) com [disfarce de porta (Pat)](#pat)| TCP </br> UDP | 

#### <a name="description"></a>Descrição

Quando a VM cria um fluxo de saída, o Azure traduz o endereço IP de origem para um endereço IP de origem pública. Esse endereço IP público **não é configurável** e não pode ser reservado. Esse endereço não conta com relação ao limite de recursos de IP público da assinatura. 

O endereço IP público será liberado e um novo IP público solicitado se você reimplantar o: 

* Máquina Virtual
* Conjunto de disponibilidade
* Conjunto de escala de máquina virtual  

Não use este cenário para adicionar IPs a uma lista de permissões. Use o cenário 1 ou 2 onde declarar explicitamente o comportamento de saída. As portas [SNAT](#snat) são prealocadas conforme descrito na [tabela de alocação de portas SNAT padrão](#snatporttable).



## <a name="port-allocation-algorithm"></a><a name="preallocatedports"></a>Algoritmo de alocação de porta

O Azure usa um algoritmo para determinar o número de portas [SNAT](#snat) previamente alocadas disponíveis. O algoritmo baseia o número de portas no tamanho do pool de back-end. 

Para cada endereço IP público associado a um balanceador de carga, 64.000 portas estão disponíveis como portas [SNAT](#snat) . O mesmo número de portas [SNAT](#snat) é pré-ALOCADO para UDP e TCP. As portas são consumidas independentemente por protocolo IP. 

O uso da porta [SNAT](#snat) é diferente dependendo se o fluxo é UDP ou TCP. 

As portas são consumidas dinamicamente até o limite prealocado.  As portas são liberadas quando o fluxo é fechado ou um tempo limite de ociosidade ocorre.

Para obter mais informações sobre os tempos limite de ociosidade, consulte [solucionar problemas de conexões de saída no Azure Load Balancer](../load-balancer/troubleshoot-outbound-connection.md#idletimeout) 

As portas são consumidas apenas se for necessário fazer fluxos exclusivos.

### <a name="dynamic-snat-ports-preallocation"></a><a name="snatporttable"></a> Prealocação de portas SNAT dinâmica

A tabela a seguir mostra as prealocações de porta [SNAT](#snat) para camadas de tamanhos de pool de back-end:

| Tamanho do pool (instâncias VM) | Portas SNAT pré-alocadas por configuração de IP |
| --- | --- |
| 1-50 | 1\.024 |
| 51-100 | 512 |
| 101-200 | 256 |
| 201-400 | 128 |
| 401-800 | 64 |
| 801-1,000 | 32 |

A alteração do tamanho do pool de back-end pode afetar alguns dos fluxos estabelecidos:

- O tamanho do pool de back-end aumenta as transições de gatilho para a próxima camada. Metade das portas [SNAT](#snat) prealocadas são recuperadas durante a transição para a próxima camada. 

- Fluxos associados a um tempo limite de porta [SNAT](#snat) recuperado e fechados. Esses fluxos devem ser reestabelecidos. Se um novo fluxo for tentado, o fluxo terá êxito imediato, desde que as portas pré-alocadas estejam disponíveis.

- Se o tamanho do pool de back-end for menor e fizer a transição para uma camada inferior, o número de portas [SNAT](#snat) disponíveis aumentará. As portas [SNAT](#snat) dadas existentes e seus respectivos fluxos não são afetados.

## <a name="outbound-rules"></a><a name="outboundrules"></a>Regras de saída

 As regras de saída permitem a configuração da conversão de endereços de rede de saída do [balanceador de carga padrão](load-balancer-standard-overview.md)público.  

> [!NOTE]
> O **NAT da rede virtual do Azure** pode fornecer conectividade de saída para máquinas virtuais em uma rede virtual.  Consulte [o que é NAT de rede virtual do Azure?](../virtual-network/nat-overview.md) para obter mais informações.

Você tem total controle declarativo sobre a conectividade de saída para dimensionar e ajustar essa capacidade às suas necessidades.

![Regras de saída do balanceador de carga](media/load-balancer-outbound-rules-overview/load-balancer-outbound-rules.png)

Com as regras de saída, você pode usar o balanceador de carga para definir o NAT de saída do zero. Você também pode dimensionar e ajustar o comportamento do NAT de saída existente.

Regras de saída permitem que você controle:

- Quais máquinas virtuais devem ser traduzidas para quais endereços IP públicos.
- Como as portas [SNAT](#snat) de saída devem ser fornecidas.
- Para quais protocolos fornecer tradução de saída.
- Qual duração usar para tempo limite de ociosidade de conexão de saída (4-120 minutos).
- Se deseja enviar uma redefinição de TCP no tempo limite ocioso
- Protocolos de transporte TCP e UDP com uma única regra

### <a name="outbound-rule-definition"></a>Definição de regra de saída

As regras de saída seguem a mesma sintaxe familiar que o balanceamento de carga e as **frontend**regras NAT de entrada: pool de  +  **parameters**  +  **back-end**de parâmetros de frontend. Uma regra de saída configura o NAT de saída para _todas as máquinas virtuais identificadas pelo pool de back-end_ a serem convertidas no _front-end_.  Os _parâmetros_ fornecem um controle adicional refinado sobre o algoritmo NAT de saída.

### <a name="scale-outbound-nat-with-multiple-ip-addresses"></a><a name="scale"></a> Dimensionar NAT de saída com vários endereços IP

Cada endereço IP adicional fornecido por um front-end fornece portas efêmeras 64.000 adicionais para o balanceador de carga usar como portas SNAT. 

Use vários endereços IP para planejar cenários em larga escala. Use regras de saída para atenuar o [esgotamento de SNAT](troubleshoot-outbound-connection.md#snatexhaust). 

Você também pode usar um [prefixo IP público](https://aka.ms/lbpublicipprefix) diretamente com uma regra de saída. 

Um prefixo IP público aumenta o dimensionamento da sua implantação. O prefixo pode ser adicionado à lista de permissões de fluxos provenientes de seus recursos do Azure. Você pode configurar uma configuração de IP de front-end dentro do balanceador de carga para fazer referência a um prefixo de endereço IP público.  

O balanceador de carga tem controle sobre o prefixo IP público. A regra de saída usará automaticamente todos os endereços IP públicos contidos no prefixo IP público para conexões de saída. 

Cada um dos endereços IP dentro do prefixo IP público fornece uma 64.000 portas efêmeras adicionais por endereço IP para o balanceador de carga usar como portas SNAT.

### <a name="outbound-flow-idle-timeout-and-tcp-reset"></a><a name="idletimeout"></a> Tempo limite de ociosidade do fluxo de saída e redefinição de TCP

Regras de saída fornecem um parâmetro de configuração para controlar o tempo limite de ociosidade de fluxo de saída e combiná-lo com as necessidades do seu aplicativo. O padrão de tempos limite de ociosidade de saída é de 4 minutos. Para obter mais informações, consulte [Configurar tempos limite de ociosidade](load-balancer-tcp-idle-timeout.md). 

O comportamento padrão do balanceador de carga é descartar o fluxo silenciosamente quando o tempo limite de ociosidade de saída for atingido. O `enableTCPReset` parâmetro habilita um comportamento e controle previsíveis do aplicativo. O parâmetro determina se a redefinição TCP bidirecional (TCP RST) deve ser enviada no tempo limite do tempo limite de ociosidade de saída. 

Examine a [redefinição de TCP no tempo limite ocioso](https://aka.ms/lbtcpreset) para obter detalhes, incluindo a disponibilidade da região.

### <a name="preventing-outbound-connectivity"></a><a name="preventoutbound"></a>Impedir conectividade de saída

As regras de balanceamento de carga fornecem programação automática de NAT de saída. Alguns cenários se beneficiam ou exigem que você desabilite a programação automática do NAT de saída pela regra de balanceamento de carga. A desabilitação por meio da regra permite controlar ou refinar o comportamento.  

Você pode usar esse parâmetro de duas maneiras:

1. Prevenção do endereço IP de entrada para SNAT de saída. Desabilite o SNAT de saída na regra de balanceamento de carga.
  
2. Ajuste os parâmetros de [SNAT](#snat) de saída de um endereço IP usado para entrada e saída simultaneamente. O NAT de saída automático deve ser desabilitado para permitir que uma regra de saída assuma o controle. Para alterar a alocação de porta SNAT de um endereço também usado para entrada, o `disableOutboundSnat` parâmetro deve ser definido como true. 

A operação para configurar uma regra de saída falhará se você tentar redefinir um endereço IP que é usado para entrada.  Desabilite primeiro o NAT de saída da regra de balanceamento de carga.

>[!IMPORTANT]
> Sua máquina virtual não terá conectividade de saída se você definir esse parâmetro como true e não tiver uma regra de saída para definir a conectividade de saída.  Algumas operações de sua VM ou do seu aplicativo podem depender de ter conectividade de saída disponível. Entenda as dependências do seu cenário e considere o impacto de fazer essa alteração.

Às vezes, não é desejável que uma VM crie um fluxo de saída. Pode haver um requisito para gerenciar quais destinos recebem fluxos de saída ou quais destinos iniciam fluxos de entrada. Use [grupos de segurança de rede](../virtual-network/security-overview.md) para gerenciar os destinos que a VM atinge. Use NSGs para gerenciar quais destinos públicos iniciam fluxos de entrada.

Ao aplicar um NSG a uma VM com balanceamento de carga, atente-se às [marcas de serviço](../virtual-network/security-overview.md#service-tags) e às [regras de segurança padrão](../virtual-network/security-overview.md#default-security-rules). Verifique se a VM pode receber solicitações de investigação de integridade de Azure Load Balancer.

Se um NSG bloquear as solicitações de investigação de integridade da AZURE_LOADBALANCER marca padrão, a investigação de integridade da VM falhará e a VM será marcada como indisponível. O Balanceador de Carga interrompe o envio de novos fluxos para a VM.

## <a name="scenarios-with-outbound-rules"></a>Cenários com regras de saída

### <a name="outbound-rules-scenarios"></a>Cenários de regras de saída

* Configure conexões de saída para um conjunto específico de IPs ou de prefixo público.
* Modificar a alocação de porta [SNAT](#snat) .
* Habilitar somente saída.
* NAT de saída somente para VMs (sem entrada).
* NAT de saída para o balanceador de carga Standard interno.
* Habilite os protocolos TCP & UDP para NAT de saída com um balanceador de carga padrão público.

### <a name="configure-outbound-connections-to-a-specific-set-of-public-ips-or-prefix"></a><a name="scenario1out"></a>Configurar conexões de saída para um conjunto específico de IPs públicos ou prefixo

#### <a name="details"></a>Detalhes

Use este cenário para personalizar as conexões de saída para originar-se de um conjunto de endereços IP públicos. Adicione IPs públicos ou prefixos a uma lista de permissão ou negação com base na origem.

Esse IP público ou prefixo pode ser o mesmo usado por uma regra de balanceamento de carga. 

Para usar um IP público ou prefixo diferente do usado por uma regra de balanceamento de carga:  

1. Crie um prefixo IP público ou endereço IP público.
2. Criar um balanceador de carga padrão público 
3. Crie um front-end referenciando o prefixo IP público ou o endereço IP público que você deseja usar. 
4. Reutilize um pool de back-end ou crie um pool de back-end e coloque as VMs em um pool de back-end do balanceador de carga público
5. Configure uma regra de saída no balanceador de carga público para habilitar o NAT de saída para as VMs usando o front-end. Se você não quiser que a regra de balanceamento de carga seja usada para saída, desabilite o SNAT de saída na regra de balanceamento de carga.

### <a name="modify-snat-port-allocation"></a><a name="scenario2out"></a>Modificar a alocação de porta [SNAT](#snat)

#### <a name="details"></a>Detalhes

Você pode usar regras de saída para ajustar a [alocação automática da porta SNAT com base no tamanho do pool de back-end](load-balancer-outbound-connections.md#preallocatedports). 

Se você experimentar o esgotamento de SNAT, aumente o número de portas [SNAT](#snat) dadas do padrão de 1024. 

Cada endereço IP público contribui para até 64.000 portas efêmeras. O número de VMs no pool de back-end determina o número de portas distribuídas para cada VM. Uma VM no pool de back-end tem acesso ao máximo de 64.000 portas. Para duas VMs, no máximo 32.000 portas [SNAT](#snat) podem ser dadas com uma regra de saída (2x 32.000 = 64.000). 

Você pode usar regras de saída para ajustar as portas SNAT fornecidas por padrão. Você dá mais ou menos do que a alocação de porta [SNAT](#snat) padrão fornecida. Cada endereço IP público de um front-end de uma regra de saída contribui para até 64.000 portas efêmeras para uso como portas [SNAT](#snat) .  

O balanceador de carga fornece portas [SNAT](#snat) em múltiplos de 8. Se você fornecer um valor não divisível por 8, a operação de configuração será rejeitada. 

Se você tentar fornecer mais portas [SNAT](#snat) do que as disponíveis com base no número de endereços IP públicos, a operação de configuração será rejeitada.

Se você fornecer 10.000 portas por VM e sete VMs em um pool de back-end compartilharem um único IP público, a configuração será rejeitada. Sete multiplicado por 10.000 excede o limite de 64.000 portas. Adicione mais endereços IP públicos ao front-end da regra de saída para habilitar o cenário. 

Reverta para a [alocação de porta padrão](load-balancer-outbound-connections.md#preallocatedports) especificando 0 para o número de portas. As primeiras 50 instâncias de VM receberão 1024 portas, as instâncias de VM 51-100 terão 512 até o máximo de instâncias.  Para obter mais informações sobre a alocação de porta SNAT padrão, consulte a [tabela de alocação de portas SNAT](#snatporttable).

### <a name="enable-outbound-only"></a><a name="scenario3out"></a>Habilitar somente saída

#### <a name="details"></a>Detalhes

Use um balanceador de carga padrão público para fornecer NAT de saída para um grupo de VMs. Nesse cenário, use uma regra de saída por si só, sem a necessidade de qualquer regra adicional.

> [!NOTE]
> O **NAT da rede virtual do Azure** pode fornecer conectividade de saída para máquinas virtuais sem a necessidade de um balanceador de carga.  Consulte [o que é NAT de rede virtual do Azure?](../virtual-network/nat-overview.md) para obter mais informações.

### <a name="outbound-nat-for-vms-only-no-inbound"></a><a name="scenario4out"></a>NAT de saída apenas para VMs (não de entrada)

> [!NOTE]
> O **NAT da rede virtual do Azure** pode fornecer conectividade de saída para máquinas virtuais sem a necessidade de um balanceador de carga.  Consulte [o que é NAT de rede virtual do Azure?](../virtual-network/nat-overview.md) para obter mais informações.

#### <a name="details"></a>Detalhes

Para este cenário:

1. Crie um IP público ou prefixo.
2. Crie um balanceador de carga padrão público. 
3. Crie um front-end associado ao IP público ou prefixo dedicado para saída.
4. Crie um pool de back-end para as VMs.
5. Coloque as VMs no pool de back-end.
6. Configure uma regra de saída para habilitar o NAT de saída.

Use um prefixo ou IP público para dimensionar portas [SNAT](#snat) . Adicione a origem das conexões de saída a uma lista de permissão ou negação.

### <a name="outbound-nat-for-internal-standard-load-balancer"></a><a name="scenario5out"></a>NAT de saída para o balanceador de carga Standard interno

> [!NOTE]
> O **NAT da rede virtual do Azure** pode fornecer conectividade de saída para máquinas virtuais utilizando um balanceador de carga padrão interno.  Consulte [o que é NAT de rede virtual do Azure?](../virtual-network/nat-overview.md) para obter mais informações.

#### <a name="details"></a>Detalhes

A conectividade de saída não está disponível para um balanceador de carga Standard interno até que tenha sido declarado explicitamente. 

Para obter mais informações, consulte [configuração de balanceador de carga somente de saída](https://docs.microsoft.com/azure/load-balancer/egress-only).


### <a name="enable-both-tcp--udp-protocols-for-outbound-nat-with-a-public-standard-load-balancer"></a><a name="scenario6out"></a>Habilitar os protocolos TCP & UDP para NAT de saída com um Load Balancer Standard público

#### <a name="details"></a>Detalhes

Ao usar um balanceador de carga padrão público, o NAT de saída automático fornecido corresponde ao protocolo de transporte da regra de balanceamento de carga. 

1. Desabilite o [SNAT](#snat) de saída na regra de balanceamento de carga. 
2. Configure uma regra de saída no mesmo balanceador de carga.
3. Reutilize o pool de back-end já usado por suas VMs. 
4. Especifique "protocolo": "Todos" como parte da regra de saída. 

Quando são usadas apenas regras NAT de entrada, nenhuma NAT de saída é fornecida. 

1. Coloque as VMs em um pool de back-end.
2. Definir uma ou mais configurações de IP de front-end com endereços IP públicos ou prefixo de IP público 
3. Configure uma regra de saída no mesmo balanceador de carga. 
4. Especifique "protocolo": "Todos" como parte da regra de saída

## <a name="terminology"></a><a name="terms"></a> Terminologia

### <a name="source-network-address-translation"></a><a name="snat"></a>Conversão de endereços de rede de origem

| Protocolo (s) aplicável |
|------------------------|
| TCP </br> UDP          |

#### <a name="details"></a>Detalhes

Uma implantação no Azure pode comunicar-se com os pontos de extremidade fora do Azure no espaço de endereços IP público.

Quando uma instância inicia o tráfego de saída para um destino com um endereço IP público, o Azure mapeia dinamicamente o endereço IP privado do recurso para um endereço IP público. 

Depois que esse mapeamento é criado, o tráfego de retorno para esse fluxo originado de saída atinge o endereço IP privado onde o fluxo foi originado. 

O Azure usa **SNAT (conversão de endereços de rede de origem)** para executar essa função.

### <a name="port-masquerading-snat-pat"></a><a name="pat"></a>Disfarce de porta SNAT (PAT)

| Protocolo (s) aplicável |
|------------------------|
| TCP </br> UDP          |

#### <a name="details"></a>Detalhes

Quando os IPs privados estão atrás de um único endereço IP público, o Azure usa a **Pat (conversão de endereços de porta)** para ocultar os endereços IP privados. 

As portas efêmeras são usadas para PAT e são [pré-alocadas](#preallocatedports) com base no tamanho do pool. 

Quando um balanceador de carga público está associado a VMs sem IPs públicos, cada fonte de conexão de saída é reescrita. 

A origem é reescrita do endereço IP privado da rede virtual para o endereço IP público de front-end do balanceador de carga. 

No espaço de endereço IP público, a cinco tupla do fluxo deve ser exclusiva:

* Endereço IP de origem
* Porta de origem
* Protocolo de transporte IP
* Endereço IP de destino
* Porta de destino 

A representação de porta SNAT pode ser usada com os protocolos TCP ou UDP. As portas SNAT são usadas após a regravação do endereço IP de origem particular porque vários fluxos são originados de um único endereço IP público. O algoritmo SNAT de mascaramento de porta fornece portas SNAT de forma diferente para UDP versus TCP.

### <a name="snat-ports-tcp"></a>Portas SNAT (TCP)

| Protocolo (s) aplicável |
|------------------------|
| TCP |

#### <a name="details"></a>Detalhes

As portas SNAT são portas efêmeras disponíveis para um endereço de origem IP público. Uma porta SNAT é consumida por fluxo para uma porta e endereço IP de destino único. 

Para vários fluxos TCP para o mesmo endereço IP, porta e protocolo de destino, cada fluxo TCP consome uma única porta SNAT. 

Esse consumo garante que os fluxos sejam exclusivos quando se originam do mesmo endereço IP público e viajam para o:

* Mesmo endereço IP de destino
* Porta
* Protocolo 

Vários fluxos compartilham uma única porta SNAT. 

O endereço IP de destino, porta e protocolo tornam os fluxos exclusivos sem a necessidade de portas de origem adicionais para distinguir fluxos no espaço de endereço IP público.


### <a name="snat-ports-udp"></a>Portas SNAT (UDP)

| Protocolo (s) aplicável |
|------------------------|
| UDP |

#### <a name="details"></a>Detalhes

Portas SNAT UDP são gerenciadas por um algoritmo diferente do que as portas SNAT TCP.  O balanceador de carga usa um algoritmo denominado NAT de cone com restrição de porta para UDP.

Uma porta SNAT é consumida para qualquer endereço IP de destino e porta para cada fluxo.


### <a name="exhaustion"></a><a name="exhaustion"></a>Esgotamento

| Protocolo (s) aplicável |
|------------------------|
| N/D |

#### <a name="details"></a>Detalhes

Quando os recursos da porta SNAT estão esgotados, os fluxos de saída falham até que os fluxos existentes liberem as portas SNAT. O balanceador de carga recupera portas SNAT quando o fluxo é fechado.

Um [tempo limite de ociosidade](../load-balancer/troubleshoot-outbound-connection.md#idletimeout) de quatro minutos é usado pelo balanceador de carga para recuperar portas SNAT.

As portas SNAT UDP geralmente esgotam muito mais rápido que as portas SNAT TCP devido à diferença no algoritmo usado. Teste de design e dimensionamento devido a essa diferença.

### <a name="snat-port-release-behavior-tcp"></a>Comportamento de liberação da porta SNAT (TCP)

| Protocolo (s) aplicável |
|------------------------|
| TCP |

#### <a name="details"></a>Detalhes

Quando um servidor ou cliente envia um FINACK, uma porta SNAT será liberada após 240 segundos. No caso de um RST ser visto, uma porta SNAT será liberada após 15 segundos. Se o tempo limite de ociosidade tiver sido atingido, a porta será liberada.

### <a name="snat-port-release-behavior-udp"></a>Comportamento de liberação de porta SNAT (UDP)

| Protocolo (s) aplicável |
|------------------------|
| TCP |

#### <a name="details"></a>Detalhes

Se o tempo limite de ociosidade tiver sido atingido, a porta será liberada.

### <a name="snat-port-reuse"></a>Reutilização de porta SNAT

| Protocolo (s) aplicável |
|------------------------|
| TCP </br> UDP |

#### <a name="details"></a>Detalhes

Depois que uma porta for liberada, a porta estará disponível para reutilização. As portas SNAT são uma sequência da mais baixa para a mais alta disponível para um determinado cenário e a primeira porta SNAT disponível é usada para novas conexões.

## <a name="limitations"></a>Limitações

- O número máximo de portas efêmeras utilizáveis por endereço IP de front-end é 64.000.
- O intervalo do tempo limite de ociosidade de saída configurável é de 4 a 120 minutos (240 a 7.200 segundos).
- O balanceador de carga não dá suporte a ICMP para NAT de saída.
- As regras de saída só podem ser aplicadas à configuração de IP primário de uma NIC.  Você não pode criar uma regra de saída para o IP secundário de uma VM ou NVA. Há suporte para várias NICs.
- As funções de Web Worker sem uma rede virtual e outros serviços de plataforma da Microsoft podem ser acessíveis quando um Load Balancer Standard interno é usado. Essa acessibilidade é devido a um efeito colateral de como os serviços de VNet e outros serviços de plataforma operam. Não confie nesse efeito colateral, pois o respectivo próprio serviço ou a plataforma subjacente poderá mudar sem aviso prévio. Sempre suponha que você precise criar uma conectividade de saída explicitamente, se desejar, ao usar apenas um balanceador de carga padrão interno. O cenário 3 descrito neste artigo não está disponível.

## <a name="next-steps"></a>Próximas etapas

Se você estiver tendo problemas com a conectividade de saída por meio de um Azure Load Balancer, consulte o [Guia de solução de problemas para conexões de saída](../load-balancer/troubleshoot-outbound-connection.md).

- Saiba mais sobre o [balanceador de carga padrão](load-balancer-standard-overview.md).
- Veja nossas [perguntas frequentes sobre Azure Load Balancer](load-balancer-faqs.md).
- Saiba mais sobre [as regras de saída](load-balancer-outbound-rules-overview.md) para o Load Balancer público padrão.

