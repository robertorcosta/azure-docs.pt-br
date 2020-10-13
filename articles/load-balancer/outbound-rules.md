---
title: Regras de saída Azure Load Balancer
description: Este artigo explica como configurar as regras de saída para controlar a egresso do tráfego de Internet com o Azure Load Balancer.
services: load-balancer
author: asudbring
ms.service: load-balancer
ms.topic: conceptual
ms.custom: contperfq1
ms.date: 10/13/2020
ms.author: allensu
ms.openlocfilehash: 51810876e3636b7023ce9c9318a071636bb00c4c
ms.sourcegitcommit: 090ea6e8811663941827d1104b4593e29774fa19
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/13/2020
ms.locfileid: "92002621"
---
# <a name="outbound-rules-azure-load-balancer"></a><a name="outboundrules"></a>Regras de saída Azure Load Balancer

As regras de saída permitem configurar o SNAT de saída do balanceador de carga padrão público (conversão de endereços de rede de origem). Essa configuração permite que você use os IPs públicos do balanceador de carga como um proxy.

Essa configuração permite:

* Representação de IP
* Simplificando suas listas de permissões.
* Reduz o número de recursos IP públicos para implantação.

Com as regras de saída, você tem total controle declarativo sobre a conectividade de Internet de saída. As regras de saída permitem que você dimensione e ajuste essa capacidade para suas necessidades específicas. 

As regras de saída serão seguidas somente se a VM de back-end não tiver um endereço IP público em nível de instância (ILPIP).

![Regras de saída do Load Balancer](media/load-balancer-outbound-rules-overview/load-balancer-outbound-rules.png)

Com as regras de saída, você pode definir explicitamente o comportamento de **SNAT** de saída.

Regras de saída permitem que você controle:

* **Quais máquinas virtuais são convertidas para quais endereços IP públicos.**
     * Duas regras o pool de back-end A usa o endereço IP A e B, o pool de back-end B usa o endereço IP C e D.
* **Como as portas SNAT de saída são dadas.**
     * O pool de back-end B é o único pool que faz conexões de saída, fornece todas as portas SNAT ao pool de back-end B e nenhuma para o pool de back-end
* **Para quais protocolos fornecer tradução de saída.**
     * O pool de back-end B precisa de portas UDP para saída. O pool de back-end A precisa de TCP. Forneça as portas TCP para as portas UDP e a B.
* **Qual duração usar para tempo limite de ociosidade de conexão de saída (4-120 minutos).**
     * Se houver conexões de longa execução com keepalives, Reserve portas ociosas para conexões de longa execução por até 120 minutos. Suponha que conexões obsoletas sejam abandonadas e libere portas em 4 minutos para conexões novas 
* **Se deve ser enviada uma redefinição de TCP no tempo limite ocioso.**
     * ao atingir o tempo limite das conexões ociosas, enviamos um TCP RST para o cliente e para o servidor para que eles saibam que o fluxo é abandonado?

## <a name="outbound-rule-definition"></a>Definição de regra de saída

As regras de saída seguem a mesma sintaxe familiar que o balanceamento de carga e as **frontend**regras NAT de entrada: pool de  +  **parameters**  +  **back-end**de parâmetros de frontend. 

Uma regra de saída configura o NAT de saída para _todas as máquinas virtuais identificadas pelo pool de back-end_ a serem convertidas no _front-end_.  

Os _parâmetros_ fornecem um controle adicional refinado sobre o algoritmo NAT de saída.

## <a name="scale-outbound-nat-with-multiple-ip-addresses"></a><a name="scale"></a> Dimensionar NAT de saída com vários endereços IP

Cada endereço IP adicional fornecido por um front-end fornece portas efêmeras 64.000 adicionais para o balanceador de carga usar como portas SNAT. 

