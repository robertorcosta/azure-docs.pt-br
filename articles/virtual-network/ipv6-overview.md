---
title: Visão geral do IPv6 para a Rede Virtual Azure
titlesuffix: Azure Virtual Network
description: IPv6 descrição de pontos finais iPv6 e caminhos de dados em uma rede virtual Azure.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.workload: infrastructure-services
ms.date: 03/31/2020
ms.author: kumud
ms.openlocfilehash: 03b279ead6b1f5d26ae92b63a8780a61dfd711bb
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2020
ms.locfileid: "80420562"
---
# <a name="what-is-ipv6-for-azure-virtual-network"></a>O que é o IPv6 para Azure Virtual Network?

O IPv6 for Azure Virtual Network (VNet) permite hospedar aplicativos no Azure com conectividade IPv6 e IPv4, tanto dentro de uma rede virtual quanto de e para a Internet. Devido ao esgotamento dos endereços Públicos IPv4, novas redes de mobilidade e Internet das Coisas (IoT) são frequentemente construídas no IPv6. Mesmo as redes de internet e mobile há muito estabelecidas estão sendo transformadas em IPv6. Os serviços somente iPv4 podem encontrar-se em desvantagem real tanto nos mercados existentes quanto nos emergentes. A conectividade IPv4/IPv6 de pilha dupla permite que os serviços hospedados no Azure atravessem essa lacuna de tecnologia com serviços disponíveis globalmente e com dois empilhados que se conectam facilmente com os dispositivos e redes IPv4 existentes e com esses novos dispositivos e redes IPv6.

A conectividade IPv6 original do Azure facilita o fornecer conectividade de Internet de pilha dupla (IPv4/IPv6) para aplicativos hospedados no Azure. Permite a implantação simples de VMs com conectividade IPv6 balanceada de carga para conexões iniciadas de entrada e saída. Este recurso ainda está disponível e mais informações estão disponíveis [aqui](../load-balancer/load-balancer-ipv6-overview.md).
A rede virtual IPv6 for Azure é muito mais completa, permitindo que arquiteturas completas de soluções IPv6 sejam implantadas no Azure.


O diagrama a seguir mostra uma simples implantação de pilha dupla (IPv4/IPv6) no Azure:

![Diagrama de implantação de rede IPv6](./media/ipv6-support-overview/ipv6-sample-diagram.png)

## <a name="benefits"></a>Benefícios

IPv6 para benefícios Do Azure VNET:

- Ajuda a expandir o alcance de seus aplicativos hospedados no Azure para os crescentes mercados de mobile e Internet of Things.
- As VMs IPv4/IPv6 empilhadas duplas fornecem a máxima flexibilidade de implantação do serviço. Uma única instância de serviço pode se conectar com clientes de Internet com capacidade para IPv4 e IPv6.
- Baseia-se na conectividade IPv6 do Azure para Internet, há muito estabelecido e estável.
- Seguro por padrão, uma vez que a conectividade IPv6 à Internet só é estabelecida quando você solicita explicitamente em sua implantação.

## <a name="capabilities"></a>Funcionalidades

O IPv6 para Azure VNet inclui os seguintes recursos:

- Os clientes do Azure podem definir seu próprio espaço de endereço de rede virtual IPv6 para atender às necessidades de seus aplicativos, clientes ou integrar-se perfeitamente em seu espaço IP no local.
- Redes virtuais de pilha dupla (IPv4 e IPv6) com sub-redes de pilha dupla permitem que os aplicativos se conectem com os recursos IPv4 e IPv6 em sua rede virtual ou - a Internet.
    > [!IMPORTANT]
    > As sub-redes para IPv6 devem ter exatamente 0,64 de tamanho.  Isso garante compatibilidade futura caso você decida ativar o roteamento da sub-rede para uma rede local, já que alguns roteadores só podem aceitar rotas /64 IPv6.  
- Proteja seus recursos com regras IPv6 para Grupos de Segurança de Rede.
    - E as proteções de Negação distribuída de Serviço (DDoS) da plataforma Azure são estendidas aos IP's públicos voltados para a Internet
- Personalize o roteamento do tráfego IPv6 em sua rede virtual com rotas definidas pelo usuário, especialmente ao aproveitar os Dispositivos Virtuais de Rede para aumentar sua aplicação.
- Máquinas virtuais linux e windows podem usar iPv6 para Azure VNET
- [Suporte padrão iPv6 public Load Balancer](virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-powershell.md) para criar aplicativos resilientes e escaláveis, que incluem:
    - Sonda de saúde IPv6 opcional para determinar quais instâncias de pool backend são de saúde e, portanto, podem receber novos fluxos.
    - Regras opcionais de saída que fornecem controle declarativo total sobre a conectividade de saída para dimensionar e ajustar essa capacidade às suas necessidades específicas.
    - Configurações front-end opcionais que permitem que um único balanceador de carga use vários endereços IP públicos IPv6- o mesmo protocolo frontend e porta podem ser reutilizados em endereços frontend.
    - As portas IPv6 opcionais podem ser reutilizadas em instâncias de back-end usando o recurso *IP flutuante* das regras de balanceamento de carga 
