---
title: Recursos de rede
description: Saiba mais sobre os recursos de rede no serviço Azure App e quais recursos são necessários para a segurança ou a funcionalidade de sua rede.
author: ccompy
ms.assetid: 5c61eed1-1ad1-4191-9f71-906d610ee5b7
ms.topic: article
ms.date: 10/18/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 860b1ac1713ac7afb7db2643d68974b399b5236b
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/19/2020
ms.locfileid: "92207029"
---
# <a name="app-service-networking-features"></a>Recursos de rede do serviço de aplicativo

Os aplicativos no serviço de Azure App podem ser implantados de várias maneiras. Por padrão, os aplicativos hospedados no serviço de aplicativo são diretamente acessíveis pela Internet e só podem acessar pontos de extremidade hospedados na Internet. No entanto, muitos aplicativos de clientes precisam controlar o tráfego de rede de entrada e saída. Há vários recursos disponíveis no serviço de aplicativo para atender a essas necessidades. O desafio é saber qual recurso deve ser usado para resolver um determinado problema. Este documento destina-se a ajudar os clientes a determinar qual recurso deve ser usado com base em alguns casos de uso de exemplo.

Há dois tipos de implantação principais para o serviço de Azure App. Há o serviço público multilocatário, que hospeda os planos do serviço de aplicativo nas SKUs de preços gratuita, compartilhada, básica, Standard, Premium, Premiumv2 e Premiumv3. Em seguida, há um único locatário Ambiente do Serviço de Aplicativo (ASE), que hospeda planos de serviço de aplicativo SKU isolados diretamente em sua VNet (rede virtual) do Azure. Os recursos que você usar irão variar em se você estiver no serviço multilocatário ou em um ASE. 

## <a name="multi-tenant-app-service-networking-features"></a>Recursos de rede do serviço de aplicativo multilocatário 

O serviço de Azure App é um sistema distribuído. As funções que tratam solicitações HTTP/HTTPS de entrada são chamadas de front-ends. As funções que hospedam a carga de trabalho do cliente são chamadas de trabalhadores. Todas as funções em uma implantação do serviço de aplicativo existem em uma rede de vários locatários. Como há muitos clientes diferentes na mesma unidade de escala do serviço de aplicativo, você não pode conectar a rede do serviço de aplicativo diretamente à sua rede. Em vez de conectar as redes, precisamos de recursos para lidar com os diferentes aspectos da comunicação do aplicativo. Os recursos que manipulam solicitações para seu aplicativo não podem ser usados para resolver problemas ao fazer chamadas de seu aplicativo. Da mesma forma, os recursos que resolvem problemas de chamadas de seu aplicativo não podem ser usados para resolver problemas em seu aplicativo.  

| Recursos de entrada | Recursos de saída |
|---------------------|-------------------|
| Endereço atribuído ao aplicativo | Conexões Híbridas |
| Restrições de acesso | Integração VNet necessária do gateway |
| Pontos de extremidade de serviço | Integração VNet |

Salvo indicação em contrário, todos os recursos podem ser usados juntos. Você pode misturar os recursos para resolver os diversos problemas.

## <a name="use-case-and-features"></a>Caso de uso e recursos

Para qualquer caso de uso específico, pode haver algumas maneiras de resolver o problema.  O recurso certo a ser usado às vezes é devido a motivos além do próprio caso de uso. Os seguintes casos de uso de entrada sugerem como usar os recursos de rede do serviço de aplicativo para resolver problemas relacionados ao controle de tráfego que vai para seu aplicativo. 
 
| Casos de uso de entrada | Recurso |
|---------------------|-------------------|
| Dar suporte às necessidades de SSL baseado em IP para seu aplicativo | endereço atribuído ao aplicativo |
| Não compartilhado, endereço de entrada dedicado para seu aplicativo | endereço atribuído ao aplicativo |
| Restringir o acesso ao seu aplicativo de um conjunto de endereços bem definidos | Restrições de acesso |
| Restringir o acesso ao meu aplicativo de recursos em uma VNet | Pontos de extremidade de serviço </br> ILB ASE </br> Pontos de extremidade privados |
| Expor meu aplicativo em um IP privado em minha VNet | ILB ASE </br> Pontos de extremidade privados </br> IP privado para entrada em um gateway de aplicativo com pontos de extremidade de serviço |
| Proteger meu aplicativo com um WAF (firewall do aplicativo Web) | Gateway de aplicativo + ILB ASE </br> Gateway de aplicativo com pontos de extremidade privados </br> Gateway de aplicativo com pontos de extremidade de serviço </br> Porta frontal do Azure com restrições de acesso |
| Balancear a carga do tráfego para meus aplicativos em regiões diferentes | Porta frontal do Azure com restrições de acesso | 
| Balancear a carga do tráfego na mesma região | [Gateway de Aplicativo com pontos de extremidade de serviço][appgwserviceendpoints] | 

