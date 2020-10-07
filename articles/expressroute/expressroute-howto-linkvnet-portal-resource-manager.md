---
title: 'Tutorial: vincular uma VNet a um circuito do ExpressRoute-portal do Azure'
description: Este tutorial mostra como criar uma conexão para vincular uma rede virtual a um circuito do Azure ExpressRoute usando o portal do Azure.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 10/06/2020
ms.author: duau
ms.custom: seodec18
ms.openlocfilehash: bfb358694cbdd214490fb41052e508b94d10baf4
ms.sourcegitcommit: ef69245ca06aa16775d4232b790b142b53a0c248
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/06/2020
ms.locfileid: "91772985"
---
# <a name="tutorial-connect-a-virtual-network-to-an-expressroute-circuit-using-the-portal"></a>Tutorial: conectar uma rede virtual a um circuito do ExpressRoute usando o portal

> [!div class="op_single_selector"]
> * [Azure portal](expressroute-howto-linkvnet-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-linkvnet-arm.md)
> * [CLI do Azure](howto-linkvnet-cli.md)
> * [Vídeo – Portal do Azure](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit)
> * [PowerShell (clássico)](expressroute-howto-linkvnet-classic.md)
> 

Este tutorial ajuda a criar uma conexão para vincular uma rede virtual a um circuito do Azure ExpressRoute usando o portal do Azure. As redes virtuais que você conecta ao seu circuito do Azure ExpressRoute podem estar na mesma assinatura ou fazer parte de outra assinatura.

Neste tutorial, você aprenderá como:
> [!div class="checklist"]
> - Conecte uma rede virtual a um circuito na mesma assinatura.
> - Conecte uma rede virtual a um circuito em uma assinatura diferente.
> - Exclua o link entre a rede virtual e o circuito do ExpressRoute.

## <a name="prerequisites"></a>Pré-requisitos

* Analise os [pré-requisitos](expressroute-prerequisites.md), os [requisitos de roteamento](expressroute-routing.md) e os [fluxos de trabalho](expressroute-workflows.md) antes de começar a configuração.

* Você deve ter um circuito do ExpressRoute ativo.
  * Siga as instruções para [criar um circuito do ExpressRoute](expressroute-howto-circuit-portal-resource-manager.md) e para que o circuito seja habilitado pelo provedor de conectividade.
  * Verifique se o emparelhamento privado do Azure está configurado para seu circuito. Consulte o artigo [criar e modificar o emparelhamento para um circuito do ExpressRoute](expressroute-howto-routing-portal-resource-manager.md) para obter instruções de emparelhamento e roteamento.
  * Verifique se o emparelhamento privado do Azure é configurado e estabelece o emparelhamento via protocolo BGP entre sua rede e a Microsoft para conectividade de ponta a ponta.
  * Verifique se tem uma rede virtual e um gateway de rede virtual criados e totalmente provisionados. Siga as instruções para [criar um gateway de rede virtual para ExpressRoute](expressroute-howto-add-gateway-resource-manager.md). Um gateway de rede virtual para ExpressRoute usa o GatewayType “ExpressRoute”, não VPN.

* Você pode vincular até 10 redes virtuais a um circuito de ExpressRoute padrão. Todas as redes virtuais deverão estar na mesma região geopolítica ao usar um circuito de ExpressRoute padrão.

* Uma única rede virtual pode ser vinculada a até quatro circuitos de ExpressRoute. Use o processo a seguir para criar um novo objeto de conexão para cada circuito do ExpressRoute ao qual você está se conectando. Os circuitos de ExpressRoute podem estar na mesma assinatura, assinaturas diferentes ou uma mistura de ambos.

* Se você habilitar o complemento Premium do ExpressRoute, poderá vincular redes virtuais fora da região geopolítica do circuito do ExpressRoute. O complemento Premium também permitirá que você conecte mais de 10 redes virtuais ao seu circuito de ExpressRoute, dependendo da largura de banda escolhida. Confira as [perguntas frequentes](expressroute-faqs.md) para obter mais detalhes sobre o complemento premium.

* Você pode [exibir um vídeo](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit) antes de começar para entender melhor as etapas.

## <a name="connect-a-vnet-to-a-circuit---same-subscription"></a>Conectar uma VNet a um circuito - mesma assinatura

> [!NOTE]
> As informações de configuração do BGP não serão exibidas se o provedor da camada 3 tiver configurado seus emparelhamentos. Se o circuito estiver no estado de provisionamento, você poderá criar conexões.
>

### <a name="to-create-a-connection"></a>Para criar uma conexão

1. Certifique-se de que o circuito de ExpressRoute e emparelhamento privado do Azure foram configurados com êxito. Siga as instruções em [criar um circuito do expressroute](expressroute-howto-circuit-arm.md) e [criar e modificar o emparelhamento para um circuito do expressroute](expressroute-howto-routing-arm.md). O circuito do ExpressRoute deve se parecer com a imagem a seguir:

    :::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/express-route-circuit.png" alt-text="Captura de tela de circuito do ExpressRoute":::

2. Agora, você pode começar a provisionar uma conexão para vincular seu gateway de rede virtual ao circuito de ExpressRoute. Selecione **conexão**  >  **Adicionar** para abrir a página **Adicionar conexão** e, em seguida, configure os valores.

    :::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/add-connection.png" alt-text="Captura de tela de circuito do ExpressRoute":::

