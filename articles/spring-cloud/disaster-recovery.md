---
title: Recuperação de desastre geográfica do Azure Spring Cloud | Microsoft Docs
description: Saiba como proteger seu aplicativo Spring Cloud de interrupções regionais
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: brendm
ms.openlocfilehash: e8f32f574a4ff7be0cc3cc7915b8203b53824c63
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/05/2020
ms.locfileid: "82792319"
---
# <a name="azure-spring-cloud-disaster-recovery"></a>Recuperação de desastres na nuvem do Azure Spring

Este artigo explica algumas estratégias que você pode usar para proteger seus aplicativos do Azure Spring Cloud de sofrerem tempo de inatividade.  Qualquer região ou data center pode enfrentar tempo de inatividade causado por desastres regionais, mas o planejamento cuidadoso pode reduzir o impacto sobre os clientes.

## <a name="plan-your-application-deployment"></a>Planejar a implantação do aplicativo

Os aplicativos de nuvem Spring do Azure são executados em uma região específica.  O Azure opera em várias regiões geográficas em todo o mundo. Uma geografia do Azure é uma área definida do mundo que contém, pelo menos, uma Região do Azure. Uma região do Azure é uma área em uma geografia, que contém um ou mais data centers.  Cada região do Azure é emparelhada com outra região na mesma área geográfica, formando juntas um par regional. O Azure serializa atualizações de plataforma (manutenção planejada) entre pares regionais, garantindo que apenas uma região em cada par seja atualizada de cada vez. Em caso de interrupção que afete várias regiões, pelo menos uma região em cada par será priorizada para recuperação.

Garantir a alta disponibilidade e a proteção contra desastres exige que você implante seus aplicativos Spring Cloud em várias regiões.  O Azure fornece uma lista de [regiões emparelhadas](../best-practices-availability-paired-regions.md) para que você possa planejar suas implantações de nuvem Spring em pares regionais.  Recomendamos que você considere três fatores principais ao projetar sua arquitetura de micro serviço: disponibilidade de região, regiões emparelhadas do Azure e disponibilidade de serviço.

*  Disponibilidade da região: escolha uma área geográfica perto de seus usuários para minimizar o tempo de transmissão e a latência da rede.
*  Regiões emparelhadas do Azure: escolha regiões emparelhadas dentro de sua área geográfica escolhida para garantir atualizações de plataforma coordenadas e esforços de recuperação priorizados, se necessário.
*  Disponibilidade do serviço: decida se suas regiões emparelhadas devem ser executadas em alta/quente, quente/quente ou quente/frio.

## <a name="use-azure-traffic-manager-to-route-traffic"></a>Usar o Gerenciador de Tráfego do Azure para rotear o tráfego

O [Gerenciador de tráfego do Azure](../traffic-manager/traffic-manager-overview.md) fornece balanceamento de carga de tráfego baseado em DNS e pode distribuir o tráfego de rede entre várias regiões.  Use o Gerenciador de tráfego do Azure para direcionar os clientes para a instância mais próxima do serviço de nuvem Spring do Azure para eles.  Para obter melhor desempenho e redundância, direcione todo o tráfego do aplicativo por meio do Gerenciador de tráfego do Azure antes de enviá-lo para o serviço de nuvem do Azure Spring.

Se você tiver aplicativos do Azure Spring Cloud em várias regiões, use o Gerenciador de tráfego do Azure para controlar o fluxo de tráfego para seus aplicativos em cada região.  Defina um ponto de extremidade do Gerenciador de tráfego do Azure para cada serviço usando o IP do serviço. Os clientes devem se conectar a um nome DNS do Gerenciador de tráfego do Azure apontando para o serviço de nuvem Spring do Azure.  O Gerenciador de tráfego do Azure equilibra o tráfego entre os pontos de extremidade definidos.  Se um desastre ocorrer um data center, o Gerenciador de tráfego do Azure direcionará o tráfego dessa região para seu par, garantindo a continuidade do serviço.

## <a name="create-azure-traffic-manager-for-azure-spring-cloud"></a>Criar o Gerenciador de tráfego do Azure para o Azure Spring Cloud

1. Crie o Azure Spring Cloud em duas regiões diferentes.
Você precisará de duas instâncias de serviço do Azure Spring Cloud implantadas em duas regiões diferentes (leste dos EUA e Europa Ocidental). Inicie um aplicativo existente do Azure Spring Cloud usando o portal do Azure para criar duas instâncias de serviço. Cada um servirá como ponto de extremidade primário e de failover para o tráfego. 

**Duas informações de instâncias de serviço:**

| Nome do Serviço | Local | Aplicativo |
|--|--|--|
| serviço-amostra-a | Leste dos EUA | Gateway/auth-Service/Account-Service |
| serviço-amostra-b | Europa Ocidental | Gateway/auth-Service/Account-Service |

2. Configurar o domínio personalizado para o serviço siga o [documento de domínio personalizado](spring-cloud-tutorial-custom-domain.md) para configurar o domínio personalizado para essas duas instâncias de serviço existentes. Após a configuração bem-sucedida, ambas as instâncias de serviço serão vinculadas ao domínio personalizado: bcdr-test.contoso.com

3. Crie um Gerenciador de tráfego e dois pontos de extremidade: [crie um perfil do Gerenciador de tráfego usando o portal do Azure](https://docs.microsoft.com/azure/traffic-manager/quickstart-create-traffic-manager-profile).

Este é o perfil do Gerenciador de tráfego:
* Nome DNS do Gerenciador de tráfego:http://asc-bcdr.trafficmanager.net
* Perfis de ponto de extremidade: 

| Perfil | Type | Destino | Prioridade | Configurações de cabeçalho personalizadas |
|--|--|--|--|--|
| Criar um perfil do ponto de extremidade | Ponto de extremidade externo | service-sample-a.asc-test.net | 1 | host: bcdr-test.contoso.com |
| Perfil do ponto de extremidade B | Ponto de extremidade externo | service-sample-b.asc-test.net | 2 | host: bcdr-test.contoso.com |

4. Crie um registro CNAME na zona DNS: bcdr-test.contoso.com CNAME asc-bcdr.trafficmanager.net. 

5. Agora, o ambiente está completamente configurado. Os clientes devem ser capazes de acessar o aplicativo via: bcdr-test.contoso.com