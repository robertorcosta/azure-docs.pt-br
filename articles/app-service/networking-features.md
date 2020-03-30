---
title: Recursos de rede
description: Conheça os recursos da rede no Azure App Service e quais recursos você precisa para as necessidades de sua rede para segurança ou funcionalidade.
author: ccompy
ms.assetid: 5c61eed1-1ad1-4191-9f71-906d610ee5b7
ms.topic: article
ms.date: 03/16/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: bc3d35830d833c0223a400140c53e583d2f6ed37
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79475418"
---
# <a name="app-service-networking-features"></a>Recursos de rede do App Service

Os aplicativos no Azure App Service podem ser implantados de várias maneiras. Por padrão, os aplicativos hospedados do App Service são diretamente acessíveis à Internet e só podem atingir pontos finais hospedados na Internet. Muitos aplicativos de clientes, no entanto, precisam controlar o tráfego de rede de entrada e saída. Existem vários recursos disponíveis no App Service para satisfazer essas necessidades. O desafio é saber qual recurso deve ser usado para resolver um determinado problema. Este documento destina-se a ajudar os clientes a determinar qual recurso deve ser usado com base em alguns casos de uso de exemplo.

Existem dois tipos de implantação principais para o Azure App Service. Há o serviço público multi-inquilino, que hospeda planos de Serviço de Aplicativo nos SKUs de preços gratuitos, compartilhados, básicos, padrão, premium e premiumv2. Em seguida, há o ASE (App Service Environment, ambiente de serviço de aplicativo de aplicativo de inquilino único), que hospeda planos isolados do Serviço de Aplicativos SKU diretamente em sua Rede Virtual Azure (VNet). Os recursos que você usa variam se você estiver no serviço multi-inquilino ou em um ASE. 

## <a name="multi-tenant-app-service-networking-features"></a>Recursos de rede de serviços de aplicativos com vários locatários 

O Azure App Service é um sistema distribuído. As funções que lidam com as solicitações HTTP/HTTPS recebidas são chamadas de front-ends. As funções que hospedam a carga horária do cliente são chamadas de trabalhadores. Todas as funções em uma implantação do App Service existem em uma rede de vários inquilinos. Como há muitos clientes diferentes na mesma unidade de escala do App Service, você não pode conectar a rede app service diretamente à sua rede. Em vez de conectar as redes, precisamos de recursos para lidar com os diferentes aspectos da comunicação de aplicativos. Os recursos que lidam com solicitações PARA o seu aplicativo não podem ser usados para resolver problemas ao fazer chamadas do seu aplicativo. Da mesma forma, os recursos que resolvem problemas para chamadas do seu aplicativo não podem ser usados para resolver problemas para o seu aplicativo.  

| Recursos de entrada | Recursos de saída |
|---------------------|-------------------|
| Endereço atribuído ao aplicativo | Conexões Híbridas |
| Restrições de acesso | Gateway necessário VNet Integração |
| Pontos de extremidade de serviço | Integração VNet |

Salvo disposição em contrário, todos os recursos podem ser usados juntos. Você pode misturar os recursos para resolver seus vários problemas.

## <a name="use-case-and-features"></a>Use case e recursos

Para qualquer caso de uso, pode haver algumas maneiras de resolver o problema.  O recurso certo para usar às vezes é devido a razões além apenas do caso de uso em si. Os seguintes casos de uso de entrada sugerem como usar os recursos de rede do App Service para resolver problemas em torno do controle do tráfego que vai para o seu aplicativo. 
 
| Casos de uso de entrada | Recurso |
|---------------------|-------------------|
| Suporte às necessidades de SSL baseadas em IP para o seu aplicativo | endereço atribuído aplicativo |
| Endereço de entrada não compartilhado e dedicado para o seu aplicativo | endereço atribuído aplicativo |
| Restringir o acesso ao seu aplicativo a partir de um conjunto de endereços bem definidos | Restrições de acesso |
| Restringir o acesso ao meu aplicativo a partir de recursos em um VNet | Pontos de extremidade de serviço </br> ILB ASE </br> Ponto final privado (Visualização) |
| Exponha meu aplicativo em um IP privado no meu VNet | ILB ASE </br> IP privado para entrada em um Gateway de aplicativo com pontos finais de serviço </br> Ponto final de serviço (visualização) |
| Proteja meu aplicativo com um WAF | Gateway de aplicativo + ILB ASE </br> Gateway de Aplicativo com pontos de extremidade de serviço </br> Porta da frente do Azure com restrições de acesso |
| Carregar tráfego de equilíbrio para meus aplicativos em diferentes regiões | Porta da frente do Azure com restrições de acesso | 
| Tráfego de equilíbrio de carga na mesma região | [Gateway de Aplicativo com pontos de extremidade de serviço][appgwserviceendpoints] | 