3. Depois que sua conexão foi configurada com êxito, seu objeto de conexão mostrará as informações para a conexão.

    :::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/connection-object.png" alt-text="Captura de tela de circuito do ExpressRoute":::

## <a name="connect-a-vnet-to-a-circuit---different-subscription"></a>Conectar uma VNet a um circuito - assinatura diferente

Você pode compartilhar um circuito do ExpressRoute entre várias assinaturas. A figura a seguir mostra um esquema simples de como funciona o compartilhamento de circuitos do ExpressRoute entre várias assinaturas.

:::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/cross-subscription.png" alt-text="Captura de tela de circuito do ExpressRoute":::

Cada uma das nuvens menores dentro da nuvem grande é usada para representar assinaturas pertencentes a diferentes departamentos dentro de uma organização. Cada um dos departamentos dentro da organização usa sua própria assinatura para implantar seus serviços, mas eles podem compartilhar um único circuito do ExpressRoute para se conectar de volta à sua rede local. Um único departamento (neste exemplo: TI) pode ter o circuito do ExpressRoute. Outras assinaturas dentro da organização podem usar o circuito do ExpressRoute.

  > [!NOTE]
  > As cobranças por conectividade e largura de banda do circuito dedicado serão aplicadas ao proprietário do circuito do ExpressRoute. Todas as redes virtuais compartilham a mesma largura de banda.
  >

### <a name="administration---about-circuit-owners-and-circuit-users"></a>Administração – sobre proprietários do circuito e usuários do circuito

O “proprietário do circuito” é um usuário avançado autorizado do recurso de circuito do ExpressRoute. O proprietário do circuito pode criar autorizações que podem ser resgatadas pelos ‘usuários do circuito’. Os usuários de circuito são proprietários de gateways de rede virtual que não estão na mesma assinatura que o circuito de ExpressRoute. Usuários do circuito podem resgatar autorizações (uma autorização por rede virtual).

O proprietário do circuito tem a capacidade de modificar e revogar autorizações a qualquer momento. Revogar uma autorização faz com que todas as conexões de links sejam excluídas da assinatura cujo acesso foi revogado.

### <a name="circuit-owner-operations"></a>Operações do proprietário do circuito

**Criar uma autorização de conexão**

O proprietário do circuito cria uma autorização, que cria uma chave de autorização a ser usada por um usuário de circuito para conectar seus gateways de rede virtual ao circuito do ExpressRoute. Uma autorização é válida apenas para uma conexão.

> [!NOTE]
> Cada conexão requer uma autorização separada.
>

1. Na página ExpressRoute, selecione **autorizações** e, em seguida, digite um **nome** para a autorização e selecione **salvar**.

    :::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/authorization.png" alt-text="Captura de tela de circuito do ExpressRoute":::

2. Após a gravação da configuração, copie a **ID do Recurso** e a **Chave de Autorização**.

    :::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/authorization-key.png" alt-text="Captura de tela de circuito do ExpressRoute":::

**Excluir uma autorização de conexão**

Você pode excluir uma conexão, selecionando o ícone **Excluir** na página de sua conexão.

### <a name="circuit-user-operations"></a>Operações do usuário do circuito

O usuário do circuito precisa da ID do recurso e de uma chave de autorização do proprietário do circuito.

**Resgatar uma autorização de conexão**

1. Selecione o botão **+ novo** .

    :::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/create-new-resources.png" alt-text="Captura de tela de circuito do ExpressRoute":::

2. Pesquise **"conexão"** no Marketplace, selecione-o e selecione **criar**.

    :::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/search-connection.png" alt-text="Captura de tela de circuito do ExpressRoute":::

3. Verifique se o **Tipo de conexão** está definido como "ExpressRoute".
4. Preencha os detalhes e selecione **OK** na página noções básicas.

    :::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/connection-basics.png" alt-text="Captura de tela de circuito do ExpressRoute":::

5. Na página **Configurações**, selecione **Gateway de rede Virtual** e marque a caixa de seleção **Resgatar autorização**.
6. Insira a **Chave de autorização** e o **URI do circuito par** e nomeie a conexão. Selecione **OK**. O **URI do circuito de mesmo nível** é a ID de recurso do circuito do expressroute (que pode ser encontrado no painel de configuração Propriedades do circuito do expressroute).

    :::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/connection-settings.png" alt-text="Captura de tela de circuito do ExpressRoute":::

7. Examine as informações na página **Resumo** e selecione **OK**.

**Liberar uma autorização de conexão**

É possível liberar uma autorização excluindo a conexão que vincula o circuito do ExpressRoute à rede virtual.

## <a name="clean-up-resources"></a>Limpar os recursos

É possível excluir uma conexão e desvincular a VNet de um circuito ExpressRoute, selecionando o ícone **Excluir** na página da sua conexão.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a conectar uma rede virtual a um circuito na mesma assinatura e em uma assinatura diferente. Para obter mais informações sobre o gateway de ExpressRoute, consulte: 

> [!div class="nextstepaction"]
> [Sobre gateways de rede virtual do ExpressRoute](expressroute-about-virtual-network-gateways.md)
