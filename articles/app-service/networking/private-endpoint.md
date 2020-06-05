---
title: Conecte-se em particular a um aplicativo Web usando o Ponto de extremidade privado do Azure
description: Conecte-se em particular a um aplicativo Web usando o Ponto de extremidade privado do Azure
author: ericgre
ms.assetid: 2dceac28-1ba6-4904-a15d-9e91d5ee162c
ms.topic: article
ms.date: 05/25/2020
ms.author: ericg
ms.service: app-service
ms.workload: web
ms.custom: fasttrack-edit
ms.openlocfilehash: 4c48a2fad927812cc45543243b48a2df81acf73b
ms.sourcegitcommit: 1f25aa993c38b37472cf8a0359bc6f0bf97b6784
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/26/2020
ms.locfileid: "83846946"
---
# <a name="using-private-endpoints-for-azure-web-app-preview"></a>Usando pontos de extremidade privados para o aplicativo Web do Azure (versão prévia)

> [!Note]
> Com a atualização da visualização, lançamos o recurso de proteção contra exfiltração de dados.
> A visualização está disponível nas regiões Leste dos EUA e Oeste dos EUA 2 para todos os Aplicativos Web para Windows e Linux PremiumV2 e Funções Premium Elastic. 

Você pode usar o Ponto de extremidade privado para o Azure Web App para permitir que os clientes localizados na sua rede privada acessem o aplicativo com segurança pelo link privado. O ponto de extremidade privado usa um endereço IP do espaço de endereço da VNet do Azure. O tráfego de rede entre um cliente em sua rede privada e o Web App atravessa a VNet e um Link Privado na rede principal da Microsoft, eliminando a exposição da Internet pública.

O uso do Ponto de extremidade privado para o seu aplicativo da Web permite:

- Proteja seu Web App configurando o ponto de extremidade privado, eliminando a exposição pública.
- Conecte-se com segurança ao Web App a partir de redes locais que se conectam à VNet usando uma conexão privada VPN ou ExpressRoute.
- Evite qualquer exfiltração de dados da sua VNet. 

Se você precisar apenas de uma conexão segura entre sua VNet e seu aplicativo Web, um ponto de extremidade de serviço será a solução mais simples. Se você também precisar acessar o aplicativo Web do local por meio de um gateway do Azure, uma VNet emparelhada regionalmente ou uma VNet emparelhada globalmente, o ponto de extremidade privado será a solução.  

Para saber mais, confira [Ponto de extremidade de serviço][serviceendpoint].

## <a name="conceptual-overview"></a>Visão geral conceitual

Um ponto de extremidade privado é uma NIC (interface de rede especial) para seu aplicativo Web do Azure em uma sub-rede em sua rede virtual (VNet).
Quando você cria um ponto de extremidade privado para seu aplicativo Web, ele fornece conectividade segura entre clientes em sua rede privada e seu aplicativo Web. O ponto de extremidade privado recebe um endereço IP do intervalo de endereços IP de sua VNet.
A conexão entre o ponto de extremidade privado e o aplicativo Web usa um [link privado][privatelink] seguro. O ponto de extremidade privado é usado apenas para fluxos de entrada no seu aplicativo da Web. Os fluxos de saída não usarão esse ponto de extremidade privado, mas você pode injetar fluxos de saída na sua rede em uma sub-rede diferente por meio do [recurso de integração da VNet][vnetintegrationfeature].

A sub-rede na qual você conecta o ponto de extremidade privado pode ter outros recursos, não é necessária uma sub-rede vazia dedicada.
Você também pode implantar o ponto de extremidade privado em uma região diferente do aplicativo Web. 

> [!Note]
>O recurso de integração da VNet não pode usar a mesma sub-rede que o Private Endpoint; essa é uma limitação do recurso de integração da VNet.

Do ponto de vista da segurança:

