---
title: Política de peering da Microsoft
titleSuffix: Azure
description: Política de peering da Microsoft
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: conceptual
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: a683ad71f5e80c91728262dc7bbabf36e9d68deb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75775219"
---
# <a name="peering-policy"></a>Política de emparelhamento
Os requisitos gerais da Microsoft de sua rede são explicados nas seções abaixo. Estes são aplicáveis tanto às solicitações de peering direct e exchange.

## <a name="technical-requirements"></a>Requisitos técnicos

* Uma rede totalmente redundante com capacidade suficiente para trocar tráfego sem congestionamento.
* O Peer terá um Número de Sistema Autônomo (ASN) publicamente routable.
* Tanto o IPv4 quanto o IPv6 são suportados e a Microsoft espera estabelecer sessões de ambos os tipos em cada local de peering.
* MD5 não é suportado.
* **Detalhes da ASN:**
    * A Microsoft gerencia o AS8075 juntamente com as seguintes ASNs: AS8068, AS8069, AS12076. Para obter uma lista completa de ASNs com peering AS8075, consulte O AS-SET MICROSOFT.
    * Todas as partes que pesquisam com a Microsoft concordam em não aceitar rotas a partir de AS12076 (Express Route) em qualquer circunstância, e devem filtrar o AS12076 em todos os pares.
* **Política de roteamento:**
    * Peer terá pelo menos um /24 publicamente routable.
    * A Microsoft substituirá os Discriminadores multi-saída recebidos (MED).
    * A Microsoft prefere receber tags de comunidade BGP de pares para indicar a originação da rota.
    * Espera-se que os pares registrem suas rotas em um banco de dados público do Registro de Roteamento da Internet (IRR), com o objetivo de filtrar, e farão esforços de boa fé para manter essas informações atualizadas.
    * Sugerimos que os pares definam um prefixo máximo de 1000 (IPv4) e 100 (IPv6) em sessões de peering com a Microsoft.
    * A menos que seja especificamente acordado de antemão, espera-se que os pares anunciem rotas consistentes em todos os locais onde eles fazem par com a Microsoft.
    * Em geral, as sessões de peering com AS8075 anunciarão todas as rotas AS-MICROSOFT. Asinterconectações AS8075 na África e ásia podem ser limitadas a rotas relevantes para a respectiva região.
    * Nenhuma das partes estabelecerá uma rota estática, uma rota de último recurso, ou de outra forma enviará tráfego para a outra parte para uma rota não anunciada via BGP.
    * Espera-se que os pares sigam os padrões do setor [de MANRS](https://www.manrs.org/) para a segurança de rotas.

## <a name="operational-requirements"></a>Requisitos operacionais
* Um Centro de Operações de Rede 24 horas por dia, 7 dias por semana , capaz de auxiliar na resolução de todos os problemas técnicos e de desempenho, violações de segurança, ataques de negação de serviço ou qualquer outro abuso originário do peer ou de seus clientes.
* Espera-se que os pares tenham um perfil completo e atualizado no [PeeringDB,](https://www.peeringdb.com) incluindo um e-mail NOC 24 x 7 do domínio corporativo e número de telefone. Usamos essas informações para validar os detalhes do peer, como informações de NOC, informações de contato técnico e sua presença nas instalações de peering etc. Contas pessoais do Yahoo, Gmail e hotmail não são aceitas.

## <a name="physical-connection-requirements"></a>Requisitos de conexão física
* Os locais onde você pode se conectar com a Microsoft para peering direto ou peering do Exchange estão listados no [PeeringDB](https://www.peeringdb.com/net/694)
* **Peering de troca:**
    * A interconexão deve ser por fibra de modo único usando a óptica apropriada de 10Gbps.
    * Espera-se que os pares atualizem suas portas quando o pico de utilização exceder 50%.
* **Peering direto:**
    * A interconexão deve ser por fibra de modo único usando a óptica apropriada de 10Gbps ou 100Gbps.
    * A Microsoft só estabelecerá peering direto com provedores de ISP ou Serviços de Rede.
    * Espera-se que os pares atualizem suas portas quando a utilização do pico exceder 50% e mantenham capacidade diversificada em cada metrô, seja dentro de um único local ou em vários locais de um metrô.
    * Cada peering direct consiste em duas conexões com dois roteadores de borda da Microsoft a partir dos roteadores do Peer localizados na borda do Peer. A Microsoft requer duas sessões de BGP nessas conexões. O peer pode optar por não implantar dispositivos redundantes no final.

## <a name="traffic-requirements"></a>Requisitos de tráfego
* Peers over Exchange peering deve ter no mínimo 200Mb de tráfego e menos de 2Gb.  Para tráfego superior a 2Gb, o peering direto deve ser revisto.
* Para peering direto, o tráfego da sua rede para a Microsoft deve atender abaixo do requisito mínimo.

    | Localização geográfica                      | Tráfego mínimo para a Microsoft   |
    | :----------------------- |:-------------------------------|
    | África                   | 500 Mbps                       |
    | APAC (exceto Índia)      |   2 Gbps                       |
    | APAC (somente na Índia)        | 500 Mbps                       |
    | Europa                   |   2 Gbps                       |
    | Latam                    |   2 Gbps                       |
    | Oriente Médio              | 500 Mbps                       |
    | NA                       |   2 Gbps                       |

* **Diversidade:**
    * Na NA, Europa, APAC e LATAM, interconectam-se em pelo menos três locais geograficamente diversos, se for viável e mantêm capacidade diversificada para permitir que o tráfego falhe dentro de cada metrô.
    * Na África, Oriente Médio e Índia, interconecte-se em tantos locais diversos quanto possível. Deve manter capacidade diversificada suficiente para garantir que o tráfego permaneça na região.

## <a name="next-steps"></a>Próximas etapas

* Para saber sobre os passos para configurar o peering direto com a Microsoft, siga [o passo a passo do Direct .](walkthrough-direct-all.md)
* Para saber sobre os passos para configurar o peering do Exchange com a Microsoft, siga [o passo a passo do Exchange](walkthrough-exchange-all.md).