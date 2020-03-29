---
title: Integrar aplicativo com a Rede Virtual Azure
description: Integre aplicativos no Azure App Service com redes virtuais do Azure.
author: ccompy
ms.assetid: 90bc6ec6-133d-4d87-a867-fcf77da75f5a
ms.topic: article
ms.date: 02/27/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 89aa78e0d26598eacf436ca88cc6c5549f91d2fc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78673218"
---
# <a name="integrate-your-app-with-an-azure-virtual-network"></a>Integrar seu aplicativo Web a uma Rede Virtual do Azure
Este documento descreve o recurso de integração de rede virtual do Azure App Service e como configurá-lo com aplicativos no [Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714). As Redes Virtuais (VNets) do [Azure][VNETOverview] permitem que você coloque muitos de seus recursos do Azure em uma rede não-internet routable.  

O Serviço de Aplicativos Azure tem duas variações.

[!INCLUDE [app-service-web-vnet-types](../../includes/app-service-web-vnet-types.md)]

## <a name="enable-vnet-integration"></a>Habilite a integração vnet 

1. Vá para a UI de Rede no portal app service. Em VNet Integration, selecione *"Clique aqui para configurar"*. 

1. Selecione **Adicionar VNet**.  

   ![Selecione a Integração VNet][1]

1. A lista de sossegos conterá todos os VNets do Gerenciador de Recursos em sua assinatura na mesma região e abaixo está uma lista de todos os VNets do Gerenciador de Recursos em todas as outras regiões. Selecione o VNet com o que deseja integrar.

   ![Selecione o VNet][2]

   * Se o VNet estiver na mesma região, crie uma nova sub-rede ou escolha uma sub-rede pré-existente vazia. 

   * Para selecionar um VNet em outra região, você deve ter um gateway de Rede Virtual provisionado com ponto a ponto ativado.

   * Para se integrar a um VNet clássico, em vez de clicar no vnet drop down, selecione **Clique aqui para se conectar a um VNet clássico**. Selecione o VNet clássico desejado. O VNet de destino já deve ter um gateway de Rede Virtual provisionado com ponto para local ativado.

    ![Selecione VNet clássico][3]
    
Durante a integração, o aplicativo é reiniciado.  Quando a integração estiver concluída, você verá detalhes no VNet com o qual está integrado. 

Uma vez que seu aplicativo esteja integrado ao seu VNet, ele usará o mesmo servidor DNS com o que seu VNet está configurado, a menos que seja a Azure DNS Private Zones. Atualmente, você não pode usar a Integração VNet com as Zonas Privadas do Azure DNS.

## <a name="regional-vnet-integration"></a>Integração VNet Regional

[!INCLUDE [app-service-web-vnet-types](../../includes/app-service-web-vnet-regional.md)]

### <a name="how-regional-vnet-integration-works"></a>Como funciona a Integração VNet Regional

Os aplicativos no Serviço de Aplicativo estão hospedados nas funções dos trabalhadores. Os planos básicos e de preços mais altos são planos de hospedagem dedicados onde não há outras cargas de trabalho de clientes sendo executados nos mesmos trabalhadores. A Integração VNet Regional funciona montando interfaces virtuais com endereços na sub-rede delegada. Como o endereço de partir está em seu VNet, ele pode acessar a maioria das coisas dentro ou através de seu VNet, assim como uma VM em seu VNet faria. A implementação de rede é diferente de executar uma VM em seu VNet e é por isso que alguns recursos de rede ainda não estão disponíveis durante o uso desse recurso.

![Como funciona a Integração VNet regional][5]

Quando a Integração VNet regional estiver ativada, seu aplicativo ainda fará chamadas de saída para a internet através dos mesmos canais que o normal. Os endereços de saída listados no portal de propriedades do aplicativo ainda são os endereços usados pelo seu aplicativo. Quais são as mudanças para o seu aplicativo, chamadas para serviços garantidos de ponto final ou endereços RFC 1918 vão para o seu VNet. Se WEBSITE_VNET_ROUTE_ALL estiver definido como 1, todo o tráfego de saída pode ser enviado para o seu VNet. 

O recurso suporta apenas uma interface virtual por trabalhador.  Uma interface virtual por trabalhador significa um plano regional de Integração VNet por Serviço de Aplicativo. Todos os aplicativos no mesmo plano de Serviço de Aplicativo podem usar a mesma Integração VNet, mas se você precisar de um aplicativo para se conectar a um VNet adicional, você precisará criar outro plano de Serviço de Aplicativo. A interface virtual utilizada não é um recurso ao que os clientes têm acesso direto.

Devido à natureza de como essa tecnologia opera, o tráfego que é usado com a Integração VNet não aparece em registros de fluxo do Network Watcher ou DO NSG.  

## <a name="gateway-required-vnet-integration"></a>Gateway necessário VNet Integração

O Gateway necessário é necessário Para que o VNet Integration seja conectado a um VNet em outra região ou a um VNet clássico. Integração VNet necessária do Gateway: 

