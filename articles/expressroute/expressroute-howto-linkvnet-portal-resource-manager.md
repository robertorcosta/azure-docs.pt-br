---
title: 'ExpressRoute: vincular uma VNet a um circuito: portal do Azure'
description: Conecte uma VNet a um circuito do Azure ExpressRoute. Etapas de instruções.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 09/17/2019
ms.author: cherylmc
ms.custom: seodec18
ms.openlocfilehash: 4c7a24ad692086398059d1afd48c8927e9d18582
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74022199"
---
# <a name="connect-a-virtual-network-to-an-expressroute-circuit-using-the-portal"></a>Conectar uma rede virtual a um circuito ExpressRoute usando o portal
> [!div class="op_single_selector"]
> * [Portal do Azure](expressroute-howto-linkvnet-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-linkvnet-arm.md)
> * [CLI do Azure](howto-linkvnet-cli.md)
> * [Vídeo – Portal do Azure](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit)
> * [PowerShell (clássico)](expressroute-howto-linkvnet-classic.md)
> 

Este artigo ajuda você a criar uma conexão para vincular uma rede virtual a um circuito do Microsoft Azure ExpressRoute usando o portal do Azure. As redes virtuais conectadas ao circuito do Microsoft Azure ExpressRoute podem estar na mesma assinatura ou serem parte de outra assinatura.

## <a name="before-you-begin"></a>Antes de começar

* Analise os [pré-requisitos](expressroute-prerequisites.md), os [requisitos de roteamento](expressroute-routing.md) e os [fluxos de trabalho](expressroute-workflows.md) antes de começar a configuração.

* Você deve ter um circuito da ExpressRoute ativo.
  * Siga as instruções para [criar um circuito do ExpressRoute](expressroute-howto-circuit-portal-resource-manager.md) e para que o circuito seja habilitado pelo provedor de conectividade.
  * Verifique se o emparelhamento privado do Azure está configurado para seu circuito. Consulte o artigo [criar e modificar o emparelhamento para um circuito do ExpressRoute](expressroute-howto-routing-portal-resource-manager.md) para obter instruções de emparelhamento e roteamento.
  * Verifique se o emparelhamento privado do Azure está configurado e se o emparelhamento BGP entre sua rede e a Microsoft está ativo para que você possa habilitar a conectividade de ponta a ponta.
  * Verifique se tem uma rede virtual e um gateway de rede virtual criados e totalmente provisionados. Siga as instruções para [criar um gateway de rede virtual para ExpressRoute](expressroute-howto-add-gateway-resource-manager.md). Um gateway de rede virtual para ExpressRoute usa o GatewayType “ExpressRoute”, não VPN.

* Você pode vincular até 10 redes virtuais a um circuito do ExpressRoute padrão. Todas as redes virtuais deverão estar na mesma região geopolítica ao usar um circuito do ExpressRoute padrão.

* Uma única rede virtual pode ser vinculada a até quatro circuitos de ExpressRoute. Use as etapas a seguir para criar uma nova conexão para cada circuito de ExpressRoute ao qual você está se conectando. Os circuitos de ExpressRoute podem estar na mesma assinatura, assinaturas diferentes ou uma mistura de ambos.

* Você poderá vincular uma rede virtual fora da região geopolítica do circuito do ExpressRoute ou conectar um grande número de redes virtuais ao circuito do ExpressRoute, se tiver habilitado o complemento premium do ExpressRoute. Confira as [perguntas frequentes](expressroute-faqs.md) para obter mais detalhes sobre o complemento premium.

* Você pode [exibir um vídeo](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit) antes de começar para entender melhor as etapas.

## <a name="connect-a-vnet-to-a-circuit---same-subscription"></a>Conectar uma VNet a um circuito - mesma assinatura

> [!NOTE]
> As informações de configuração do BGP não aparecerão se o provedor de camada 3 tiver configurado seus emparelhamentos. Se o circuito estiver no estado de provisionamento, você poderá criar conexões.
>

### <a name="to-create-a-connection"></a>Para criar uma conexão