Os seguintes casos de uso de saída sugerem como usar os recursos de rede do serviço de aplicativo para resolver as necessidades de acesso de saída para seu aplicativo. 

| Casos de uso de saída | Recurso |
|---------------------|-------------------|
| Acessar recursos em um Entrada na Rede virtual do Azure na mesma região | Integração VNet </br> ASE |
| Acessar recursos em um Entrada na Rede virtual do Azure em uma região diferente | Integração VNet necessária do gateway </br> Emparelhamento de ASE e VNet |
| Acessar recursos protegidos com pontos de extremidade de serviço | Integração VNet </br> ASE |
| Acessar recursos em uma rede privada não conectada ao Azure | Conexões Híbridas |
| Acessar recursos nos circuitos do ExpressRoute | Integração VNet </br> ASE | 
| Proteger o tráfego de saída de seu aplicativo Web | Integração VNet e grupos de segurança de rede </br> ASE | 
| Rotear o tráfego de saída do seu aplicativo Web | Integração VNet e tabelas de rotas </br> ASE | 


### <a name="default-networking-behavior"></a>Comportamento de rede padrão

As unidades de escala de serviço Azure App dão suporte a muitos clientes em cada implantação. Os planos de SKU gratuito e compartilhado hospedam cargas de trabalho do cliente em trabalhos com vários locatários. Os planos básico e acima hospedam cargas de trabalho do cliente que são dedicadas a apenas um único plano do serviço de aplicativo (ASP). Se você tivesse um plano do serviço de aplicativo padrão, todos os aplicativos nesse plano serão executados no mesmo trabalho. Se você escalar horizontalmente o trabalho, todos os aplicativos nesse ASP serão replicados em um novo trabalhador para cada instância em seu ASP. 

#### <a name="outbound-addresses"></a>Endereços de saída

As VMs de trabalho são divididas em grande parte pelos planos de preços do serviço de aplicativo. Todos os tipos gratuito, compartilhado, básico, Standard e Premium usam o mesmo tipo de VM de trabalho. Premiumv2 está em outro tipo de VM. Premiumv3 ainda é outro tipo de VM. Com cada alteração na família de VMs, há um conjunto diferente de endereços de saída. Se você dimensionar de Standard para Premiumv2, seus endereços de saída serão alterados. Se você dimensionar de Premiumv2 para Premiumv3, seus endereços de saída serão alterados. Há algumas unidades de escala mais antigas que mudarão os endereços de entrada e de saída quando você dimensionar de padrão para Premiumv2. Há vários endereços usados para fazer chamadas de saída. Os endereços de saída usados pelo seu aplicativo para fazer chamadas de saída são listados nas propriedades do seu aplicativo. Esses endereços são compartilhados por todos os aplicativos em execução na mesma família de VMs de trabalho na implantação do serviço de aplicativo. Se você quiser ver todos os endereços possíveis que seu aplicativo pode usar nessa unidade de escala, há outra propriedade chamada possibleOutboundAddresses que os listará. 

![Propriedades do aplicativo](media/networking-features/app-properties.png)

O serviço de aplicativo tem vários pontos de extremidade que são usados para gerenciar o serviço.  Esses endereços são publicados em um documento separado e também estão na marca de serviço do AppServiceManagement IP. A marca AppServiceManagement é usada apenas com um Ambiente do Serviço de Aplicativo onde você precisa permitir tal tráfego. Os endereços de entrada do serviço de aplicativo são acompanhados na marca de serviço do AppService IP. Não há nenhuma marca de serviço IP que contenha os endereços de saída usados pelo serviço de aplicativo. 

![Diagrama de entrada e saída do serviço de aplicativo](media/networking-features/default-behavior.png)