- [Suporte ao Balancer de carga interno Padrão IPv6](ipv6-dual-stack-standard-internal-load-balancer-powershell.md) para criar aplicativos multiníveis resilientes dentro dos VNETs do Azure.  
- Suporte básico ao Balancer de carga pública IPv6 para compatibilidade com implantações legados
- [Endereços IP públicos e faixas de endereços iPv6 reservados](ipv6-public-ip-address-prefix.md) fornecem endereços IPv6 estáveis e previsíveis que facilitam a listagem em branco de seus aplicativos hospedados no azul para sua empresa e seus clientes.
- O IP público em nível de instância fornece conectividade de Internet IPv6 diretamente para VMs individuais.
- [Adicione iPv6 às implantações somente IPv4 existentes](ipv6-add-to-existing-vnet-powershell.md)- esse recurso permite adicionar facilmente conectividade IPv6 às implantações iPv4 existentes sem a necessidade de recriar implantações.  O tráfego de rede IPv4 não é afetado durante esse processo, portanto, dependendo do seu aplicativo e do sO, você pode ser capaz de adicionar IPv6 até mesmo a serviços ao vivo.    
- Deixe que os clientes da Internet acessem perfeitamente seu aplicativo de pilha dupla usando seu protocolo de escolha com suporte ao DNS do Azure para registros IPv6 (AAAA). 
- Crie aplicativos de pilha dupla que são dimensionados automaticamente para sua carga com conjuntos de escala de máquina virtual com IPv6.
- [Rede Virtual (VNET) Peering](virtual-network-peering-overview.md) - tanto dentro do peering regional quanto global - permite conectar sem querer VNETs de pilha dupla- tanto os pontos finais IPv4 quanto IPv6 em VMs nas redes peered poderão se comunicar entre si. Você pode até mesmo peer dual stack com VNETs somente IPv4 à medida que você está transitando suas implantações para pilha dupla. 
- IPv6 Solução de problemas e Diagnósticos estão disponíveis com métricas/alertas de balanceador de carga e recursos de Observador de Rede, como captura de pacotes, registros de fluxo NSG, solução de problemas de conexão e monitoramento de conexão.   

## <a name="scope"></a>Escopo
IPv6 for Azure VNET é um conjunto de recursos fundamentais que permite que os clientes hospedem aplicativos dual stack (IPv4+IPv6) no Azure.  Pretendemos adicionar suporte ao IPv6 a mais recursos de rede Azure ao longo do tempo e, eventualmente, oferecer versões de pilha dupla dos serviços Do Azure PaaS, mas enquanto isso todos os serviços do Azure PaaS podem ser acessados através dos pontos finais Do IPv4 em Máquinas Virtuais de pilha dupla.   

## <a name="limitations"></a>Limitações
A versão atual da rede virtual IPv6 para Azure tem as seguintes limitações:
- A rede virtual IPv6 for Azure está disponível em todas as regiões globais do Azure, mas apenas no Global Azure, ainda não nas nuvens do governo.
- Os gateways ExpressRoute e VPN não podem ser usados em um VNET com IPv6 ativado, diretamente ou com o "UseRemoteGateway". 
- A plataforma Azure (AKS, etc.) não suporta comunicação IPv6 para Containers.  

## <a name="pricing"></a>Preços

Os recursos do IPv6 Azure e a largura de banda são cobrados nas mesmas taxas que o IPv4. Não há taxas adicionais ou diferentes para iPv6. Você pode encontrar detalhes sobre preços para [endereços IP públicos,](https://azure.microsoft.com/pricing/details/ip-addresses/) [largura de banda de rede](https://azure.microsoft.com/pricing/details/bandwidth/)ou [Balanceador de carga.](https://azure.microsoft.com/pricing/details/load-balancer/)

## <a name="next-steps"></a>Próximas etapas

- Saiba como [implantar um aplicativo de pilha dupla IPv6 usando o Azure PowerShell](virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-powershell.md).
- Saiba como [implantar um aplicativo de pilha dupla IPv6 usando o Azure CLI](virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-cli.md).
- Saiba como [implantar um aplicativo de pilha dupla IPv6 usando o JSON (Resource Manager Templates, modelos de gerenciamento de recursos)](ipv6-configure-standard-load-balancer-template-json.md)