- Ao habilitar pontos de extremidade privados para seu aplicativo Web, você desabilita todo o acesso público.
- Você pode habilitar vários pontos de extremidade privados em outros VNets e sub-redes, incluindo VNets em outras regiões.
- O endereço IP da NIC do ponto de extremidade privado deve ser dinâmico, mas permanecerá o mesmo até que você exclua o ponto de extremidade privado.
- A NIC do ponto de extremidade privado não pode ter um NSG associado.
- A sub-rede que hospeda o ponto final privado pode ter um NSG associado, mas você deve desativar a imposição de políticas de rede para o ponto final privado: consulte [Desativar diretivas de rede para pontos finais privados][disablesecuritype]. Como resultado, você não pode filtrar por nenhum NSG o acesso ao seu endpoint privado.
- Quando você ativa o Ponto de extremidade privado no seu Web App, a configuração de [restrições de acesso][accessrestrictions] do Web App não é avaliada.
- Você pode eliminar o risco de vazamento de dados da VNet removendo todas as regras de NSG em que o destino é marca Internet ou serviços do Azure. Ao implantar um ponto de extremidade privado para um aplicativo da Web, você só pode acessar esse aplicativo da Web específico por meio do ponto de extremidade privado. Se você tiver outro aplicativo da Web, implante outro ponto de extremidade privado dedicado para esse outro aplicativo da Web.

Nos logs HTTP da Web do seu Web App, você encontrará o IP de origem do cliente. Isso é implementado usando o protocolo TCP Proxy, encaminhando a propriedade IP do cliente até o Web App. Para obter mais informações, consulte [Obtendo informações de conexão usando o TCP Proxy v2][tcpproxy].


  > [!div class="mx-imgBorder"]
  > ![Visão geral global do endpoint privado do aplicativo Web](media/private-endpoint/global-schema-web-app.png)

## <a name="dns"></a>DNS

Como esse recurso está em versão prévia, não alteramos a entrada DNS durante a visualização. Você mesmo precisa gerenciar a entrada DNS no seu servidor DNS privado ou na zona privada do DNS do Azure.
Se você precisar usar um nome DNS personalizado, deverá adicionar o nome personalizado em seu aplicativo Web. Durante a visualização, o nome personalizado deve ser validado como qualquer nome personalizado, usando a resolução de DNS público. Para mais informações, consulte [validação DNS personalizada][dnsvalidation].

Se você precisar usar o console do kudu ou a API REST do kudu (implantação com agentes do Azure DevOps auto-hospedados, por exemplo), será necessário criar dois registros na zona privada do DNS do Azure ou no servidor DNS personalizado. 
- PrivateEndpointIP yourwebappname.azurewebsites.net 
- PrivateEndpointIP yourwebappname.scm.azurewebsites.net 

## <a name="pricing"></a>Preços

Para obter detalhes de preço, confira [Preço do Link Privado do Azure][pricing].

## <a name="limitations"></a>Limitações

Ao usar a Função do Azure no Elastic Premium Plan com ponto de extremidade privado, para executar ou executar a função no portal da Web do Azure, você deve ter acesso direto à rede ou receberá um erro HTTP 403. Em outras palavras, seu navegador deve ser capaz de acessar o ponto de extremidade privado para executar a função no portal da Web do Azure. 

Durante a visualização, somente o slot de produção é exposto por trás do ponto de extremidade privado, outros slots devem ser acessados pelo ponto de extremidade público.

Estamos melhorando o recurso de link privado e o ponto de extremidade privado regularmente, marque [este artigo][pllimitations] para obter informações atualizadas sobre as limitações.

## <a name="next-steps"></a>Próximas etapas

Para implantar o ponto de extremidade privado para seu aplicativo Web por meio do portal, consulte [como conectar-se em modo privado a um aplicativo Web][howtoguide]




<!--Links-->
[serviceendpoint]: https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview
[privatelink]: https://docs.microsoft.com/azure/private-link/private-link-overview
[vnetintegrationfeature]: https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet
[disablesecuritype]: https://docs.microsoft.com/azure/private-link/disable-private-endpoint-network-policy
[accessrestrictions]: https://docs.microsoft.com/azure/app-service/app-service-ip-restrictions
[tcpproxy]: ../../private-link/private-link-service-overview.md#getting-connection-information-using-tcp-proxy-v2
[dnsvalidation]: https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-domain
[pllimitations]: https://docs.microsoft.com/azure/private-link/private-endpoint-overview#limitations
[pricing]: https://azure.microsoft.com/pricing/details/private-link/
[howtoguide]: https://docs.microsoft.com/azure/private-link/create-private-endpoint-webapp-portal