### <a name="app-assigned-address"></a>Endereço atribuído ao aplicativo 

O recurso de endereço atribuído ao aplicativo é um offshoot do recurso SSL baseado em IP e é acessado Configurando o SSL com seu aplicativo. Esse recurso pode ser usado para chamadas SSL baseadas em IP, mas também pode ser usado para dar a seu aplicativo um endereço que só tenha. 

![Diagrama de endereço atribuído ao aplicativo](media/networking-features/app-assigned-address.png)

Quando você usa um endereço atribuído a um aplicativo, seu tráfego ainda passa pelas mesmas funções de front-end que manipulam todo o tráfego de entrada na unidade de escala do serviço de aplicativo. No entanto, o endereço atribuído ao seu aplicativo é usado apenas pelo seu aplicativo. Os casos de uso para esse recurso são:

* Dar suporte às necessidades de SSL baseado em IP para seu aplicativo
* Definir um endereço dedicado para seu aplicativo que não é compartilhado com nada mais

Você pode aprender a definir um endereço em seu aplicativo com o tutorial sobre como [Adicionar um certificado TLS/SSL no serviço de Azure app][appassignedaddress]. 

### <a name="access-restrictions"></a>Restrições de acesso 

O recurso de restrições de acesso permite filtrar solicitações de **entrada** com base no endereço IP de origem. A ação de filtragem ocorre nas funções de front-end que são upstream das funções de trabalho em que seus aplicativos estão em execução. Como as funções de front-end são upstream dos trabalhadores, a capacidade de restrições de acesso pode ser considerada como proteção de nível de rede para seus aplicativos. O recurso permite que você crie uma lista de blocos de endereços allow e Deny que são avaliados em ordem de prioridade. É semelhante ao recurso NSG (grupo de segurança de rede) que existe na rede do Azure.  Você pode usar esse recurso em um ASE ou no serviço multilocatário. Quando usado com um ASE ILB, você pode restringir o acesso de blocos de endereços privados.

![Restrições de acesso](media/networking-features/access-restrictions.png)

O recurso de restrições de acesso ajuda em cenários em que você deseja restringir os endereços IP que podem ser usados para acessar seu aplicativo. Entre os casos de uso para esse recurso estão:

* Restringir o acesso ao seu aplicativo de um conjunto de endereços bem definidos 
* Restringir o acesso à entrada de um serviço de balanceamento de carga, como o Azure front door. Se você quisesse bloquear seu tráfego de entrada para a porta frontal do Azure, crie regras para permitir o tráfego de 147.243.0.0/16 e 2a01:111:2050::/44. 

![Restrições de acesso com a porta frontal](media/networking-features/access-restrictions-afd.png)

Se você quiser bloquear o acesso ao seu aplicativo para que ele só possa ser acessado de recursos em sua VNet (rede virtual) do Azure, você precisará de um endereço público estático em qualquer sua fonte em sua VNet. Se os recursos não tiverem um endereço público, você deverá usar o recurso de pontos de extremidade de serviço em vez disso. Saiba como habilitar esse recurso com o tutorial sobre como [Configurar restrições de acesso][iprestrictions].

### <a name="service-endpoints"></a>Pontos de extremidade de serviço

Os pontos de extremidade de serviço permitem bloquear o acesso de **entrada** ao seu aplicativo, de modo que o endereço de origem deve vir de um conjunto de sub-redes que você selecionar. Esse recurso funciona em conjunto com as restrições de acesso de IP. Os pontos de extremidade de serviço não são compatíveis com a depuração remota. Para usar a depuração remota com seu aplicativo, o cliente não pode estar em uma sub-rede com pontos de extremidade de serviço habilitados. Os pontos de extremidade de serviço são definidos na mesma experiência de usuário que as restrições de acesso de IP. Você pode criar uma lista de permissão/negação de regras de acesso que inclui endereços públicos, bem como sub-redes em seu VNets. Esse recurso dá suporte a cenários como:

![pontos de extremidade de serviço](media/networking-features/service-endpoints.png)

* Configurando um gateway de aplicativo com seu aplicativo para bloquear o tráfego de entrada para seu aplicativo
* Restrição de acesso ao seu aplicativo aos recursos em sua VNet. Isso pode incluir VMs, ASEs ou até mesmo outros aplicativos que usam a integração de VNet 

