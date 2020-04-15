---
author: ccompy
ms.service: app-service-web
ms.topic: include
ms.date: 04/15/2020
ms.author: ccompy
ms.openlocfilehash: 7f2b011b2de5af0e4ace9cbeb4399911d8e83b7f
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81312806"
---
O uso da Integração VNet regional permite que seu aplicativo acesse:

* Recursos em um VNet na mesma região que seu aplicativo.
* Recursos em VNets associados ao VNet com o seu aplicativo está integrado.
* O ponto final do serviço garantiu serviços.
* Recursos em conexões Azure ExpressRoute.
* Recursos no VNet com os que você está integrado.
* Recursos em conexões peered, que inclui conexões Azure ExpressRoute.
* Pontos finais privados - Nota: o DNS deve ser gerenciado separadamente em vez de usar as zonas privadas do Azure DNS.

Quando você usa a Integração VNet com VNets na mesma região, você pode usar os seguintes recursos de rede do Azure:

* **Grupos de segurança de rede (NSGs)**: Você pode bloquear o tráfego de saída com um NSG colocado em sua sub-rede de integração. As regras de entrada não se aplicam porque você não pode usar o VNet Integration para fornecer acesso de entrada ao seu aplicativo.
* **Tabelas de rota (UDRs)**: Você pode colocar uma tabela de rota na sub-rede de integração para enviar tráfego de saída onde quiser.

Por padrão, seu aplicativo roteia apenas o tráfego RFC1918 em seu VNet. Se você quiser direcionar todo o seu tráfego de saída para o seu VNet, aplique a configuração do aplicativo WEBSITE_VNET_ROUTE_ALL ao seu aplicativo. Para configurar a configuração do aplicativo:

1. Vá para a **ui de configuração** no portal do seu aplicativo. Selecione **Nova configuração de aplicativo**.
1. Digite **WEBSITE_VNET_ROUTE_ALL** na caixa **Nome** e digite **1** na caixa **Valor.**

   ![Fornecer configuração de aplicativo][4]

1. Selecione **OK**.
1. Clique em **Salvar**.

Se você direcionar todo o seu tráfego de saída para o seu VNet, ele está sujeito aos NSGs e UDRs que são aplicados à sua sub-rede de integração. Quando você direciona todo o seu tráfego de saída para o seu VNet, seus endereços de saída ainda são os endereços de saída listados nas propriedades do aplicativo, a menos que você forneça rotas para enviar o tráfego para outro lugar.

Existem algumas limitações com o uso da Integração VNet com VNets na mesma região:

* Você não pode alcançar recursos através de conexões globais de peering.
* O recurso está disponível apenas em unidades de escala mais novas do Azure App Service que suportam planos premiumV2 App Service.
* A sub-rede de integração pode ser usada por apenas um plano de Serviço de Aplicativo.
* O recurso não pode ser usado por aplicativos de plano isolados que estão em um ambiente de serviço de aplicativos.
* O recurso requer uma sub-rede não utilizada que seja uma /27 com 32 endereços ou maior escancarado em um VNet do Azure Resource Manager.
* O aplicativo e o VNet devem estar na mesma região.
* Você não pode excluir um VNet com um aplicativo integrado. Remova a integração antes de excluir o VNet.
* Você só pode se integrar com VNets na mesma assinatura do aplicativo.
* Você pode ter apenas um plano regional de Integração VNet por Serviço de Aplicativo. Vários aplicativos no mesmo plano de Serviço de Aplicativo podem usar o mesmo VNet.
* Você não pode alterar a assinatura de um aplicativo ou um plano enquanto há um aplicativo que está usando a Integração VNet regional.
* Seu aplicativo não pode resolver endereços em Zonas Privadas Do Azure DNS.

Um endereço é usado para cada instância do plano. Se você dimensionar seu aplicativo para cinco instâncias, então cinco endereços serão usados. Uma vez que o tamanho da sub-rede não pode ser alterado após a atribuição, você deve usar uma sub-rede que seja grande o suficiente para acomodar qualquer escala que seu aplicativo possa alcançar. Um /26 com 64 endereços é o tamanho recomendado. Um /26 com 64 endereços acomoda um plano Premium com 30 instâncias. Quando você escala um plano para cima ou para baixo, você precisa de duas vezes mais endereços por um curto período de tempo.

Se você quiser que seus aplicativos em outro plano cheguem a um VNet que já esteja conectado por aplicativos em outro plano, selecione uma sub-rede diferente da usada pela Pré-existente Integração VNet.

