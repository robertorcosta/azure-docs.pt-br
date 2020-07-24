---
title: Conectar-se de forma privada a um aplicativo Web do Azure usando o ponto de extremidade privado
description: Conecte-se em particular a um aplicativo Web usando o Ponto de extremidade privado do Azure
author: ericgre
ms.assetid: 2dceac28-1ba6-4904-a15d-9e91d5ee162c
ms.topic: article
ms.date: 07/07/2020
ms.author: ericg
ms.service: app-service
ms.workload: web
ms.custom: fasttrack-edit, references_regions
ms.openlocfilehash: 4fab75aef2a94ba7108085e9d5b5dbbf190342f6
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87068296"
---
# <a name="using-private-endpoints-for-azure-web-app-preview"></a>Usando pontos de extremidade privados para o aplicativo Web do Azure (versão prévia)

> [!Note]
> Com a atualização da visualização, lançamos o recurso de proteção contra exfiltração de dados.
>
> A versão prévia está disponível em todas as regiões públicas para Aplicativos Web para Windows PremiumV2 e Linux e Funções Premium Elásticas. 

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
>O recurso de integração VNet não pode usar a mesma sub-rede que o ponto de extremidade privado, essa é uma limitação do recurso de integração VNet.

Do ponto de vista da segurança:

- Ao habilitar pontos de extremidade privados para seu aplicativo Web, você desabilita todo o acesso público.
- Você pode habilitar vários pontos de extremidade privados em outros VNets e sub-redes, incluindo VNets em outras regiões.
- O endereço IP da NIC do ponto de extremidade privado deve ser dinâmico, mas permanecerá o mesmo até que você exclua o ponto de extremidade privado.
- A NIC do ponto de extremidade privado não pode ter um NSG associado.
- A sub-rede que hospeda o ponto final privado pode ter um NSG associado, mas você deve desativar a imposição de políticas de rede para o ponto final privado: consulte [Desativar diretivas de rede para pontos finais privados][disablesecuritype]. Como resultado, você não pode filtrar por nenhum NSG o acesso ao seu endpoint privado.
- Quando você ativa o Ponto de extremidade privado no seu Web App, a configuração de [restrições de acesso][accessrestrictions] do Web App não é avaliada.
- Você pode eliminar o risco de vazamento de dados da VNet removendo todas as regras de NSG em que o destino é marca Internet ou serviços do Azure. Ao implantar um ponto de extremidade privado para um aplicativo da Web, você só pode acessar esse aplicativo da Web específico por meio do ponto de extremidade privado. Se você tiver outro aplicativo da Web, implante outro ponto de extremidade privado dedicado para esse outro aplicativo da Web.

Nos logs HTTP da Web do seu Web App, você encontrará o IP de origem do cliente. Esse recurso é implementado usando o protocolo proxy TCP, encaminhando a propriedade IP do cliente para o aplicativo Web. Para obter mais informações, consulte [Obtendo informações de conexão usando o TCP Proxy v2][tcpproxy].


  > [!div class="mx-imgBorder"]
  > ![Visão geral global do endpoint privado do aplicativo Web](media/private-endpoint/global-schema-web-app.png)

## <a name="dns"></a>DNS

Quando você usa o ponto de extremidade privado para o aplicativo Web, a URL solicitada deve corresponder ao nome do seu aplicativo Web. Por padrão, mywebappname.azurewebsites.net.

Por padrão, sem o ponto de extremidade privado, o nome público do seu aplicativo Web é um nome canônico para o cluster.
Por exemplo, a resolução de nome será:

|Nome |Tipo |Valor |
|-----|-----|------|
|mywebapp.azurewebsites.net|CNAME|clustername.azurewebsites.windows.net|
|clustername.azurewebsites.windows.net|CNAME|cloudservicename.cloudapp.net|
|cloudservicename.cloudapp.net|Um|40.122.110.154| 


Quando você implanta um ponto de extremidade privado, atualizamos a entrada DNS para apontar para o nome canônico mywebapp.privatelink.azurewebsites.net.
Por exemplo, a resolução de nome será:

