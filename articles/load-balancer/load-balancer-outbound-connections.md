---
title: Azure Load Balancer de proxy de saída
description: Descreve como Azure Load Balancer é usado como um proxy para conectividade de Internet de saída
services: load-balancer
author: asudbring
ms.service: load-balancer
ms.topic: conceptual
ms.custom: contperfq1
ms.date: 10/13/2020
ms.author: allensu
ms.openlocfilehash: 422f8106ac52c85f0680d54e420d0f1b4d326910
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/14/2020
ms.locfileid: "92017685"
---
# <a name="outbound-proxy-azure-load-balancer"></a>Azure Load Balancer de proxy de saída

Um balanceador de carga do Azure pode ser usado como um proxy para a conectividade de Internet de saída. O balanceador de carga fornece a conectividade de saída para as instâncias de back-end. 

Essa configuração usa a **conversão de endereços de rede (SNAT) de origem**. O SNAT reescreve o endereço IP do back-end para o endereço IP público do balanceador de carga. 

O SNAT permite a **representação de IP** da instância de back-end. Essa representação impede que fontes externas tenham um endereço direto para as instâncias de back-end. 

O compartilhamento de um endereço IP entre instâncias de back-end reduz o custo de IPs públicos estáticos e dá suporte a cenários como a simplificação de listas de permissões de IP com tráfego de IPs públicos conhecidos. 

## <a name="sharing-ports-across-resources"></a><a name ="snat"></a> Compartilhando portas entre recursos

Se os recursos de back-end de um balanceador de carga não tiverem endereços ILPIP (IP público em nível de instância), eles estabelecerão a conectividade de saída por meio do IP de front-end do balanceador de carga público.

As portas são usadas para gerar identificadores exclusivos usados para manter fluxos distintos. A Internet usa uma cinco tuplas para fornecer essa distinção.

A cinco tuplas consiste em:

* IP de destino
* Porta de destino
* IP de origem
* Porta de origem e protocolo para fornecer essa distinção.

Se uma porta for usada para conexões de entrada, ela terá um **ouvinte** para solicitações de conexão de entrada nessa porta e não poderá ser usada para conexões de saída. 

Para estabelecer uma conexão de saída, uma **porta efêmera** deve ser usada para fornecer o destino com uma porta na qual se comunicará e manterá um fluxo de tráfego distinto. 

Cada endereço IP tem 65.535 portas. As primeiras 1024 portas são reservadas como **portas do sistema**. Cada porta pode ser usada para conexões de entrada ou de saída para TCP e UDP. 

Das portas restantes, o Azure fornece 64.000 para uso como **portas efêmeras**. Quando um endereço IP é adicionado como uma configuração de IP de front-end, essas portas efêmeras podem ser usadas para SNAT.

Por meio de regras de saída, essas portas SNAT podem ser distribuídas para instâncias de back-end para permitir que elas compartilhem os IPs públicos do balanceador de carga para conexões de saída.

A rede no host para cada instância de back-end fará SNAT para pacotes que fazem parte de uma conexão de saída. O host reescreve o IP de origem para um dos IPs públicos. O host reescreve a porta de origem de cada pacote de saída para uma das portas SNAT.

## <a name="exhausting-ports"></a><a name="scenarios"></a> Portas esgotadas

Todas as conexões com o mesmo IP de destino e porta de destino usarão uma porta SNAT. Essa conexão mantém um **fluxo de tráfego** distinto da instância de back-end ou do **cliente** para um **servidor**. Esse processo fornece ao servidor uma porta distinta na qual tratar o tráfego. Sem esse processo, o computador cliente não está ciente do fluxo do qual um pacote faz parte.

Imagine ter vários navegadores indo para https://www.microsoft.com :

* IP de destino = 23.53.254.142
* Porta de destino = 443
* Protocolo = TCP

Sem portas de destino diferentes para o tráfego de retorno (a porta SNAT usada para estabelecer a conexão), o cliente não terá como separar um resultado de consulta de outro.

As conexões de saída podem ser intermitentes. Uma instância de back-end pode ser alocada para portas insuficientes. Sem a **reutilização de conexão** habilitada, o risco de **esgotamento da porta** SNAT é aumentado.

