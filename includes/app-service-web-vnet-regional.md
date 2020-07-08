---
author: ccompy
ms.service: app-service-web
ms.topic: include
ms.date: 06/08/2020
ms.author: ccompy
ms.openlocfilehash: ee81b391587b994bd79e9f0950d041de70153b5c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84488776"
---
O uso da integração de VNet regional permite que seu aplicativo acesse:

* Recursos em uma VNet na mesma região que seu aplicativo.
* Recursos no VNets emparelhados com a VNet à qual seu aplicativo está integrado.
* Serviços protegidos do ponto de extremidade de serviço.
* Recursos entre as conexões do Azure ExpressRoute.
* Recursos na VNet com a qual você está integrado.
* Recursos entre conexões emparelhadas, que incluem conexões do Azure ExpressRoute.
* Pontos de extremidade privados 

Ao usar a integração VNet com o VNets na mesma região, você pode usar os seguintes recursos de rede do Azure:

* **NSGs (grupos de segurança de rede)**: você pode bloquear o tráfego de saída com um NSG que é colocado em sua sub-rede de integração. As regras de entrada não se aplicam porque você não pode usar a integração VNet para fornecer acesso de entrada ao seu aplicativo.
* **UDRs (tabelas de rotas)**: você pode inserir uma tabela de rotas na sub-rede de integração para enviar o tráfego de saída onde desejar.

Por padrão, seu aplicativo roteia apenas o tráfego RFC1918 para sua VNet. Se você quiser rotear todo o tráfego de saída para sua VNet, aplique a configuração do aplicativo WEBSITE_VNET_ROUTE_ALL ao seu aplicativo. Para definir a configuração do aplicativo:

1. Vá para a interface do usuário de **configuração** no portal do seu aplicativo. Selecione **nova configuração de aplicativo**.
1. Insira **WEBSITE_VNET_ROUTE_ALL** na caixa **nome** e digite **1** na caixa **valor** .

   ![Fornecer configuração de aplicativo][4]

1. Selecione **OK**.
1. Clique em **Salvar**.

Se você rotear todo o tráfego de saída para sua VNet, ele estará sujeito aos NSGs e UDRs que são aplicados à sua sub-rede de integração. Quando você roteia todo o tráfego de saída para sua VNet, seus endereços de saída ainda são os endereços de saída listados nas propriedades do aplicativo, a menos que você forneça rotas para enviar o tráfego em outro lugar.

Há algumas limitações no uso da integração VNet com o VNets na mesma região:

* Você não pode acessar recursos entre conexões de emparelhamento globais.
* O recurso está disponível somente de unidades de escala de serviço Azure App mais recentes que dão suporte a planos do serviço de aplicativo PremiumV2.
* A sub-rede de integração pode ser usada por apenas um plano do serviço de aplicativo.
* O recurso não pode ser usado por aplicativos de plano isolado que estão em um Ambiente do Serviço de Aplicativo.
* O recurso requer uma sub-rede não usada que seja de/27 com 32 endereços ou maior em uma VNet Azure Resource Manager.
* O aplicativo e a VNet devem estar na mesma região.
* Não é possível excluir uma VNet com um aplicativo integrado. Remova a integração antes de excluir a VNet.
* Você só pode integrar com VNets na mesma assinatura que o aplicativo.
* Você pode ter apenas uma integração VNet regional por plano do serviço de aplicativo. Vários aplicativos no mesmo plano do serviço de aplicativo podem usar a mesma VNet.
* Você não pode alterar a assinatura de um aplicativo ou um plano enquanto há um aplicativo que está usando a integração VNet regional.
* Seu aplicativo não pode resolver endereços em Zonas Privadas do DNS do Azure sem alterações de configuração

Um endereço é usado para cada instância do plano. Se você dimensionar seu aplicativo para cinco instâncias, serão usados cinco endereços. Como o tamanho da sub-rede não pode ser alterado após a atribuição, você deve usar uma sub-rede que seja grande o suficiente para acomodar qualquer escala que seu aplicativo possa alcançar. R/26 com 64 endereços é o tamanho recomendado. R/26 com 64 endereços acomoda um plano Premium com 30 instâncias. Quando você dimensiona um plano para cima ou para baixo, precisará de duas vezes mais endereços por um curto período de tempo.

Se você quiser que seus aplicativos em outro plano alcancem uma VNet que já está conectada por aplicativos em outro plano, selecione uma sub-rede diferente daquela que está sendo usada pela integração VNet pré-existente.

O recurso tem suporte total para aplicativos Web do Windows e Linux. Todos os comportamentos agem da mesma entre aplicativos do Windows e aplicativos do Linux.

