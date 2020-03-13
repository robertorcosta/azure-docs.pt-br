---
title: Conectar-se de forma privada a um aplicativo Web usando o ponto de extremidade privado do Azure
description: Conectar-se de forma privada a um aplicativo Web usando o ponto de extremidade privado do Azure
author: ericgre
ms.assetid: 2dceac28-1ba6-4904-a15d-9e91d5ee162c
ms.topic: article
ms.date: 03/12/2020
ms.author: ericg
ms.service: app-service
ms.workload: web
ms.openlocfilehash: ad7e04d611129766fe9fb72285fe35ccfbb17626
ms.sourcegitcommit: 05a650752e9346b9836fe3ba275181369bd94cf0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/12/2020
ms.locfileid: "79136665"
---
# <a name="using-private-endpoints-for-azure-web-app-preview"></a>Usando pontos de extremidade privados para o aplicativo Web do Azure (versão prévia)

Você pode usar o ponto de extremidade privado para seu aplicativo Web do Azure para permitir que clientes localizados em sua rede privada acessem com segurança o aplicativo sobre o link privado. O ponto de extremidade privado usa um endereço IP do seu espaço de endereço de VNet do Azure. O tráfego de rede entre o cliente em sua rede privada e o aplicativo Web atravessa a vnet e um link privado na rede de backbone da Microsoft, eliminando a exposição da Internet pública.

Usar o ponto de extremidade privado para seu aplicativo Web permite que você:

- Proteja seu aplicativo Web Configurando o ponto de extremidade de serviço, eliminando a exposição pública
- Conecte-se com segurança ao aplicativo Web de redes locais que se conectam à vnet usando um emparelhamento privado VPN ou ExpressRoute.

Se você precisar apenas de uma conexão segura entre sua vnet e seu aplicativo Web, o ponto de extremidade de serviço será a solução mais simples. Se você também precisar acessar o aplicativo Web do local por meio de um gateway do Azure, uma vnet emparelhada regionalmente ou uma vnet emparelhada globalmente, o ponto de extremidade privado será a solução.  

Para obter mais informações sobre o [ponto de extremidade de serviço][serviceendpoint]

## <a name="conceptual-overview"></a>Visão geral conceitual

Um ponto de extremidade privado é uma NIC (interface de rede especial) para seu aplicativo Web do Azure em sua sub-rede em sua rede virtual (vnet).
Quando você cria um ponto de extremidade privado para seu aplicativo Web, ele fornece uma conectividade segura entre clientes em sua rede privada e seu aplicativo Web. O ponto de extremidade privado recebe um endereço IP do intervalo de endereços IP de sua vnet.
A conexão entre o ponto de extremidade privado e o aplicativo Web usa um [link privado][privatelink]seguro. O ponto de extremidade privado só é usado para fluxos de entrada para seu aplicativo Web. Os fluxos de saída não usarão esse ponto de extremidade privado, mas você pode injetar fluxos de saída para sua rede em uma sub-rede diferente por meio do [recurso de integração vnet][vnetintegrationfeature].

A sub-rede na qual você conecta o ponto de extremidade privado pode ter outros recursos, você não precisa de uma sub-rede vazia dedicada.
Você pode implantar um ponto de extremidade privado em uma região diferente do aplicativo Web. 

> [!Note]
>O recurso de integração vnet não pode usar a mesma sub-rede do que o ponto de extremidade privado, essa é uma limitação do recurso de integração vnet

Da perspectiva de segurança:

- Ao habilitar o ponto de extremidade de serviço para seu aplicativo Web, você desabilita todo o acesso público
- Você pode habilitar vários pontos de extremidade privados em outros Vnets e sub-redes, incluindo Vnets em outras regiões
- O endereço IP da NIC do ponto de extremidade privado deve ser dinâmico, mas permanecerá o mesmo até que você exclua o ponto de extremidade privado
- A NIC do ponto de extremidade privado não pode ter um NSG associado
- A sub-rede que hospeda o ponto de extremidade privado pode ter um NSG associado, mas você deve desabilitar a imposição de políticas de rede para o ponto de extremidade privado, consulte [Este artigo][disablesecuritype]. Como resultado, você não pode filtrar por nenhum NSG o acesso ao seu ponto de extremidade privado
- Quando você habilita o ponto de extremidade privado para seu aplicativo Web, a configuração de [restrições de acesso][accessrestrictions] do aplicativo Web não é avaliada.
- Você pode reduzir o risco de vazamento de dados da vnet removendo todas as regras do NSG em que o destino é marca Internet ou serviços do Azure. Mas adicionar um ponto de extremidade do serviço de aplicativo Web em sua sub-rede permitirá que você alcance qualquer aplicativo Web hospedado no mesmo carimbo e exposto à Internet.

O ponto de extremidade privado para o aplicativo Web está disponível para a camada PremiumV2 e isolado com um ASE externo.

Nos logs http da Web do seu aplicativo Web, você encontrará o IP de origem do cliente. Implementamos o protocolo proxy TCP, encaminhando para o aplicativo Web a propriedade IP do cliente. Para obter mais informações, consulte [este artigo][tcpproxy].

![Visão geral global][1]


## <a name="dns"></a>DNS

Como esse recurso está em versão prévia, não alteramos a entrada DNS durante a visualização. Você precisa gerenciar a entrada DNS em seu servidor DNS privado ou na zona privada DNS do Azure. Se você precisar usar um nome DNS personalizado, deverá adicionar o nome personalizado em seu aplicativo Web. Durante a visualização, o nome personalizado deve ser validado como qualquer nome personalizado, usando a resolução de DNS público. [referência técnica de validação de DNS personalizado][dnsvalidation]

## <a name="pricing"></a>Preços

Para obter detalhes de preço, confira [Preço do Link Privado do Azure][pricing].

## <a name="limitations"></a>Limitações

Estamos melhorando o recurso de link privado e o ponto de extremidade privado regularmente. consulte [Este artigo][pllimitations] para obter informações atualizadas sobre as limitações.

## <a name="next-steps"></a>Próximas etapas

Para implantar um ponto de extremidade privado para seu aplicativo Web por meio do portal, consulte [como conectar-se de forma privada a um aplicativo Web][howtoguide]


<!--Image references-->
[1]: ./media/private-endpoint/schemaglobaloverview.png

<!--Links-->
[serviceendpoint]: https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview
[privatelink]: https://docs.microsoft.com/azure/private-link/private-link-overview
[vnetintegrationfeature]: https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet
[disablesecuritype]: https://docs.microsoft.com/azure/private-link/disable-private-endpoint-network-policy
[accessrestrictions]: https://docs.microsoft.com/azure/app-service/app-service-ip-restrictions
[tcpproxy]: ../../private-link/private-link-service-overview.md#getting-connection-information-using-tcp-proxy-v2
[dnsvalidation]: https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-domain
[pllimitations]: https://docs.microsoft.com/azure/private-link/private-endpoint-overview#limitations
[pricing]: https://azure.microsoft.com/pricing/details/private-link/
[howtoguide]: https://docs.microsoft.com/azure/private-link/create-private-endpoint-webapp-portal