* Permite que um aplicativo se conecte a apenas 1 VNet por vez
* Permite que até cinco VNets sejam integrados dentro de um Plano de Serviço de Aplicativos 
* Permite que o mesmo VNet seja usado por vários aplicativos em um Plano de Serviço de Aplicativo sem afetar o número total que pode ser usado por um plano de Serviço de Aplicativo.  Se você tem seis aplicativos usando o mesmo VNet no mesmo plano de Serviço de Aplicativo, isso conta como 1 VNet sendo usado. 
* Suporta um SLA de 99,9% devido ao SLA no gateway
* Permite que seus aplicativos usem o DNS com o que o VNet está configurado
* Requer um gateway baseado em rota de rede virtual configurado com VPN ponto a ponto sSTP antes de ser conectado ao aplicativo. 

Você não pode usar a integração VNet necessária do gateway:

* Com aplicativos Linux
* Com um VNet conectado com expressroute 
* Para acessar os recursos protegidos do Service Endpoints
* Com um gateway de coexistência que suporta expressroute e apontar para site/site para VPNs do site

### <a name="set-up-a-gateway-in-your-vnet"></a>Configurar um gateway em sua VNet ###

Para criar um gateway:

1. [Criar uma sub-rede de gateway][creategatewaysubnet] em sua VNet.  

1. [Crie o gateway VPN][creategateway]. Selecione um tipo VPN baseado em rota.

1. [Defina os endereços de ponto a site][setp2saddresses]. Se o gateway não estiver no SKU básico, então o IKEV2 deve ser desativado na configuração ponto a ponto do local e o SSTP deve ser selecionado. O espaço de endereço ponto a ponto deve estar nos blocos de endereços RFC 1918, 10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16

Se você está apenas criando o gateway para uso com o App Service VNet Integration, então você não precisa carregar um certificado. A criação do gateway pode levar 30 minutos. Você não poderá integrar seu aplicativo com sua VNet até que o gateway seja provisionado. 

### <a name="how-gateway-required-vnet-integration-works"></a>Como funciona o Gateway VNet Integration

O Gateway exigiu a Integração VNet construída em cima da tecnologia VPN ponto a ponto. As VPNs de ponto para o site limitam o acesso à rede apenas à máquina virtual que hospeda o aplicativo. Os aplicativos são restritos apenas ao envio de tráfego para a Internet, por meio de Conexões Híbridas ou por meio da Integração VNet. Quando seu aplicativo é configurado com o portal para usar o Gateway Required VNet Integration, uma negociação complexa é gerenciada em seu nome para criar e atribuir certificados no gateway e no lado do aplicativo. O resultado final é que os trabalhadores usados para hospedar seus aplicativos são capazes de se conectar diretamente ao gateway de rede virtual no VNet selecionado. 

![Como funciona o Gateway VNet Integration][6]

### <a name="accessing-on-premises-resources"></a>Como acessar recursos locais

Aplicativos podem acessar recursos locais ao integrarem-se com VNets que têm conexões site a site. Se você estiver usando o gateway necessário para a Integração VNet, você precisa atualizar suas rotas de gateway VPN no local com seus blocos de endereços ponto a local. Quando o VPN site a site é configurado pela primeira vez, os scripts usados para configurá-lo devem configurar as rotas adequadamente. Se adicionar os endereços ponto a site depois de criar uma VPN site a site, você precisará atualizar as rotas manualmente. Os detalhes sobre como fazer isso variam de acordo com o gateway, e não são descritos aqui. Não é possível configurar o BGP com uma conexão VPN site-site.

Não é necessária uma configuração adicional para que o recurso regional de Integração VNet chegue através do seu VNet e ao local. Você simplesmente precisa conectar seu VNet a locais usando o ExpressRoute ou uma VPN site-to-site. 

> [!NOTE]
> O recurso de integração VNet necessário não integra um aplicativo com um VNet que tenha um Gateway ExpressRoute. Mesmo se o Gateway ExpressRoute estiver configurado no [modo de coexistência][VPNERCoex], a Integração VNet não funcionará. Se você precisar acessar recursos através de uma conexão ExpressRoute, então você pode usar o recurso regional VNet Integration ou um [App Service Environment][ASE], que é executado em seu VNet. 
> 
> 

### <a name="peering"></a>Emparelhamento

Se você estiver usando o peering com a Integração VNet regional, você não precisa fazer nenhuma configuração adicional. 

Se você estiver usando o gateway necessário de Integração VNet com peering, você precisa configurar alguns itens adicionais. Para configurar o emparelhamento para funcionar com o aplicativo:

1. Adicione que uma conexão de emparelhamento na rede virtual à qual o aplicativo se conecta. Ao adicionar a conexão de emparelhamento, habilite **Permitir acesso à rede virtual** e marque **Permitir tráfego encaminhado** e **Permitir trânsito de gateway**.
1. Adicione uma conexão de emparelhamento na rede virtual que está sendo emparelhada à rede virtual à qual você está conectado. Ao adicionar a conexão de emparelhamento à VNet de destino, habilite **Permitir acesso à rede virtual** e marque **Permitir tráfego encaminhado** e **Permitir gateways remotos**.
1. Vá para o Plano do Serviço de Aplicativo > Rede > Interface do usuário da Integração VNet no portal.  Selecione a VNet à qual o aplicativo se conecta. Na seção de roteamento, adicione o intervalo de endereços da VNet que está emparelhada com a VNet à qual o aplicativo está conectado.  

## <a name="managing-vnet-integration"></a>Gerenciando a Integração VNet 

Conectar-se e desconectar-se com um VNet está em um nível de aplicativo. As operações que podem afetar a Integração VNet entre vários aplicativos estão no nível do Plano do Serviço de Aplicativo. A partir do aplicativo > Networking > portal VNet Integration, você pode obter detalhes em seu VNet. Você pode ver informações semelhantes no nível ASP no portal ASP > Networking > VNet Integration.

A única operação que você pode executar no modo de exibição de aplicativo da Integração VNet é desconectar-se da VNet à qual seu aplicativo está atualmente conectado. Para desconectar o aplicativo de uma VNet, selecione **Desconectar**. Seu aplicativo será reiniciado quando você se desconectar de uma VNet. Desconectar-se não altera a VNet. A sub-rede ou gateway não é removido. Se você quiser excluir seu VNet, você precisa primeiro desconectar seu aplicativo do VNet e excluir os recursos nele, como gateways. 

A Interface do Rei da Integração ASP VNet mostrará todas as integrações vnet usadas pelos aplicativos em seu ASP. Para ver detalhes em cada VNet, clique na VNet que lhe interessa. Existem duas ações que você pode executar aqui para a integração VNet necessária do gateway.

* **Sincronizar rede**. A operação de rede de sincronização é apenas para o recurso de integração VNet dependente de gateway. A realização de uma operação de rede de sincronização garante que seus certificados e informações de rede estejam em sincronia. Se você adicionar ou alterar o DNS do seu VNet, você precisa executar uma operação **de rede Sync.** Esta operação reiniciará todos os aplicativos usando essa VNet.
* **Adicionar rotas** A adição de rotas orientará o tráfego de saída em sua VNet. 

**Roteamento de integração VNet necessário** As rotas definidas em seu VNet são usadas para direcionar o tráfego para o seu VNet a partir do seu aplicativo. Se você precisar enviar o tráfego de saída adicional para a VNet, você poderá adicionar esses blocos de endereço aqui. Esse recurso só funciona com a integração VNet necessária do gateway. As tabelas de rota não afetam o tráfego do aplicativo ao usar o Gateway Required VNet Integration da maneira que fazem com a Integração VNet regional.

**Gateway exigiu certificados de integração VNet** Quando o gateway exigiu a Integração VNet ativada, há uma troca de certificados necessária para garantir a segurança da conexão. Juntamente com os certificados, há a configuração do DNS, as rotas e outros itens semelhantes que descrevem a rede.
Se os certificados ou as informações de rede forem alterados, você precisará clicar em "Sincronizar Rede". Quando clica em "Sincronizar Rede", você causa uma breve interrupção na conectividade entre o aplicativo e a VNet. Embora seu aplicativo não seja reiniciado, a perda de conectividade pode fazer com que seu site não funcione corretamente. 

## <a name="pricing-details"></a>Detalhes de preço
O recurso regional De Integração VNet não tem nenhuma taxa adicional para uso além das taxas de nível de preços ASP.

Existem três encargos relacionados ao uso do recurso de integração VNet necessário ao gateway:

* Taxas de nível de preços ASP - Seus aplicativos precisam estar em um Plano de Serviço de Aplicativo Padrão, Premium ou PremiumV2. Você pode ver mais detalhes sobre esses custos aqui: [Preço do Serviço de Aplicativo][ASPricing]. 
* Custos de transferência de dados - Há uma cobrança pela saída de dados, mesmo que o VNet esteja no mesmo data center. Esses encargos são descritos em [Detalhes de Preços de Transferência de Dados][DataPricing]. 
* Custos do VPN Gateway - Há um custo para o gateway VNet que é necessário para a VPN ponto a ponto. Os detalhes estão na página [Preços de Gateway de VPN][VNETPricing].

## <a name="troubleshooting"></a>Solução de problemas

[!INCLUDE [app-service-web-vnet-troubleshooting](../../includes/app-service-web-vnet-troubleshooting.md)]

## <a name="automation"></a>Automação

Há suporte cli para integração regional VNet. Para acessar os seguintes comandos, [instale o Azure CLI][installCLI]. 

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

Para a integração VNet necessária do gateway, você pode integrar o App Service com uma rede virtual do Azure usando o PowerShell. Para um script pronto para uso, consulte [Conectar um aplicativo do Azure no Serviço de Aplicativo do Azure a uma Rede Virtual do Azure](https://gallery.technet.microsoft.com/scriptcenter/Connect-an-app-in-Azure-ab7527e3).


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