Use vários endereços IP para planejar cenários em larga escala. Use regras de saída para atenuar o [esgotamento de SNAT](troubleshoot-outbound-connection.md#snatexhaust). 

Você também pode usar um [prefixo IP público](https://aka.ms/lbpublicipprefix) diretamente com uma regra de saída. 

Um prefixo IP público aumenta o dimensionamento da sua implantação. O prefixo pode ser adicionado à lista de permissões de fluxos provenientes de seus recursos do Azure. Você pode configurar uma configuração de IP de front-end dentro do balanceador de carga para fazer referência a um prefixo de endereço IP público.  

O balanceador de carga tem controle sobre o prefixo IP público. A regra de saída usará automaticamente todos os endereços IP públicos contidos no prefixo IP público para conexões de saída. 

Cada um dos endereços IP dentro do prefixo IP público fornece uma 64.000 portas efêmeras adicionais por endereço IP para o balanceador de carga usar como portas SNAT.

## <a name="outbound-flow-idle-timeout-and-tcp-reset"></a><a name="idletimeout"></a> Tempo limite de ociosidade do fluxo de saída e redefinição de TCP

Regras de saída fornecem um parâmetro de configuração para controlar o tempo limite de ociosidade de fluxo de saída e combiná-lo com as necessidades do seu aplicativo. O padrão de tempos limite de ociosidade de saída é de 4 minutos. Para obter mais informações, consulte [Configurar tempos limite de ociosidade](load-balancer-tcp-idle-timeout.md). 

O comportamento padrão do balanceador de carga é descartar o fluxo silenciosamente quando o tempo limite de ociosidade de saída for atingido. O `enableTCPReset` parâmetro habilita um comportamento e controle previsíveis do aplicativo. O parâmetro determina se a redefinição TCP bidirecional (TCP RST) deve ser enviada no tempo limite do tempo limite de ociosidade de saída. 

Examine a [redefinição de TCP no tempo limite ocioso](https://aka.ms/lbtcpreset) para obter detalhes, incluindo a disponibilidade da região.

## <a name="securing-and-controlling-outbound-connectivity-explicitly"></a><a name="preventoutbound"></a>Proteger e controlar a conectividade de saída explicitamente

As regras de balanceamento de carga fornecem programação automática de NAT de saída. Alguns cenários se beneficiam ou exigem que você desabilite a programação automática do NAT de saída pela regra de balanceamento de carga. A desabilitação por meio da regra permite controlar ou refinar o comportamento.  

Você pode usar esse parâmetro de duas maneiras:

1. Prevenção do endereço IP de entrada para SNAT de saída. Desabilite o SNAT de saída na regra de balanceamento de carga.
  
2. Ajuste os parâmetros de **SNAT** de saída de um endereço IP usado para entrada e saída simultaneamente. O NAT de saída automático deve ser desabilitado para permitir que uma regra de saída assuma o controle. Para alterar a alocação de porta SNAT de um endereço também usado para entrada, o `disableOutboundSnat` parâmetro deve ser definido como true. 

A operação para configurar uma regra de saída falhará se você tentar redefinir um endereço IP que é usado para entrada.  Desabilite primeiro o NAT de saída da regra de balanceamento de carga.

>[!IMPORTANT]
> Sua máquina virtual não terá conectividade de saída se você definir esse parâmetro como true e não tiver uma regra de saída para definir a conectividade de saída.  Algumas operações de sua VM ou do seu aplicativo podem depender de ter conectividade de saída disponível. Entenda as dependências do seu cenário e considere o impacto de fazer essa alteração.

Às vezes, não é desejável que uma VM crie um fluxo de saída. Pode haver um requisito para gerenciar quais destinos recebem fluxos de saída ou quais destinos iniciam fluxos de entrada. Use [grupos de segurança de rede](../virtual-network/security-overview.md) para gerenciar os destinos que a VM atinge. Use NSGs para gerenciar quais destinos públicos iniciam fluxos de entrada.

Ao aplicar um NSG a uma VM com balanceamento de carga, atente-se às [marcas de serviço](../virtual-network/security-overview.md#service-tags) e às [regras de segurança padrão](../virtual-network/security-overview.md#default-security-rules). 

Verifique se a VM pode receber solicitações de investigação de integridade de Azure Load Balancer.

Se um NSG bloquear as solicitações de investigação de integridade da AZURE_LOADBALANCER marca padrão, a investigação de integridade da VM falhará e a VM será marcada como indisponível. O balanceador de carga para de enviar novos fluxos para essa VM.

## <a name="limitations"></a>Limitações

- O número máximo de portas efêmeras utilizáveis por endereço IP de front-end é 64.000.
- O intervalo do tempo limite de ociosidade de saída configurável é de 4 a 120 minutos (240 a 7.200 segundos).
- O balanceador de carga não dá suporte a ICMP para NAT de saída.
- As regras de saída só podem ser aplicadas à configuração de IP primário de uma NIC.  Você não pode criar uma regra de saída para o IP secundário de uma VM ou NVA. Há suporte para várias NICs.
- Todas as máquinas virtuais em um **conjunto de disponibilidade** devem ser adicionadas ao pool de back-end para conectividade de saída. 
- Todas as máquinas virtuais em um **conjunto de dimensionamento de máquinas virtuais** devem ser adicionadas ao pool de back-end para conectividade de saída.

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre o [Azure Standard Load Balancer](load-balancer-overview.md)
- Veja nossas [perguntas frequentes sobre Azure Load Balancer](load-balancer-faqs.md)