Os seguintes casos de uso de saída sugerem como usar os recursos de rede do App Service para resolver as necessidades de acesso de saída para o seu aplicativo. 

| Casos de uso de saída | Recurso |
|---------------------|-------------------|
| Recursos de acesso em uma Rede Virtual Azure na mesma região | Integração VNet </br> ASE |
| Recursos de acesso em uma Rede Virtual Azure em uma região diferente | Gateway necessário VNet Integração </br> Peering ASE e VNet |
| Recursos de acesso protegidos com pontos finais de serviço | Integração VNet </br> ASE |
| Acessar recursos em uma rede privada não conectada ao Azure | Conexões Híbridas |
| Recursos de acesso em circuitos ExpressRoute | Integração VNet </br> ASE | 
| Tráfego de saída seguro do seu aplicativo web | Grupos de integração e segurança de rede da VNet </br> ASE | 
| Tráfego de saída de rota do seu aplicativo web | Tabelas de integração e rota da VNet </br> ASE | 


### <a name="default-networking-behavior"></a>Comportamento padrão de rede

As unidades da escala Azure App Service suportam muitos clientes em cada implantação. O SKU livre e compartilhado planeja hospedar cargas de trabalho de clientes em trabalhadores multi-inquilinos. O Básico e acima planos hospedam cargas de trabalho de clientes que são dedicadas a apenas um plano de Serviço de Aplicativo (ASP). Se você tinha um plano standard app service, então todos os aplicativos nesse plano serão executados no mesmo trabalhador. Se você dimensionar o trabalhador, todos os aplicativos nesse ASP serão replicados em um novo trabalhador para cada instância em seu ASP. Os trabalhadores que são utilizados para o Premiumv2 são diferentes dos trabalhadores utilizados para os outros planos. Cada implantação do App Service tem um endereço IP que é usado para todo o tráfego de entrada para os aplicativos nessa implantação do App Service. No entanto, existem de 4 a 11 endereços usados para fazer chamadas de saída. Esses endereços são compartilhados por todos os aplicativos nessa implantação do App Service. Os endereços de saída são diferentes com base nos diferentes tipos de trabalhadores. Isso significa que os endereços usados pelos ASPs Gratuitos, Compartilhados, Básicos, Padrão e Premium são diferentes dos endereços usados para chamadas de saída dos ASPs Premiumv2. Se você olhar nas propriedades do seu aplicativo, você poderá ver os endereços de entrada e saída que são usados pelo seu aplicativo. Se você precisar bloquear uma dependência com uma ACL IP, use os possíveisEndereços de Saída. 

![Propriedades do aplicativo](media/networking-features/app-properties.png)

O App Service tem uma série de pontos finais que são usados para gerenciar o serviço.  Esses endereços são publicados em um documento separado e também estão na tag de serviço IP do AppServiceManagement. A tag AppServiceManagement só é usada com um App Service Environment (ASE) onde você precisa permitir esse tráfego. Os endereços de entrada do App Service são rastreados na tag de serviço IP do AppService. Não há nenhuma tag de serviço IP que contenha os endereços de saída usados pelo App Service. 

![Diagrama de entrada e saída do Serviço de Aplicativos](media/networking-features/default-behavior.png)

### <a name="app-assigned-address"></a>Endereço atribuído ao aplicativo 

O recurso de endereço atribuído ao aplicativo é um desdobramento do recurso SSL baseado em IP e é acessado configurando SSL com seu aplicativo. Esse recurso pode ser usado para chamadas SSL baseadas em IP, mas também pode ser usado para dar ao seu aplicativo um endereço que só ele tem. 

