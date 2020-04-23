---
title: 'Azure ExpressRoute: amostras de configuração do roteador'
description: Esta página fornece exemplos de configuração do roteador para os roteadores da série Cisco ASA e Juniper.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: article
ms.date: 03/26/2020
ms.author: osamaz
ms.openlocfilehash: 3603bc45b920dc62eb8bf6f2eb8557f98e21638e
ms.sourcegitcommit: 75089113827229663afed75b8364ab5212d67323
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/22/2020
ms.locfileid: "82024805"
---
# <a name="router-configuration-samples-to-set-up-and-manage-routing"></a>Exemplos de configuração do roteador para configurar e gerenciar o roteamento
Esta página fornece amostras de configuração de interface e roteamento para roteadores da série Cisco IOS-XE e Juniper MX quando você está trabalhando com o Azure ExpressRoute.

> [!IMPORTANT]
> As amostras nesta página são puramente para orientação. Você deve trabalhar com a equipe técnica/de vendas do seu fornecedor e sua equipe de rede para encontrar configurações apropriadas para atender às suas necessidades. A Microsoft não suportará problemas relacionados às configurações listadas nesta página. Entre em contato com o fornecedor do dispositivo para obter problemas de suporte.
> 
> 

## <a name="mtu-and-tcp-mss-settings-on-router-interfaces"></a>Configurações de MSS TCP e MTU em interfaces do roteador
A unidade de transmissão máxima (MTU) para a interface ExpressRoute é 1500, que é o MTU padrão típico para uma interface Ethernet em um roteador. A menos que seu roteador tenha uma MTU diferente por padrão, não é necessário especificar um valor na interface do roteador.

Ao contrário de um gateway Azure VPN, o tamanho máximo do segmento TCP (MSS) para um circuito ExpressRoute não precisa ser especificado.

As amostras de configuração do roteador neste artigo aplicam-se a todos os peerings. Examine [emparelhamentos do ExpressRoute](expressroute-circuit-peerings.md) e [requisitos de roteamento do ExpressRoute](expressroute-routing.md) para obter mais detalhes sobre roteamento.


## <a name="cisco-ios-xe-based-routers"></a>Roteadores com base em Cisco IOS-XE
As amostras nesta seção aplicam-se a qualquer roteador que executa a família IOS-XE OS.

### <a name="configure-interfaces-and-subinterfaces"></a>Configurar interfaces e subinterfaces
Você precisará de uma subinterface por peering em cada roteador que você conectar à Microsoft. Uma subinterface pode ser identificada com um ID VLAN ou um par de IDs VLAN empilhados e um endereço IP.

**Definição da interface Dot1Q**

Esta amostra fornece a definição de subinterface para uma subinterface com um único ID VLAN. A ID de VLAN é exclusiva por emparelhamento. O último octeto de seu endereço IPv4 sempre será um número ímpar.

    interface GigabitEthernet<Interface_Number>.<Number>
     encapsulation dot1Q <VLAN_ID>
     ip address <IPv4_Address><Subnet_Mask>

**Definição da interface QinQ**

Esta amostra fornece a definição de subinterface para uma subinterface com dois IDs de VLAN. O ID VLAN externo (s-tag), se usado, permanece o mesmo em todos os peerings. A ID de VLAN interna (marca c) é exclusiva por emparelhamento. O último octeto de seu endereço IPv4 sempre será um número ímpar.

    interface GigabitEthernet<Interface_Number>.<Number>
     encapsulation dot1Q <s-tag> seconddot1Q <c-tag>
     ip address <IPv4_Address><Subnet_Mask>

### <a name="set-up-ebgp-sessions"></a>Configure as sessões de eBGP
Você deve configurar uma sessão de BGP com a Microsoft para cada peering. Configure uma sessão BGP usando a seguinte amostra. Se o endereço IPv4 que você usou para sua subinterface foi a.b.c.d, então o endereço IP do vizinho BGP (Microsoft) será a.b.c.d+1. O último octeto do endereço de IPv4 do vizinho BGP sempre será um número par.

    router bgp <Customer_ASN>
     bgp log-neighbor-changes
     neighbor <IP#2_used_by_Azure> remote-as 12076
     !        
     address-family ipv4
     neighbor <IP#2_used_by_Azure> activate
     exit-address-family
    !

### <a name="set-up-prefixes-to-be-advertised-over-the-bgp-session"></a>Configure prefixos a serem anunciados durante a sessão BGP
Configure seu roteador para anunciar prefixos selecionados para a Microsoft usando a seguinte amostra.

    router bgp <Customer_ASN>
     bgp log-neighbor-changes
     neighbor <IP#2_used_by_Azure> remote-as 12076
     !        
     address-family ipv4
      network <Prefix_to_be_advertised> mask <Subnet_mask>
      neighbor <IP#2_used_by_Azure> activate
     exit-address-family
    !

### <a name="route-maps"></a>Mapas de rotas
Use mapas de rota e listas de prefixos para filtrar prefixos propagados em sua rede. Consulte a amostra a seguir e certifique-se de que você tenha as listas de prefixos apropriadas configuradas.

    router bgp <Customer_ASN>
     bgp log-neighbor-changes
     neighbor <IP#2_used_by_Azure> remote-as 12076
     !        
     address-family ipv4
      network <Prefix_to_be_advertised> mask <Subnet_mask>
      neighbor <IP#2_used_by_Azure> activate
      neighbor <IP#2_used_by_Azure> route-map <MS_Prefixes_Inbound> in
     exit-address-family
    !
    route-map <MS_Prefixes_Inbound> permit 10
     match ip address prefix-list <MS_Prefixes>
    !

