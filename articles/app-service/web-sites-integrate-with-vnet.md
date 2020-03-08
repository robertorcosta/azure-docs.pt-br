---
title: Integrar o aplicativo à rede virtual do Azure
description: Integre aplicativos no serviço de Azure App com redes virtuais do Azure.
author: ccompy
ms.assetid: 90bc6ec6-133d-4d87-a867-fcf77da75f5a
ms.topic: article
ms.date: 02/27/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 89aa78e0d26598eacf436ca88cc6c5549f91d2fc
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78673218"
---
# <a name="integrate-your-app-with-an-azure-virtual-network"></a>Integrar seu aplicativo Web a uma Rede Virtual do Azure
Este documento descreve o recurso de integração de rede virtual do serviço Azure App e como configurá-lo com aplicativos no [serviço Azure app](https://go.microsoft.com/fwlink/?LinkId=529714). As [redes virtuais do Azure][VNETOverview] (VNets) permitem que você coloque muitos dos seus recursos do Azure em uma rede roteável que não seja da Internet.  

O serviço de Azure App tem duas variações.

[!INCLUDE [app-service-web-vnet-types](../../includes/app-service-web-vnet-types.md)]

## <a name="enable-vnet-integration"></a>Habilitar integração VNet 

1. Vá para a interface do usuário de rede no portal do serviço de aplicativo. Em integração VNet, selecione *"clique aqui para configurar"* . 

1. Selecione **Adicionar VNet**.  

   ![Selecionar integração VNet][1]

1. A lista suspensa conterá todos os VNets do Resource Manager em sua assinatura na mesma região e abaixo, que é uma lista de todos os VNets do Resource Manager em todas as outras regiões. Selecione a VNet com a qual você deseja integrar.

   ![Selecione a VNet][2]

   * Se a VNet estiver na mesma região, crie uma nova sub-rede ou escolha uma sub-rede preexistente vazia. 

   * Para selecionar uma VNet em outra região, você deve ter um gateway de rede virtual provisionado com o ponto a site habilitado.

   * Para integrar com uma VNet clássica, em vez de clicar no menu suspenso VNet, selecione **clique aqui para se conectar a uma vnet clássica**. Selecione a VNet clássica desejada. A VNet de destino já deve ter um gateway de rede virtual provisionado com o ponto a site habilitado.

    ![Selecionar VNet clássica][3]
    
Durante a integração, o aplicativo é reiniciado.  Quando a integração for concluída, você verá detalhes sobre a VNet com a qual está integrado. 

Depois que seu aplicativo estiver integrado à sua VNet, ele usará o mesmo servidor DNS com o qual sua VNet está configurada, a menos que seja Zonas Privadas do DNS do Azure. No momento, você não pode usar a integração VNet com Zonas Privadas do DNS do Azure.

## <a name="regional-vnet-integration"></a>Integração de VNet regional

[!INCLUDE [app-service-web-vnet-types](../../includes/app-service-web-vnet-regional.md)]

### <a name="how-regional-vnet-integration-works"></a>Como funciona a integração da VNet regional

Os aplicativos no serviço de aplicativo são hospedados em funções de trabalho. Os planos de preços básicos e mais altos são planos de hospedagem dedicados em que não há nenhuma outra carga de trabalho de clientes em execução nos mesmos trabalhadores. A integração VNet regional funciona por meio da montagem de interfaces virtuais com endereços na sub-rede delegada. Como o endereço de está em sua VNet, ele pode acessar a maioria das coisas de ou através de sua VNet, assim como uma VM em sua VNet. A implementação de rede é diferente de executar uma VM em sua VNet e é por isso que alguns recursos de rede ainda não estão disponíveis ao usar esse recurso.

![Como funciona a integração da VNet regional][5]

Quando a integração VNet regional estiver habilitada, seu aplicativo ainda fará chamadas de saída para a Internet por meio dos mesmos canais que o normal. Os endereços de saída listados no portal de propriedades do aplicativo ainda são os endereços usados pelo seu aplicativo. As alterações para seu aplicativo são chamadas para serviços protegidos de ponto de extremidade de serviço ou endereços RFC 1918 vão para sua VNet. Se WEBSITE_VNET_ROUTE_ALL for definido como 1, todo o tráfego de saída poderá ser enviado para sua VNet. 

O recurso só dá suporte a uma interface virtual por trabalho.  Uma interface virtual por trabalho significa uma integração VNet regional por plano do serviço de aplicativo. Todos os aplicativos no mesmo plano do serviço de aplicativo podem usar a mesma integração de VNet, mas se você precisar de um aplicativo para se conectar a uma VNet adicional, será necessário criar outro plano do serviço de aplicativo. A interface virtual usada não é um recurso ao qual os clientes têm acesso direto.

Devido à natureza de como essa tecnologia Opera, o tráfego usado com a integração VNet não aparece no observador de rede ou nos logs de fluxo do NSG.  

## <a name="gateway-required-vnet-integration"></a>Integração VNet necessária do gateway

A integração VNet necessária do gateway dá suporte à conexão a uma VNet em outra região ou a uma VNet clássica. Integração VNet necessária do gateway: 

* Permite que um aplicativo se conecte a apenas uma VNet por vez
* Permite que até cinco VNets sejam integrados em um plano do serviço de aplicativo 
* Permite que a mesma VNet seja usada por vários aplicativos em um plano do serviço de aplicativo sem afetar o número total que pode ser usado por um plano do serviço de aplicativo.  Se você tiver seis aplicativos usando a mesma VNet no mesmo plano do serviço de aplicativo, isso contará como uma VNet sendo usada. 
* Dá suporte a um SLA de 99,9% devido ao SLA no gateway
* Permite que seus aplicativos usem o DNS com o qual a VNet está configurada
* Requer um gateway baseado em rota de rede virtual configurado com VPN ponto a site SSTP antes que possa ser conectado ao aplicativo. 

Você não pode usar a integração VNet necessária do gateway:

* Com aplicativos do Linux
* Com uma VNet conectada com o ExpressRoute 
* Para acessar recursos protegidos dos pontos de extremidade de serviço
* Com um gateway de coexistência que dá suporte a VPNs de ExpressRoute e ponto a site/site a site

### <a name="set-up-a-gateway-in-your-vnet"></a>Configurar um gateway em sua VNet ###

Para criar um gateway:

1. [Crie uma sub-rede de gateway][creategatewaysubnet] em sua VNet.  

1. [Crie o gateway de VPN][creategateway]. Selecione um tipo VPN baseado em rota.

1. [Defina os endereços ponto a site][setp2saddresses]. Se o gateway não estiver no SKU básico, o IKEV2 deverá ser desabilitado na configuração ponto a site e o SSTP deverá ser selecionado. O espaço de endereço ponto a site deve estar nos blocos de endereço RFC 1918, 10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16

Se você estiver criando apenas o gateway para uso com a integração VNet do serviço de aplicativo, não será necessário carregar um certificado. A criação do gateway pode levar 30 minutos. Você não poderá integrar seu aplicativo com sua VNet até que o gateway seja provisionado. 

### <a name="how-gateway-required-vnet-integration-works"></a>Como funciona a integração VNet do gateway

O gateway exigia integração VNet criada com base na tecnologia de VPN ponto a site. As VPNs ponto a site limitam o acesso à rede apenas à máquina virtual que hospeda o aplicativo. Os aplicativos são restritos apenas ao envio de tráfego para a Internet, por meio de Conexões Híbridas ou por meio da Integração VNet. Quando seu aplicativo é configurado com o portal para usar a integração VNet necessária do gateway, uma negociação complexa é gerenciada em seu nome para criar e atribuir certificados no gateway e no lado do aplicativo. O resultado final é que os trabalhos usados para hospedar seus aplicativos podem se conectar diretamente ao gateway de rede virtual na VNet selecionada. 

![Como funciona a integração VNet do gateway][6]

### <a name="accessing-on-premises-resources"></a>Como acessar recursos locais

Aplicativos podem acessar recursos locais ao integrarem-se com VNets que têm conexões site a site. Se estiver usando a integração VNet necessária do gateway, você precisará atualizar suas rotas de gateway de VPN locais com seus blocos de endereço ponto a site. Quando o VPN site a site é configurado pela primeira vez, os scripts usados para configurá-lo devem configurar as rotas adequadamente. Se adicionar os endereços ponto a site depois de criar uma VPN site a site, você precisará atualizar as rotas manualmente. Os detalhes sobre como fazer isso variam de acordo com o gateway, e não são descritos aqui. Você não pode ter o BGP configurado com uma conexão VPN site a site.

Não há nenhuma configuração adicional necessária para que o recurso de integração VNet regional alcance por meio de sua VNet e para o local. Você simplesmente precisa conectar sua VNet ao local usando o ExpressRoute ou uma VPN site a site. 

> [!NOTE]
> O recurso de integração VNet do gateway necessário não integra um aplicativo a uma VNet que tem um gateway de ExpressRoute. Mesmo que o gateway de ExpressRoute esteja configurado no [modo de coexistência][VPNERCoex] , a integração VNet não funciona. Se você precisar acessar recursos por meio de uma conexão do ExpressRoute, poderá usar o recurso de integração VNet regional ou um [ambiente do serviço de aplicativo][ASE], que é executado em sua VNet. 
> 
> 

### <a name="peering"></a>Emparelhamento

Se você estiver usando o emparelhamento com a integração VNet regional, não será necessário fazer nenhuma configuração adicional. 

Se estiver usando a integração VNet necessária do gateway com o emparelhamento, você precisará configurar alguns itens adicionais. Para configurar o emparelhamento para funcionar com o aplicativo:

1. Adicione que uma conexão de emparelhamento na rede virtual à qual o aplicativo se conecta. Ao adicionar a conexão de emparelhamento, habilite **Permitir acesso à rede virtual** e marque **Permitir tráfego encaminhado** e **Permitir trânsito de gateway**.
1. Adicione uma conexão de emparelhamento na rede virtual que está sendo emparelhada à rede virtual à qual você está conectado. Ao adicionar a conexão de emparelhamento à VNet de destino, habilite **Permitir acesso à rede virtual** e marque **Permitir tráfego encaminhado** e **Permitir gateways remotos**.
1. Vá para o Plano do Serviço de Aplicativo > Rede > Interface do usuário da Integração VNet no portal.  Selecione a VNet à qual o aplicativo se conecta. Na seção de roteamento, adicione o intervalo de endereços da VNet que está emparelhada com a VNet à qual o aplicativo está conectado.  

## <a name="managing-vnet-integration"></a>Gerenciando a integração VNet 

A conexão e a desconexão com uma VNet estão em um nível de aplicativo. As operações que podem afetar a Integração VNet entre vários aplicativos estão no nível do Plano do Serviço de Aplicativo. No aplicativo > rede > Portal de integração VNet, você pode obter detalhes sobre sua VNet. Você pode ver informações semelhantes no nível ASP no portal de integração de VNet > rede > ASP.

A única operação que você pode executar no modo de exibição de aplicativo da Integração VNet é desconectar-se da VNet à qual seu aplicativo está atualmente conectado. Para desconectar o aplicativo de uma VNet, selecione **Desconectar**. Seu aplicativo será reiniciado quando você se desconectar de uma VNet. Desconectar-se não altera a VNet. A sub-rede ou o gateway não é removido. Se você quiser excluir sua VNet, primeiro você precisa desconectar seu aplicativo da VNet e excluir os recursos nele, como gateways. 

A interface do usuário de integração da VNet ASP mostrará todas as integrações de VNet usadas pelos aplicativos em seu ASP. Para ver detalhes em cada VNet, clique na VNet que lhe interessa. Há duas ações que você pode executar aqui para a integração VNet necessária do gateway.

* **Sincronizar rede**. A operação de rede de sincronização é apenas para o recurso de integração VNet dependente de gateway. A execução de uma operação de rede de sincronização garante que os certificados e as informações de rede estejam em sincronia. Se você adicionar ou alterar o DNS de sua VNet, será necessário executar uma operação de **sincronização de rede** . Esta operação reiniciará todos os aplicativos usando essa VNet.
* **Adicionar rotas** A adição de rotas orientará o tráfego de saída em sua VNet. 

**Roteamento de integração VNet do gateway necessário** As rotas que são definidas em sua VNet são usadas para direcionar o tráfego para sua VNet do seu aplicativo. Se você precisar enviar o tráfego de saída adicional para a VNet, você poderá adicionar esses blocos de endereço aqui. Essa funcionalidade só funciona com a integração VNet necessária do gateway. As tabelas de rotas não afetam o tráfego do aplicativo ao usar a integração VNet necessária do gateway da maneira que fazem com a integração VNet regional.

**Certificados de integração VNet necessários ao gateway** Quando o gateway exigia a integração VNet habilitada, há uma troca necessária de certificados para garantir a segurança da conexão. Juntamente com os certificados, há a configuração do DNS, as rotas e outros itens semelhantes que descrevem a rede.
Se os certificados ou as informações de rede forem alterados, você precisará clicar em "Sincronizar Rede". Quando clica em "Sincronizar Rede", você causa uma breve interrupção na conectividade entre o aplicativo e a VNet. Embora seu aplicativo não seja reiniciado, a perda de conectividade pode fazer com que seu site não funcione corretamente. 

## <a name="pricing-details"></a>Detalhes de preço
O recurso de integração VNet regional não tem nenhum custo adicional para uso além dos encargos do tipo de preço ASP.

Há três encargos relacionados ao uso do recurso de integração VNet exigido pelo gateway:

* Cobranças do tipo de preço do ASP – seus aplicativos precisam estar em um plano do serviço de aplicativo Standard, Premium ou PremiumV2. Você pode ver mais detalhes sobre esses custos aqui: [preços do serviço de aplicativo][ASPricing]. 
* Custos de transferência de dados – há um encargo para a saída de dados, mesmo que a VNet esteja na mesma data center. Esses encargos são descritos em [transferência de dados detalhes de preços][DataPricing]. 
* Custos de gateway de VPN – há um custo para o gateway de VNet que é necessário para a VPN ponto a site. Os detalhes estão na página de [preços do gateway de VPN][VNETPricing] .

## <a name="troubleshooting"></a>solução de problemas

[!INCLUDE [app-service-web-vnet-troubleshooting](../../includes/app-service-web-vnet-troubleshooting.md)]

## <a name="automation"></a>Automação

Há suporte de CLI para integração de VNet regional. Para acessar os comandos a seguir, [instale CLI do Azure][installCLI]. 

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

Para a integração VNet necessária do gateway, você pode integrar o serviço de aplicativo com uma rede virtual do Azure usando o PowerShell. Para um script pronto para uso, consulte [Conectar um aplicativo do Azure no Serviço de Aplicativo do Azure a uma Rede Virtual do Azure](https://gallery.technet.microsoft.com/scriptcenter/Connect-an-app-in-Azure-ab7527e3).


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
