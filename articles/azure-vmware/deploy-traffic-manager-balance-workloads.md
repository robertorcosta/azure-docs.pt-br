---
title: Implantar o Gerenciador de tráfego para balancear cargas de trabalho da AVS (solução VMware) do Azure
description: Saiba como integrar o Gerenciador de tráfego à solução de VMware do Azure (AVS) para balancear cargas de trabalho de aplicativos em vários pontos de extremidade em regiões diferentes.
ms.topic: how-to
ms.date: 08/14/2020
ms.openlocfilehash: ed74bb0dfc533abadd50af32afc06c9cb4106193
ms.sourcegitcommit: 642988f1ac17cfd7a72ad38ce38ed7a5c2926b6c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/18/2020
ms.locfileid: "94874263"
---
# <a name="deploy-traffic-manager-to-balance-azure-vmware-solution-avs-workloads"></a>Implantar o Gerenciador de tráfego para balancear cargas de trabalho da AVS (solução VMware) do Azure

Este artigo explica como integrar o Gerenciador de tráfego com a AVS (solução VMware do Azure) para balancear cargas de trabalho de aplicativos em vários pontos de extremidade. Veremos um cenário no qual o Gerenciador de tráfego direcionará o tráfego entre três gateways de aplicativo abrangendo várias regiões da AVS: oeste dos EUA, Europa Ocidental e local no leste dos EUA. 

O Gerenciador de tráfego do Azure é um balanceador de carga de tráfego baseado em DNS que permite distribuir o tráfego de forma ideal para serviços em regiões globais do Azure. Ele balanceará a carga do tráfego do aplicativo entre as cargas de trabalho em execução do Azure e os pontos de extremidade públicos externos. Para obter mais informações sobre o Gerenciador de tráfego, consulte [o que é o Gerenciador de tráfego?](../traffic-manager/traffic-manager-overview.md)

