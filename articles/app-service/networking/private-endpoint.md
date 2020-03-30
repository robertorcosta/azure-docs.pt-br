---
title: Conecte-se privadamente a um aplicativo web usando o Azure Private Endpoint
description: Conecte-se privadamente a um aplicativo web usando o Azure Private Endpoint
author: ericgre
ms.assetid: 2dceac28-1ba6-4904-a15d-9e91d5ee162c
ms.topic: article
ms.date: 03/18/2020
ms.author: ericg
ms.service: app-service
ms.workload: web
ms.custom: fasttrack-edit
ms.openlocfilehash: c2717b1f29af39c6fdc4602b11acba131d959f03
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79534381"
---
# <a name="using-private-endpoints-for-azure-web-app-preview"></a>Usando pontos finais privados para o aplicativo Web Do Azure (Preview)

> [!Note]
> A pré-visualização está disponível nas regiões leste dos EUA e Oeste dos EUA 2 para todos os Aplicativos PremiumV2 Windows e Linux Web e Funções Premium Elásticas. 

Você pode usar o Private Endpoint para o seu Azure Web App para permitir que os clientes localizados em sua rede privada acessem o aplicativo com segurança via Private Link. O Private Endpoint usa um endereço IP do espaço de endereço Do Azure VNet. O tráfego de rede entre um cliente em sua rede privada e o Aplicativo Web atravessa o VNet e um Link Privado na rede backbone da Microsoft, eliminando a exposição da Internet pública.

Usar o Private Endpoint para o seu Aplicativo web permite:

- Proteja seu Aplicativo web configurando o Ponto Final de Serviço, eliminando a exposição pública.
- Conecte-se com segurança ao Web App a partir de redes locais que se conectam à VNet usando uma VPN ou peering privado ExpressRoute.

Se você só precisa de uma conexão segura entre seu VNet e seu Aplicativo Web, um Ponto Final de Serviço é a solução mais simples. Se você também precisa acessar o aplicativo web a partir de locais através de um gateway Azure, um VNet regionalmente peered ou um VNet com peerly globalmente, private endpoint é a solução.  

Para obter mais informações, consulte [Pontos finais de serviço][serviceendpoint].

## <a name="conceptual-overview"></a>Visão geral conceitual

Um Private Endpoint é uma interface de rede especial (NIC) para o seu Aplicativo Web Azure em uma sub-rede em sua Rede Virtual (VNet).
Quando você cria um Ponto Final Privado para o seu Aplicativo Web, ele fornece conectividade segura entre os clientes em sua rede privada e seu Aplicativo Web. O Ponto Final Privado é atribuído a um endereço IP da faixa de endereço IP do seu VNet.
A conexão entre o Private Endpoint e o Web App usa um [link privado][privatelink]seguro . Private Endpoint é usado apenas para fluxos de entrada para o seu Aplicativo Web. Os fluxos de saída não usarão este ponto final privado, mas você pode injetar fluxos de saída para sua rede em uma sub-rede diferente através do [recurso de integração VNet][vnetintegrationfeature].

A sub-rede onde você conecta o Ponto Final Privado pode ter outros recursos nele, você não precisa de uma sub-rede vazia dedicada.
Você também pode implantar o Private Endpoint em uma região diferente do Web App. 

> [!Note]
>O recurso de integração VNet não pode usar a mesma sub-rede que o Private Endpoint, esta é uma limitação do recurso de integração VNet.

De uma perspectiva de segurança:

- Quando você habilita pontos finais privados ao seu Aplicativo web, você desativa todo o acesso público.
- Você pode habilitar vários pontos finais privados em outras VNets e Subnets, incluindo VNets em outras regiões.
- O endereço IP da NIC private endpoint deve ser dinâmico, mas permanecerá o mesmo até que você exclua o Ponto Final Privado.
- A NIC do Private Endpoint não pode ter um NSG associado.
- A Subnet que hospeda o Private Endpoint pode ter um NSG associado, mas você deve desativar a aplicação das políticas de rede para o Ponto Final Privado: consulte [Desativar políticas de rede para pontos finais privados][disablesecuritype]. Como resultado, você não pode filtrar por qualquer NSG o acesso ao seu Ponto Final Privado.
- Quando você habilita o Private Endpoint para o seu Aplicativo Web, a configuração de [restrições][accessrestrictions] de acesso do Aplicativo Web não é avaliada.
- Você pode reduzir o risco de exfiltração de dados do VNet removendo todas as regras do NSG onde o destino é a tag Internet ou serviços Azure. Mas adicionar um Web App Private Endpoint em sua sub-rede permitirá que você alcance qualquer Web App hospedado no mesmo selo de implantação e exposto à Internet.

Nos logs Web HTTP do seu Web App, você encontrará o IP de origem do cliente. Isso é implementado usando o protocolo TCP Proxy, encaminhando a propriedade IP do cliente até o Web App. Para obter mais informações, consulte [Obter informações de conexão usando o Proxy TCP v2][tcpproxy].


  > [!div class="mx-imgBorder"]
  > ![Visão geral global do Web App Private Endpoint](media/private-endpoint/global-schema-web-app.png)

## <a name="dns"></a>DNS

Como este recurso está em pré-visualização, não alteramos a entrada do DNS durante a pré-visualização. Você precisa gerenciar a entrada dNS em seu servidor DNS privado ou zona privada Do Zure DNS você mesmo.
Se você precisar usar um nome DNS personalizado, você deve adicionar o nome personalizado em seu Aplicativo web. Durante a pré-visualização, o nome personalizado deve ser validado como qualquer nome personalizado, usando resolução DNS pública. Consulte [a validação personalizada do DNS][dnsvalidation] para obter mais informações.

## <a name="pricing"></a>Preços

Para obter detalhes de preço, confira [Preço do Link Privado do Azure][pricing].

## <a name="limitations"></a>Limitações

Estamos melhorando o recurso Private Link e o Private Endpoint regularmente, verifique [este artigo][pllimitations] para obter informações atualizadas sobre limitações.

## <a name="next-steps"></a>Próximas etapas

Para implantar o ponto final privado para o seu Aplicativo web através do portal, veja [como se conectar privadamente a um aplicativo da Web][howtoguide]




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
