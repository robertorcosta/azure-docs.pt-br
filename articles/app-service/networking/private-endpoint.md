---
title: Conectar-se de forma privada a um aplicativo Web usando o ponto de extremidade privado do Azure
description: Conectar-se de forma privada a um aplicativo Web usando o ponto de extremidade privado do Azure
author: ericgre
ms.assetid: 2dceac28-1ba6-4904-a15d-9e91d5ee162c
ms.topic: article
ms.date: 05/12/2020
ms.author: ericg
ms.service: app-service
ms.workload: web
ms.custom: fasttrack-edit
ms.openlocfilehash: 6a95c021153a458a4e3f804e64724b73ea1f1937
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83198811"
---
# <a name="using-private-endpoints-for-azure-web-app-preview"></a>Usando pontos de extremidade privados para o aplicativo Web do Azure (versão prévia)

> [!Note]
> A versão prévia está disponível nas regiões leste dos EUA e oeste dos EUA 2 para todos os aplicativos Web PremiumV2 do Windows e Linux e funções Premium elásticos. 

Você pode usar o ponto de extremidade privado para seu aplicativo Web do Azure para permitir que clientes localizados em sua rede privada acessem com segurança o aplicativo sobre o link privado. O ponto de extremidade privado usa um endereço IP do seu espaço de endereço de VNet do Azure. O tráfego de rede entre um cliente na sua rede privada e o aplicativo Web atravessa a VNet e um link privado na rede de backbone da Microsoft, eliminando a exposição da Internet pública.

Usar o ponto de extremidade privado para seu aplicativo Web permite que você:

- Proteja seu aplicativo Web Configurando o ponto de extremidade privado, eliminando a exposição pública.
- Conecte-se com segurança ao aplicativo Web de redes locais que se conectam à VNet usando um emparelhamento privado VPN ou ExpressRoute.

Se você precisar apenas de uma conexão segura entre sua VNet e seu aplicativo Web, um ponto de extremidade de serviço será a solução mais simples. Se você também precisar acessar o aplicativo Web do local por meio de um gateway do Azure, uma VNet emparelhada regionalmente ou uma VNet emparelhada globalmente, o ponto de extremidade privado será a solução.  

Para obter mais informações, consulte [pontos de extremidade de serviço][serviceendpoint].

## <a name="conceptual-overview"></a>Visão geral conceitual

Um ponto de extremidade privado é uma NIC (interface de rede especial) para seu aplicativo Web do Azure em uma sub-rede em sua rede virtual (VNet).
Quando você cria um ponto de extremidade privado para seu aplicativo Web, ele fornece conectividade segura entre clientes em sua rede privada e seu aplicativo Web. O ponto de extremidade privado recebe um endereço IP do intervalo de endereços IP de sua VNet.
A conexão entre o ponto de extremidade privado e o aplicativo Web usa um [link privado][privatelink]seguro. O ponto de extremidade privado só é usado para fluxos de entrada para seu aplicativo Web. Os fluxos de saída não usarão esse ponto de extremidade privado, mas você pode injetar fluxos de saída para sua rede em uma sub-rede diferente por meio do [recurso de integração VNet][vnetintegrationfeature].

A sub-rede na qual você conecta o ponto de extremidade privado pode ter outros recursos, você não precisa de uma sub-rede vazia dedicada.
Você também pode implantar o ponto de extremidade privado em uma região diferente do aplicativo Web. 

> [!Note]
>O recurso de integração VNet não pode usar a mesma sub-rede do que o ponto de extremidade privado, essa é uma limitação do recurso de integração VNet.

Do ponto de vista da segurança:

- Ao habilitar pontos de extremidade privados para seu aplicativo Web, você desabilita todo o acesso público.
- Você pode habilitar vários pontos de extremidade privados em outros VNets e sub-redes, incluindo VNets em outras regiões.
- O endereço IP da NIC do ponto de extremidade privado deve ser dinâmico, mas permanecerá o mesmo até que você exclua o ponto de extremidade privado.
- A NIC do ponto de extremidade privado não pode ter um NSG associado.
- A sub-rede que hospeda o ponto de extremidade privado pode ter um NSG associado, mas você deve desabilitar a imposição de políticas de rede para o ponto de extremidade privado: consulte [desabilitar políticas de rede para pontos de extremidades privados][disablesecuritype]. Como resultado, você não pode filtrar por nenhum NSG o acesso ao seu ponto de extremidade privado.
- Quando você habilita o ponto de extremidade privado para seu aplicativo Web, a configuração de [restrições de acesso][accessrestrictions] do aplicativo Web não é avaliada.
- Você pode reduzir o risco de vazamento de dados da VNet removendo todas as regras de NSG em que o destino é marca Internet ou serviços do Azure. Mas adicionar um ponto de extremidade particular do aplicativo Web em sua sub-rede permitirá que você alcance qualquer aplicativo Web hospedado no mesmo carimbo de implantação e exposto à Internet.

Nos logs HTTP da Web do seu aplicativo Web, você encontrará o IP de origem do cliente. Isso é implementado usando o protocolo proxy TCP, encaminhando a propriedade IP do cliente para o aplicativo Web. Para obter mais informações, consulte [obtendo informações de conexão usando o proxy TCP v2][tcpproxy].


  > [!div class="mx-imgBorder"]
  > ![Visão geral global do ponto de extremidade particular do aplicativo Web](media/private-endpoint/global-schema-web-app.png)

## <a name="dns"></a>DNS

Como esse recurso está em versão prévia, não alteramos a entrada DNS durante a visualização. Você mesmo precisa gerenciar a entrada DNS no seu servidor DNS privado ou na zona privada do DNS do Azure.
Se você precisar usar um nome DNS personalizado, deverá adicionar o nome personalizado em seu aplicativo Web. Durante a visualização, o nome personalizado deve ser validado como qualquer nome personalizado, usando a resolução de DNS público. Para obter mais informações, consulte [validação de DNS personalizado][dnsvalidation].

Se você precisar usar o console do kudu ou a API REST do kudu (implantação com agentes do Azure DevOps auto-hospedados, por exemplo), será necessário criar dois registros na zona privada do DNS do Azure ou no servidor DNS personalizado. 
- PrivateEndpointIP yourwebappname.azurewebsites.net 
- PrivateEndpointIP yourwebappname.scm.azurewebsites.net 

## <a name="pricing"></a>Preços

Para obter detalhes de preço, confira [Preço do Link Privado do Azure][pricing].

## <a name="limitations"></a>Limitações

Quando você usa o Azure function no plano Premium elástico com o ponto de extremidade privado, para executar ou executar a função no portal da Web do Azure, você deve ter acesso direto à rede ou receberá um erro HTTP 403. Em outras palavras, seu navegador deve ser capaz de acessar o ponto de extremidade privado para executar a função no portal da Web do Azure. 

Durante a visualização, apenas o slot de produção é exposto por trás do ponto de extremidade privado, outros slots podem ser acessados somente pelo ponto de extremidade público.

Estamos melhorando o recurso de link privado e o ponto de extremidade privado regularmente. consulte [Este artigo][pllimitations] para obter informações atualizadas sobre as limitações.

## <a name="next-steps"></a>Próximas etapas

Para implantar um ponto de extremidade privado para seu aplicativo Web por meio do portal, consulte [como conectar-se de forma privada a um aplicativo Web][howtoguide]




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