![Diagrama de endereço atribuído pelo aplicativo](media/networking-features/app-assigned-address.png)

Quando você usa um endereço atribuído a um aplicativo, seu tráfego ainda passa pelas mesmas funções front-end que lidam com todo o tráfego de entrada na unidade de escala do Serviço de Aplicativo. O endereço atribuído ao seu aplicativo, no entanto, só é usado pelo seu aplicativo. Os casos de uso para este recurso são:

* Suporte às necessidades de SSL baseadas em IP para o seu aplicativo
* Defina um endereço dedicado para o seu aplicativo que não seja compartilhado com qualquer outra coisa

Você pode aprender como definir um endereço em seu aplicativo com o tutorial sobre [Configuração de SSL baseado em IP][appassignedaddress]. 

### <a name="access-restrictions"></a>Restrições de acesso 

O recurso Restrições de acesso permite filtrar solicitações **de entrada** com base no endereço IP de originação. A ação de filtragem ocorre nas funções front-end que estão a montante das funções do trabalhador onde seus aplicativos estão sendo executados. Uma vez que as funções front-end são upstream dos trabalhadores, o recurso Restrições de Acesso pode ser considerado como proteção de nível de rede para seus aplicativos. O recurso permite que você construa uma lista de permitir e negar blocos de endereços que são avaliados em ordem prioritária. É semelhante ao recurso DE NETWORK SECURITY Group (NSG) que existe no Azure Networking.  Você pode usar esse recurso em um ASE ou no serviço multi-inquilino. Quando usado com um ILB ASE, você pode restringir o acesso a partir de blocos de endereços privados.

![Restrições de acesso](media/networking-features/access-restrictions.png)

O recurso Restrições de Acesso ajuda em cenários onde você deseja restringir os endereços IP que podem ser usados para acessar seu aplicativo. Entre os casos de uso para este recurso estão:

* Restringir o acesso ao seu aplicativo a partir de um conjunto de endereços bem definidos 
* Restringir o acesso a entrar em um serviço de balanceamento de carga, como o Azure Front Door. Se você quiser bloquear seu tráfego de entrada para o Azure Front Door, crie regras para permitir o tráfego de 147.243.0.0/16 e 2a01:111:2050::/44. 

![Restrições de acesso com porta da frente](media/networking-features/access-restrictions-afd.png)

Se você deseja bloquear o acesso ao seu aplicativo para que ele só possa ser alcançado a partir de recursos em sua Rede Virtual Azure (VNet), você precisa de um endereço público estático em qualquer que seja sua fonte em seu VNet. Se os recursos não tiverem um endereço público, você deve usar o recurso Pontos finais de serviço. Saiba como ativar esse recurso com o tutorial sobre [Configuração de Restrições de Acesso][iprestrictions].

### <a name="service-endpoints"></a>Pontos de extremidade de serviço

Os pontos finais de serviço permitem que você bloqueie o acesso **de entrada** ao seu aplicativo, de forma que o endereço de origem deve vir de um conjunto de sub-redes que você seleciona. Esse recurso funciona em conjunto com as restrições de acesso ip. Os pontos finais do serviço são definidos na mesma experiência do usuário que as Restrições de Acesso IP. Você pode construir uma lista de regras de acesso que inclui endereços públicos e sub-redes em seus VNets. Este recurso suporta cenários como:

![pontos de extremidade de serviço](media/networking-features/service-endpoints.png)

* Configurando um Gateway de aplicativo com seu aplicativo para bloquear o tráfego de entrada para o seu aplicativo
* Restringindo o acesso ao seu aplicativo a recursos em seu VNet. Isso pode incluir VMs, ASEs ou até mesmo outros aplicativos que usam a Integração VNet 

![pontos finais de serviço com gateway de aplicativo](media/networking-features/service-endpoints-appgw.png)

Você pode aprender mais sobre como configurar pontos finais de serviço com seu aplicativo no tutorial sobre [Configuração de restrições de acesso ao ponto final do serviço][serviceendpoints]

### <a name="private-endpoint-preview"></a>Ponto final privado (visualização)