Examine os [pré-requisitos](#prerequisites) primeiro; em seguida, vamos examinar os procedimentos para:

> [!div class="checklist"]
> * Verificar a configuração de seus gateways de aplicativo
> * Verificar a configuração do segmento NSX-T
> * Criar seu perfil do Gerenciador de tráfego
> * Adicionar pontos de extremidade externos ao seu perfil do Gerenciador de tráfego

## <a name="topology"></a>Topologia

Conforme mostrado na figura a seguir, o Gerenciador de tráfego do Azure fornece balanceamento de carga para os aplicativos no nível de DNS entre pontos de extremidade regionais. Os gateways de aplicativo têm membros do pool de back-end configurados como servidores IIS e são referenciados como pontos de extremidade externos da AVS.

A conexão pela rede virtual entre as duas regiões de nuvem privada da AVS, oeste dos EUA e Europa Ocidental e um servidor local no leste dos EUA, usa um gateway de ExpressRoute.   

:::image type="content" source="media/traffic-manager/traffic-manager-topology.png" alt-text="Diagrama da arquitetura da integração do Gerenciador de tráfego com a solução VMware do Azure" lightbox="media/traffic-manager/traffic-manager-topology.png" border="false":::
 
## <a name="prerequisites"></a>Pré-requisitos

- Três máquinas virtuais configuradas como servidores IIS da Microsoft em execução em diferentes regiões da AVS: oeste dos EUA, Europa Ocidental e local. 

- Um gateway de aplicativo com pontos de extremidade externos no oeste dos EUA, Europa Ocidental e no local.

- Hospede com conectividade com a Internet para verificação. 

## <a name="verify-configuration-of-your-application-gateways"></a>Verificar a configuração de seus gateways de aplicativo

[Aplicativo Azure gateway](https://azure.microsoft.com/services/application-gateway/) é um balanceador de carga de tráfego da Web de camada 7 que permite que você gerencie o tráfego para seus aplicativos Web. Para obter mais informações sobre o gateway de aplicativo, consulte [o que é aplicativo Azure gateway?](../application-gateway/overview.md) 

Nesse cenário, três instâncias de gateway de aplicativo são configuradas como pontos de extremidade da AVS externa. Os gateways de aplicativo têm máquinas virtuais de AVS configuradas como membros do pool de back-end para balancear a carga das solicitações de entrada da camada 7. (Para saber como configurar o gateway de aplicativo com máquinas virtuais de AVS como pools de back-end, consulte [usar aplicativo Azure gateway para proteger seus aplicativos Web na solução do Azure VMware](protect-azure-vmware-solution-with-application-gateway.md).)  

As etapas a seguir verificam a configuração correta de seus gateways de aplicativo.

1. Abra o portal do Azure e selecione **gateways de aplicativo** para exibir uma lista de seus gateways de aplicativo atuais. 

    Para este cenário, configuramos três gateways de aplicativo:
    - AVS-GW-WUS
    - AVS-GW-EUS (local)
    - AVS-GW-WEU

    :::image type="content" source="media/traffic-manager/app-gateways-list-1.png" alt-text="Captura de tela da página do gateway de aplicativo mostrando a lista de gateways de aplicativo configurados." lightbox="media/traffic-manager/app-gateways-list-1.png":::

2. Selecione um dos gateways de aplicativo implantados anteriormente. Uma janela é aberta mostrando várias informações no gateway de aplicativo. Selecione **pools de back-end** para verificar a configuração de um dos pools de back-end.

   :::image type="content" source="media/traffic-manager/backend-pool-config.png" alt-text="Captura de tela da página do gateway de aplicativo mostrando detalhes do gateway de aplicativo selecionado." lightbox="media/traffic-manager/backend-pool-config.png":::
 
3. Nesse caso, vemos um membro do pool de back-end de máquina virtual configurado como um servidor Web com um endereço IP de 172.29.1.10.
 
    :::image type="content" source="media/traffic-manager/backend-pool-ip-address.png" alt-text="Captura de tela da página Editar pool de back-end com endereço IP de destino realçado.":::

    Da mesma forma, você pode verificar a configuração dos outros gateways de aplicativo e membros do pool de back-end. 

## <a name="verify-configuration-of-the-nsx-t-segment"></a>Verificar a configuração do segmento NSX-T

Os segmentos de rede criados no Gerenciador de NSX-T são usados como redes para máquinas virtuais no vCenter. Para obter mais informações, consulte o tutorial [criar um segmento de rede do NSX-T na solução VMware do Azure (AVS)](tutorial-nsx-t-network-segment.md).

Em nosso cenário, um segmento do NSX-T é configurado no ambiente de AVS em que a máquina virtual do membro do pool de back-end está conectada.

1. Selecione **segmentos** para exibir seus segmentos configurados. Nesse caso, vemos que contoso-segment1 está conectado ao gateway contoso-T01, um roteador flexível de camada 1.

    :::image type="content" source="media/traffic-manager/nsx-t-segment-avs.png" alt-text="Captura de tela mostrando perfis de segmento no Gerenciador de NSX-T." lightbox="media/traffic-manager/nsx-t-segment-avs.png":::    

2. Selecione **gateways de camada 1** para ver uma lista de seus gateways de camada 1 com o número de segmentos vinculados. Selecione o segmento vinculado ao contoso-T01. Uma janela é aberta mostrando a interface lógica configurada no roteador camada-01. Isso serve como gateway para a máquina virtual do membro do pool de back-end conectada ao segmento.

   :::image type="content" source="media/traffic-manager/nsx-t-segment-linked-2.png" alt-text="Captura de tela mostrando o endereço do gateway do segmento selecionado.":::    

3. No cliente VM vSphere, selecione a máquina virtual para exibir seus detalhes. Observe que seu endereço IP corresponde ao que vimos na etapa 3 da seção anterior: 172.29.1.10.

    :::image type="content" source="media/traffic-manager/nsx-t-vm-details.png" alt-text="Captura de tela mostrando detalhes da VM no cliente VSphere." lightbox="media/traffic-manager/nsx-t-vm-details.png":::    

4. Selecione a máquina virtual e clique em **ações > editar configurações** para verificar a conexão com o segmento NSX-T.

## <a name="create-your-traffic-manager-profile"></a>Criar seu perfil do Gerenciador de tráfego

1. Faça logon no [Portal do Azure](https://rc.portal.azure.com/#home). Em **Serviços do Azure > rede**, selecione **perfis do Gerenciador de tráfego**.

2. Selecione **+ Adicionar** para criar um novo perfil do Gerenciador de tráfego.
 
3. Forneça o nome do perfil, o método de roteamento (usaremos ponderado nesse cenário; consulte [métodos de roteamento do Gerenciador de tráfego](../traffic-manager/traffic-manager-routing-methods.md)), assinatura e grupo de recursos e selecione **criar**.

## <a name="add-external-endpoints-into-the-traffic-manager-profile"></a>Adicionar pontos de extremidade externos ao perfil do Gerenciador de tráfego

1. Selecione o perfil do Gerenciador de tráfego no painel de resultados da pesquisa, selecione **pontos de extremidade** e **+ Adicionar**.

2. Insira os detalhes necessários: tipo, nome, FQDN (nome de domínio totalmente qualificado) ou IP e peso (nesse cenário, estamos atribuindo um peso de 1 a cada ponto de extremidade). Selecione **Adicionar**. Isso cria o ponto de extremidade externo. O status do monitor deve estar **online**. Repita as mesmas etapas para criar dois pontos de extremidade externos, um em uma região diferente e o outro local. Depois de criado, todos os três serão exibidos no perfil do Gerenciador de tráfego e o status de todos os três deve estar **online**.

3. Selecione **Visão geral**. Copie a URL sob o **nome DNS**.

   :::image type="content" source="media/traffic-manager/traffic-manager-endpoints.png" alt-text="Captura de tela mostrando uma visão geral do ponto de extremidade do Traffic Manager com nome DNS realçado" lightbox="media/traffic-manager/traffic-manager-endpoints.png"::: 

4. Cole a URL do nome DNS em um navegador. A captura de tela a seguir mostra o tráfego direcionado para a região de Europa Ocidental.

   :::image type="content" source="media/traffic-manager/traffic-to-west-europe.png" alt-text="Captura de tela da janela do navegador mostrando o tráfego roteado para Europa Ocidental."::: 

5. Atualize seu navegador. A captura de tela a seguir mostra o tráfego agora direcionando para outro conjunto de membros do pool de back-end na região oeste dos EUA.

   :::image type="content" source="media/traffic-manager/traffic-to-west-us.png" alt-text="Captura de tela da janela do navegador mostrando o tráfego roteado para o oeste dos EUA."::: 

6. Atualize o navegador novamente. A captura de tela a seguir mostra o tráfego agora direcionando para o conjunto final de membros do pool de back-end no local.

   :::image type="content" source="media/traffic-manager/traffic-to-on-premises.png" alt-text="Captura de tela da janela do navegador mostrando o tráfego roteado para o local.":::

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre:

- [Usando o gateway de Aplicativo Azure na solução VMware do Azure (AVS)](protect-azure-vmware-solution-with-application-gateway.md)
- [Métodos de roteamento do Gerenciador de Tráfego](../traffic-manager/traffic-manager-routing-methods.md)
- [Combinando serviços de balanceamento de carga no Azure](../traffic-manager/traffic-manager-load-balancing-azure.md)
- [Medindo o desempenho do Gerenciador de tráfego](../traffic-manager/traffic-manager-performance-considerations.md)
