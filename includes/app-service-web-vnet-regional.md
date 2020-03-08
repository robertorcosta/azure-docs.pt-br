---
author: ccompy
ms.service: app-service-web
ms.topic: include
ms.date: 02/27/2020
ms.author: ccompy
ms.openlocfilehash: c731e2a7d4da1a66aabb50b335d526fbb6a0a302
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78671513"
---
O uso da integração de VNet regional permite que seu aplicativo acesse:

* recursos na VNet na mesma região com a qual você se integra 
* recursos em VNets emparelhados para sua VNet que estão na mesma região
* serviços protegidos do ponto de extremidade de serviço
* recursos entre conexões do ExpressRoute
* recursos na VNet à qual você está conectado
* recursos entre conexões emparelhadas, incluindo conexões de ExpressRoute
* Pontos de extremidade privados 

Ao usar a integração VNet com o VNets na mesma região, você pode usar os seguintes recursos de rede do Azure:

* NSGs (grupos de segurança de rede) – você pode bloquear o tráfego de saída com um grupo de segurança de rede que é colocado em sua sub-rede de integração. As regras de entrada não se aplicam, pois você não pode usar a integração VNet para fornecer acesso de entrada ao seu aplicativo.
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
* Você só pode integrar com VNets na mesma assinatura que o aplicativo
* Você pode ter apenas uma integração VNet regional por plano do serviço de aplicativo. Vários aplicativos no mesmo plano do serviço de aplicativo podem usar a mesma VNet. 
* Você não pode alterar a assinatura de um aplicativo ou um plano enquanto há um aplicativo que está usando a integração VNet regional

Um endereço é usado para cada instância do plano. Se você dimensionar seu aplicativo para cinco instâncias, serão usados cinco endereços. Como o tamanho da sub-rede não pode ser alterado após a atribuição, você deve usar uma sub-rede que seja grande o suficiente para acomodar qualquer escala que seu aplicativo possa atingir. R/26 com 64 endereços é o tamanho recomendado. R/26 com 64 endereços acomodarão um plano Premium com 30 instâncias. Quando você dimensiona um plano para cima ou para baixo, precisará de duas vezes mais endereços por um curto período de tempo. 

Se você quiser que seus aplicativos em outro plano alcancem uma VNet que está conectada ao já por aplicativos em outro plano, será necessário selecionar uma sub-rede diferente daquela que está sendo usada pela integração VNet pré-existente.  

O recurso está em versão prévia para Linux. O formulário Linux do recurso só dá suporte à realização de chamadas para endereços RFC 1918 (10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16).

### <a name="web--function-app-for-containers"></a>Web/Aplicativo de funções para contêineres

Se você hospedar seu aplicativo no Linux com as imagens internas, a integração VNet regional funcionará sem alterações adicionais. Se você usar Web/Aplicativo de funções para contêineres, precisará modificar a imagem do Docker para usar a integração VNet. Na imagem do Docker, use a variável de ambiente PORT como a porta de escuta do servidor Web principal, em vez de usar um número de porta codificado. A variável de ambiente PORT é definida automaticamente pela plataforma no tempo de inicialização do contêiner. Se você estiver usando SSH, o daemon SSH deverá ser configurado para escutar no número da porta especificado pela variável de ambiente SSH_PORT ao usar a integração VNet regional.  Não há suporte para a integração VNet necessária do gateway no Linux. 

### <a name="service-endpoints"></a>Pontos de extremidade de serviço

A integração VNet regional permite que você use pontos de extremidade de serviço.  Para usar pontos de extremidade de serviço com seu aplicativo, use a integração VNet regional para se conectar a uma VNet selecionada e, em seguida, configure os pontos de extremidade de serviço na sub-rede usada para a integração. 

### <a name="network-security-groups"></a>Grupos de segurança de rede

Os grupos de segurança de rede permitem bloquear o tráfego de entrada e saída para recursos em uma VNet. Um aplicativo que usa a integração VNet regional pode usar o [grupo de segurança de rede][VNETnsg] para bloquear o tráfego de saída para recursos em sua VNet ou na Internet. Para bloquear o tráfego para endereços públicos, você deve ter a configuração de aplicativo WEBSITE_VNET_ROUTE_ALL definida como 1. As regras de entrada em um NSG não se aplicam ao seu aplicativo, pois a integração VNet afeta apenas o tráfego de saída do seu aplicativo. Para controlar o tráfego de entrada para seu aplicativo, use o recurso de restrições de acesso. Um NSG que é aplicado à sua sub-rede de integração estará em vigor, independentemente de quaisquer rotas aplicadas à sua sub-rede de integração. Se WEBSITE_VNET_ROUTE_ALL foi definido como 1 e você não tiver nenhuma rota afetando o tráfego de endereço público em sua sub-rede de integração, todo o tráfego de saída ainda estaria sujeito a NSGs atribuído à sua sub-rede de integração. Se WEBSITE_VNET_ROUTE_ALL não tiver sido definida, NSGs só será aplicada ao tráfego RFC1918.

### <a name="routes"></a>Rotas

As tabelas de rotas permitem que você encaminhe o tráfego de saída do seu aplicativo para onde quiser. Por padrão, as tabelas de rotas afetarão apenas o tráfego de destino do RFC1918.  Se você definir WEBSITE_VNET_ROUTE_ALL como 1, todas as suas chamadas de saída serão afetadas. As rotas definidas em sua sub-rede de integração não afetarão as respostas a solicitações de aplicativo de entrada. Os destinos comuns podem incluir dispositivos de firewall ou gateways. Se você quiser rotear todo o tráfego de saída local, poderá usar uma tabela de rotas para enviar todo o tráfego de saída para o gateway de ExpressRoute. Se você rotear o tráfego para um gateway, certifique-se de definir rotas na rede externa para enviar respostas.

As rotas Border Gateway Protocol (BGP) também afetarão o tráfego do aplicativo. Se você tiver rotas BGP de algo como um gateway de ExpressRoute, o tráfego de saída do aplicativo será afetado. Por padrão, as rotas BGP afetarão apenas o tráfego de destino RFC1918. Se WEBSITE_VNET_ROUTE_ALL for definido como 1, todo o tráfego de saída poderá ser afetado por suas rotas BGP. 


<!--Image references-->
[4]: ../includes/media/web-sites-integrate-with-vnet/vnetint-appsetting.png

<!--Links-->
[VNETnsg]: https://docs.microsoft.com/azure/virtual-network/security-overview/