### <a name="service-endpoints"></a>Pontos de extremidade de serviço

A integração VNet regional permite que você use pontos de extremidade de serviço. Para usar pontos de extremidade de serviço com seu aplicativo, use a integração VNet regional para se conectar a uma VNet selecionada e, em seguida, configure os pontos de extremidade de serviço com o serviço de destino na sub-rede usada para a integração. Se você quisesse acessar um serviço em pontos de extremidade de serviço:

1. configurar a integração VNet regional com seu aplicativo Web
1. Vá para o serviço de destino e configure os pontos de extremidade de serviço em relação à sub-rede usada para integração

### <a name="network-security-groups"></a>Grupos de segurança de rede

Você pode usar grupos de segurança de rede para bloquear o tráfego de entrada e saída para recursos em uma VNet. Um aplicativo que usa a integração VNet regional pode usar um [grupo de segurança de rede][VNETnsg] para bloquear o tráfego de saída para recursos em sua VNet ou na Internet. Para bloquear o tráfego para endereços públicos, você deve ter a configuração de aplicativo WEBSITE_VNET_ROUTE_ALL definida como 1. As regras de entrada em um NSG não se aplicam ao seu aplicativo porque a integração VNet afeta apenas o tráfego de saída de seu aplicativo.

Para controlar o tráfego de entrada para seu aplicativo, use o recurso de restrições de acesso. Um NSG que é aplicado à sua sub-rede de integração está em vigor, independentemente de quaisquer rotas aplicadas à sua sub-rede de integração. Se WEBSITE_VNET_ROUTE_ALL for definido como 1 e você não tiver nenhuma rota que afete o tráfego de endereço público em sua sub-rede de integração, todo o tráfego de saída ainda estará sujeito a NSGs atribuído à sua sub-rede de integração. Se WEBSITE_VNET_ROUTE_ALL não estiver definido, NSGs só será aplicado ao tráfego RFC1918.

### <a name="routes"></a>Rotas

Você pode usar tabelas de rotas para rotear o tráfego de saída do seu aplicativo para onde quiser. Por padrão, as tabelas de rotas afetam apenas o tráfego de destino do RFC1918. Se você definir WEBSITE_VNET_ROUTE_ALL como 1, todas as suas chamadas de saída serão afetadas. As rotas definidas em sua sub-rede de integração não afetarão as respostas a solicitações de aplicativo de entrada. Os destinos comuns podem incluir dispositivos de firewall ou gateways.

Se você quiser rotear todo o tráfego de saída local, poderá usar uma tabela de rotas para enviar todo o tráfego de saída para o gateway de ExpressRoute. Se você rotear o tráfego para um gateway, certifique-se de definir rotas na rede externa para enviar respostas.

As rotas Border Gateway Protocol (BGP) também afetam o tráfego do aplicativo. Se você tiver rotas BGP de algo como um gateway de ExpressRoute, o tráfego de saída do aplicativo será afetado. Por padrão, as rotas BGP afetam apenas o tráfego de destino RFC1918. Se WEBSITE_VNET_ROUTE_ALL for definido como 1, todo o tráfego de saída poderá ser afetado por suas rotas BGP.

### <a name="azure-dns-private-zones"></a>Zonas Privadas do DNS do Azure 

Depois que o aplicativo se integra à sua VNet, ele usa o mesmo servidor DNS com o qual sua VNet está configurada. Por padrão, seu aplicativo não funcionará com Zonas Privadas do DNS do Azure. Para trabalhar com Zonas Privadas do DNS do Azure você precisa adicionar as seguintes configurações de aplicativo:

1. WEBSITE_DNS_SERVER com o valor 168.63.129.16 
1. WEBSITE_VNET_ROUTE_ALL com valor 1

Essas configurações enviarão todas as suas chamadas de saída do seu aplicativo para sua VNet, além de habilitar seu aplicativo para usar zonas privadas do DNS do Azure.

### <a name="private-endpoints"></a>Pontos de extremidade privados

Se você quiser fazer chamadas para pontos de extremidade [privados][privateendpoints], será necessário integrar com zonas privadas do DNS do Azure ou gerenciar o ponto de extremidade privado no servidor DNS usado pelo seu aplicativo. 

<!--Image references-->
[4]: ../includes/media/web-sites-integrate-with-vnet/vnetint-appsetting.png

<!--Links-->
[VNETnsg]: https://docs.microsoft.com/azure/virtual-network/security-overview/
[privateendpoints]: https://docs.microsoft.com/azure/app-service/networking/private-endpoint