|Nome |Tipo |Valor |Comentário |
|-----|-----|------|-------|
|mywebapp.azurewebsites.net|CNAME|mywebapp.privatelink.azurewebsites.net|
|mywebapp.privatelink.azurewebsites.net|CNAME|clustername.azurewebsites.windows.net|
|clustername.azurewebsites.windows.net|CNAME|cloudservicename.cloudapp.net|
|cloudservicename.cloudapp.net|Um|40.122.110.154|<--esse IP público não é seu ponto de extremidade privado, você receberá um erro 403|

Você deve configurar um servidor DNS privado ou uma zona privada de DNS do Azure, para testes, você pode modificar a entrada de host do seu computador de teste.
A zona DNS que você precisa criar é: **privatelink.azurewebsites.net**. Registre o registro para seu aplicativo Web com um registro a e o IP do ponto de extremidade privado.
Por exemplo, a resolução de nome será:

|Nome |Tipo |Valor |Comentário |
|-----|-----|------|-------|
|mywebapp.azurewebsites.net|CNAME|mywebapp.privatelink.azurewebsites.net|
|mywebapp.privatelink.azurewebsites.net|Um|10.10.10.8|<--você gerencia essa entrada em seu sistema DNS para apontar para seu endereço IP do ponto de extremidade privado|

Após essa configuração de DNS, você pode acessar seu aplicativo Web de forma privada com o nome padrão mywebappname.azurewebsites.net.


Se você precisar usar um nome DNS personalizado, deverá adicionar o nome personalizado em seu aplicativo Web. Durante a visualização, o nome personalizado deve ser validado como qualquer nome personalizado, usando a resolução de DNS público. Para mais informações, consulte [validação DNS personalizada][dnsvalidation].

Para o console do kudu ou a API REST do kudu (implantação com agentes do Azure DevOps auto-hospedados, por exemplo), você deve criar dois registros em sua zona privada do DNS do Azure ou seu servidor DNS personalizado. 

| Nome | Tipo | Valor |
|-----|-----|-----|
| mywebapp.privatelink.azurewebsites.net | Um | PrivateEndpointIP | 
| mywebapp.scm.privatelink.azurewebsites.net | Um | PrivateEndpointIP | 



## <a name="pricing"></a>Preços

Para obter detalhes de preço, confira [Preço do Link Privado do Azure][pricing].

## <a name="limitations"></a>Limitações

Ao usar a Função do Azure no Elastic Premium Plan com ponto de extremidade privado, para executar ou executar a função no portal da Web do Azure, você deve ter acesso direto à rede ou receberá um erro HTTP 403. Em outras palavras, seu navegador deve ser capaz de acessar o ponto de extremidade privado para executar a função no portal da Web do Azure. 

Durante a visualização, somente o slot de produção é exposto por trás do ponto de extremidade privado, outros slots devem ser acessados pelo ponto de extremidade público.

Estamos melhorando o recurso de link privado e o ponto de extremidade privado regularmente, marque [este artigo][pllimitations] para obter informações atualizadas sobre as limitações.

## <a name="next-steps"></a>Próximas etapas

- Para implantar o ponto de extremidade privado para seu aplicativo Web por meio do portal, consulte [como conectar-se de forma privada a um aplicativo Web com o portal][howtoguide1]
- Para implantar o ponto de extremidade privado para seu aplicativo Web usando CLI do Azure, consulte [como conectar-se de forma privada a um aplicativo Web com CLI do Azure][howtoguide2]
- Para implantar o ponto de extremidade privado para seu aplicativo Web usando o PowerShell, consulte [como conectar-se de forma privada a um aplicativo Web com o PowerShell][howtoguide3]
- Para implantar o ponto de extremidade privado para seu aplicativo Web usando o modelo do Azure, confira [como conectar-se de forma privada a um aplicativo Web com o modelo do Azure][howtoguide4]


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
[howtoguide1]: https://docs.microsoft.com/azure/private-link/create-private-endpoint-webapp-portal
[howtoguide2]: https://docs.microsoft.com/azure/app-service/scripts/cli-deploy-privateendpoint
[howtoguide3]: https://docs.microsoft.com/azure/app-service/scripts/powershell-deploy-private-endpoint
[howtoguide4]: https://docs.microsoft.com/azure/app-service/scripts/template-deploy-private-endpoint
