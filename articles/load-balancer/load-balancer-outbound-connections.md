---
title: Conexões de saída no Azure
titleSuffix: Azure Load Balancer
description: Este artigo explica como o Azure permite que as VMs comuniquem-se com serviços de Internet públicos.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.custom: seodec18
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/24/2020
ms.author: allensu
ms.openlocfilehash: b22ce64e7058f093a102aebec8b00842c8a41cb5
ms.sourcegitcommit: cec9676ec235ff798d2a5cad6ee45f98a421837b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85849403"
---
# <a name="outbound-connections-in-azure"></a>Conexões de saída no Azure

O Azure Load Balancer fornece conectividade de saída por meio de mecanismos diferentes. Este artigo descreve os cenários e como gerenciá-los. Se você estiver tendo problemas com a conectividade de saída por meio de um Azure Load Balancer, consulte o [Guia de solução de problemas para conexões de saída](../load-balancer/troubleshoot-outbound-connection.md).

>[!NOTE]
>Este artigo aborda as implantações do Resource Manager. A Microsoft recomenda o Resource Manager para cargas de trabalho de produção.

## <a name="terminology"></a>Terminologia

| Termo | Protocolo (s) aplicável | Detalhes|
| ---------|---------| -------|
| Conversão de endereços de rede de origem (SNAT) | TCP, UDP | Uma implantação no Azure pode comunicar-se com os pontos de extremidade fora do Azure no espaço de endereços IP público. Quando uma instância inicia um fluxo de saída para um destino no espaço de endereços IP público, o Azure mapeia dinamicamente o endereço IP privado para um endereço IP público. Depois que esse mapeamento é criado, o tráfego de retorno para esse fluxo originado de saída também pode alcançar o endereço IP privado onde o fluxo foi originado. O Azure usa **SNAT (conversão de endereços de rede de origem)** para executar essa função.|
| Disfarce de porta SNAT (PAT)| TCP, UDP |  Quando vários endereços IP privados estão sendo mascarados por trás de um único endereço IP público, o Azure usa a **Pat (conversão de endereços de porta)** para mascarar/ocultar endereços IP privados. As portas efêmeras são usadas para PAT e são [pré-alocadas](#preallocatedports) com base no tamanho do pool. Quando um recurso público do Load Balancer estiver associado a instâncias de VM, que não têm endereços IP públicos dedicados, cada fonte de conexão de saída será reescrita. A origem é regravada do espaço do endereço IP privado da rede virtual para o endereço IP Público de front-end do balanceador de carga. No espaço de endereço IP público, as 5 tuplas do fluxo (endereço IP de origem, porta de origem, protocolo de transporte IP, endereço IP de destino, porta de destino) devem ser exclusivas. O SNAT simulado de porta pode ser usado com protocolos TCP ou IP UDP. As portas efêmeras (portas SNAT) são usadas para conseguir isso após a regravação do endereço IP de origem privada, já que vários fluxos originam-se de um único endereço IP público. O algoritmo de SNAT de disfarce de porta aloca as portas SNAT de forma diferente para UDP e TCP.|
| Portas SNAT| TCP | As portas SNAT são portas efêmeras disponíveis para um determinado endereço de origem IP público. Uma porta SNAT é consumida por fluxo para um único endereço IP e porta de destino. Para vários fluxos TCP para o mesmo endereço IP, porta e protocolo de destino, cada fluxo TCP consome uma única porta SNAT. Isso garante que os fluxos sejam exclusivos quando são originados do mesmo endereço IP público e sigam para o mesmo endereço IP, porta e protocolo de destino. Vários fluxos, cada um para um endereço IP, porta e protocolo de destino diferente, compartilham uma única porta SNAT. O endereço IP de destino, porta e protocolo tornam os fluxos exclusivos sem a necessidade de portas de origem adicionais para distinguir fluxos no espaço de endereço IP público.|
|Portas SNAT | UDP | Portas SNAT UDP são gerenciadas por um algoritmo diferente do que as portas SNAT TCP.  O Load Balancer usa um algoritmo conhecido como "NAT de cone com restrição de porta" para UDP.  Uma porta SNAT é consumida para cada fluxo, independentemente do endereço IP e da porta de destino.|
| Esgotamento | - | Quando os recursos da porta SNAT estão esgotados, os fluxos de saída falham até que os fluxos existentes liberem as portas SNAT. O balanceador de carga recupera as portas SNAT quando o fluxo fecha e usa um [tempo limite de ociosidade de 4 minutos](../load-balancer/troubleshoot-outbound-connection.md#idletimeout) para recuperar as portas SNAT dos fluxos ociosos. Portas SNAT UDP geralmente esgotam-se muito mais rapidamente que as portas SNAT TCP devido à diferença no algoritmo usado. Você deve projetar e realizar os testes de escala com essa diferença em mente.|
| Comportamento de liberação da porta SNAT | TCP | Se o servidor ou o cliente enviar FINACK, a porta SNAT será liberada após 240 segundos. Se um RST for visto, a porta SNAT será liberada após 15 segundos. Se o tempo limite de ociosidade tiver sido atingido, a porta será liberada.|
| Comportamento de liberação da porta SNAT | UDP |Se o tempo limite de ociosidade tiver sido atingido, a porta será liberada.|
| Reutilização de porta SNAT | TCP, UDP | Depois que uma porta for liberada, a porta estará disponível para reutilização conforme necessário.  Você pode considerar as portas SNAT como uma sequência da mais baixa para a mais alta disponível para um determinado cenário, em que a primeira porta SNAT disponível é usada para novas conexões.|

### <a name="port-allocation-algorithm"></a><a name="preallocatedports"></a>Algoritmo de alocação de porta

O Azure usa um algoritmo para determinar o número de portas SNAT previamente alocadas disponíveis com base no tamanho do pool de back-end ao usar PAT. Para cada endereço IP público associado a um balanceador de carga, há 64.000 portas disponíveis como portas SNAT para cada protocolo de transporte IP. O mesmo número de portas SNAT é pré-alocado respectivamente para TCP e UDP e independentemente consumido por protocolo de transporte IP.  No entanto, o uso da porta SNAT é diferente dependendo de o fluxo ser UDP ou TCP. Quando os fluxos de saída são criados, essas portas são consumidas dinamicamente (até o limite prealocado) e liberadas quando o fluxo fecha ou os [tempos limite de ociosidade](../load-balancer/troubleshoot-outbound-connection.md#idletimeout) acontecem. As portas são consumidas apenas se for necessário fazer fluxos exclusivos.

#### <a name="default-snat-ports-allocated"></a><a name="snatporttable"></a>Portas de SNAT padrão alocadas

A tabela a seguir mostra as pré-alocações de porta SNAT para níveis de tamanhos de pool de back-end:

| Tamanho do pool (instâncias VM) | Portas SNAT pré-alocadas por configuração de IP |
| --- | --- |
| 1-50 | 1\.024 |
| 51-100 | 512 |
| 101-200 | 256 |
| 201-400 | 128 |
| 401-800 | 64 |
| 801-1,000 | 32 |

A alteração do tamanho do pool de back-end pode afetar alguns dos fluxos estabelecidos:

- Se o tamanho do pool de back-end aumenta e faz transições para a próxima camada, metade das suas portas SNAT pré-alocadas é recuperada durante a transição para a próxima camada maior de pool de back-end. Os fluxos que estão associados a uma porta SNAT recuperada atingirão limite de tempo e deverão ser restabelecidos. Se um novo fluxo for tentado, o fluxo terá êxito imediato, desde que as portas pré-alocadas estejam disponíveis.
- Se o tamanho do pool de back-end diminuir e fizer transição para uma camada mais baixa, o número de portas SNAT disponíveis aumentará. Nesse caso, as portas SNAT alocadas existentes e seus respectivos fluxos não são afetados.

## <a name="outbound-connections-scenario-overview"></a><a name="scenarios"></a>Visão geral do cenário de conexões de saída

| Cenário | Método | Protocolos IP | Descrição |
|  --- | --- | --- | --- |
|  1. VM com um endereço IP público (com ou sem Azure Load Balancer | SNAT, disfarce de porta não usado | TCP, UDP, ICMP, ESP | O Azure usa o IP público atribuído à configuração de IP da NIC da instância para todos os fluxos de saída. A instância possui todas as portas efêmeras disponíveis. Não importa se a VM tem balanceamento de carga ou não. Esse cenário tem precedência sobre os outros. Um IP público atribuído a uma VM é uma relação 1:1 (em vez de 1:muitos) e implementado como sem estado 1:1 NAT. |
| 2. Load Balancer público associado a uma VM (nenhum endereço IP público na VM/instância) | SNAT com PAT (disfarce de porta) usando front-ends do Load Balancer | TCP, UDP | Nesse cenário, o recurso de Load Balancer deve ser configurado com uma regra de balanceador de carga para criar um vínculo entre o front-end IP público com o pool de back-end. Se você não concluir essa configuração de regra, o comportamento será conforme descrito no cenário 3. Não é necessário que a regra tenha um ouvinte trabalhando no pool de back-end ou a investigação de integridade para ter êxito. Quando a VM cria um fluxo de saída, o Azure converte o endereço IP de origem privado do fluxo de saída para o endereço IP público do front-end de Load Balancer público via SNAT. As portas efêmeras do endereço IP público front-end do balanceador de carga são usadas para distinguir fluxos individuais originados pela VM. A SNAT usa dinamicamente [ortas efêmeras pré-alocadas](#preallocatedports) quando os fluxos de saída são criados. Neste contexto, as portas efêmeras usadas para SNAT são chamadas de portas SNAT. As portas SNAT são previamente alocadas conforme descrito na [tabela de portas de SNAT padrão alocadas](#snatporttable). |
| 3. VM (sem Load Balancer, nenhum endereço IP público) ou VM associada ao Load Balancer básico interno | SNAT com disfarce de porta (PAT) | TCP, UDP | Quando a VM cria um fluxo de saída, o Azure converte o endereço IP de origem particular do fluxo de saída para um endereço IP de origem pública. Esse endereço IP público **não é configurável**, não pode ser reservado e não conta no limite de recursos de IP público da assinatura. Se você reimplantar o conjunto de dimensionamento de VMs ou conjuntos de disponibilidade ou máquinas virtuais, esse endereço IP público será liberado e um novo endereço IP público será solicitado. Não use esse cenário para endereços IP de lista de permissões. Em vez disso, use o cenário 1 ou 2 onde declarar explicitamente o comportamento de saída. As portas SNAT são prealocadas conforme descrito na [tabela de portas de SNAT padrão alocadas](#snatporttable).

## <a name="outbound-rules"></a><a name="outboundrules"></a>Regras de saída

 Regras de saída simplificam a configuração da conversão de endereços de rede de saída do [Standard Load Balancer](load-balancer-standard-overview.md) público.  Você tem controle declarativo completo sobre a conectividade de saída para dimensionar e ajustar essa capacidade conforme suas necessidades específicas. Esta seção expande o cenário 2 (B) em descrito acima.

![Regras de saída do Load Balancer](media/load-balancer-outbound-rules-overview/load-balancer-outbound-rules.png)

Com as regras de saída, você pode usar Load Balancer para definir o NAT de saída do zero. Você também pode dimensionar e ajustar o comportamento do NAT de saída existente.

Regras de saída permitem que você controle:

- quais máquinas virtuais devem ser convertidas em quais endereços IP públicos.
- como portas SNAT de saída devem ser alocadas.
- para quais protocolos fornecer conversão de saída.
- qual duração usar para o tempo limite de ociosidade da conexão de saída (4-120 minutos).
- Se deseja enviar uma redefinição de TCP no tempo limite ocioso
- protocolos de transporte TCP e UDP com uma única regra

### <a name="outbound-rule-definition"></a>Definição de regra de saída

Como todas as regras de Load Balancer, as regras de saída seguem a mesma sintaxe familiar que o balanceamento de carga **frontend**e as regras de NAT de entrada: pool de  +  **parameters**  +  **back-end**de parâmetros de frontend Uma regra de saída configura o NAT de saída para _todas as máquinas virtuais identificadas pelo pool de back-end_ a serem convertidas no _front-end_.  Os _parâmetros_ fornecem um controle adicional refinado sobre o algoritmo NAT de saída.

### <a name="scale-outbound-nat-with-multiple-ip-addresses"></a><a name="scale"></a> Dimensionar NAT de saída com vários endereços IP

Cada endereço IP adicional fornecido por um front-end fornece portas efêmeras 64.000 adicionais para Load Balancer usarem como portas SNAT. Você pode usar vários endereços IP para planejar cenários de grande escala e pode usar regras de saída para atenuar [padrões propensos à exaustão de SNAT](troubleshoot-outbound-connection.md#snatexhaust).  

Você também pode usar um [prefixo IP público](https://aka.ms/lbpublicipprefix) diretamente com uma regra de saída.  Prefixo usando o IP público fornece para dimensionamento mais fácil e simplificada em uma lista de fluxos provenientes de sua implantação do Azure. Você pode configurar uma configuração de IP de front-end no recurso do Load Balancer para fazer referência a um prefixo de endereço IP público diretamente.  Isso dá ao Load Balancer controle exclusivo sobre o prefixo de IP público, e a regra de saída usará automaticamente todos os endereços IP públicos contidos dentro do prefixo de IP público para conexões de saída.  Cada um dos endereços IP dentro do prefixo IP público fornece uma 64.000 portas efêmeras adicionais por endereço IP para Load Balancer usar como portas SNAT.

### <a name="outbound-flow-idle-timeout-and-tcp-reset"></a><a name="idletimeout"></a>Tempo limite de ociosidade do fluxo de saída e redefinição de TCP

Regras de saída fornecem um parâmetro de configuração para controlar o tempo limite de ociosidade de fluxo de saída e combiná-lo com as necessidades do seu aplicativo. O padrão de tempos limite de ociosidade de saída é de 4 minutos. Você pode aprender a [Configurar tempos limite de ociosidade](load-balancer-tcp-idle-timeout.md#tcp-idle-timeout). O comportamento padrão do Load Balancer é remover o fluxo silenciosamente quando o tempo limite de ociosidade de saída é atingido.  Com o `enableTCPReset` parâmetro, você pode habilitar um comportamento de aplicativo mais previsível e controlar se deseja enviar a redefinição TCP bidirecional (TCP RST) no momento do tempo limite de ociosidade de saída. Examine a [redefinição de TCP no tempo limite ocioso](https://aka.ms/lbtcpreset) para obter detalhes, incluindo a disponibilidade da região.

### <a name="preventing-outbound-connectivity"></a><a name="preventoutbound"></a>Impedir conectividade de saída

As regras de balanceamento de carga fornecem programação automática de NAT de saída. No entanto, alguns cenários se beneficiam ou exigem que você desabilite a programação automática de NAT de saída pela regra de balanceamento de carga para permitir que você controle ou refine o comportamento.  
Você pode usar esse parâmetro de duas maneiras:

1. Supressão opcional do uso do endereço IP de entrada para SNAT de saída por meio da desabilitação de SNAT de saída para uma regra de balanceamento de carga
  
2. Ajuste os parâmetros de SNAT de saída de um endereço IP usado para entrada e saída simultaneamente.  A programação automática NAT de saída deve ser desabilitada para permitir que uma regra de saída assuma o controle.  Por exemplo, para alterar a alocação de porta SNAT de um endereço também usado para entrada, o `disableOutboundSnat` parâmetro deve ser definido como true.  Se você tentar usar uma regra de saída para redefinir os parâmetros de um endereço IP também usado para entrada e não tiver liberado a programação NAT de saída da regra de balanceamento de carga, a operação de configurar uma regra de saída falhará.

>[!IMPORTANT]
> Sua máquina virtual não terá conectividade de saída se você definir esse parâmetro como true e não tiver uma regra de saída para definir a conectividade de saída.  Algumas operações de sua VM ou do seu aplicativo podem depender de ter conectividade de saída disponível. Entenda as dependências do seu cenário e considere o impacto de fazer essa alteração.

Às vezes, não é desejável que uma VM tenha permissão para criar um fluxo de saída. Ou pode haver um requisito para gerenciar quais destinos podem ser alcançados com os fluxos de saída ou quais destinos podem começar os fluxos de entrada. Nesse caso, é possível usar os [grupos de segurança de rede](../virtual-network/security-overview.md) para gerenciar os destinos que a VM pode acessar. Você também pode usar NSGs para gerenciar qual destino público pode iniciar os fluxos de entrada.

Ao aplicar um NSG a uma VM com balanceamento de carga, atente-se às [marcas de serviço](../virtual-network/security-overview.md#service-tags) e às [regras de segurança padrão](../virtual-network/security-overview.md#default-security-rules). Certifique-se de que a VM possa receber solicitações de investigação de integridade do Azure Load Balancer.

Se um NSG bloquear solicitações de investigação de integridade da marcação padrão AZURE_LOADBALANCER, o teste de integridade da VM falhará e a VM será reduzida. O Balanceador de Carga interrompe o envio de novos fluxos para a VM.

## <a name="scenarios-with-outbound-rules"></a>Cenários com regras de saída

| # | Cenário| Detalhes|
|---|---|---|
| I |  Limpar conexões de saída para um conjunto específico de endereços IP públicos| Você pode usar uma regra de saída para limpar conexões de saída para que pareçam originarem-se de um conjunto específico de endereços IP públicos para facilitar cenários de colocação na lista de permissões.  Esse endereço IP público de origem pode ser o mesmo usado por uma regra de balanceamento de carga ou um conjunto diferente de endereços IP públicos do que os usados por uma regra de balanceamento de carga.  1. Crie um [prefixo IP público](https://aka.ms/lbpublicipprefix) (ou endereços IP públicos do prefixo IP público) 2. Crie um Standard Load Balancer público 3. Crie front-ends referenciando o prefixo IP público (ou endereços IP públicos) que você deseja usar 4. Reutilize um pool de back-end ou crie um pool de back-end e coloque as VMs em um pool de back-end do público Load Balancer 5. Configure uma regra de saída no Load Balancer público para programar o NAT de saída para essas VMs usando os front-ends. Se não desejar que a regra de balanceamento de carga seja usada para saída, precisará desabilitar SNAT de saída na regra de balanceamento de carga.
| II |  Modificar a alocação da porta SNAT| Você pode usar regras de saída para ajustar a [alocação automática da porta SNAT com base no tamanho do pool de back-end](load-balancer-outbound-connections.md#preallocatedports). Por exemplo, se você tiver duas máquinas virtuais compartilhando um único endereço IP público para NAT de saída, poderá aumentar o número de portas SNAT alocadas das 1.024 portas padrão se estiver enfrentando esgotamento de SNAT. Cada endereço IP público pode contribuir com até 64.000 portas efêmeras.  Se você configurar uma regra de saída com um único front-end de endereço IP público, poderá distribuir um total de 64.000 portas SNAT para VMs no pool de back-end.  Para duas VMs, um máximo de 32.000 portas SNAT pode ser alocado com uma regra de saída (2 x 32.000 = 64.000). Você pode usar regras de saída para ajustar as portas SNAT alocadas por padrão. Você aloca mais ou menos do que a alocação de porta SNAT padrão fornecida. Cada endereço IP público de todos os front-ends de uma regra de saída contribui para até 64.000 portas efêmeras para uso como portas SNAT.  O Load Balancer aloca portas SNAT em múltiplos de 8. Se você fornecer um valor não divisível por 8, a operação de configuração será rejeitada.  Se você tentar alocar mais portas SNAT do que estão disponíveis com base no número de endereços IP públicos, a operação de configuração será rejeitada.  Por exemplo, se você alocar 10.000 portas por VM e 7 VMs em um pool de back-end compartilharão um único endereço IP público, a configuração será rejeitada (7 x 10.000 portas SNAT > 64.000 portas SNAT).  Você pode adicionar mais endereços de IP ao front-end da regra de saída para habilitar o cenário. Você pode reverter para a [alocação de porta SNAT padrão com base no tamanho do pool de back-end](load-balancer-outbound-connections.md#preallocatedports) , especificando 0 para número de portas. Nesse caso, as primeiras 50 instâncias de VM receberão 1024 portas, 51-100 as instâncias de VM terão 512 e assim por diante, de acordo com a [tabela](#snatporttable).|
| III|  Habilitar apenas saída | Você pode usar um Standard Load Balancer público para fornecer NAT de saída a um grupo de VMs. Nesse cenário, você pode usar uma regra de saída sozinha, sem a necessidade de todas as regras adicionais.|
| IV | NAT de saída apenas para VMs (não de entrada) | Defina um Standard Load Balancer público, coloque as VMs no pool de back-end e configure uma regra de saída para programar NAT de saída e limpar as conexões de saída para originarem-se de um endereço IP público específico. Você também pode usar um prefixo IP público para simplificar a colocação da origem de conexões de saída em lista de permissões. 1. Crie um Standard Load Balancer público. 2. Crie um pool de back-end e coloque as VMs em um pool de back-end do Load Balancer público. 3. Configure uma regra de saída no Load Balancer público para programar NAT de saída para essas VMs.
| V| NAT de saída para cenários de Standard Load Balancer interno| Ao usar um Standard Load Balancer interno, a NAT de saída não estará disponível até que uma conectividade de saída já tenha sido declarada. Você pode definir a conectividade de saída usando uma regra de saída para criar conectividade de saída para VMs por trás de um Standard Load Balancer interno com estas etapas: 1. Crie um Standard Load Balancer público. 2. Crie um pool de back-end e coloque as VMs em um pool de back-end do Load Balancer público, além do Load Balancer interno. 3. Configure uma regra de saída no Load Balancer público para programar NAT de saída para essas VMs.|
| VI | Habilitar protocolos TCP e UDP para NAT de saída com um Standard Load Balancer público | Ao usar um Standard Load Balancer público, a programação de NAT de saída automática fornecida corresponde ao protocolo de transporte da regra de balanceamento de carga. 1. Desabilite a SNAT de saída na regra de balanceamento de carga. 2. Configure uma regra de saída no mesmo Load Balancer. 3. Reutilize o pool de back-end já usado por suas VMs. 4. Especifique "protocolo": "Todos" como parte da regra de saída. Quando são usadas apenas regras NAT de entrada, nenhuma NAT de saída é fornecida. 1. Coloque as VMs em um pool de back-end. 2. Defina uma ou mais configurações de IP de front-end com endereço (s) IP público ou prefixo de IP público 3. Configure uma regra de saída no mesmo Load Balancer. 4. Especifique "protocolo": "Todos" como parte da regra de saída |


## <a name="limitations"></a>Limitações

- O número máximo de portas efêmeras utilizáveis por endereço IP de front-end é 64.000.
- O intervalo do tempo limite de ociosidade de saída configurável é de 4 a 120 minutos (240 a 7.200 segundos).
- O Load Balancer não dá suporte a ICMP para NAT de saída.
- As regras de saída só podem ser aplicadas à configuração de IP primário de uma NIC.  Há suporte para várias NICs.
- Funções de trabalho sem uma rede virtual e outros serviços da plataforma Microsoft podem ser acessados quando apenas um Standard Load Balancer interno é usado devido a um efeito colateral de como os serviços pré-VNet e outros serviços da plataforma funcionam. Não confie neste efeito colateral como o próprio serviço em si ou a plataforma subjacente pode mudar sem aviso prévio. Você sempre deve supor que precisa criar conectividade de saída explicitamente se desejado ao usar apenas um Standard Load Balancer interno. O cenário 3 descrito neste artigo não está disponível.

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre o [Load Balancer Standard](load-balancer-standard-overview.md).
- Veja nossas [perguntas frequentes sobre Azure Load Balancer](load-balancer-faqs.md).
- Saiba mais sobre as [regras de saída](load-balancer-outbound-rules-overview.md) para o Load Balancer Standard público.
- Saiba mais sobre o [Azure Load Balancer](load-balancer-overview.md).
- Saiba mais sobre [grupos de segurança de rede](../virtual-network/security-overview.md).
- Saiba mais sobre alguns dos outros principais [recursos de rede](../networking/networking-overview.md) no Azure.
