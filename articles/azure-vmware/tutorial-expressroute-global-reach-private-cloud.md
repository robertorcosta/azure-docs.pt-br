---
title: Emparelhar ambientes locais com uma nuvem privada
description: Neste tutorial da Solução VMware no Azure, você cria o emparelhamento de Alcance Global do ExpressRoute com uma nuvem privada em uma Solução VMware no Azure.
ms.topic: tutorial
ms.date: 07/16/2020
ms.openlocfilehash: db3f5988cb8c07d9b6e80f500ac6aff8f96dfded
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/22/2020
ms.locfileid: "88750456"
---
# <a name="tutorial-peer-on-premises-environments-to-a-private-cloud"></a>Tutorial: Emparelhar ambientes locais com uma nuvem privada

O Alcance Global do ExpressRoute conecta seu ambiente local às nuvens privadas. A conexão de Alcance Global do ExpressRoute é estabelecida entre um circuito do ExpressRoute de nuvem privada e uma conexão existente do ExpressRoute para os ambientes locais.  Há instruções para configurar o Alcance Global do ExpressRoute com a CLI do Azure e o PowerShell, e ampliamos os [comandos da CLI](../expressroute/expressroute-howto-set-global-reach-cli.md) com detalhes e exemplos específicos para ajudar você a configurar o emparelhamento do Alcance Global do ExpressRoute entre ambientes locais e uma nuvem privada da Solução VMware no Azure.   

Antes de habilitar a conectividade entre dois circuitos do ExpressRoute usando o Alcance Global do ExpressRoute, examine a documentação sobre como [habilitar a conectividade em diferentes assinaturas do Azure](../expressroute/expressroute-howto-set-global-reach-cli.md#enable-connectivity-between-expressroute-circuits-in-different-azure-subscriptions).  O circuito do ExpressRoute que você usa ao [configurar a rede entre o Azure e a nuvem privada](tutorial-configure-networking.md) requer que você crie e use chaves de autorização ao emparelhar com gateways do ExpressRoute ou com outros circuitos do ExpressRoute usando o Alcance Global. Você já usou uma chave de autorização do circuito do ExpressRoute e criará uma segunda chave para emparelhar com o circuito do ExpressRoute local.

> [!TIP]
> No contexto destas instruções, o circuito do ExpressRoute local é _circuito 1_, e o circuito do ExpressRoute na nuvem privada está em uma assinatura diferente e rotulada como _circuito 2_. 

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Aproveitar as instruções existentes para gerenciar circuitos do ExpressRoute e emparelhamentos de Alcance Global do ExpressRoute
> * Criar uma chave de autorização para o _circuito 2_, o circuito do ExpressRoute na nuvem privada
> * Usar a CLI do Azure em um Cloud Shell na assinatura do _circuito 1_ para habilitar o emparelhamento de Alcance Global do ExpressRoute do local com a nuvem privada

## <a name="prerequisites"></a>Pré-requisitos

Os pré-requisitos deste tutorial são:
- Uma nuvem privada com o circuito do ExpressRoute emparelhado com um gateway do ExpressRoute em uma VNet (rede virtual) do Azure – esse é o _circuito 2_ da perspectiva dos procedimentos de emparelhamento.
- Um circuito do ExpressRoute em funcionamento e separado, usado para conectar ambientes locais ao Azure – esse é o _circuito 1_ da perspectiva dos procedimentos de emparelhamento.
- [Bloco de endereço de rede](../expressroute/expressroute-routing.md#ip-addresses-used-for-peerings) A/29 não sobreposto para o emparelhamento de Alcance Global do ExpressRoute.

## <a name="create-an-expressroute-authorization-key-in-the-azure-vmware-solution-private-cloud"></a>Criar uma chave de autorização do ExpressRoute na nuvem privada da Solução VMware no Azure

1. Na **Visão geral** da nuvem privada, em Gerenciar, selecione **Conectividade > ExpressRoute > Solicitar uma chave de autorização**.

   :::image type="content" source="media/expressroute-global-reach/start-request-auth-key.png" alt-text="Selecionar Conectividade > ExpressRoute > Solicitar uma chave de autorização para iniciar uma nova solicitação.":::

2. Insira o nome da chave de autorização e selecione **Criar**. 

   :::image type="content" source="media/expressroute-global-reach/create-global-reach-auth-key.png" alt-text="Clicar em Criar para criar uma chave de autorização. ":::

   Depois de criada, a nova chave aparece na lista de chaves de autorização para a nuvem privada. 

   :::image type="content" source="media/expressroute-global-reach/show-global-reach-auth-key.png" alt-text="Confirmar se a nova chave de autorização aparece na lista de chaves para a nuvem privada. ":::

3. Anote a chave de autorização e a ID do ExpressRoute, junto com o bloco de endereço /29. Você os usará na próxima etapa para concluir o emparelhamento. 

## <a name="peer-private-cloud-to-on-premises-using-authorization-key"></a>Emparelhar a nuvem privada com o local usando a chave de autorização

Agora que você criou uma chave de autorização para o circuito do ExpressRoute da nuvem privada, poderá emparelhá-lo com o circuito do ExpressRoute local.  O emparelhamento é feito pela perspectiva do circuito do ExpressRoute local usando a CLI do Azure em um Cloud Shell e a ID de recurso e a chave de autorização do circuito do ExpressRoute na nuvem privada, que foi obtida nas etapas anteriores.

> [!TIP]  
> Para ser breve quanto à saída de comando da CLI do Azure, estas instruções podem usar um argumento [`–query` para executar uma consulta JMESPath e mostrar apenas os resultados necessários](https://docs.microsoft.com/cli/azure/query-azure-cli?view=azure-cli-latest).


1. Entre no portal do Azure usando a mesma assinatura que o circuito do ExpressRoute local e abra uma Cloud Shell. Deixe o shell como Bash.
 
   :::image type="content" source="media/expressroute-global-reach/open-cloud-shell.png" alt-text="Entrar no portal do Azure e abrir um Cloud Shell.":::
 
2. Na linha de comando, insira o comando da CLI do Azure para criar o emparelhamento, usando a ID de recurso e as informações específicas, a chave de autorização e o bloco de rede CIDR /29. 

   Confira a seguir um exemplo do comando que você usará e a saída indicando um emparelhamento bem-sucedido. O comando de exemplo é baseado no comando usado na [etapa 3 de "Habilitar a conectividade entre circuitos do ExpressRoute em diferentes assinaturas do Azure"](../expressroute/expressroute-howto-set-global-reach-cli.md#enable-connectivity-between-expressroute-circuits-in-different-azure-subscriptions).

   :::image type="content" source="media/expressroute-global-reach/azure-command-with-results.png" alt-text="Criar o emparelhamento de Alcance Global do ExpressRoute com o comando da CLI do Azure em um Cloud Shell.":::
 
   Agora você deve conseguir se conectar dos ambientes locais à sua nuvem privada por meio do emparelhamento de Alcance Global do ExpressRoute.

> [!TIP]
> Você pode excluir o emparelhamento que acabou de criar seguindo as instruções em [Desabilitar a conectividade entre suas redes locais](../expressroute/expressroute-howto-set-global-reach-cli.md#disable-connectivity-between-your-on-premises-networks).


## <a name="next-steps"></a>Próximas etapas

Se planejar usar a HCX (Extensão de Nuvem Híbrida) para migrar cargas de trabalho de VM para sua nuvem privada, use o procedimento [Instalar a HCX para a Solução VMware no Azure](hybrid-cloud-extension-installation.md).


<!-- LINKS - external-->

<!-- LINKS - internal -->
