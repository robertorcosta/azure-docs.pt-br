---
title: Solucionar problemas do Azure Load Balancer
description: Saiba como solucionar problemas conhecidos com o Azure Load Balancer.
services: load-balancer
documentationcenter: na
author: asudbring
manager: dcscontentpm
ms.custom: seodoc18
ms.service: load-balancer
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/28/2020
ms.author: allensu
ms.openlocfilehash: 1cfe27fd5c63bc4c1436982212b91e07f54aedb5
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85801913"
---
# <a name="troubleshoot-azure-load-balancer"></a>Solucionar problemas do Azure Load Balancer

Esta página fornece informações para solução de problemas comuns do Azure Load Balancer Basic e Standard. Para obter mais informações sobre o Load Balancer Standard, veja [Visão geral do Load Balancer Standard](load-balancer-standard-diagnostics.md).

Quando a conectividade do Load Balancer não estiver disponível, os sintomas mais comuns são os seguintes: 

- VMs por trás do Load Balancer não estão respondendo às investigações de integridade 
- VMs por trás do Load Balancer não estão respondendo ao tráfego na porta configurada

Quando os clientes externos para as VMs de back-end passarem pelo balanceador de carga, o endereço IP dos clientes será usado para a comunicação. Garanta que o endereço IP dos clientes foi adicionado à lista de permissões do NSG. 

## <a name="symptom-vms-behind-the-load-balancer-are-not-responding-to-health-probes"></a>Sintoma: VMs por trás do Load Balancer não estão respondendo às investigações de integridade
Para que os servidores back-end participem do conjunto de balanceadores de carga, eles devem passar na verificação de investigação. Para saber mais sobre investigações de integridade, confira [Noções básicas sobre investigações do Load Balancer](load-balancer-custom-probe-overview.md). 

As VMs do pool de back-end do Load Balancer podem não estar respondendo às investigações devido a algum destes motivos: 
- A VM do pool de back-end do Load Balancer não está em estado íntegro 
- A VM do pool de back-end do Load Balancer não está escutando na porta de investigação 
- O firewall, ou o grupo de segurança de rede, está bloqueando a porta nas VMs do pool de back-end do Load Balancer 
- Outras configurações incorretas no Load Balancer

### <a name="cause-1-load-balancer-backend-pool-vm-is-unhealthy"></a>Causa 1: A VM do pool de back-end do Load Balancer não está em estado íntegro 

**Validação e resolução**

Para resolver esse problema, faça logon nas VMs participantes e verifique se a VM está em estado íntegro e pode responder a **PsPing** ou **TCPing** de outra VM no pool. Se a VM não estiver em estado íntegro ou não puder responder à investigação, você deverá retificar o problema e colocar a VM de volta em estado íntegro para que ela possa participar do balanceamento de carga.

### <a name="cause-2-load-balancer-backend-pool-vm-is-not-listening-on-the-probe-port"></a>Causa 2: A VM do pool de back-end do Load Balancer não está escutando na porta de investigação
Se a VM estiver em estado íntegro, mas não estiver respondendo à investigação, é possível que a porta de investigação não esteja aberta na VM participante ou que a VM não esteja escutando nessa porta.

**Validação e resolução**

1. Faça logon na VM de back-end. 
2. Abra um prompt de comando e execute o seguinte comando para verificar se existe um aplicativo escutando na porta de investigação:   
            netstat -an
3. Se o estado da porta não estiver listado como **LISTENING**, configure a porta correta. 
4. Como alternativa, selecione outra porta, que esteja listada como **LISTENING**, e atualize adequadamente a configuração do Load Balancer.              

### <a name="cause-3-firewall-or-a-network-security-group-is-blocking-the-port-on-the-load-balancer-backend-pool-vms"></a>Causa 3: O firewall, ou um grupo de segurança de rede, está bloqueando a porta nas VMs do pool de back-end do balanceador de carga  
Se o firewall na VM estiver bloqueando a porta de investigação, ou um ou mais grupos de segurança de rede configurados na sub-rede ou na VM, não estiverem permitindo que a investigação alcance a porta, a VM não poderá responder à investigação de integridade.          

**Validação e resolução**

