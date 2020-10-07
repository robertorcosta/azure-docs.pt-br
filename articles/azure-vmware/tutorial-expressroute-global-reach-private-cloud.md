---
title: Tutorial – Emparelhar ambientes locais com uma nuvem privada
description: Saiba como criar um emparelhamento de Alcance Global do ExpressRoute com uma nuvem privada em uma Solução VMware no Azure.
ms.topic: tutorial
ms.date: 09/21/2020
ms.openlocfilehash: 07b7e1c2636f3754eda56af574586a1027403d3e
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/30/2020
ms.locfileid: "91576720"
---
# <a name="tutorial-peer-on-premises-environments-to-a-private-cloud"></a>Tutorial: Emparelhar ambientes locais com uma nuvem privada

O Alcance Global do ExpressRoute conecta seu ambiente local à nuvem privada da Solução VMware no Azure. A conexão de Alcance Global do ExpressRoute é estabelecida entre um circuito do ExpressRoute de nuvem privada e uma conexão existente do ExpressRoute para os ambientes locais. 

O circuito do ExpressRoute que você usa ao [configurar a rede entre o Azure e a nuvem privada](tutorial-configure-networking.md) requer que você crie e use chaves de autorização ao emparelhar com gateways do ExpressRoute ou com outros circuitos do ExpressRoute usando o Alcance Global. Você já usou uma chave de autorização do circuito do ExpressRoute. Neste tutorial, criará uma segunda chave para emparelhar com o circuito do ExpressRoute local.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar uma segunda chave de autorização para o _circuito 2_, o circuito do ExpressRoute na nuvem privada
> * Usar o [portal do Azure](#azure-portal-method) ou a [CLI do Azure em um método do Cloud Shell](#azure-cli-in-a-cloud-shell-method) na assinatura do _circuito 1_ para habilitar o emparelhamento do Alcance Global do ExpressRoute entre o local e a nuvem privada


## <a name="before-you-begin"></a>Antes de começar

Antes de habilitar a conectividade entre dois circuitos do ExpressRoute usando o Alcance Global do ExpressRoute, examine a documentação sobre como [habilitar a conectividade em diferentes assinaturas do Azure](../expressroute/expressroute-howto-set-global-reach-cli.md#enable-connectivity-between-expressroute-circuits-in-different-azure-subscriptions).  


## <a name="prerequisites"></a>Pré-requisitos

1. Estabelecer a conectividade entre a nuvem privada da Solução VMware no Azure e o circuito do ExpressRoute emparelhado com o gateway do ExpressRoute em uma rede virtual do Azure (VNet) – o _circuito 2_ dos procedimentos de emparelhamento.  
1. Um circuito do ExpressRoute em funcionamento e separado, usado para conectar ambientes locais ao Azure – esse é o _circuito 1_ da perspectiva dos procedimentos de emparelhamento.
1. [Bloco de endereço de rede](../expressroute/expressroute-routing.md#ip-addresses-used-for-peerings) A/29 não sobreposto para o emparelhamento de Alcance Global do ExpressRoute.

> [!TIP]
> No contexto desses pré-requisitos, o circuito do ExpressRoute local é o _circuito 1_ e o circuito do ExpressRoute na nuvem privada está em uma assinatura diferente rotulada como _circuito 2_. 


## <a name="create-an-expressroute-authorization-key-in-the-private-cloud"></a>Criar uma chave de autorização do ExpressRoute na nuvem privada

1. Na **Visão geral** da nuvem privada, em Gerenciar, selecione **Conectividade > ExpressRoute > Solicitar uma chave de autorização**.

   :::image type="content" source="media/expressroute-global-reach/start-request-auth-key.png" alt-text="Selecionar Conectividade > ExpressRoute > Solicitar uma chave de autorização para iniciar uma nova solicitação.":::

2. Insira o nome da chave de autorização e selecione **Criar**. 

   :::image type="content" source="media/expressroute-global-reach/create-global-reach-auth-key.png" alt-text="Selecionar Conectividade > ExpressRoute > Solicitar uma chave de autorização para iniciar uma nova solicitação.":::

   Depois de criada, a nova chave aparece na lista de chaves de autorização para a nuvem privada. 

   :::image type="content" source="media/expressroute-global-reach/show-global-reach-auth-key.png" alt-text="Selecionar Conectividade > ExpressRoute > Solicitar uma chave de autorização para iniciar uma nova solicitação.":::

3. Anote a chave de autorização e a ID do ExpressRoute, junto com o bloco de endereço /29. Você os usará na próxima etapa para concluir o emparelhamento. 

## <a name="peer-private-cloud-to-on-premises-using-authorization-key"></a>Emparelhar a nuvem privada com o local usando a chave de autorização

Agora que você criou uma chave de autorização para o circuito do ExpressRoute da nuvem privada, poderá emparelhá-lo com o circuito do ExpressRoute local.  O emparelhamento é feito da perspectiva do circuito do ExpressRoute local no [portal do Azure](#azure-portal-method) ou na [CLI do Azure em um Cloud Shell](#azure-cli-in-a-cloud-shell-method). Com qualquer um dos métodos, você usará a ID do recurso e a chave de autorização do circuito do ExpressRoute na nuvem privada, que você criou nas etapas anteriores, para concluir o emparelhamento.

### <a name="azure-portal-method"></a>Método no portal do Azure

1. Entre no [portal do Azure](https://portal.azure.com) usando a mesma assinatura do circuito do ExpressRoute local.

1. Na **Visão geral** da nuvem privada, em "Gerenciar", selecione **Conectividade > Alcance Global do ExpressRoute > Adicionar**.

   :::image type="content" source="./media/expressroute-global-reach/expressroute-global-reach-tab.png" alt-text="Selecionar Conectividade > ExpressRoute > Solicitar uma chave de autorização para iniciar uma nova solicitação.":::

1. Você pode criar uma conexão de nuvem local seguindo um destes procedimentos:

   - Selecione o circuito do ExpressRoute na lista.
   - Se você tiver uma ID de circuito, copie e cole-a.

1. Selecione **Conectar**. A nova conexão é mostrada na lista conexões de nuvem locais.  

>[!TIP]
>Você pode excluir ou desconectar uma conexão da lista selecionando **Mais**.  
>
> :::image type="content" source="./media/expressroute-global-reach/on-premises-connection-disconnect.png" alt-text="Selecionar Conectividade > ExpressRoute > Solicitar uma chave de autorização para iniciar uma nova solicitação.":::

### <a name="azure-cli-in-a-cloud-shell-method"></a>Método da CLI do Azure em um Cloud Shell

Ampliamos os [comandos da CLI](../expressroute/expressroute-howto-set-global-reach-cli.md) com detalhes e exemplos específicos para ajudar você a configurar o emparelhamento do Alcance Global do ExpressRoute entre ambientes locais e uma nuvem privada da Solução VMware no Azure.  

> [!TIP]  
> Para ser breve quanto à saída de comando da CLI do Azure, estas instruções podem usar um argumento [`–query` para executar uma consulta JMESPath e mostrar apenas os resultados necessários](https://docs.microsoft.com/cli/azure/query-azure-cli).


1. Entre no portal do Azure usando a mesma assinatura que o circuito do ExpressRoute local e abra uma Cloud Shell. Deixe o shell como Bash.
 
   :::image type="content" source="media/expressroute-global-reach/open-cloud-shell.png" alt-text="Selecionar Conectividade > ExpressRoute > Solicitar uma chave de autorização para iniciar uma nova solicitação.":::
 
2. Na linha de comando, insira o comando da CLI do Azure para criar o emparelhamento, usando a ID de recurso e as informações específicas, a chave de autorização e o bloco de rede CIDR /29. 

   Confira a seguir um exemplo do comando que você usará e a saída indicando um emparelhamento bem-sucedido. O comando de exemplo é baseado no comando usado na [etapa 3 de "Habilitar a conectividade entre circuitos do ExpressRoute em diferentes assinaturas do Azure"](../expressroute/expressroute-howto-set-global-reach-cli.md#enable-connectivity-between-expressroute-circuits-in-different-azure-subscriptions).

   :::image type="content" source="media/expressroute-global-reach/azure-command-with-results.png" alt-text="Selecionar Conectividade > ExpressRoute > Solicitar uma chave de autorização para iniciar uma nova solicitação.":::
 
   Agora você deve conseguir se conectar dos ambientes locais à sua nuvem privada por meio do emparelhamento de Alcance Global do ExpressRoute.

> [!TIP]
> Você pode excluir o emparelhamento que acabou de criar seguindo as instruções em [Desabilitar a conectividade entre suas redes locais](../expressroute/expressroute-howto-set-global-reach-cli.md#disable-connectivity-between-your-on-premises-networks).


## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a criar uma segunda chave de autorização para o circuito do ExpressRoute na nuvem privada e habilitou o emparelhamento do Alcance Global do ExpressRoute entre o local e a nuvem privada. 

Siga para o próximo tutorial para aprender a implantar e configurar a solução VMware HCX para a nuvem privada da Solução VMware no Azure.

> [!div class="nextstepaction"]
> [Implantar e configurar o VMware HCX](tutorial-deploy-vmware-hcx.md)


<!-- LINKS - external-->

<!-- LINKS - internal -->
