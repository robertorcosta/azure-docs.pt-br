---
title: Integrar o aplicativo à rede virtual do Azure
description: Integre aplicativos no serviço de Azure App com redes virtuais do Azure.
author: ccompy
ms.assetid: 90bc6ec6-133d-4d87-a867-fcf77da75f5a
ms.topic: article
ms.date: 02/27/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 76139716fe11536faa0ff792185ba1643801c641
ms.sourcegitcommit: 96dc60c7eb4f210cacc78de88c9527f302f141a9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2020
ms.locfileid: "77648969"
---
# <a name="integrate-your-app-with-an-azure-virtual-network"></a>Integrar seu aplicativo Web a uma Rede Virtual do Azure
Este documento descreve o recurso de integração de rede virtual do serviço Azure App e como configurá-lo com aplicativos no [serviço Azure app](https://go.microsoft.com/fwlink/?LinkId=529714). As [redes virtuais do Azure][VNETOverview] (VNets) permitem que você coloque muitos dos seus recursos do Azure em uma rede roteável que não seja da Internet.  

O serviço de Azure App tem duas variações. 

1. Os sistemas multilocatário que dão suporte a toda a gama de planos de preço, exceto Isolado
2. O Ambiente do Serviço de Aplicativo (ASE), que é implantado em sua VNet e dá suporte a aplicativos de plano de preços isolados

Este documento passa pelo recurso de integração VNet, que é para uso no serviço de aplicativo multilocatário. Se seu aplicativo estiver em [ambiente do serviço de aplicativo][ASEintro], ele já estará em uma VNet e não exigirá o uso do recurso de integração VNet para alcançar recursos na mesma VNet. Para obter detalhes sobre todos os recursos de rede do serviço de aplicativo, leia [recursos de rede do serviço de aplicativo](networking-features.md)

A integração VNet dá ao seu aplicativo Web acesso aos recursos em sua rede virtual, mas não concede acesso privado de entrada ao seu aplicativo Web por meio da VNet. Acesso ao site privado significa tornar seu aplicativo acessível somente de uma rede privada, como de dentro de uma rede virtual do Azure. A integração VNet é apenas para fazer chamadas de saída de seu aplicativo para sua VNet. O recurso de integração VNet se comporta de maneira diferente quando usado com VNets na mesma região e com VNets em outras regiões. O recurso integração VNet tem duas variações.

1. Integração de VNet regional – ao se conectar ao Gerenciador de recursos VNets na mesma região, você deve ter uma sub-rede dedicada na VNet com a qual está se integrando. 
2. Integração VNet necessária do gateway-ao conectar-se ao VNets em outras regiões ou a uma VNet clássica na mesma região, você precisa de um gateway de rede virtual provisionado na VNet de destino.

Os recursos de integração VNet:

* exigir um plano de preços Standard, Premium, PremiumV2 ou elástico Premium 
* suporte a TCP e UDP
* trabalhar com aplicativos do serviço de aplicativo e aplicativos de funções

Há algumas coisas a que a Integração VNet não dá suporte, incluindo:

* montagem de unidade
* integração ao AD 
* NetBios

A integração VNet necessária do gateway só fornece acesso aos recursos na VNet de destino ou em redes conectadas à VNet de destino com emparelhamento ou VPNs. A integração VNet necessária do gateway não permite o acesso aos recursos disponíveis em conexões do ExpressRoute ou funciona com pontos de extremidade de serviço. 

Independentemente da versão usada, a integração VNet dá ao seu aplicativo Web acesso aos recursos em sua rede virtual, mas não concede acesso privado de entrada ao seu aplicativo Web a partir da rede virtual. Acesso ao site privado significa tornar seu aplicativo acessível somente de uma rede privada, como de dentro de uma rede virtual do Azure. A integração VNet é apenas para fazer chamadas de saída de seu aplicativo para sua VNet. 

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

O uso da integração de VNet regional permite que seu aplicativo acesse:

* recursos na VNet na mesma região com a qual você se integra 
* recursos em VNets emparelhados para sua VNet que estão na mesma região
* serviços protegidos do ponto de extremidade de serviço
* recursos entre conexões do ExpressRoute
* recursos na VNet à qual você está conectado
* recursos entre conexões emparelhadas, incluindo conexões de ExpressRoute
* Pontos de extremidade privados 

Ao usar a integração VNet com o VNets na mesma região, você pode usar os seguintes recursos de rede do Azure:

* NSGs (grupos de segurança de rede) – você pode bloquear o tráfego de saída com um grupo de segurança de rede que é colocado em sua sub-rede de integração. As regras de entrada não se aplicam, pois você não pode usar a integração VNet para fornecer acesso de entrada ao seu aplicativo Web.
* UDRs (tabelas de rotas) – você pode inserir uma tabela de rotas na sub-rede de integração para enviar o tráfego de saída onde desejar. 

Por padrão, seu aplicativo só roteará o tráfego RFC1918 em sua VNet. Se você quiser rotear todo o tráfego de saída para sua VNet, aplique a configuração do aplicativo WEBSITE_VNET_ROUTE_ALL ao seu aplicativo. Para definir a configuração do aplicativo:

1. Vá para a interface do usuário de configuração no portal do seu aplicativo. Selecionar **nova configuração de aplicativo**
1. Digite **WEBSITE_VNET_ROUTE_ALL** no campo nome e **1** no campo valor

   ![Fornecer configuração de aplicativo][4]

1. Selecione **OK**
1. Selecione **Salvar**

Se você rotear todo o tráfego de saída para sua VNet, ele estará sujeito aos NSGs e UDRs que são aplicados à sua sub-rede de integração. Ao rotear todo o tráfego de saída para sua VNet, seus endereços de saída ainda serão os endereços de saída listados nas propriedades do aplicativo, a menos que você forneça rotas para enviar o tráfego em outro lugar. 

Há algumas limitações no uso da integração VNet com o VNets na mesma região:

* Você não pode acessar recursos entre conexões de emparelhamento globais
* O recurso só está disponível em unidades de escala do serviço de aplicativo mais recentes que dão suporte a planos do serviço de aplicativo do PremiumV2.
* A sub-rede de integração só pode ser usada por um plano do serviço de aplicativo
* O recurso não pode ser usado por aplicativos de plano isolado que estão em um Ambiente do Serviço de Aplicativo
* O recurso requer uma sub-rede não usada que seja de/27 com 32 endereços ou maior em uma VNet do Resource Manager
* O aplicativo e a VNet devem estar na mesma região
* Você não pode excluir uma VNet com um aplicativo integrado. Remova a integração antes de excluir a VNet. 
* Você só pode integrar com VNets na mesma assinatura que o aplicativo Web
* Você pode ter apenas uma integração VNet regional por plano do serviço de aplicativo. Vários aplicativos no mesmo plano do serviço de aplicativo podem usar a mesma VNet. 
* Você não pode alterar a assinatura de um aplicativo ou um plano do serviço de aplicativo enquanto houver um aplicativo que está usando a integração VNet regional

Um endereço é usado para cada instância de plano do serviço de aplicativo. Se você dimensionar seu aplicativo para cinco instâncias, serão usados cinco endereços. Como o tamanho da sub-rede não pode ser alterado após a atribuição, você deve usar uma sub-rede que seja grande o suficiente para acomodar qualquer escala que seu aplicativo possa atingir. R/26 com 64 endereços é o tamanho recomendado. R/26 com 64 endereços acomodarão um plano do serviço de aplicativo Premium com 30 instâncias. Ao dimensionar um plano do serviço de aplicativo para cima ou para baixo, você precisará de duas vezes mais endereços por um curto período de tempo. 

Se você quiser que seus aplicativos em outro plano do serviço de aplicativo alcancem uma VNet que está conectada ao já por aplicativos em outro plano do serviço de aplicativo, será necessário selecionar uma sub-rede diferente daquela que está sendo usada pela integração VNet pré-existente.  

O recurso está em versão prévia para Linux. O formulário Linux do recurso só dá suporte à realização de chamadas para endereços RFC 1918 (10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16).

### <a name="web-app-for-containers"></a>Aplicativo Web para Contêineres

Se você usar o serviço de aplicativo no Linux com as imagens internas, a integração VNet regional funcionará sem alterações adicionais. Se você usar Aplicativo Web para Contêineres, precisará modificar a imagem do Docker para usar a integração VNet. Na imagem do Docker, use a variável de ambiente PORT como a porta de escuta do servidor Web principal, em vez de usar um número de porta codificado. A variável de ambiente PORT é definida automaticamente pela plataforma do serviço de aplicativo no momento da inicialização do contêiner. Se você estiver usando SSH, o daemon SSH deverá ser configurado para escutar no número da porta especificado pela variável de ambiente SSH_PORT ao usar a integração VNet regional.  Não há suporte para a integração VNet necessária do gateway no Linux. 

### <a name="service-endpoints"></a>Pontos de extremidade de serviço

A integração VNet regional permite que você use pontos de extremidade de serviço.  Para usar pontos de extremidade de serviço com seu aplicativo, use a integração VNet regional para se conectar a uma VNet selecionada e, em seguida, configure os pontos de extremidade de serviço na sub-rede usada para a integração. 

### <a name="network-security-groups"></a>Grupos de segurança de rede

Os grupos de segurança de rede permitem bloquear o tráfego de entrada e saída para recursos em uma VNet. Um aplicativo Web usando a integração VNet regional pode usar o [grupo de segurança de rede][VNETnsg] para bloquear o tráfego de saída para recursos em sua VNet ou na Internet. Para bloquear o tráfego para endereços públicos, você deve ter a configuração de aplicativo WEBSITE_VNET_ROUTE_ALL definida como 1. As regras de entrada em um NSG não se aplicam ao seu aplicativo, pois a integração VNet afeta apenas o tráfego de saída do seu aplicativo. Para controlar o tráfego de entrada para seu aplicativo Web, use o recurso de restrições de acesso. Um NSG que é aplicado à sua sub-rede de integração estará em vigor, independentemente de quaisquer rotas aplicadas à sua sub-rede de integração. Se WEBSITE_VNET_ROUTE_ALL foi definido como 1 e você não tiver nenhuma rota afetando o tráfego de endereço público em sua sub-rede de integração, todo o tráfego de saída ainda estaria sujeito a NSGs atribuído à sua sub-rede de integração. Se WEBSITE_VNET_ROUTE_ALL não tiver sido definida, NSGs só será aplicada ao tráfego RFC1918.

### <a name="routes"></a>Rotas

As tabelas de rotas permitem que você encaminhe o tráfego de saída do seu aplicativo para onde quiser. Por padrão, as tabelas de rotas afetarão apenas o tráfego de destino do RFC1918.  Se você definir WEBSITE_VNET_ROUTE_ALL como 1, todas as suas chamadas de saída serão afetadas. As rotas definidas em sua sub-rede de integração não afetarão as respostas a solicitações de aplicativo de entrada. Os destinos comuns podem incluir dispositivos de firewall ou gateways. Se você quiser rotear todo o tráfego de saída local, poderá usar uma tabela de rotas para enviar todo o tráfego de saída para o gateway de ExpressRoute. Se você rotear o tráfego para um gateway, certifique-se de definir rotas na rede externa para enviar respostas.

As rotas Border Gateway Protocol (BGP) também afetarão o tráfego do aplicativo. Se você tiver rotas BGP de algo como um gateway de ExpressRoute, o tráfego de saída do aplicativo será afetado. Por padrão, as rotas BGP afetarão apenas o tráfego de destino RFC1918. Se WEBSITE_VNET_ROUTE_ALL for definido como 1, todo o tráfego de saída poderá ser afetado por suas rotas BGP. 

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
Embora o recurso seja fácil de configurar, isso não significa que sua experiência estará livre de problemas. Se você encontrar problemas ao acessar o ponto de extremidade desejado, há utilitários que você pode usar para testar a conectividade do console do aplicativo. Há dois consoles que você pode usar. Uma é o console do Kudu e a outra é o console no portal do Azure. Para acessar o console do Kudu do aplicativo, vá para Ferramentas -> Kudu. Você também pode acessar o console do Kudo em [SiteName]. SCM. azurewebsites. net. Depois que o site for carregado, vá para a guia Console de depuração. Para acessar o console do portal do Azure hospedado, em seguida, em seu aplicativo, acesse ferramentas-> console. 

#### <a name="tools"></a>Ferramentas
As ferramentas **ping**, **nslookup**e **tracert** não funcionarão no console do devido a restrições de segurança. Para preencher o void, duas ferramentas separadas são adicionadas. Para testar a funcionalidade do DNS, adicionamos uma ferramenta chamada nameresolver.exe. A sintaxe do é:

    nameresolver.exe hostname [optional: DNS Server]

Você pode usar **nameresolver** para verificar os nomes de host de que seu aplicativo depende. Dessa forma, você pode testar se não há nada mal configurado no DNS ou se não tem acesso ao seu servidor DNS. Você pode ver o servidor DNS que seu aplicativo usará no console examinando as variáveis de ambiente WEBSITE_DNS_SERVER e WEBSITE_DNS_ALT_SERVER.

A próxima ferramenta permite testar a conectividade do TCP de uma combinação de host e porta. Essa ferramenta é chamada **tcpping** e a sintaxe é:

    tcpping.exe hostname [optional: port]

O utilitário **tcpping** informa se você pode acessar um host específico e uma porta. Ele só pode mostrar êxito se: houver um aplicativo escutando na combinação de porta e host, e houver acesso de rede de seu aplicativo para o host e porta especificados.

#### <a name="debugging-access-to-vnet-hosted-resources"></a>Depurar o acesso a recursos hospedados por VNet
Há várias coisas que podem impedir que seu aplicativo alcance um host e uma porta específicos. Na maioria das vezes, é uma dentre três coisas:

* **Há um firewall no caminho.** Se houver um firewall no caminho, você atingirá o tempo limite de TCP. O tempo limite de TCP é 21 segundos neste caso. Use a ferramenta **tcpping** para testar a conectividade. Tempos limite de TCP podem ocorrer por muitos motivos, além de firewalls, mas comece com isso. 
* **O DNS não está acessível.** O tempo limite do DNS é de três segundos por servidor DNS. Se você tiver dois servidores DNS, o tempo limite será de seis segundos. Use nameresolver para ver se o DNS está funcionando. Lembre-se de que você não pode usar nslookup, pois ele não usa o DNS com o qual sua VNet está configurada. Se estiver inacessível, você pode ter um firewall ou NSG bloqueando o acesso ao DNS ou pode estar inoperante.

Se esses itens não responderem a seus problemas, veja primeiro algo como: 

**Integração de VNet regional**
* seu destino é um endereço não RFC1918 e você não tem WEBSITE_VNET_ROUTE_ALL definido como 1
* Há um NSG bloqueando a saída de sua sub-rede de integração
* Se estiver indo pelo ExpressRoute ou por uma VPN, o gateway local será configurado para rotear o tráfego de volta para o Azure? Se você puder acessar pontos de extremidade em sua VNet, mas não no local, verifique suas rotas.
* Você tem permissões suficientes para definir a delegação na sub-rede de integração? Durante a configuração de integração VNet regional, sua sub-rede de integração será delegada para Microsoft. Web. A interface do usuário de integração VNet delegará a sub-rede ao Microsoft. Web automaticamente. Se sua conta não tiver permissões de rede suficientes para definir a delegação, você precisará de alguém que possa definir atributos em sua sub-rede de integração para delegar a sub-rede. Para delegar manualmente a sub-rede de integração, vá para a interface do usuário da sub-rede da rede virtual do Azure e defina delegação para Microsoft. Web. 

**Integração VNet necessária do gateway**
* é o intervalo de endereços de ponto a site nos intervalos RFC 1918 (10.0.0.0-10.255.255.255/172.16.0.0-172.31.255.255/192.168.0.0-192.168.255.255)?
* O Gateway é mostrado como estando em execução no Portal? Se o gateway estiver inativo, ative-o.
* Os certificados são mostrados como estando em sincronia ou você suspeita que a configuração de rede foi alterada?  Se os certificados estiverem fora de sincronia ou se você suspeitar de que houve uma alteração na configuração da VNet que não foi sincronizada com suas ASPs, pressione "sincronizar rede".
* Se estiver passando por uma VPN, o gateway local configurado para rotear o tráfego de volta para o Azure? Se você puder acessar pontos de extremidade em sua VNet, mas não no local, verifique suas rotas.
* Você está tentando usar um gateway de coexistência que dá suporte a ponto a site e ExpressRoute? Não há suporte para gateways de coexistência com integração VNet 

A depuração de problemas de rede é um desafio porque não é possível ver o que está bloqueando o acesso a uma combinação de hosts específicos: porta. Algumas das causas incluem:

* você tem um firewall no seu host que impede o acesso à porta do aplicativo usando o intervalo de IP ponto a site. O cruzamento de sub-redes geralmente exige acesso Público.
* o host de destino está inoperante
* seu aplicativo está inoperante
* você tinha o IP ou nome de host incorreto
* seu aplicativo está escutando em uma porta diferente da que você esperava. Você pode corresponder a sua ID de processo com a porta de escuta usando "netstat -aon" no host do ponto de extremidade. 
* os grupos de segurança de rede estão configurados de modo a impedir o acesso ao host do aplicativo e à porta do intervalo de IP ponto a site.

Lembre-se de que você não sabe qual endereço seu aplicativo realmente usará. Pode ser qualquer endereço na sub-rede de integração ou intervalo de endereços de ponto a site, portanto, você precisa permitir o acesso de todo o intervalo de endereços. 

As etapas de depuração adicionais incluem:

* conecte-se a uma VM na sua VNet e tente acessar o host:porta do recurso de lá. Para testar o acesso TCP, use o comando do PowerShell **test-netconnection**. A sintaxe do é:

      test-netconnection hostname [optional: -Port]

* abrir um aplicativo em uma VM e testar o acesso ao host e à porta do console a partir do seu aplicativo usando o **tcpping**

#### <a name="on-premises-resources"></a>Recursos locais ####

Se o aplicativo não puder acessar um recurso local, verifique se é possível acessar o recurso da sua VNet. Use o comando do PowerShell **test-netconnection** para verificar se há acesso TCP. Se sua VM não conseguir acessar o recurso local, sua conexão VPN ou ExpressRoute poderá não estar configurada corretamente.

Se a VM hospedada na VNet puder acessar seu sistema local, mas seu aplicativo não, isso provavelmente ocorrerá devido a um dos seguintes motivos:

* suas rotas não estão configuradas com a sub-rede ou intervalos de endereços de ponto a site em seu gateway local
* seus grupos de segurança de rede estão bloqueando o acesso para o intervalo de IP Ponto a Site
* os firewalls locais estão bloqueando o tráfego do intervalo de IP Ponto a Site
* Você está tentando acessar um endereço não RFC 1918 usando o recurso de integração VNet regional


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
[4]: ./media/web-sites-integrate-with-vnet/vnetint-appsetting.png
[5]: ./media/web-sites-integrate-with-vnet/vnetint-regionalworks.png
[6]: ./media/web-sites-integrate-with-vnet/vnetint-gwworks.png


<!--Links-->
[VNETOverview]: https://azure.microsoft.com/documentation/articles/virtual-networks-overview/ 
[AzurePortal]: https://portal.azure.com/
[ASPricing]: https://azure.microsoft.com/pricing/details/app-service/
[VNETPricing]: https://azure.microsoft.com/pricing/details/vpn-gateway/
[DataPricing]: https://azure.microsoft.com/pricing/details/data-transfers/
[V2VNETP2S]: https://azure.microsoft.com/documentation/articles/vpn-gateway-howto-point-to-site-rm-ps/
[ASEintro]: environment/intro.md
[ILBASE]: environment/create-ilb-ase.md
[V2VNETPortal]: ../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md
[VPNERCoex]: ../expressroute/expressroute-howto-coexist-resource-manager.md
[ASE]: environment/intro.md
[creategatewaysubnet]: ../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md#creategw
[creategateway]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal#creategw
[setp2saddresses]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal#addresspool
[VNETnsg]: https://docs.microsoft.com/azure/virtual-network/security-overview/
[VNETRouteTables]: https://docs.microsoft.com/azure/virtual-network/manage-route-table/
[installCLI]: https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest/