Private Endpoint é uma interface de rede que o conecta de forma privada e segura ao seu Aplicativo web pelo Azure Private Link. O Private Endpoint usa um endereço IP privado do seu VNet, efetivamente trazendo o Web App para o seu VNet. Este recurso é apenas para fluxos **de entrada** para o seu Aplicativo web.
[Usando pontos finais privados para o aplicativo Web Do Azure (Preview)][privateendpoints]
 
### <a name="hybrid-connections"></a>Conexões Híbridas

O App Service Hybrid Connections permite que seus aplicativos façam chamadas **de saída** para pontos finais TCP especificados. O ponto final pode ser no local, em um VNet ou em qualquer lugar que permita tráfego de saída para o Azure na porta 443. O recurso requer a instalação de um agente de relé chamado Hybrid Connection Manager (HCM) em um Windows Server 2012 ou host mais novo. O HCM precisa ser capaz de alcançar o Azure Relay na porta 443. O HCM pode ser baixado da II de Conexões Híbridas do Serviço de Aplicativo no portal. 

![Fluxo de rede de conexões híbridas](media/networking-features/hybrid-connections.png)

O recurso Conexões Híbridas do Serviço de Aplicativo é construído sobre o recurso Conexões Híbridas do Relé Azure. O App Service usa uma forma especializada do recurso que só suporta fazer chamadas de saída do seu aplicativo para um host e porta TCP. Este host e a porta só precisam ser resolvidos no host onde o HCM está instalado. Quando o aplicativo, no App Service, faz uma olhada no DNS no host e na porta definida em sua Conexão Híbrida, o tráfego é automaticamente redirecionado para passar pela Conexão Híbrida e sair do Hybrid Connection Manager. Para saber mais sobre conexões híbridas, leia a documentação sobre [conexões híbridas de serviço de aplicativo][hybridconn]

Este recurso é comumente usado para:

* Acesse recursos em redes privadas que não estão conectadas ao Azure com uma VPN ou ExpressRoute
* Suporte ao levantamento e mudança de aplicativos locais para o App Service sem precisar também mover bancos de dados de suporte  
* Forneça com segurança acesso a um único host e porta por Conexão Híbrida. A maioria dos recursos de rede tem acesso aberto a uma rede e com conexões híbridas você só tem o host único e a porta que você pode alcançar.
* Cobrir cenários não cobertos por outros métodos de conectividade de saída
* Realize o desenvolvimento no App Service, onde os aplicativos podem facilmente aproveitar os recursos no local 

Como o recurso permite o acesso a recursos no local sem um buraco de firewall de entrada, ele é popular entre os desenvolvedores. Os outros recursos de rede de serviço de aplicativo de saída são muito relacionados à Rede Virtual Azure. As Conexões Híbridas não têm dependência de passar por um VNet e podem ser usadas para uma variedade mais ampla de necessidades de rede. É importante notar que o recurso App Service Hybrid Connections não se importa ou sabe o que você está fazendo em cima dele. Ou seja, você pode usá-lo para acessar um banco de dados, um serviço web ou um soquete TCP arbitrário em um mainframe. O recurso essencialmente encapsula pacotes TCP. 

Embora o Hybrid Connections seja popular para o desenvolvimento, ele também é usado em inúmeras aplicações de produção. É ótimo para acessar um serviço web ou banco de dados, mas não é apropriado para situações que envolvam a criação de muitas conexões. 

### <a name="gateway-required-vnet-integration"></a>Gateway necessário VNet Integração 

O recurso de integração VNet do Serviço de Aplicativo necessário permite que seu aplicativo faça solicitações **de saída** em uma Rede Virtual Azure. O recurso funciona conectando o host que seu aplicativo está executando em um gateway de Rede Virtual em sua VNet com uma VPN ponto a ponto. Quando você configura o recurso, seu aplicativo recebe um dos endereços ponto a ponto atribuídos a cada instância. Esse recurso permite que você acesse recursos em VNets clássicos ou gerenciadores de recursos em qualquer região. 

![Gateway necessário VNet Integração](media/networking-features/gw-vnet-integration.png)

