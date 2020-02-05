---
title: Azure VMware Solutions (AVS)-conexão local usando o ExpressRoute
description: Descreve como solicitar uma conexão local usando o ExpressRoute da rede da região AVS
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 10a21faf2790b4c7a26d80e46bf44c8bffabf27f
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77019614"
---
# <a name="connect-from-on-premises-to-avs-using-expressroute"></a>Conectar-se do local para a AVS usando o ExpressRoute

Se você já tiver uma conexão do ExpressRoute do Azure de um local externo (como no local) para o Azure, poderá conectá-lo ao seu ambiente da AVS. Você pode fazer isso por meio de um recurso do Azure que permite que dois circuitos do ExpressRoute se conectem entre si. Esse método estabelece uma conexão segura, privada, de alta largura de banda e baixa latência entre os dois ambientes.

[![conexão do ExpressRoute local-Alcance Global](media/cloudsimple-global-reach-connection.png)](media/cloudsimple-global-reach-connection.png)

## <a name="before-you-begin"></a>Antes de começar

Um bloco de endereço de rede **/29** é necessário para estabelecer alcance global conexão do local. O espaço de endereço/29 é usado para a rede de trânsito entre circuitos do ExpressRoute. A rede de trânsito não deve se sobrepor a nenhuma de suas redes virtuais do Azure, redes locais ou redes de nuvem privada da AVS.

## <a name="prerequisites"></a>Pré-requisitos

* Um circuito do Azure ExpressRoute é necessário antes que você possa estabelecer a conexão entre o circuito e as redes de nuvem privada da AVS.
* Um usuário é necessário com privilégios para criar chaves de autorização em um circuito do ExpressRoute.

## <a name="scenarios"></a>Cenários

Conectar sua rede local à sua rede de nuvem privada da AVS permite que você use a nuvem privada da AVS de várias maneiras, incluindo os seguintes cenários:

* Acesse sua rede de nuvem privada da AVS sem criar uma conexão VPN site a site.
* Use seu Active Directory local como uma fonte de identidade em sua nuvem privada de AVS.
* Migre máquinas virtuais em execução localmente para sua nuvem privada de AVS.
* Use sua nuvem privada de AVS como parte de uma solução de recuperação de desastre.
* Consuma recursos locais em suas VMs de carga de trabalho de nuvem privada da AVS.

## <a name="connecting-expressroute-circuits"></a>Conectando circuitos do ExpressRoute

Para estabelecer a conexão do ExpressRoute, você deve criar uma autorização no circuito do ExpressRoute e fornecer as informações de autorização para a AVS.


### <a name="create-expressroute-authorization"></a>Criar autorização de ExpressRoute

1. Entre no portal do Azure.

2. Na barra de pesquisa superior, pesquise o **circuito do expressroute** e clique em **circuitos do expressroute** em **Serviços**.
    [![circuitos do ExpressRoute](media/azure-expressroute-transit-search.png)](media/azure-expressroute-transit-search.png)

3. Selecione o circuito do ExpressRoute que você pretende conectar à sua rede AVS.

4. Na página ExpressRoute, clique em **autorizações**, insira um nome para a autorização e clique em **salvar**.
    [![autorização de circuito do ExpressRoute](media/azure-expressroute-transit-authorizations.png)](media/azure-expressroute-transit-authorizations.png)

5. Copie a ID de recurso e a chave de autorização clicando no ícone de cópia. Cole a ID e a chave em um arquivo de texto.
    [Cópia de autorização de circuito do ExpressRoute ![](media/azure-expressroute-transit-authorization-copy.png)](media/azure-expressroute-transit-authorization-copy.png)

    > [!IMPORTANT]
    > A **ID do recurso** deve ser copiada da interface do usuário e deve estar no formato ```/subscriptions/<subscription-ID>/resourceGroups/<resource-group-name>/providers/Microsoft.Network/expressRouteCircuits/<express-route-circuit-name>``` quando você fornecer suporte para.

6. Arquivo um tíquete com <a href="https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest" target="_blank">suporte</a> para a conexão a ser criada.
    * Tipo de problema: **técnico**
    * Assinatura: **assinatura em que o serviço de sincronização automática está implantado**
    * Serviço: **soluções VMware (AVS)**
    * Tipo de problema: **solicitação de serviço**
    * Subtipo de problema: **criar conexão de ExpressRoute para local**
    * Forneça a ID de recurso e a chave de autorização que você copiou e salvou no painel de detalhes.
    * Forneça um/29 espaço de endereço de rede para a rede de trânsito.
    * Você está enviando a rota padrão por meio do ExpressRoute?
    * O tráfego da nuvem privada AVS deve usar a rota padrão enviada por meio do ExpressRoute?

    > [!IMPORTANT]
    > O envio de rota padrão permite enviar todo o tráfego da Internet da nuvem privada da AVS usando sua conexão de Internet local. Para desabilitar a rota padrão configurada na nuvem privada da AVS e usar a rota padrão de conexão local, forneça os detalhes no tíquete de suporte.

## <a name="next-steps"></a>Próximos passos

* [Saiba mais sobre as conexões de rede do Azure](cloudsimple-azure-network-connection.md)  
