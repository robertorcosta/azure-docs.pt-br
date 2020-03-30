---
title: Solução Azure VMware por CloudSimple - Conexão on-premises usando expressRoute
description: Descreve como solicitar uma conexão no local usando o ExpressRoute da rede de região CloudSimple
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 0dd5ede110255b6e53bbc397e683e66b3beffc65
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77019614"
---
# <a name="connect-from-on-premises-to-cloudsimple-using-expressroute"></a>Conecte-se de on-premises ao CloudSimple usando expressroute

Se você já tem uma conexão Azure ExpressRoute de um local externo (como no local) ao Azure, você pode conectá-la ao seu ambiente CloudSimple. Você pode fazê-lo através de um recurso Azure que permite que dois circuitos ExpressRoute se conectem entre si. Este método estabelece uma conexão segura, privada, de alta largura de banda e baixa latência entre os dois ambientes.

[![Conexão ExpressRoute no local - Alcance Global](media/cloudsimple-global-reach-connection.png)](media/cloudsimple-global-reach-connection.png)

## <a name="before-you-begin"></a>Antes de começar

Um bloco de endereços de rede **/29** é necessário para estabelecer a conexão Global Reach a partir do local.  O espaço de endereço /29 é usado para rede de trânsito entre circuitos ExpressRoute.  A rede de trânsito não deve se sobrepor a nenhuma de suas redes virtuais Azure, redes locais ou redes CloudSimple Private Cloud.

## <a name="prerequisites"></a>Pré-requisitos

* Um circuito Azure ExpressRoute é necessário antes que você possa estabelecer a conexão entre o circuito e as redes CloudSimple Private Cloud.
* Um usuário é obrigado com privilégios para criar chaves de autorização em um circuito ExpressRoute.

## <a name="scenarios"></a>Cenários

Conectar sua rede local à sua rede Private Cloud permite que você use a Nuvem Privada de várias maneiras, incluindo os seguintes cenários:

* Acesse sua rede Private Cloud sem criar uma conexão VPN site-to-site.
* Use seu Diretório Ativo no local como fonte de identidade em sua Nuvem Privada.
* Migre as máquinas virtuais que executam no local para a sua Nuvem Privada.
* Use sua Nuvem Privada como parte de uma solução de recuperação de desastres.
* Consumir recursos no local em suas VMs de carga de trabalho privadas em nuvem.

## <a name="connecting-expressroute-circuits"></a>Conectando circuitos ExpressRoute

Para estabelecer a conexão ExpressRoute, você deve criar uma autorização em seu circuito ExpressRoute e fornecer as informações de autorização ao CloudSimple.


### <a name="create-expressroute-authorization"></a>Criar autorização ExpressRoute

1. Entre no portal do Azure.

2. Na barra de pesquisa superior, procure o **circuito ExpressRoute** e clique em **circuitos ExpressRoute** em **Serviços**.
    [![Circuitos ExpressRoute](media/azure-expressroute-transit-search.png)](media/azure-expressroute-transit-search.png)

3. Selecione o circuito ExpressRoute que você pretende conectar à sua rede CloudSimple.

4. Na página ExpressRoute, clique em **Autorizações,** digite um nome para a autorização e clique **em Salvar**.
    [![Autorização do circuito expressroute](media/azure-expressroute-transit-authorizations.png)](media/azure-expressroute-transit-authorizations.png)

5. Copie a id de recurso e a chave de autorização clicando no ícone de cópia. Cole o ID e a chave em um arquivo de texto.
    [![Cópia da autorização do circuito expressroute](media/azure-expressroute-transit-authorization-copy.png)](media/azure-expressroute-transit-authorization-copy.png)

    > [!IMPORTANT]
    > **O ID** de recurso deve ser copiado da ```/subscriptions/<subscription-ID>/resourceGroups/<resource-group-name>/providers/Microsoft.Network/expressRouteCircuits/<express-route-circuit-name>``` ui e deve estar no formato quando você fornecê-lo para suportar.

6. Arquive um ticket com <a href="https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest" target="_blank">suporte</a> para que a conexão seja criada.
    * Tipo de emissão: **Técnico**
    * Assinatura: Assinatura onde o **serviço CloudSimple é implantado**
    * Serviço: **Solução VMware pela CloudSimple**
    * Tipo de problema: **Solicitação de serviço**
    * Subtipo de **problema: Crie conexão ExpressRoute para locais**
    * Forneça a iD de recurso e a chave de autorização que você copiou e salvou no painel de detalhes.
    * Forneça um espaço de endereço de rede /29 para rede de trânsito.
    * Você está enviando a rota padrão através do ExpressRoute?
    * O tráfego da Nuvem Privada deve usar a rota padrão enviada através do ExpressRoute?

    > [!IMPORTANT]
    > O envio de rota padrão permite que você envie todo o tráfego da Internet do Private Cloud usando sua conexão de internet no local.  Para desativar a rota padrão configurada na Nuvem Privada e usar a rota padrão de conexão no local, forneça os detalhes no ticket de suporte.

## <a name="next-steps"></a>Próximas etapas

* [Saiba mais sobre as conexões de rede do Azure](cloudsimple-azure-network-connection.md)  