Esse recurso resolve o problema de acessar recursos em outros VNets e pode até ser usado para se conectar através de um VNet a outros VNets ou mesmo no local. Ele não funciona com VNets conectados ao ExpressRoute, mas funciona com redes conectadas vpn site-to-site. Normalmente é inapropriado usar esse recurso de um aplicativo em um App Service Environment (ASE), porque o ASE já está em seu VNet. Os casos de uso que este recurso resolve são:

* Acessando recursos em IPs privados em suas redes virtuais Do Azure 
* Acessando recursos no local se houver uma VPN site-to-site 
* Acessando recursos em VNets peered 

Quando esse recurso estiver ativado, seu aplicativo usará o servidor DNS com o que o VNet de destino está configurado. Você pode ler mais sobre este recurso na documentação no [App Service VNet Integration][vnetintegrationp2s]. 

### <a name="vnet-integration"></a>Integração VNet

O recurso de integração VNet necessário é muito útil, mas ainda não resolve o acesso aos recursos através do ExpressRoute. Além de precisar alcançar as conexões ExpressRoute, há a necessidade de que os aplicativos sejam capazes de fazer chamadas para serviços garantidos no ponto final. Para resolver essas duas necessidades adicionais, outro recurso de Integração VNet foi adicionado. O novo recurso de Integração VNet permite que você coloque o backend do seu aplicativo em uma sub-rede em um VNet do Gerenciador de Recursos na mesma região. Esse recurso não está disponível em um Ambiente de Serviço de Aplicativo, que já está em um VNet. Esse recurso permite:

* Acessando recursos em VNets gerenciadores de recursos na mesma região
* Acessando recursos que são protegidos com pontos finais de serviço 
* Acessando recursos acessíveis em conexões ExpressRoute ou VPN
* Protegendo todo o tráfego de saída 
* Força a túnel de todo o tráfego de saída. 

![Integração VNet](media/networking-features/vnet-integration.png)

Para saber mais sobre esse recurso, leia os docs no [App Service VNet Integration][vnetintegration].

## <a name="app-service-environment"></a>Ambiente do Serviço de Aplicativo 

Um App Service Environment (ASE) é uma única implantação de inquilino do Azure App Service que é executado em seu VNet. O ASE permite o uso de casos como:

* Acesse recursos em seu VNet
* Recursos de acesso em ExpressRoute
* Exponha seus aplicativos com um endereço privado em seu VNet 
* Acessar recursos em pontos finais de serviço 

Com um ASE, você não precisa usar recursos como O VNet Integration ou pontos finais de serviço porque o ASE já está em seu VNet. Se você quiser acessar recursos como SQL ou Armazenamento sobre pontos finais de serviço, habilite pontos finais de serviço na sub-rede ASE. Se você quiser acessar recursos no VNet, não há nenhuma configuração adicional necessária.  Se você quiser acessar recursos através do ExpressRoute, você já está no VNet e não precisa configurar nada no ASE ou nos aplicativos dentro dele. 

Como os aplicativos em um ILB ASE podem ser expostos em um endereço IP privado, você pode facilmente adicionar dispositivos WAF para expor apenas os aplicativos que você deseja para a internet e manter o resto seguro. Ele se presta ao desenvolvimento fácil de aplicativos de vários níveis. 

Existem algumas coisas que ainda não são possíveis a partir do serviço multi-inquilino que são de uma ASE. Isso inclui coisas como:

* Exponha seus aplicativos em um endereço IP privado
* Proteja todo o tráfego de saída com controles de rede que não fazem parte do seu aplicativo 
* Hospede seus aplicativos em um único serviço de inquilino 
* Dimensione até muito mais instâncias do que são possíveis no serviço multi-inquilino 
* Carregue certificados de cliente ca privados para uso por seus aplicativos com pontos finais protegidos por CA privados 
* Force O TLS 1.1 em todos os aplicativos hospedados no sistema sem qualquer capacidade de desativar no nível do aplicativo 
* Forneça um endereço de saída dedicado para todos os aplicativos em seu ASE que não seja compartilhado com nenhum cliente 

![ASE em um VNet](media/networking-features/app-service-environment.png)