1. Certifique-se de que o circuito de ExpressRoute e emparelhamento privado do Azure foram configurados com êxito. Siga as instruções em [criar um circuito do expressroute](expressroute-howto-circuit-arm.md) e [criar e modificar o emparelhamento para um circuito do expressroute](expressroute-howto-routing-arm.md). O circuito do ExpressRoute deve se parecer com a imagem a seguir:

   [![Captura de tela do circuito do ExpressRoute](./media/expressroute-howto-linkvnet-portal-resource-manager/routing1.png "Exibir circuito")](./media/expressroute-howto-linkvnet-portal-resource-manager/routing1-exp.png#lightbox)
2. Agora, você pode começar a provisionar uma conexão para vincular seu gateway de rede virtual ao circuito de ExpressRoute. Clique em **Conexão** > **Adicionar** para abrir o painel **Adicionar conexão** e, em seguida, configure os valores.

   [![Adicionar captura de tela de conexão](./media/expressroute-howto-linkvnet-portal-resource-manager/samesub1.png "Adicionar captura de tela de conexão")](./media/expressroute-howto-linkvnet-portal-resource-manager/samesub1-exp.png#lightbox)
3. Depois que sua conexão foi configurada com êxito, seu objeto de conexão mostrará as informações para a conexão.

   ![Captura de tela de objeto de conexão](./media/expressroute-howto-linkvnet-portal-resource-manager/samesub2.png)


## <a name="connect-a-vnet-to-a-circuit---different-subscription"></a>Conectar uma VNet a um circuito - assinatura diferente

Você pode compartilhar um circuito do ExpressRoute entre várias assinaturas. A figura abaixo mostra um esquema simples de como funciona o compartilhamento de circuitos do ExpressRoute entre várias assinaturas.

![Conectividade entre assinaturas](./media/expressroute-howto-linkvnet-portal-resource-manager/cross-subscription.png)

- Cada uma das nuvens menores dentro da nuvem grande é usada para representar assinaturas pertencentes a diferentes departamentos dentro de uma organização.
- Cada um dos departamentos dentro da organização pode usar sua própria assinatura para implantar seus serviços, mas pode compartilhar um único circuito do ExpressRoute para se conectar de volta à respectiva rede local.
- Um único departamento (neste exemplo: TI) pode ter o circuito do ExpressRoute. Outras assinaturas na organização podem usar o circuito do ExpressRoute e as autorizações associadas ao circuito, incluindo as assinaturas vinculadas a outros locatários do Azure Active Directory e os registros do Contrato Enterprise.

  > [!NOTE]
  > As cobranças por conectividade e largura de banda do circuito dedicado serão aplicadas ao proprietário do circuito do ExpressRoute. Todas as redes virtuais compartilham a mesma largura de banda.
  >
  >

### <a name="administration---about-circuit-owners-and-circuit-users"></a>Administração – sobre proprietários do circuito e usuários do circuito

O “proprietário do circuito” é um usuário avançado autorizado do recurso de circuito do ExpressRoute. O proprietário do circuito pode criar autorizações que podem ser resgatadas pelos ‘usuários do circuito’. Usuários do circuito são proprietários de gateways de rede virtual que não estão na mesma assinatura que o circuito do ExpressRoute. Usuários do circuito podem resgatar autorizações (uma autorização por rede virtual).

O proprietário do circuito tem a capacidade de modificar e revogar autorizações a qualquer momento. Revogar uma autorização faz com que todas as conexões de links sejam excluídas da assinatura cujo acesso foi revogado.

### <a name="circuit-owner-operations"></a>Operações do proprietário do circuito

**Criar uma autorização de conexão**

O proprietário do circuito cria uma autorização. Isso resulta na criação de uma chave de autorização que pode ser usada por um usuário do circuito para conectar seus gateways de rede virtual ao circuito do ExpressRoute. Uma autorização é válida apenas para uma conexão.

> [!NOTE]
> Cada conexão requer uma autorização separada.
>

1. Na página ExpressRoute, clique em **Autorizações** e, em seguida, digite um **nome** para a autorização e clique em **Salvar**.

   ![Autorizações](./media/expressroute-howto-linkvnet-portal-resource-manager/authorization.png)
2. Após a gravação da configuração, copie a **ID do Recurso** e a **Chave de Autorização**.

   ![Chave de autorização](./media/expressroute-howto-linkvnet-portal-resource-manager/authkey.png)

**Excluir uma autorização de conexão**

Você pode excluir uma conexão, selecionando o ícone **Excluir** na página de sua conexão.

### <a name="circuit-user-operations"></a>Operações do usuário do circuito

O usuário do circuito precisa da ID do recurso e de uma chave de autorização do proprietário do circuito.

**Resgatar uma autorização de conexão**

1. Clique no botão **+Novo**.

   ![Clique em Novo](./media/expressroute-howto-linkvnet-portal-resource-manager/Connection1.png)
2. Pesquise **“Conexão”** no Marketplace, selecione-a e clique em **Criar**.

   ![Pesquisar conexão](./media/expressroute-howto-linkvnet-portal-resource-manager/Connection2.png)
3. Verifique se o **Tipo de conexão** está definido como "ExpressRoute".
4. Preencha os detalhes e clique em **OK** na página Noções Básicas.

   ![Página de Noções Básicas](./media/expressroute-howto-linkvnet-portal-resource-manager/Connection3.png)
5. Na página **Configurações**, selecione **Gateway de rede Virtual** e marque a caixa de seleção **Resgatar autorização**.
6. Insira a **Chave de autorização** e o **URI do circuito par** e nomeie a conexão. Clique em **OK**. O **URI do circuito de mesmo nível** é a ID de recurso do circuito do expressroute (que pode ser encontrado no painel de configuração Propriedades do circuito do expressroute).

   ![Página Configurações](./media/expressroute-howto-linkvnet-portal-resource-manager/Connection4.png)
7. Revise as informações na página **Resumo** e clique em **OK**.

**Liberar uma autorização de conexão**

É possível liberar uma autorização excluindo a conexão que vincula o circuito do ExpressRoute à rede virtual.

## <a name="delete-a-connection-to-unlink-a-vnet"></a>Excluir uma conexão para desvincular uma VNet

É possível excluir uma conexão e desvincular a VNet de um circuito ExpressRoute, selecionando o ícone **Excluir** na página da sua conexão.

## <a name="next-steps"></a>Próximas etapas
Para obter mais informações sobre o ExpressRoute, consulte [Perguntas Frequentes sobre o ExpressRoute](expressroute-faqs.md).