![pontos de extremidade de serviço com o gateway de aplicativo](media/networking-features/service-endpoints-appgw.png)

Você pode aprender mais sobre a configuração de pontos de extremidade de serviço com seu aplicativo no tutorial sobre como [Configurar restrições de acesso de ponto final de serviço][serviceendpoints]

### <a name="private-endpoints"></a>Pontos de extremidade privados

O ponto de extremidade privado é uma interface de rede que conecta você de forma privada e segura ao seu aplicativo Web pelo link privado do Azure. O ponto de extremidade privado usa um endereço IP privado de sua VNet, colocando efetivamente o aplicativo Web em sua VNet. Esse recurso é apenas para fluxos de **entrada** para seu aplicativo Web.
[Usando pontos de extremidade privados para o aplicativo Web do Azure][privateendpoints]

Pontos de extremidade privados habilitam cenários como:

* Restringir o acesso ao meu aplicativo de recursos em uma VNet 
* Expor meu aplicativo em um IP privado em minha VNet 
* Proteger meu aplicativo com um WAF 

Os pontos de extremidade privados impedem a vazamento de dados como a única coisa que você pode acessar por meio do ponto de extremidade privado é o aplicativo com o qual ele está configurado. 
 
### <a name="hybrid-connections"></a>Conexões Híbridas

O serviço de aplicativo Conexões Híbridas permite que seus aplicativos façam chamadas de **saída** para pontos de extremidade TCP especificados. O ponto de extremidade pode ser local, em uma VNet ou em qualquer lugar que permita o tráfego de saída para o Azure na porta 443. O recurso requer a instalação de um agente de retransmissão chamado HCM (Gerenciador de Conexões Híbridas) em um host Windows Server 2012 ou mais recente. O HCM precisa ser capaz de alcançar a retransmissão do Azure na porta 443. O HCM pode ser baixado da interface do usuário Conexões Híbridas do serviço de aplicativo no Portal. 

![Conexões Híbridas o fluxo de rede](media/networking-features/hybrid-connections.png)

O recurso de Conexões Híbridas do serviço de aplicativo é criado no recurso de Conexões Híbridas de retransmissão do Azure. O serviço de aplicativo usa uma forma especializada do recurso que dá suporte apenas para fazer chamadas de saída de seu aplicativo para um host e uma porta TCP. Esse host e a porta precisam apenas ser resolvidos no host em que o HCM está instalado. Quando o aplicativo, no serviço de aplicativo, faz uma pesquisa de DNS no host e na porta definida em sua conexão híbrida, o tráfego é automaticamente redirecionado para passar pela conexão híbrida e para a Gerenciador de Conexões Híbridas. Para saber mais sobre Conexões Híbridas, leia a documentação sobre o [serviço de aplicativo conexões híbridas][hybridconn]

Esse recurso é comumente usado para:

* Acessar recursos em redes privadas que não estão conectadas ao Azure com uma VPN ou ExpressRoute
* Suporte ao aumento e deslocamento de aplicativos locais para o serviço de aplicativo sem a necessidade de mover bancos de dados de suporte  
* Forneça acesso com segurança a um único host e porta por conexão híbrida. A maioria dos recursos de rede abre o acesso a uma rede e com Conexões Híbridas você tem apenas o host único e a porta que pode alcançar.
* Cenários de cobertura não cobertos por outros métodos de conectividade de saída
* Execute o desenvolvimento no serviço de aplicativo em que os aplicativos podem aproveitar facilmente os recursos locais 

Como o recurso habilita o acesso a recursos locais sem um buraco de firewall de entrada, ele é popular com os desenvolvedores. Os outros recursos de rede do serviço de aplicativo de saída são relacionados à rede virtual do Azure. A Conexões Híbridas não tem uma dependência de passar por uma VNet e pode ser usada para uma variedade maior de necessidades de rede. É importante observar que o recurso de Conexões Híbridas do serviço de aplicativo não se preocupa nem sabe o que você está fazendo sobre ele. Isso significa que você pode usá-lo para acessar um banco de dados, um serviço Web ou um soquete TCP arbitrário em um mainframe. Essencialmente, o recurso encapsula pacotes TCP. 

Embora Conexões Híbridas seja popular para o desenvolvimento, ela também é usada em inúmeros aplicativos de produção. É ótimo para acessar um serviço Web ou banco de dados, mas não é apropriado para situações envolvendo a criação de várias conexões. 