* Se o firewall estiver habilitado, verifique se ele está configurado para permitir a porta de investigação. Caso contrário, configure o firewall para permitir o tráfego na porta de investigação e teste novamente. 
* Na lista de grupos de segurança de rede, verifique se o tráfego de entrada ou saída na porta de investigação tem interferência. 
* Verifique também se uma regra dos grupos de segurança de rede **Negar Tudo** na NIC da VM ou da sub-rede tem uma prioridade mais alta do que a regra padrão que permite investigações e tráfego do LB (grupos de segurança de rede devem permitir o IP 168.63.129.16 do Load Balancer). 
* Se qualquer uma dessas regras estiver bloqueando o tráfego de investigação, remova e reconfigure as regras para permitir o tráfego de investigação.  
* Agora, verifique se a VM começou a responder às investigações de integridade. 

### <a name="cause-4-other-misconfigurations-in-load-balancer"></a>Causa 4: Outras configurações incorretas no Load Balancer
Se todas as causas anteriores, aparentemente, tiverem sido validadas e resolvidas corretamente, e a VM de back-end continuar não respondendo à investigação de integridade, teste a conectividade manualmente e colete alguns rastreamentos para entender a conectividade.

**Validação e resolução**

* Use **Psping** em uma das VMs na VNet para testar a resposta da porta de investigação (exemplo:.\psping.exe -t 10.0.0.4:3389) e registrar resultados. 
* Use **TCPing** em uma das VMs na VNet para testar a resposta da porta de investigação (exemplo: .\tcping.exe 10.0.0.4 3389) e registrar resultados. 
* Se nenhuma resposta for recebida nesses testes de ping
    - Execute um rastreamento Netsh simultâneo na VM do pool de back-end de destino e na outra VM de teste da mesma VNet. Agora, execute um teste PsPing por algum tempo, colete alguns rastreamentos de rede e interrompa o teste. 
    - Analise a captura de rede e verifique se há pacotes de entrada e saída relacionados à consulta de ping. 
        - Se não forem observados pacotes de entrada na VM do pool de back-end, possivelmente, há uma configuração incorreta de UDR ou grupos de segurança de rede bloqueando o tráfego. 
        - Se não forem observados pacotes de saída na VM do pool de back-end, será preciso verificar se há problemas não relacionados na VM (por exemplo, um aplicativo bloqueando a porta de investigação). 
    - Verifique se os pacotes de investigação estão sendo forçados para outro destino (possivelmente por meio de configurações UDR) antes de chegarem ao Load Balancer. Isso pode fazer com que o tráfego nunca chegue à VM de back-end. 
