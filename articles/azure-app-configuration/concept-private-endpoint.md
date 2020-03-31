---
title: Usando pontos finais privados para configuração do aplicativo Azure
description: Proteja sua loja de configuração de aplicativos usando pontos finais privados
services: azure-app-configuration
author: lisaguthrie
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 3/12/2020
ms.author: lcozzens
ms.openlocfilehash: f18672b9e3a368a833fc8cba279d748dfe3c2a9e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79366761"
---
# <a name="using-private-endpoints-for-azure-app-configuration"></a>Usando pontos finais privados para configuração do aplicativo Azure

Você pode usar [pontos finais privados](../private-link/private-endpoint-overview.md) para a configuração do aplicativo Azure para permitir que os clientes em uma rede virtual (VNet) acessem com segurança os dados por meio de um [link privado](../private-link/private-link-overview.md). O ponto final privado usa um endereço IP do espaço de endereço VNet para sua loja de configuração de aplicativos. O tráfego de rede entre os clientes no VNet e na loja de configuração de aplicativos atravessa o VNet usando um link privado na rede backbone da Microsoft, eliminando a exposição à internet pública.

O uso de pontos finais privados para sua loja de configuração de aplicativos permite:
- Proteja os detalhes de configuração do aplicativo configurando o firewall para bloquear todas as conexões à Configuração do aplicativo no ponto final público.
- Aumente a segurança da rede virtual (VNet) garantindo que os dados não escapem do VNet.
- Conecte-se com segurança à loja de configuração de aplicativos a partir de redes locais que se conectam ao VNet usando [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) ou [ExpressRoutes](../expressroute/expressroute-locations.md) com peering privado.

> [!NOTE]
> A configuração do aplicativo Azure oferece o uso de pontos finais privados como uma visualização pública. As ofertas de visualização pública permitem que os clientes experimentem os novos recursos antes do lançamento oficial.  Os serviços e recursos de visualização pública não são destinados ao uso em produção.

## <a name="conceptual-overview"></a>Visão geral conceitual

Um ponto final privado é uma interface de rede especial para um serviço Azure em sua [Rede Virtual](../virtual-network/virtual-networks-overview.md) (VNet). Quando você cria um ponto final privado para sua loja app Config, ele fornece conectividade segura entre os clientes em seu VNet e sua loja de configuração. O ponto final privado é atribuído a um endereço IP da faixa de endereço IP do seu VNet. A conexão entre o ponto final privado e o armazenamento de configuração usa um link privado seguro.

Os aplicativos no VNet podem se conectar ao armazenamento de configuração através do ponto final privado **usando as mesmas strings de conexão e mecanismos de autorização que eles usariam de outra forma**. Os pontos finais privados podem ser usados com todos os protocolos suportados pela loja de configuração de aplicativos.

Embora a configuração do aplicativo não suporte pontos finais de serviço, os pontos finais privados podem ser criados em sub-redes que usam [pontos finais de serviço](../virtual-network/virtual-network-service-endpoints-overview.md). Os clientes em uma sub-rede podem se conectar com segurança a uma loja de configuração de aplicativos usando o ponto final privado enquanto usam pontos finais de serviço para acessar outros.  

Quando você cria um ponto final privado para um serviço em seu VNet, uma solicitação de consentimento é enviada para aprovação ao proprietário da conta de serviço. Se o usuário que solicitar a criação do ponto final privado também for proprietário da conta, essa solicitação de consentimento será automaticamente aprovada.

Os proprietários de contas de serviço `Private Endpoints` podem gerenciar solicitações de consentimento e pontos finais privados através da guia da loja de configuração no [portal Azure](https://portal.azure.com).

### <a name="private-endpoints-for-app-configuration"></a>Pontos finais privados para configuração do aplicativo 

Ao criar um ponto final privado, você deve especificar a loja de configuração do aplicativo à qual ele se conecta. Se você tiver várias instâncias de configuração de aplicativo dentro de uma conta, você precisará de um ponto final privado separado para cada loja.

### <a name="connecting-to-private-endpoints"></a>Conectando-se a pontos finais privados

O Azure conta com a resolução DNS para encaminhar conexões do VNet para o armazenamento de configuração por um link privado. Você pode encontrar rapidamente strings de conexões no portal Azure selecionando sua loja de configuração de aplicativos e, em seguida, selecionando > **Teclas de acesso de** **configurações**.  

> [!IMPORTANT]
> Use a mesma seqüência de conexões para se conectar à sua loja de configuração de aplicativos usando pontos finais privados como você usaria para um ponto final público. Não se conecte à conta `privatelink` de armazenamento usando sua URL de subdomínio.

## <a name="dns-changes-for-private-endpoints"></a>Alterações de DNS para pontos finais privados

Quando você cria um ponto final privado, o registro de recursos DNS CNAME para o `privatelink`armazenamento de configuração é atualizado para um alias em um subdomínio com o prefixo . O Azure também cria uma zona `privatelink` [DNS privada](../dns/private-dns-overview.md) correspondente ao subdomínio, com os registros de recursos DNS A para os pontos finais privados.

Quando você resolve a URL de ponto final de fora do VNet, ela se resolve para o ponto final público da loja. Quando resolvido dentro do VNet hospedando o ponto final privado, a URL do ponto final se resolve para o ponto final privado.

Você pode controlar o acesso de clientes fora do VNet através do ponto final público usando o serviço Azure Firewall.

Essa abordagem permite o acesso à loja **usando a mesma cadeia de conexão** para clientes no VNet que hospeda os pontos finais privados, bem como clientes fora do VNet.

Se você estiver usando um servidor DNS personalizado em sua rede, os clientes devem ser capazes de resolver o nome de domínio totalmente qualificado (FQDN) para o ponto final do serviço para o endereço IP de ponto final privado. Configure seu servidor DNS para delegar seu subdomínio de link privado à zona DNS privada para o VNet ou configure os registros A para `AppConfigInstanceA.privatelink.azconfig.io` com o endereço IP de ponto final privado.

> [!TIP]
> Ao usar um servidor DNS personalizado ou no local, você deve configurar seu `privatelink` servidor DNS para resolver o nome da loja no subdomínio para o endereço IP de ponto final privado. Você pode fazer isso delegando o `privatelink` subdomínio para a zona DNS privada do VNet ou configurando a zona DNS em seu servidor DNS e adicionando os registros DNS A.

## <a name="pricing"></a>Preços

A ativação de pontos finais privados requer uma loja de configuração de aplicativos [de nível padrão.](https://azure.microsoft.com/pricing/details/app-configuration/)  Para saber mais sobre os detalhes dos preços dos links privados, consulte [os preços do Azure Private Link](https://azure.microsoft.com/pricing/details/private-link).

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre como criar um ponto final privado para sua loja de configuração de aplicativos, consulte os seguintes artigos:

- [Crie um ponto final privado usando o Private Link Center no portal Azure](../private-link/create-private-endpoint-portal.md)
- [Criar um ponto de extremidade privado usando a CLI do Azure](../private-link/create-private-endpoint-cli.md)
- [Crie um ponto final privado usando o Azure PowerShell](../private-link/create-private-endpoint-powershell.md)

Aprenda a configurar seu servidor DNS com pontos finais privados:

- [Resolução de nomes para recursos em redes virtuais do Azure](/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances#name-resolution-that-uses-your-own-dns-server)
- [Configuração de DNS para pontos finais privados](/azure/private-link/private-endpoint-overview#dns-configuration)