### <a name="gateway-required-vnet-integration"></a>Integração VNet necessária do gateway 

O recurso de integração de VNet do serviço de aplicativo necessário ao Gateway permite que seu aplicativo faça solicitações de **saída** em uma rede virtual do Azure. O recurso funciona conectando o host em que seu aplicativo está sendo executado em um gateway de rede virtual em sua VNet com uma VPN ponto a site. Quando você configura o recurso, seu aplicativo obtém um dos endereços ponto a site atribuídos a cada instância. Esse recurso permite que você acesse recursos tanto no VNets clássico quanto no Resource Manager em qualquer região. 

![Integração VNet necessária do gateway](media/networking-features/gw-vnet-integration.png)

Esse recurso resolve o problema de acessar recursos em outros VNets e pode até mesmo ser usado para se conectar por meio de uma VNet a outro VNets ou até mesmo local. Ele não funciona com o VNets conectado do ExpressRoute, mas faz com redes VPN site a site conectadas. Normalmente, é inapropriado usar esse recurso de um aplicativo em um Ambiente do Serviço de Aplicativo (ASE), pois o ASE já está em sua VNet. Os casos de uso que esse recurso resolve são:

* Acessando recursos em IPs privados em suas redes virtuais do Azure 
* Acessando recursos no local se houver uma VPN site a site 
* Acessando recursos no VNets emparelhado 

Quando esse recurso estiver habilitado, seu aplicativo usará o servidor DNS com o qual a VNet de destino está configurada. Você pode ler mais sobre esse recurso na documentação sobre [integração de VNet do serviço de aplicativo][vnetintegrationp2s]. 

### <a name="vnet-integration"></a>Integração VNet

O recurso de integração VNet do gateway necessário é útil, mas ainda não resolve o acesso a recursos no ExpressRoute. Além de precisar alcançar entre conexões de ExpressRoute, há a necessidade de os aplicativos serem capazes de fazer chamadas para serviços protegidos de ponto de extremidade de serviço. Para resolver essas duas necessidades adicionais, outro recurso de integração VNet foi adicionado. O novo recurso de integração VNet permite que você coloque o back-end de seu aplicativo em uma sub-rede em uma VNet do Resource Manager na mesma região. Esse recurso não está disponível em um Ambiente do Serviço de Aplicativo, que já está em uma VNet. Esse recurso permite:

* Acessando recursos no VNets do Resource Manager na mesma região
* Acessando recursos que são protegidos com pontos de extremidade de serviço 
* Acessando recursos que são acessíveis em conexões de ExpressRoute ou VPN
* Protegendo todo o tráfego de saída 
* Forçar túnel de todo o tráfego de saída. 

![Integração VNet](media/networking-features/vnet-integration.png)

Para saber mais sobre esse recurso, leia os documentos na [integração VNet do serviço de aplicativo][vnetintegration].

## <a name="app-service-environment"></a>Ambiente do Serviço de Aplicativo 

Um Ambiente do Serviço de Aplicativo (ASE) é uma implantação de locatário único do serviço de Azure App que é executado em sua VNet. O ASE habilita casos de uso como:

* Acessar recursos em sua VNet
* Acessar recursos no ExpressRoute
* Expor seus aplicativos com um endereço privado em sua VNet 
* Acessar recursos entre pontos de extremidade de serviço 

Com um ASE, você não precisa usar recursos como a integração VNet ou pontos de extremidade de serviço porque o ASE já está em sua VNet. Se você quiser acessar recursos como SQL ou armazenamento em pontos de extremidade de serviço, habilite os pontos de extremidade de serviço na sub-rede do ASE. Se você quiser acessar recursos na VNet, não será necessária nenhuma configuração adicional.  Se você quiser acessar recursos no ExpressRoute, você já está na VNet e não precisa configurar nada no ASE ou nos aplicativos dentro dele. 

Como os aplicativos em um ASE ILB podem ser expostos em um endereço IP privado, você pode facilmente adicionar dispositivos WAF para expor apenas os aplicativos que você deseja para a Internet e manter o resto seguro. Ele se presta para facilitar o desenvolvimento de aplicativos de várias camadas. 

Há algumas coisas que ainda não são possíveis do serviço multilocatário que são de um ASE. Eles incluem itens como:

* Expor seus aplicativos em um endereço IP privado
* Proteger todo o tráfego de saída com controles de rede que não fazem parte de seu aplicativo 
* Hospede seus aplicativos em um único serviço de locatário 
* Escalar verticalmente para muitas outras instâncias do que é possível no serviço multilocatário 
* Carregar certificados de cliente de autoridade de certificação privada para uso por seus aplicativos com pontos de extremidade protegidos da AC privada 
* Forçar o TLS 1,1 em todos os aplicativos hospedados no sistema sem qualquer capacidade de desabilitar no nível do aplicativo 
* Forneça um endereço de saída dedicado para todos os aplicativos em seu ASE que não são compartilhados com nenhum cliente 

![ASE em uma VNet](media/networking-features/app-service-environment.png)

O ASE fornece a melhor história sobre hospedagem de aplicativo isolada e dedicada, mas vem com alguns desafios de gerenciamento. Algumas coisas a serem consideradas antes de usar um ASE operacional são:
 
 * Um ASE é executado dentro de sua VNet, mas tem dependências fora da VNet. Essas dependências devem ser permitidas. Leia mais em [considerações de rede para um ambiente do serviço de aplicativo][networkinfo]
 * Um ASE não é dimensionado imediatamente como o serviço multilocatário. Você precisa prever as necessidades de dimensionamento em vez de dimensionar reativamente. 
 * Um ASE tem um custo antecipado mais alto associado a ele. Para aproveitar ao máximo seu ASE, você deve planejar a colocação de várias cargas de trabalho em um ASE em vez de usá-lo para pequenos esforços
 * Os aplicativos em um ASE não podem restringir o acesso a alguns aplicativos em um ASE e não a outros.
 * O ASE está em uma sub-rede e todas as regras de rede se aplicam a todo o tráfego de e para esse ASE. Se você quiser atribuir regras de tráfego de entrada para apenas um aplicativo, use as restrições de acesso. 

## <a name="combining-features"></a>Combinando recursos 

Os recursos indicados para o serviço multilocatário podem ser usados juntos para resolver casos de uso mais elaborados. Dois dos casos de uso mais comuns são descritos aqui, mas são apenas exemplos. Ao entender o que os diversos recursos fazem, você pode resolver quase todas as suas necessidades de arquitetura do sistema.

### <a name="inject-app-into-a-vnet"></a>Injetar aplicativo em uma VNet

Uma solicitação comum é sobre como colocar seu aplicativo em uma VNet. Colocar seu aplicativo em uma VNet significa que os pontos de extremidade de entrada e saída para um aplicativo estão dentro de uma VNet. O ASE fornece a melhor solução para resolver esse problema, mas você pode obter a maior parte do que é necessário com o serviço de vários locatários combinando recursos. Por exemplo, você pode hospedar aplicativos somente de intranet com endereços de entrada e saída privados:

* Criando um gateway de aplicativo com endereço de entrada e saída privado
* Protegendo o tráfego de entrada para seu aplicativo com pontos de extremidade de serviço 
* Use a nova integração de VNet para que o back-end de seu aplicativo esteja em sua VNet 

Esse estilo de implantação não forneceria um endereço dedicado para o tráfego de saída para a Internet ou permite que você bloqueie todo o tráfego de saída de seu aplicativo.  Esse estilo de implantação daria a você uma grande parte do que você poderia obter com um ASE. 

### <a name="create-multi-tier-applications"></a>Criar aplicativos de várias camadas

Um aplicativo de várias camadas é um aplicativo em que os aplicativos de back-end de API só podem ser acessados da camada de front-end. Há duas maneiras de criar um aplicativo de várias camadas. Comece usando a integração VNet para conectar seu aplicativo Web de front-end com uma sub-rede em uma VNet. Isso permitirá que seu aplicativo Web faça chamadas em sua VNet. Depois que o aplicativo de front-end estiver conectado à VNet, você precisará escolher como bloquear o acesso ao aplicativo de API.  Você pode:

* Hospede o front-end e o aplicativo de API no mesmo ILB ASE e expor o aplicativo de front-end para a Internet com um gateway de aplicativo
* Hospede o front-end no serviço multilocatário e o back-end em um ASE ILB
* hospedar o front-end e o aplicativo de API no serviço multilocatário