A ASE fornece a melhor história em torno de hospedagem de aplicativos isolada e dedicada, mas vem com alguns desafios de gerenciamento. Algumas coisas a considerar antes de usar um ASE operacional são:
 
 * Um ASE é executado dentro do seu VNet, mas tem dependências fora do VNet. Essas dependências devem ser permitidas. Leia mais em [considerações em rede para um ambiente de serviço de aplicativo][networkinfo]
 * Uma ASE não é dimensionada imediatamente como o serviço de vários inquilinos. Você precisa antecipar as necessidades de dimensionamento em vez de escalar reativamente. 
 * Uma ASE tem um custo inicial mais alto associado a ele. A fim de tirar o máximo do seu ASE, você deve planejar colocar muitas cargas de trabalho em um ASE em vez de tê-lo usado para pequenos esforços
 * Os aplicativos em um ASE não podem restringir o acesso a alguns aplicativos em um ASE e não outros.
 * A ASE está em uma sub-rede e todas as regras de rede se aplicam a todo o tráfego de e para essa ASE. Se você quiser atribuir regras de tráfego de entrada para apenas um aplicativo, use Restrições de acesso. 

## <a name="combining-features"></a>Combinando recursos 

Os recursos conhecidos pelo serviço multilocatário podem ser usados em conjunto para resolver casos de uso mais elaborados. Dois dos casos de uso mais comuns são descritos aqui, mas são apenas exemplos. Ao entender o que os vários recursos fazem, você pode resolver quase todas as necessidades de arquitetura do seu sistema.

### <a name="inject-app-into-a-vnet"></a>Injete aplicativo em um VNet

Uma solicitação comum é sobre como colocar seu aplicativo em um VNet. Colocar seu aplicativo em um VNet significa que os pontos finais de entrada e saída de um aplicativo estão dentro de um VNet. A ASE fornece a melhor solução para resolver esse problema, mas, você pode obter a maior parte do que é necessário no serviço multi-inquilino, combinando recursos. Por exemplo, você pode hospedar aplicativos somente de intranet com endereços privados de entrada e saída por:

* Criando um Gateway de aplicativo com endereço de entrada e saída privado
* Protegendo o tráfego de entrada para o seu aplicativo com pontos finais de serviço 
* Use a nova Integração VNet para que o backend do seu aplicativo esteja no seu VNet 

Este estilo de implantação não lhe daria um endereço dedicado para tráfego de saída para a internet ou lhe daria a capacidade de bloquear todo o tráfego de saída do seu aplicativo.  Este estilo de implantação lhe daria muito do que você só obteria de outra forma com um ASE. 

### <a name="create-multi-tier-applications"></a>Crie aplicativos de vários níveis

Um aplicativo multinível é um aplicativo onde os aplicativos de back-end da API só podem ser acessados a partir do nível front-end. Para criar um aplicativo de vários níveis, você pode:

* Use a Integração VNet para conectar o backend do seu aplicativo web front-end com uma sub-rede em um VNet
* Use pontos finais de serviço para proteger o tráfego de entrada para o seu aplicativo de API para apenas proveniente da sub-rede usada pelo seu aplicativo web front-end

![aplicativo de vários níveis](media/networking-features/multi-tier-app.png)

Você pode ter vários aplicativos front-end usando o mesmo aplicativo de API usando o VNet Integration dos outros aplicativos front-end e pontos finais de serviço do aplicativo API com suas subredes.  

<!--Links-->
[appassignedaddress]: https://docs.microsoft.com/azure/app-service/configure-ssl-certificate
[iprestrictions]: https://docs.microsoft.com/azure/app-service/app-service-ip-restrictions
[serviceendpoints]: https://docs.microsoft.com/azure/app-service/app-service-ip-restrictions
[hybridconn]: https://docs.microsoft.com/azure/app-service/app-service-hybrid-connections
[vnetintegrationp2s]: https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet
[vnetintegration]: https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet
[networkinfo]: https://docs.microsoft.com/azure/app-service/environment/network-info
[appgwserviceendpoints]: https://docs.microsoft.com/azure/app-service/networking/app-gateway-with-service-endpoints
[privateendpoints]: https://docs.microsoft.com/azure/app-service/networking/private-endpoint