O recurso está em pré-visualização para Linux. O formulário Linux do recurso só suporta fazer chamadas para endereços RFC 1918 (10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16).

### <a name="web-or-function-app-for-containers"></a>Aplicativo web ou função para contêineres

Se você hospedar seu aplicativo no Linux com as imagens incorporadas, a Integração VNet regional funcionará sem alterações adicionais. Se você usar o Web ou o Function App for Containers, você deve modificar sua imagem de docker para usar a Integração VNet. Na imagem do docker, use a variável ambiente PORT como a porta de escuta do servidor web principal, em vez de usar um número de porta codificado. A variável ambiente PORT é definida automaticamente pela plataforma no momento da inicialização do contêiner. Se você usar SSH, o daemon SSH deve ser configurado para ouvir o número da porta especificado pela SSH_PORT variável de ambiente quando você usa a Integração VNet regional. Não há suporte para a Integração VNet necessária por gateway no Linux.

### <a name="service-endpoints"></a>Pontos de extremidade de serviço

A Integração VNet Regional permite que você use pontos finais de serviço. Para usar pontos finais de serviço com seu aplicativo, use a Integração VNet regional para se conectar a um VNet selecionado e, em seguida, configure pontos finais de serviço com o serviço de destino na sub-rede usada para a integração. Se você quiser acessar um serviço sobre pontos finais de serviço:

1. configurar a integração regional do VNet com o seu aplicativo web
1. ir para o serviço de destino e configurar pontos finais de serviço contra a sub-rede usada para integração

### <a name="network-security-groups"></a>Grupos de segurança de rede

Você pode usar grupos de segurança de rede para bloquear o tráfego de entrada e saída para recursos em um VNet. Um aplicativo que usa a Integração VNet regional pode usar um [grupo de segurança de rede][VNETnsg] para bloquear o tráfego de saída para recursos em seu VNet ou na internet. Para bloquear o tráfego em endereços públicos, você deve ter a configuração do aplicativo WEBSITE_VNET_ROUTE_ALL definida como 1. As regras de entrada em um NSG não se aplicam ao seu aplicativo porque a Integração VNet afeta apenas o tráfego de saída do seu aplicativo.

Para controlar o tráfego de entrada no seu aplicativo, use o recurso Restrições de acesso. Um NSG aplicado à sua sub-rede de integração está em vigor, independentemente de quaisquer rotas aplicadas à sua sub-rede de integração. Se WEBSITE_VNET_ROUTE_ALL estiver definido como 1 e você não tiver nenhuma rota que afete o tráfego de endereços públicos em sua sub-rede de integração, todo o seu tráfego de saída ainda está sujeito a NSGs atribuídos à sua sub-rede de integração. Se WEBSITE_VNET_ROUTE_ALL não for definido, os NSGs só serão aplicados ao tráfego RFC1918.

### <a name="routes"></a>Rotas

Você pode usar tabelas de rotas para direcionar o tráfego de saída do seu aplicativo para onde quiser. Por padrão, as tabelas de rota afetam apenas o tráfego de destino RFC1918. Se você definir WEBSITE_VNET_ROUTE_ALL para 1, todas as suas chamadas de saída serão afetadas. As rotas definidas na sua sub-rede de integração não afetarão as respostas às solicitações de aplicativos de entrada. Destinos comuns podem incluir dispositivos de firewall ou gateways.

Se você quiser encaminhar todo o tráfego de saída no local, você pode usar uma tabela de rota para enviar todo o tráfego de saída para o gateway ExpressRoute. Se você fizer o tráfego de rota para um gateway, certifique-se de definir rotas na rede externa para enviar quaisquer respostas de volta.

As rotas do Border Gateway Protocol (BGP) também afetam o tráfego do aplicativo. Se você tiver rotas BGP a partir de algo como um gateway ExpressRoute, o tráfego de saída do aplicativo será afetado. Por padrão, as rotas BGP afetam apenas o tráfego de destino RFC1918. Se WEBSITE_VNET_ROUTE_ALL estiver definido como 1, todo o tráfego de saída pode ser afetado pelas rotas BGP.


<!--Image references-->
[4]: ../includes/media/web-sites-integrate-with-vnet/vnetint-appsetting.png

<!--Links-->
[VNETnsg]: https://docs.microsoft.com/azure/virtual-network/security-overview/