### <a name="configure-bfd"></a>Configurar BFD

Você configurará o BFD em dois lugares: um no nível de interface e outro no nível BGP. O exemplo aqui é para a interface QinQ. 

    interface GigabitEthernet<Interface_Number>.<Number>
     bfd interval 300 min_rx 300 multiplier 3
     encapsulation dot1Q <s-tag> seconddot1Q <c-tag>
     ip address <IPv4_Address><Subnet_Mask>
    
    router bgp <Customer_ASN>
     bgp log-neighbor-changes
     neighbor <IP#2_used_by_Azure> remote-as 12076
     !        
     address-family ipv4
      neighbor <IP#2_used_by_Azure> activate
      neighbor <IP#2_used_by_Azure> fall-over bfd
     exit-address-family
    !


## <a name="juniper-mx-series-routers"></a>Roteadores da série Juniper MX
As amostras nesta seção aplicam-se a qualquer roteador da série Juniper MX.

### <a name="configure-interfaces-and-subinterfaces"></a>Configurar interfaces e subinterfaces

**Definição da interface Dot1Q**

Esta amostra fornece a definição de subinterface para uma subinterface com um único ID VLAN. A ID de VLAN é exclusiva por emparelhamento. O último octeto de seu endereço IPv4 sempre será um número ímpar.

    interfaces {
        vlan-tagging;
        <Interface_Number> {
            unit <Number> {
                vlan-id <VLAN_ID>;
                family inet {
                    address <IPv4_Address/Subnet_Mask>;
                }
            }
        }
    }


**Definição da interface QinQ**

Esta amostra fornece a definição de subinterface para uma subinterface com dois IDs de VLAN. O ID VLAN externo (s-tag), se usado, permanece o mesmo em todos os peerings. A ID de VLAN interna (marca c) é exclusiva por emparelhamento. O último octeto de seu endereço IPv4 sempre será um número ímpar.

    interfaces {
        <Interface_Number> {
            flexible-vlan-tagging;
            unit <Number> {
                vlan-tags outer <S-tag> inner <C-tag>;
                family inet {
                    address <IPv4_Address/Subnet_Mask>;
                }                           
            }                               
        }                                   
    }                           

### <a name="set-up-ebgp-sessions"></a>Configure as sessões de eBGP
Você deve configurar uma sessão de BGP com a Microsoft para cada peering. Configure uma sessão BGP usando a seguinte amostra. Se o endereço IPv4 que você usou para sua subinterface foi a.b.c.d, então o endereço IP do vizinho BGP (Microsoft) será a.b.c.d+1. O último octeto do endereço de IPv4 do vizinho BGP sempre será um número par.

    routing-options {
        autonomous-system <Customer_ASN>;
    }
    }
    protocols {
        bgp { 
            group <Group_Name> { 
                peer-as 12076;              
                neighbor <IP#2_used_by_Azure>;
            }                               
        }                                   
    }

### <a name="set-up-prefixes-to-be-advertised-over-the-bgp-session"></a>Configure prefixos a serem anunciados durante a sessão BGP
Configure seu roteador para anunciar prefixos selecionados para a Microsoft usando a seguinte amostra.

    policy-options {
        policy-statement <Policy_Name> {
            term 1 {
                from protocol OSPF;
        route-filter 
    <Prefix_to_be_advertised/Subnet_Mask> exact;
                then {
                    accept;
                }
            }
        }
    }
    protocols {
        bgp { 
            group <Group_Name> { 
                export <Policy_Name>
                peer-as 12076;              
                neighbor <IP#2_used_by_Azure>;
            }                               
        }                                   
    }


### <a name="route-policies"></a>Políticas de rotas
Você pode usar mapas de rota e listas de prefixos para filtrar prefixos propagados em sua rede. Consulte a amostra a seguir e certifique-se de ter as listas de prefixos apropriadas configuradas.

    policy-options {
        prefix-list MS_Prefixes {
            <IP_Prefix_1/Subnet_Mask>;
            <IP_Prefix_2/Subnet_Mask>;
        }
        policy-statement <MS_Prefixes_Inbound> {
            term 1 {
                from {
                prefix-list MS_Prefixes;
                }
                then {
                    accept;
                }
            }
        }
    }
    protocols {
        bgp { 
            group <Group_Name> { 
                export <Policy_Name>
                import <MS_Prefixes_Inbound>
                peer-as 12076;              
                neighbor <IP#2_used_by_Azure>;
            }                               
        }                                   
    }

### <a name="configure-bfd"></a>Configurar BFD
Configure bfd somente sob a seção BGP do protocolo.

    protocols {
        bgp { 
            group <Group_Name> { 
                peer-as 12076;              
                neighbor <IP#2_used_by_Azure>;
                bfd-liveness-detection {
                       minimum-interval 3000;
                       multiplier 3;
                }
            }                               
        }                                   
    }


## <a name="next-steps"></a>Próximas etapas
Consulte as [Perguntas Frequentes sobre ExpressRoute](expressroute-faqs.md) para obter mais detalhes.



