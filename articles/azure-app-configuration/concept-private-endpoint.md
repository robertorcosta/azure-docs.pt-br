---
title: Usando pontos de extremidade privados para configuração de Azure App
description: Proteger o repositório de configuração do aplicativo usando pontos de extremidade privados
services: azure-app-configuration
author: lisaguthrie
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 3/12/2020
ms.author: lcozzens
ms.openlocfilehash: f18672b9e3a368a833fc8cba279d748dfe3c2a9e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "79366761"
---
# <a name="using-private-endpoints-for-azure-app-configuration"></a>Usando pontos de extremidade privados para configuração de Azure App

Você pode usar [pontos de extremidade privados](../private-link/private-endpoint-overview.md) para Azure app configuração para permitir que clientes em uma rede virtual (VNet) acessem dados com segurança por meio de um [link privado](../private-link/private-link-overview.md). O ponto de extremidade privado usa um endereço IP do espaço de endereço de VNet para seu repositório de configuração de aplicativo. O tráfego de rede entre os clientes na VNet e o armazenamento de configuração de aplicativo atravessa a VNet usando um link privado na rede de backbone da Microsoft, eliminando a exposição à Internet pública.

O uso de pontos de extremidade privados para seu repositório de configuração de aplicativo permite que você:
- Proteja os detalhes de configuração do aplicativo Configurando o firewall para bloquear todas as conexões com a configuração do aplicativo no ponto de extremidade público.
- Aumente a segurança para a VNet (rede virtual) garantindo que os dados não escapem da VNet.
- Conecte-se com segurança ao repositório de configurações do aplicativo de redes locais que se conectam à VNet usando [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) ou [expressroute ao qual](../expressroute/expressroute-locations.md) com emparelhamento privado.

> [!NOTE]
> Azure App configuração oferece o uso de pontos de extremidade privados como uma visualização pública. As ofertas de visualização pública permitem que os clientes experimentem os novos recursos antes do lançamento oficial.  Os serviços e recursos de visualização pública não são destinados ao uso em produção.

## <a name="conceptual-overview"></a>Visão geral conceitual

Um ponto de extremidade privado é uma interface de rede especial para um serviço do Azure em sua [rede virtual](../virtual-network/virtual-networks-overview.md) (VNet). Quando você cria um ponto de extremidade privado para seu repositório de configuração de aplicativo, ele fornece conectividade segura entre clientes em sua VNet e seu repositório de configuração. O ponto de extremidade privado recebe um endereço IP do intervalo de endereços IP de sua VNet. A conexão entre o ponto de extremidade privado e o repositório de configuração usa um link privado seguro.

Os aplicativos na VNet podem se conectar ao armazenamento de configuração por meio do ponto de extremidade privado **usando as mesmas cadeias de conexão e mecanismos de autorização que eles usariam de outra forma**. Pontos de extremidade privados podem ser usados com todos os protocolos compatíveis com o repositório de configuração de aplicativo.

Embora a configuração de aplicativo não dê suporte a pontos de extremidade de serviço, os pontos de extremidade privados podem ser criados em sub-redes que usam [pontos de extremidade de serviço](../virtual-network/virtual-network-service-endpoints-overview.md). Os clientes em uma sub-rede podem se conectar com segurança a um repositório de configuração de aplicativo usando o ponto de extremidade privado ao usar pontos de extremidades de serviço para acessar outros.  

Quando você cria um ponto de extremidade privado para um serviço em sua VNet, uma solicitação de consentimento é enviada para aprovação para o proprietário da conta de serviço. Se o usuário que solicita a criação do ponto de extremidade privado também for um proprietário da conta, essa solicitação de consentimento será aprovada automaticamente.