Novas conexões de saída para um IP de destino falharão quando ocorrer esgotamento de porta. As conexões terão sucesso quando uma porta for disponibilizada. Esse esgotamento ocorre quando as portas 64.000 de um endereço IP são espalhadas de modo dinâmico em várias instâncias de back-end. Para obter orientação sobre a mitigação do esgotamento de porta SNAT, consulte o [Guia de solução de problemas](https://docs.microsoft.com/azure/load-balancer/troubleshoot-outbound-connection).  

Para conexões TCP, o balanceador de carga usará uma única porta SNAT para cada IP de destino e porta. Esse Multiuse permite várias conexões com o mesmo IP de destino com a mesma porta SNAT. Esse Multiuse será limitado se a conexão não for para portas de destino diferentes.

Para conexões UDP, o balanceador de carga usa um algoritmo **NAT de cone com restrição de porta** , que consome uma porta SNAT por IP de destino, seja qual for a porta de destino. 

Uma porta é reutilizada para um número ilimitado de conexões. A porta só será reutilizada se o IP ou a porta de destino for diferente.

## <a name="port-allocation"></a><a name="preallocatedports"></a> Alocação de porta

Cada IP público atribuído como um IP de front-end do balanceador de carga recebe 64.000 portas SNAT para seus membros do pool de back-end. Portas não podem ser compartilhadas com membros do pool de back-end. Um intervalo de portas SNAT só pode ser usado por uma única instância de back-end para garantir que os pacotes de retorno sejam roteados corretamente. 

É recomendável usar uma regra de saída explícita para configurar a alocação de porta SNAT. Essa regra maximizará o número de portas SNAT que cada instância de back-end disponibilizou para conexões de saída. 

Se você usar a alocação automática de SNAT de saída por meio de uma regra de balanceamento de carga, a tabela de alocação definirá a alocação de porta.

A tabela a seguir <a name="snatporttable"></a> mostra as prealocações de porta SNAT para camadas de tamanhos de pool de back-end:

| Tamanho do pool (instâncias VM) | Portas SNAT pré-alocadas por configuração de IP |
| --- | --- |
| 1-50 | 1\.024 |
| 51-100 | 512 |
| 101-200 | 256 |
| 201-400 | 128 |
| 401-800 | 64 |
| 801-1,000 | 32 | 

>[!NOTE]
> Se você tiver um pool de back-end com um tamanho máximo de 6, cada instância poderá ter 64000/10 = 6.400 portas se você definir uma regra de saída explícita. De acordo com a tabela acima, cada uma terá apenas 1.024 se você escolher a alocação automática.

## <a name="outbound-rules-and-virtual-network-nat"></a><a name="outboundrules"></a> Regras de saída e NAT de rede virtual

Azure Load Balancer regras de saída e NAT de rede virtual são opções disponíveis para saída de uma rede virtual.

Para obter mais informações sobre regras de saída, consulte [regras de saída](outbound-rules.md).

Para obter mais informações sobre o NAT de rede virtual do Azure, consulte [o que é NAT de rede virtual do Azure](../virtual-network/nat-overview.md).

## <a name="constraints"></a>Restrições

*   As portas serão liberadas após 15 segundos se um **TCP RST** for recebido ou enviado
*   As portas serão liberadas após 240 segundos se um **FINACK** for recebido ou enviado
*   Quando uma conexão estiver ociosa sem que nenhum novo pacote seja enviado, as portas serão liberadas após 4 a 120 minutos.
  * Esse limite pode ser configurado por meio de regras de saída.
*   Cada endereço IP fornece 64.000 portas que podem ser usadas para SNAT.
*   Cada porta pode ser usada para conexões TCP e UDP a um endereço IP de destino
  * Uma porta TCP SNAT é necessária se a porta de destino for exclusiva ou não. Para cada conexão UDP com um IP de destino, é usada uma porta TCP SNAT.
  * Uma porta TCP SNAT pode ser usada para várias conexões com o mesmo IP de destino, desde que as portas de destino sejam diferentes.
*   O esgotamento de SNAT ocorre quando uma instância de back-end é executada de determinadas portas SNAT. Um balanceador de carga ainda pode ter portas SNAT não utilizadas. Se as portas SNAT usadas de uma instância de back-end excederem suas portas SNAT determinadas, não será possível estabelecer novas conexões de saída.

## <a name="next-steps"></a>Próximas etapas

*   [Solucionar problemas de falhas de conexão de saída devido ao esgotamento de SNAT](https://docs.microsoft.com/azure/load-balancer/troubleshoot-outbound-connection)
*   [Examine as métricas SNAT](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-diagnostics#how-do-i-check-my-snat-port-usage-and-allocation) e familiarize-se com a maneira correta de filtrá-las, dividi-las e exibi-las.