* Altere o tipo de investigação (por exemplo, HTTP para TCP) e configure a porta correspondente nas ACLs dos grupos de segurança de rede e no firewall a fim de verificar se o problema é com a configuração da resposta de investigação. Para saber mais sobre a configuração da investigação de integridade, confira [Endpoint Load Balancing health probe configuration](https://blogs.msdn.microsoft.com/mast/2016/01/26/endpoint-load-balancing-heath-probe-configuration-details/) (Configuração da investigação de integridade no balanceamento de carga do ponto de extremidade).

## <a name="symptom-vms-behind-load-balancer-are-not-responding-to-traffic-on-the-configured-data-port"></a>Sintoma: As VMs por trás do Load Balancer não estão respondendo ao tráfego na porta de dados configurada

Se uma VM do pool de back-end estiver listada como íntegra e responder às investigações de integridade, mas ainda não estiver participando do Balanceamento de Carga, ou não estiver respondendo ao tráfego de dados, pode ser devido a um destes motivos: 
* A VM do pool de back-end do Load Balancer não está escutando na porta de dados 
* O grupo de segurança de rede está bloqueando a porta na VM do pool de back-end do Load Balancer  
* Acessando o Load Balancer da mesma VM e NIC 
* Acessando o frontend do Load Balancer da Internet a partir da VM participante do pool de back-end do Load Balancer 

### <a name="cause-1-load-balancer-backend-pool-vm-is-not-listening-on-the-data-port"></a>Causa 1: A VM do pool de back-end do Load Balancer não está escutando na porta de dados 
Se uma VM não responder ao tráfego de dados, pode ser porque a porta de destino não está aberta na VM participante ou porque a VM não está escutando nessa porta. 

**Validação e resolução**

1. Faça logon na VM de back-end. 
2. Abra um prompt de comando e execute o seguinte comando para verificar se existe um aplicativo escutando na porta de dados:   netstat -an 
3. Se a porta não estiver listada com o estado "LISTENING", configure a porta de ouvinte apropriada 
4. Se a porta estiver marcada como Listening, verifique se há algum problema no aplicativo de destino dessa porta.

### <a name="cause-2-network-security-group-is-blocking-the-port-on-the-load-balancer-backend-pool-vm"></a>Causa 2: O grupo de segurança de rede está bloqueando a porta na VM do pool de back-end do Load Balancer  

Se um ou mais grupos de segurança de rede configurados na sub-rede ou na VM estiverem bloqueando o IP de origem ou a porta, a VM não poderá responder.

Para o balanceador de carga público, o endereço IP dos clientes da Internet será usado para comunicação entre os clientes e as VMs de back-end do balanceador de carga. Garanta que o endereço IP dos clientes é permitido no grupo de segurança de rede da VM de back-end.

1. Liste os grupos de segurança de rede configurados na VM de back-end. Para obter mais informações, consulte [Gerenciar grupos de segurança de rede](../virtual-network/manage-network-security-group.md)
1. Na lista de grupos de segurança de rede, verifique se:
    - O tráfego de entrada ou saída na porta de dados tem interferência. 
    - Uma regra do grupo de segurança de rede **Negar Tudo** na NIC da VM ou na sub-rede tem uma prioridade mais alta do que a regra padrão que permite as investigações e o tráfego do Load Balancer (grupos de segurança de rede devem permitir o IP 168.63.129.16 do Load Balancer, que é a porta de investigação)
1. Se qualquer uma dessas regras estiver bloqueando o tráfego, remova e reconfigure as regras para permitir o tráfego de dados.  
1. Agora, verifique se a VM começou a responder às investigações de integridade.

### <a name="cause-3-accessing-the-load-balancer-from-the-same-vm-and-network-interface"></a>Causa 3: Acessando o Load Balancer da mesma VM e interface de rede 

Se seu aplicativo hospedado na VM de back-end de um Load Balancer estiver tentando acessar outro aplicativo hospedado na mesma VM de back-end pela mesma Interface de Rede, trata-se de um cenário sem suporte e que falhará. 

**Resolução** Você pode resolver esse problema usando um dos seguintes métodos:
* Configure VMs de pool de back-end separadas por aplicativo. 
* Configure o aplicativo em VMs de NIC dupla para que cada aplicativo use sua própria interface de rede e seu endereço IP. 

### <a name="cause-4-accessing-the-internal-load-balancer-frontend-from-the-participating-load-balancer-backend-pool-vm"></a>Causa 4: Acessando o front-end interno do Load Balancer a partir da VM participante do pool de back-end do Load Balancer

Se um Balanceador de Carga interno estiver configurado dentro de uma Rede Virtual e uma das VMs de backend do participante estiver tentando acessar o frontend interno do Load Balancer, poderão ocorrer falhas quando o fluxo for mapeado para a VM de origem. Não há suporte para esse cenário.

**Resolução** há várias maneiras para desbloquear este cenário, incluindo o uso de um proxy. Avalie o Gateway de aplicativo ou outros proxies 3ª de terceiros (por exemplo, nginx ou haproxy). Para saber mais sobre o Gateway de Aplicativo, confira [Visão geral do Gateway de Aplicativo](../application-gateway/application-gateway-introduction.md)

**Detalhes** Os Load Balancers internos não movem conexões originadas pela saída para o front-end de um Load Balancer interno porque ambos estão no espaço de endereços IP privado. Os Load Balancers fornecem [conexões de saída](load-balancer-outbound-connections.md) de endereços IP privados dentro da rede virtual para os endereços IP públicos. Para Load Balancers internos, essa abordagem evita possível esgotamento da porta SNAT em um espaço de endereços IP interno único, onde a movimentação não é obrigatória.

O efeito colateral é que, se um fluxo de saída de uma VM no pool de back-end tentar estabelecer um fluxo para o front-end do Load Balancer interno em seu pool _e_ for mapeado de volta para si mesmo, os dois lados do fluxo não serão correspondentes. Como eles não correspondem, o fluxo falha. O fluxo será bem-sucedido se não for mapeado de volta para a mesma VM no pool de back-end que criou o fluxo para o front-end.

Quando o fluxo é mapeado de volta para si mesmo, o fluxo de saída parece ser originado da VM para o front-end e o fluxo de entrada correspondente parece ser originado da VM para si mesmo. Do ponto de vista do sistema operacional convidado, as partes de entrada e de saída do mesmo fluxo não são correspondentes dentro da máquina virtual. A pilha TCP não reconhece essas metades do mesmo fluxo como parte do mesmo fluxo. A origem e o destino não correspondem. Quando o fluxo é mapeado para qualquer outra VM no pool de back-end, as metades do fluxo são correspondentes e a VM pode responder ao fluxo.

O sintoma desse cenário é que a conexão intermitente se esgota quando o fluxo retorna para o mesmo back-end que originou o fluxo. Soluções alternativas comuns incluem a inserção de uma camada proxy por trás do Load Balancer interno e o uso de regras de estilo DSR (Retorno de Servidor Direto). Para obter mais informações, consulte [Vários front-ends para o Azure Load Balancer](load-balancer-multivip-overview.md).

É possível combinar um Load Balancer interno com qualquer proxy de terceiros ou usar o [Gateway de Aplicativo](../application-gateway/application-gateway-introduction.md) interno para cenários de proxy com HTTP/HTTPS. Embora você possa usar um Load Balancer público para atenuar esse problema, o cenário resultante estará propenso ao [esgotamento de SNAT](load-balancer-outbound-connections.md). Evite essa segunda abordagem, a menos que seja cuidadosamente gerenciada.

## <a name="symptom-cannot-change-backend-port-for-existing-lb-rule-of-a-load-balancer-which-has-vm-scale-set-deployed-in-the-backend-pool"></a>Sintoma: Não é possível alterar a porta de back-end para a regra de LB existente de um balanceador de carga que tem um conjunto de dimensionamento de VM implantado no pool de back-end. 
### <a name="cause--the-backend-port-cannot-be-modified-for-a-load-balancing-rule-thats-used-by-a-health-probe-for-load-balancer-referenced-by-vm-scale-set"></a>Causa: a porta de back-end não pode ser modificada para uma regra de balanceamento de carga usada por uma investigação de integridade para o balanceador de carga referenciado pelo conjunto de dimensionamento de VM.
**Resolução** Para alterar a porta, você pode remover a investigação de integridade através da atualização do conjunto de dimensionamento de VM, atualizar a porta e, em seguida, configurar a investigação de integridade novamente.

## <a name="symptom-small-traffic-is-still-going-through-load-balancer-after-removing-vms-from-backend-pool-of-the-load-balancer"></a>Sintoma: O tráfego pequeno ainda passará pelo balanceador de carga depois de remover as VMs do pool de back-end do balanceador de carga. 
### <a name="cause--vms-removed-from-backend-pool-should-no-longer-receive-traffic-the-small-amount-of-network-traffic-could-be-related-to-storage-dns-and-other-functions-within-azure"></a>Causa: As VMs removidas do pool de back-end não devem mais receber tráfego. A pequena quantidade de tráfego de rede pode estar relacionada ao armazenamento, ao DNS e a outras funções no Azure. 
Para verificar, você pode conduzir um rastreamento de rede. O FQDN usado para suas contas de armazenamento de blobs é listado dentro das propriedades de cada conta de armazenamento.  De uma máquina virtual em sua assinatura do Azure, você pode executar um nslookup para determinar o IP do Azure atribuído a essa conta de armazenamento.

## <a name="additional-network-captures"></a>Capturas de rede adicionais
Se você optar por abrir um caso de suporte, colete as informações a seguir para uma resolução mais rápida. Escolha uma única VM de back-end para executar os seguintes testes:
- Use Psping em uma das VMs de back-end na VNet para testar a resposta da porta de investigação (exemplo: psping 10.0.0.4:3389) e registrar resultados. 
- Se nenhuma resposta for recebida nesses testes de ping, execute um rastreamento Netsh simultâneo na VM de back-end e na VM de teste da VNet enquanto executa PsPing. Em seguida, interrompa o rastreamento Netsh. 
 
## <a name="next-steps"></a>Próximas etapas

Se as etapas anteriores não resolverem o problema, abra um [tíquete de suporte](https://azure.microsoft.com/support/options/).