Os proprietários da conta de serviço podem gerenciar solicitações de consentimento e pontos de `Private Endpoints` extremidade privados por meio da guia do repositório de configuração no [portal do Azure](https://portal.azure.com).

### <a name="private-endpoints-for-app-configuration"></a>Pontos de extremidade privados para configuração de aplicativo 

Ao criar um ponto de extremidade privado, você deve especificar o repositório de configuração do aplicativo ao qual ele se conecta. Se você tiver várias instâncias de configuração de aplicativo em uma conta, precisará de um ponto de extremidade privado separado para cada loja.

### <a name="connecting-to-private-endpoints"></a>Conectando-se a pontos de extremidade privados

O Azure conta com a resolução DNS para rotear conexões da VNet para o armazenamento de configuração por meio de um link privado. Você pode encontrar rapidamente as cadeias de conexão no portal do Azure selecionando seu repositório de configuração de aplicativo e, em seguida, selecionando **configurações** > **chaves de acesso**.  

> [!IMPORTANT]
> Use a mesma cadeia de conexão para se conectar ao armazenamento de configuração do aplicativo usando pontos de extremidade privados como você usaria para um ponto de extremidades público. Não se conecte à conta de armazenamento usando `privatelink` sua URL de subdomínio.

## <a name="dns-changes-for-private-endpoints"></a>Alterações de DNS para pontos de extremidade particulares

Quando você cria um ponto de extremidade privado, o registro de recurso DNS CNAME para o repositório de configuração é atualizado para um alias em um subdomínio com o prefixo `privatelink`. O Azure também cria uma [zona DNS privada](../dns/private-dns-overview.md) correspondente ao `privatelink` subdomínio, com os registros de recurso de DNS a para os pontos de extremidade privados.

Quando você resolve a URL do ponto de extremidade de fora da VNet, ela é resolvida para o ponto de extremidade público da loja. Quando resolvido de dentro da VNet que hospeda o ponto de extremidade privado, a URL do ponto de extremidade é resolvida para o ponto de extremidade privado.

Você pode controlar o acesso para clientes fora da VNet por meio do ponto de extremidade público usando o serviço de firewall do Azure.

Essa abordagem permite o acesso ao armazenamento **usando a mesma cadeia de conexão** para clientes na vnet que hospeda os pontos de extremidade privados, bem como clientes fora da vnet.

Se você estiver usando um servidor DNS personalizado em sua rede, os clientes deverão ser capazes de resolver o FQDN (nome de domínio totalmente qualificado) para o ponto de extremidade de serviço para o endereço IP do ponto de extremidade privado. Configure o servidor DNS para delegar seu subdomínio de link privado para a zona DNS privada para a VNet ou configure os registros a `AppConfigInstanceA.privatelink.azconfig.io` para com o endereço IP do ponto de extremidade privado.

> [!TIP]
> Ao usar um servidor DNS local ou personalizado, você deve configurar o servidor DNS para resolver o nome do repositório no `privatelink` subdomínio para o endereço IP do ponto de extremidade privado. Você pode fazer isso delegando o `privatelink` subdomínio à zona DNS privada da VNet ou configurando a zona DNS no servidor DNS e adicionando os registros a do DNS.

## <a name="pricing"></a>Preços

A habilitação de pontos de extremidade privados requer um repositório de configuração de aplicativo de [camada Standard](https://azure.microsoft.com/pricing/details/app-configuration/) .  Para saber mais sobre os detalhes de preços do link privado, confira [preços do link privado do Azure](https://azure.microsoft.com/pricing/details/private-link).

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre como criar um ponto de extremidade privado para seu repositório de configuração de aplicativo, consulte os seguintes artigos:

- [Criar um ponto de extremidade privado usando o centro de links privado no portal do Azure](../private-link/create-private-endpoint-portal.md)
- [Criar um ponto de extremidade privado usando a CLI do Azure](../private-link/create-private-endpoint-cli.md)
- [Criar um ponto de extremidade privado usando Azure PowerShell](../private-link/create-private-endpoint-powershell.md)

Saiba como configurar o servidor DNS com pontos de extremidade privados:

- [Resolução de nomes para recursos em redes virtuais do Azure](/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances#name-resolution-that-uses-your-own-dns-server)
- [Configuração de DNS para pontos de extremidade privados](/azure/private-link/private-endpoint-overview#dns-configuration)