Se você estiver hospedando o front-end e o aplicativo de API para um aplicativo de várias camadas, você poderá:

Expor seu aplicativo de API com pontos de extremidade privados em sua VNet

![aplicativo de duas camadas de pontos de extremidade privado](media/networking-features/multi-tier-app-private-endpoint.png)

Use pontos de extremidade de serviço para proteger o tráfego de entrada para seu aplicativo de API apenas proveniente da sub-rede usada pelo seu aplicativo Web de front-end

![aplicativo de pontos de extremidade de serviço protegido](media/networking-features/multi-tier-app.png)

As compensações entre as duas técnicas são:

* com os pontos de extremidade de serviço, você só precisa proteger o tráfego para o aplicativo de API para a sub-rede de integração. Isso protege o aplicativo de API, mas você ainda pode ter um vazamento de dados, a possibilidade de seu aplicativo front-end a outros aplicativos no serviço de aplicativo.
* com pontos de extremidade privados, você tem duas sub-redes em jogo. Isso aumenta a complexidade. Além disso, o ponto de extremidade privado é um recurso de nível superior e adiciona mais para gerenciar. O benefício de usar pontos de extremidade privados é que você não tem uma possibilidade de vazamento de dados. 

Qualquer uma das técnicas funcionará com vários front-ends. Em pequena escala, os pontos de extremidade de serviço são muito mais fáceis de usar porque você simplesmente habilita pontos de extremidade de serviço para o aplicativo de API na sub-rede de integração de front-end. Ao adicionar mais aplicativos de front-end, você precisa ajustar cada aplicativo de API para ter pontos de extremidade de serviço com a sub-rede de integração. Com pontos de extremidade privados, você tem mais complexidade, mas não precisa alterar nada em seus aplicativos de API depois de definir um ponto de extremidade privado. 

### <a name="line-of-business-applications"></a>Aplicativos de linha de negócios

Aplicativos de linha de negócios (LOB) são aplicativos internos que normalmente não são expostos para acesso da Internet. Esses aplicativos são chamados de dentro de redes corporativas onde o acesso pode ser estritamente controlado. Se você usar um ASE ILB, será fácil hospedar seus aplicativos de linha de negócios. Se você usar o serviço multilocatário, poderá usar pontos de extremidade privados ou pontos de extremidade de serviço combinados com um gateway de aplicativo. Há dois motivos para usar um gateway de aplicativo com pontos de extremidade de serviço em vez de pontos de extremidade privados:

* Você precisa de proteção WAF em seus aplicativos LOB
* você deseja balancear a carga para várias instâncias de seus aplicativos LOB

Se nenhum for o caso, você será melhor usar pontos de extremidade privados. Com pontos de extremidade privados disponíveis no serviço de aplicativo, você pode expor seus aplicativos em endereços privados em sua VNet. O ponto de extremidade privado que você coloca em sua VNet pode ser acessado em conexões de ExpressRoute e VPN. Configurar pontos de extremidade privados vai expor seus aplicativos em um endereço privado, mas você precisará configurar o DNS para alcançar esse endereço local. Para fazer isso funcionar, você precisará encaminhar a zona privada do DNS do Azure contendo seus pontos de extremidade privados para seus servidores DNS locais. As zonas privadas do DNS do Azure não dão suporte ao encaminhamento de zona, mas você pode dar suporte a isso usando um servidor DNS para essa finalidade. Esse modelo, [encaminhador DNS](https://azure.microsoft.com/resources/templates/301-dns-forwarder/), facilita o encaminhamento da zona privada do DNS do Azure para seus servidores DNS locais.

## <a name="app-service-ports"></a>Portas do serviço de aplicativo

Se você examinar o serviço de aplicativo, encontrará várias portas que são expostas para conexões de entrada. Não é possível bloquear ou controlar o acesso a essas portas no serviço multilocatário. As portas que são expostas são as seguintes:

| Use | Portas |
|----------|-------------|
|  HTTP/HTTPS  | 80, 443 |
|  Gerenciamento | 454, 455 |
|  FTP/FTPS    | 21, 990, 10001-10020 |
|  Depuração remota no Visual Studio  |  4020, 4022, 4024 |
|  Serviço de Implantação da Web | 8172 |
|  Uso da infraestrutura | 7654, 1221 |

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
