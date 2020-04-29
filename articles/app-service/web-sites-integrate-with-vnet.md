---
title: Integrar o aplicativo à rede virtual do Azure
description: Integre o aplicativo no serviço de Azure App com redes virtuais do Azure.
author: ccompy
ms.assetid: 90bc6ec6-133d-4d87-a867-fcf77da75f5a
ms.topic: article
ms.date: 04/16/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 78b49b8b7e17f12d49825390a302e28a61e10d16
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81770850"
---
# <a name="integrate-your-app-with-an-azure-virtual-network"></a>Integrar seu aplicativo a uma rede virtual do Azure

Este artigo descreve o recurso de integração VNet de serviço Azure App e como configurá-lo com aplicativos no [serviço Azure app](https://go.microsoft.com/fwlink/?LinkId=529714). Com a [rede virtual do Azure][VNETOverview] (VNets), você pode inserir muitos dos seus recursos do Azure em uma rede não roteável para a Internet.

O serviço de Azure App tem duas variações:

[!INCLUDE [app-service-web-vnet-types](../../includes/app-service-web-vnet-types.md)]

## <a name="enable-vnet-integration"></a>Habilitar integração VNet

> [!NOTE]
> Se a folha "rede" estiver desabilitada (esmaecida) no menu para seus aplicativos do Linux, significa que o recurso não está disponível no momento.
>

1. Vá para a interface do usuário de **rede** no portal do serviço de aplicativo. Em **integração VNet**, selecione **clique aqui para configurar**.

1. Selecione **Adicionar VNet**.

   ![Selecionar integração VNet][1]

1. A lista suspensa contém todas as Azure Resource Manager redes virtuais em sua assinatura na mesma região. Abaixo disso está uma lista das redes virtuais do Resource Manager em todas as outras regiões. Selecione a VNet com a qual você deseja integrar.

   ![Selecione a VNet][2]

   * Se a VNet estiver na mesma região, crie uma nova sub-rede ou selecione uma sub-rede preexistente vazia.
   * Para selecionar uma VNet em outra região, você deve ter um gateway de VNet provisionado com o ponto a site habilitado.
   * Para integrar com uma VNet clássica, em vez de selecionar a lista suspensa **rede virtual** , selecione **clique aqui para se conectar a uma VNet clássica**. Selecione a rede virtual clássica desejada. A VNet de destino já deve ter um gateway de rede virtual provisionado com ponto a site habilitado.

    ![Selecionar VNet clássica][3]

Durante a integração, o aplicativo é reiniciado. Quando a integração for concluída, você verá detalhes sobre a VNet com a qual você está integrado.

## <a name="regional-vnet-integration"></a>Integração de VNet regional

[!INCLUDE [app-service-web-vnet-types](../../includes/app-service-web-vnet-regional.md)]

### <a name="how-regional-vnet-integration-works"></a>Como funciona a integração da VNet regional

Os aplicativos no serviço de aplicativo são hospedados em funções de trabalho. Os planos de preços básicos e mais altos são planos de hospedagem dedicados em que não há cargas de trabalho de outros clientes em execução nos mesmos trabalhadores. A integração VNet regional funciona por meio da montagem de interfaces virtuais com endereços na sub-rede delegada. Como o endereço de está em sua VNet, ele pode acessar a maioria das coisas de ou através de sua VNet como uma VM em sua VNet. A implementação de rede é diferente de executar uma VM em sua VNet. É por isso que alguns recursos de rede ainda não estão disponíveis para esse recurso.

![Como funciona a integração da VNet regional][5]

Quando a integração de VNet regional está habilitada, seu aplicativo faz chamadas de saída para a Internet por meio dos mesmos canais que o normal. Os endereços de saída listados no portal de propriedades do aplicativo são os endereços ainda usados pelo seu aplicativo. Quais alterações para seu aplicativo são chamadas para serviços protegidos de ponto de extremidade de serviço ou endereços RFC 1918 vão para sua VNet. Se WEBSITE_VNET_ROUTE_ALL for definido como 1, todo o tráfego de saída poderá ser enviado para sua VNet.

O recurso dá suporte a apenas uma interface virtual por trabalho. Uma interface virtual por trabalho significa uma integração VNet regional por plano do serviço de aplicativo. Todos os aplicativos no mesmo plano do serviço de aplicativo podem usar a mesma integração de VNet. Se precisar que um aplicativo se conecte a uma VNet adicional, você precisará criar outro plano do serviço de aplicativo. A interface virtual usada não é um recurso ao qual os clientes têm acesso direto.

Devido à natureza de como essa tecnologia Opera, o tráfego usado com a integração VNet não aparece no observador de rede do Azure ou nos logs de fluxo do NSG.

## <a name="gateway-required-vnet-integration"></a>Integração de VNet necessária ao gateway

O gateway-a integração VNet necessária dá suporte à conexão a uma VNet em outra região ou a uma rede virtual clássica. Integração de VNet necessária ao gateway:

* Permite que um aplicativo se conecte a apenas uma VNet por vez.
* Permite que até cinco VNets sejam integradas em um plano do serviço de aplicativo.
* Permite que a mesma VNet seja usada por vários aplicativos em um plano do serviço de aplicativo sem afetar o número total que pode ser usado por um plano do serviço de aplicativo. Se você tiver seis aplicativos usando a mesma VNet no mesmo plano do serviço de aplicativo, isso contará como uma VNet sendo usada.
* Dá suporte a um SLA de 99,9% devido ao SLA no gateway.
* Permite que seus aplicativos usem o DNS com o qual a VNet está configurada.
* Requer um gateway baseado em rota de rede virtual configurado com uma VPN ponto a site SSTP antes que possa ser conectado a um aplicativo.

Você não pode usar a integração VNet exigida pelo gateway:

* Com aplicativos do Linux.
* Com uma VNet conectada com o Azure ExpressRoute.
* Para acessar recursos protegidos do ponto de extremidade de serviço.
* Com um gateway de coexistência que dá suporte a VPNs de ExpressRoute e ponto a site ou site a site.

### <a name="set-up-a-gateway-in-your-azure-virtual-network"></a>Configurar um gateway em sua rede virtual do Azure ###

Para criar um gateway:

1. [Criar uma sub-rede de gateway][creategatewaysubnet] em sua VNet.  

1. [Crie o gateway de VPN][creategateway]. Selecione um tipo VPN baseado em rota.

1. [Defina os endereços de ponto a site][setp2saddresses]. Se o gateway não estiver no SKU básico, então o IKEV2 precisará ser desabilitado na configuração ponto a site e o SSTP deverá ser selecionado. O espaço de endereço de ponto a site deve estar nos blocos de endereço RFC 1918 10.0.0.0/8, 172.16.0.0/12 e 192.168.0.0/16.

Se você criar o gateway para uso com a integração VNet do serviço de aplicativo, não precisará carregar um certificado. A criação do gateway pode levar 30 minutos. Você não poderá integrar seu aplicativo à VNet até que o gateway seja provisionado.

### <a name="how-gateway-required-vnet-integration-works"></a>Como funciona a integração VNet exigida pelo gateway

A integração VNet exigida pelo gateway é criada sobre a tecnologia VPN ponto a site. As VPNs ponto a site limitam o acesso à rede para a máquina virtual que hospeda o aplicativo. Os aplicativos são restritos a enviar tráfego para a Internet somente por meio de Conexões Híbridas ou por meio da integração VNet. Quando seu aplicativo é configurado com o portal para usar a integração VNet exigida pelo gateway, uma negociação complexa é gerenciada em seu nome para criar e atribuir certificados no gateway e no lado do aplicativo. O resultado é que os trabalhos usados para hospedar seus aplicativos são capazes de se conectar diretamente ao gateway de rede virtual na VNet selecionada.

![Como funciona a integração VNet exigida pelo gateway][6]

### <a name="access-on-premises-resources"></a>Acessar recursos locais

Aplicativos podem acessar recursos locais ao integrarem-se com VNets que têm conexões site a site. Se você usar a integração VNet exigida por gateway, atualize suas rotas de gateway de VPN locais com os blocos de endereço ponto a site. Quando o VPN site a site é configurado pela primeira vez, os scripts usados para configurá-lo devem configurar as rotas adequadamente. Se adicionar os endereços ponto a site depois de criar uma VPN site a site, você precisará atualizar as rotas manualmente. Os detalhes sobre como fazer isso variam por gateway e não são descritos aqui. Você não pode ter o BGP configurado com uma conexão VPN site a site.

Nenhuma configuração adicional é necessária para que o recurso de integração VNet regional alcance por meio de sua VNet para recursos locais. Você simplesmente precisa conectar sua VNet a recursos locais usando o ExpressRoute ou uma VPN site a site.

> [!NOTE]
> O recurso de integração VNet exigida pelo gateway não integra um aplicativo com uma VNet que tem um gateway de ExpressRoute. Mesmo que o gateway de ExpressRoute esteja configurado no [modo de coexistência][VPNERCoex], a integração VNet não funciona. Se você precisar acessar recursos por meio de uma conexão do ExpressRoute, use o recurso de integração VNet regional ou um [ambiente do serviço de aplicativo][ASE], que é executado em sua VNet.
> 
> 

### <a name="peering"></a>Emparelhamento

Se você usar o emparelhamento com a integração VNet regional, não precisará fazer nenhuma configuração adicional.

Se você usar a integração VNet exigida por gateway com o emparelhamento, será necessário configurar alguns itens adicionais. Para configurar o emparelhamento para funcionar com o aplicativo:

1. Adicione que uma conexão de emparelhamento na rede virtual à qual o aplicativo se conecta. Ao adicionar a conexão de emparelhamento, habilite **permitir acesso à rede virtual** e selecione **Permitir tráfego encaminhado** e **Permitir trânsito de gateway**.
1. Adicione uma conexão de emparelhamento na VNet que está sendo emparelhada com a VNet à qual você está conectado. Ao adicionar a conexão de emparelhamento na VNet de destino, habilite **permitir acesso à rede virtual** e selecione **Permitir tráfego encaminhado** e **permitir gateways remotos**.
1. Vá até o **plano** > **Networking** > do serviço de aplicativo interface do usuário**integração VNet** no Portal. Selecione a VNet à qual o aplicativo se conecta. Na seção roteamento, adicione o intervalo de endereços da VNet emparelhada com a VNet à qual seu aplicativo está conectado.

## <a name="manage-vnet-integration"></a>Gerenciar integração VNet

A conexão e a desconexão com uma VNet estão em um nível de aplicativo. As operações que podem afetar a integração VNet entre vários aplicativos estão no nível do plano do serviço de aplicativo. No portal de**integração VNet** de **rede** > > de aplicativos, você pode obter detalhes sobre sua VNet. Você pode ver informações semelhantes no nível do plano do serviço de aplicativo **no plano** > do serviço de aplicativo portal de integração de**rede** > **virtual** .

A única operação que você pode executar na exibição do aplicativo da sua instância de integração VNet é desconectar seu aplicativo da VNet à qual ele está conectado no momento. Para desconectar o aplicativo de uma VNet, selecione **Desconectar**. Seu aplicativo é reiniciado quando você se desconecta de uma VNet. Desconectar-se não altera a VNet. A sub-rede ou o gateway não é removido. Se você quiser excluir sua VNet, primeiro desconecte seu aplicativo da VNet e exclua os recursos nele, como gateways.

A interface de usuário de integração VNet do plano do serviço de aplicativo mostra todas as integrações VNet usadas pelos aplicativos em seu plano do serviço de aplicativo. Para ver os detalhes em cada VNet, selecione a VNet em que você está interessado. Há duas ações que você pode executar aqui para integração VNet exigida pelo gateway:

* **Rede de sincronização**: a operação de sincronização de rede é usada somente para o recurso de integração VNet dependente de gateway. A execução de uma operação de rede de sincronização garante que os certificados e as informações de rede estejam em sincronia. Se você adicionar ou alterar o DNS de sua VNet, execute uma operação de sincronização de rede. Esta operação reinicia todos os aplicativos que usam essa VNet.
* **Adicionar rotas**: adicionar rotas conduz o tráfego de saída em sua VNet.

### <a name="gateway-required-vnet-integration-routing"></a>Roteamento de integração VNet exigido pelo gateway
As rotas que são definidas em sua VNet são usadas para direcionar o tráfego para sua VNet do seu aplicativo. Para enviar tráfego de saída adicional para a VNet, adicione esses blocos de endereço aqui. Essa funcionalidade só funciona com a integração VNet exigida pelo Gateway. As tabelas de rotas não afetam o tráfego do aplicativo quando você usa a integração VNet exigida pelo gateway da maneira que fazem com a integração VNet regional.

### <a name="gateway-required-vnet-integration-certificates"></a>Certificados de integração VNet necessários ao gateway
Quando a integração VNet exigida pelo Gateway está habilitada, há uma troca necessária de certificados para garantir a segurança da conexão. Juntamente com os certificados, há a configuração do DNS, as rotas e outros itens semelhantes que descrevem a rede.

Se os certificados ou as informações de rede forem alterados, selecione **sincronizar rede**. Ao selecionar **sincronizar rede**, você causa uma breve interrupção na conectividade entre seu aplicativo e sua VNet. Embora seu aplicativo não seja reiniciado, a perda de conectividade pode fazer com que seu site não funcione corretamente.

## <a name="pricing-details"></a>Detalhes de preço
O recurso de integração VNet regional não tem nenhum custo adicional para uso além dos encargos do plano do serviço de aplicativo.

Três encargos estão relacionados ao uso do recurso de integração VNet exigido pelo gateway:

* **Cobranças de tipo de preço do plano do serviço de aplicativo**: seus aplicativos precisam estar em um plano do serviço de aplicativo Standard, Premium ou PremiumV2. Para obter mais informações sobre esses custos, consulte [preços do serviço de aplicativo][ASPricing].
* **Custos de transferência de dados**: há um encargo para saída de dados, mesmo que a VNet esteja no mesmo datacenter. Esses encargos são descritos em [transferência de dados detalhes de preços][DataPricing].
* **Custos de gateway de VPN**: há um custo para o gateway de rede virtual necessário para a VPN ponto a site. Para obter mais informações, consulte [preços do gateway de VPN][VNETPricing].

## <a name="troubleshooting"></a>Solução de problemas

[!INCLUDE [app-service-web-vnet-troubleshooting](../../includes/app-service-web-vnet-troubleshooting.md)]

## <a name="automation"></a>Automação

O suporte à CLI está disponível para integração de VNet regional. Para acessar os comandos a seguir, [Instale o CLI do Azure][installCLI].

        az webapp vnet-integration --help

        Group
            az webapp vnet-integration : Methods that list, add, and remove virtual network integrations
            from a webapp.
                This command group is in preview. It may be changed/removed in a future release.
        Commands:
            add    : Add a regional virtual network integration to a webapp.
            list   : List the virtual network integrations on a webapp.
            remove : Remove a regional virtual network integration from webapp.

        az appservice vnet-integration --help

        Group
            az appservice vnet-integration : A method that lists the virtual network integrations used in an
            appservice plan.
                This command group is in preview. It may be changed/removed in a future release.
        Commands:
            list : List the virtual network integrations used in an appservice plan.

Para integração VNet exigida pelo gateway, você pode integrar o serviço de aplicativo com uma rede virtual do Azure usando o PowerShell. Para um script pronto para execução, consulte [conectar um aplicativo no serviço de Azure app a uma rede virtual do Azure](https://gallery.technet.microsoft.com/scriptcenter/Connect-an-app-in-Azure-ab7527e3).


<!--Image references-->
[1]: ./media/web-sites-integrate-with-vnet/vnetint-app.png
[2]: ./media/web-sites-integrate-with-vnet/vnetint-addvnet.png
[3]: ./media/web-sites-integrate-with-vnet/vnetint-classic.png
[5]: ./media/web-sites-integrate-with-vnet/vnetint-regionalworks.png
[6]: ./media/web-sites-integrate-with-vnet/vnetint-gwworks.png


<!--Links-->
[VNETOverview]: https://azure.microsoft.com/documentation/articles/virtual-networks-overview/ 
[AzurePortal]: https://portal.azure.com/
[ASPricing]: https://azure.microsoft.com/pricing/details/app-service/
[VNETPricing]: https://azure.microsoft.com/pricing/details/vpn-gateway/
[DataPricing]: https://azure.microsoft.com/pricing/details/data-transfers/
[V2VNETP2S]: https://azure.microsoft.com/documentation/articles/vpn-gateway-howto-point-to-site-rm-ps/
[ILBASE]: environment/create-ilb-ase.md
[V2VNETPortal]: ../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md
[VPNERCoex]: ../expressroute/expressroute-howto-coexist-resource-manager.md
[ASE]: environment/intro.md
[creategatewaysubnet]: ../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md#creategw
[creategateway]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal#creategw
[setp2saddresses]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal#addresspool
[VNETRouteTables]: https://docs.microsoft.com/azure/virtual-network/manage-route-table/
[installCLI]: https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest/
[privateendpoints]: networking/private-endpoint.md
