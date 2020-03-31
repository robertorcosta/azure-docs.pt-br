---
author: ccompy
ms.service: app-service-web
ms.topic: include
ms.date: 02/27/2020
ms.author: ccompy
ms.openlocfilehash: c731e2a7d4da1a66aabb50b335d526fbb6a0a302
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78671513"
---
O uso da Integração VNet regional permite que seu aplicativo acesse:

* recursos no VNet na mesma região que você integra com 
* recursos em VNets peered para o seu VNet que estão na mesma região
* ponto final de serviço serviço saqueado serviços
* recursos através de conexões ExpressRoute
* recursos no VNet a que você está conectado
* recursos em conexões com pares, incluindo conexões ExpressRoute
* pontos finais privados 

Ao usar a Integração VNet com VNets na mesma região, você pode usar os seguintes recursos de Rede Azure:

* Grupos de segurança de rede (NSGs) - Você pode bloquear o tráfego de saída com um Grupo de Segurança de Rede que é colocado em sua sub-rede de integração. As regras de entrada não se aplicam, pois você não pode usar o VNet Integration para fornecer acesso de entrada ao seu aplicativo.
* Tabelas de rotas (UDRs) - Você pode colocar uma tabela de rotas na sub-rede de integração para enviar tráfego de saída onde quiser. 

Por padrão, seu aplicativo só irá direcionar o tráfego RFC1918 para o seu VNet. Se você quiser direcionar todo o seu tráfego de saída para o seu VNet, aplique a configuração do aplicativo WEBSITE_VNET_ROUTE_ALL ao seu aplicativo. Para configurar a configuração do aplicativo:

1. Vá para a ui de configuração no portal do seu aplicativo. Selecione **Nova configuração de aplicativo**
1. Digite **WEBSITE_VNET_ROUTE_ALL** no campo Nome e **1** no campo Valor

   ![Fornecer configuração de aplicativo][4]

1. Selecione **OK**
1. Selecione **Salvar**

Se você direcionar todo o seu tráfego de saída para o seu VNet, então ele estará sujeito aos NSGs e UDRs que são aplicados à sua sub-rede de integração. Quando você direciona todo o seu tráfego de saída para o seu VNet, seus endereços de saída ainda serão os endereços de saída listados nas propriedades do aplicativo, a menos que você forneça rotas para enviar o tráfego para outro lugar. 

Existem algumas limitações com o uso da Integração VNet com VNets na mesma região:

* Você não pode alcançar recursos em conexões globais de peering
* O recurso só está disponível em unidades de escala de serviço de aplicativo mais novas que suportam planos premiumv2 app service.
* A sub-rede de integração só pode ser usada por apenas um plano de Serviço de Aplicativo
* O recurso não pode ser usado por aplicativos de plano isolado que estão em um ambiente de serviço de aplicativos
* O recurso requer uma sub-rede não utilizada que seja um /27 com 32 endereços ou maior estonteantes em um VNet do Gerenciador de recursos
* O aplicativo e a VNet devem estar na mesma região
* Você não pode excluir uma VNet com um aplicativo integrado. Remova a integração antes de excluir o VNet. 
* Você só pode se integrar com VNets na mesma assinatura do aplicativo
* Você pode ter apenas um plano regional de Integração VNet por Serviço de Aplicativo. Vários aplicativos no mesmo plano de Serviço de Aplicativo podem usar o mesmo VNet. 
* Você não pode alterar a assinatura de um aplicativo ou um plano enquanto houver um aplicativo que esteja usando a Regional VNet Integration

Um endereço é usado para cada instância do plano. Se você dimensionar seu aplicativo para cinco instâncias, então cinco endereços serão usados. Uma vez que o tamanho da sub-rede não pode ser alterado após a atribuição, você deve usar uma sub-rede que seja grande o suficiente para acomodar qualquer escala que seu aplicativo possa alcançar. Um /26 com 64 endereços é o tamanho recomendado. Um /26 com 64 endereços acomodará um plano Premium com 30 instâncias. Quando você escala um plano para cima ou para baixo, você precisa de duas vezes mais endereços por um curto período de tempo. 

Se você quer que seus aplicativos em outro plano cheguem a um VNet que já esteja conectado por aplicativos em outro plano, você precisa selecionar uma sub-rede diferente da usada pela Pré-Existente VNet Integration.  

O recurso está em pré-visualização para Linux. O formulário Linux do recurso só suporta fazer chamadas para endereços RFC 1918 (10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16).

### <a name="web--function-app-for-containers"></a>Web / Aplicativo de função para contêineres

Se você hospedar seu aplicativo no Linux com as imagens incorporadas, a Integração VNet regional funcionará sem alterações adicionais. Se você usa o Aplicativo Web / Função para Contêineres, você precisa modificar sua imagem de docker para usar a Integração VNet. Na imagem do docker, use a variável ambiente PORT como a porta de escuta do servidor web principal, em vez de usar um número de porta codificado. A variável ambiente PORT é definida automaticamente pela plataforma no momento da inicialização do contêiner. Se você estiver usando SSH, então o daemon SSH deve ser configurado para ouvir o número da porta especificado pela variável ambiente SSH_PORT ao usar a integração vnet regional.  Não há suporte para a Integração VNet necessária no Linux. 

### <a name="service-endpoints"></a>Pontos de extremidade de serviço

A Integração VNet Regional permite que você use pontos finais de serviço.  Para usar pontos finais de serviço com seu aplicativo, use a Integração VNet regional para se conectar a um VNet selecionado e, em seguida, configure pontos finais de serviço na sub-rede usada para a integração. 

### <a name="network-security-groups"></a>Grupos de segurança de rede

Os Grupos de Segurança de Rede permitem bloquear o tráfego de entrada e saída para recursos em um VNet. Um aplicativo que usa a Integração VNet regional pode usar o [Network Security Group][VNETnsg] para bloquear o tráfego de saída para recursos em seu VNet ou na internet. Para bloquear o tráfego em endereços públicos, você deve ter a configuração do aplicativo WEBSITE_VNET_ROUTE_ALL definida como 1. As regras de entrada em um NSG não se aplicam ao seu aplicativo, pois a Integração VNet afeta apenas o tráfego de saída do seu aplicativo. Para controlar o tráfego de entrada no seu aplicativo, use o recurso Restrições de acesso. Um NSG aplicado à sua sub-rede de integração estará em vigor independentemente de quaisquer rotas aplicadas à sua sub-rede de integração. Se WEBSITE_VNET_ROUTE_ALL foi definida como 1 e você não tiver nenhuma rota afetando o tráfego de endereços públicos em sua sub-rede de integração, todo o seu tráfego de saída ainda estaria sujeito a NSGs atribuídos à sua sub-rede de integração. Se WEBSITE_VNET_ROUTE_ALL não fosse definido, os NSGs só seriam aplicados ao tráfego RFC1918.

### <a name="routes"></a>Rotas

As Tabelas de rota permitem que você direcione o tráfego de saída do seu aplicativo para onde quiser. Por padrão, as tabelas de rota afetarão apenas o tráfego de destino RFC1918.  Se você definir WEBSITE_VNET_ROUTE_ALL para 1, então todas as suas chamadas de saída serão afetadas. As rotas definidas na sua sub-rede de integração não afetarão as respostas às solicitações de aplicativos de entrada. Destinos comuns podem incluir dispositivos de firewall ou gateways. Se você quiser encaminhar todo o tráfego de saída no local, você pode usar uma tabela de rota para enviar todo o tráfego de saída para o gateway ExpressRoute. Se você fizer o tráfego de rota para um gateway, certifique-se de definir rotas na rede externa para enviar quaisquer respostas de volta.

As rotas do Border Gateway Protocol (BGP) também afetarão o tráfego do aplicativo. Se você tiver rotas BGP a partir de algo como um gateway ExpressRoute, o tráfego de saída do aplicativo será afetado. Por padrão, as rotas BGP afetarão apenas o tráfego de destino RFC1918. Se WEBSITE_VNET_ROUTE_ALL estiver definido como 1, todo o tráfego de saída pode ser afetado pelas rotas BGP. 


<!--Image references-->
[4]: ../includes/media/web-sites-integrate-with-vnet/vnetint-appsetting.png

<!--Links-->
[VNETnsg]: https://docs.microsoft.com/azure/virtual-network/security-overview/