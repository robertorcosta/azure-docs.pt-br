---
title: Solucionar problemas de conectividade de link privado do Azure
description: Orientações passo a passo para diagnosticar conectividade de link privado
services: private-link
documentationcenter: na
author: rdhillon
manager: narayan
editor: ''
ms.service: private-link
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/31/2020
ms.author: rdhillon
ms.openlocfilehash: 667fa1c85c63ffb87e49c4bf99112f57d0c85a72
ms.sourcegitcommit: f0f73c51441aeb04a5c21a6e3205b7f520f8b0e1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77031914"
---
# <a name="troubleshoot-private-link-service-connectivity-problems"></a>Solucionar problemas de conectividade do serviço de vínculo privado

Este guia fornece orientações passo a passo para validar e diagnosticar a conectividade para a instalação do serviço de vínculo privado. 

O link privado do Azure permite que você acesse os serviços de PaaS do Azure (por exemplo, armazenamento do Azure, Azure Cosmos DB e banco de dados SQL) e os serviços hospedados de cliente/parceiro do Azure por meio de um ponto de extremidade privado em sua rede virtual. O tráfego entre a rede virtual e o serviço percorre a rede de backbone da Microsoft, eliminando a exposição da Internet pública. Você também pode criar seu próprio serviço de vínculo privado em sua rede virtual (VNet) e fornecê-lo de forma privada aos seus clientes. 

Você pode habilitar seu serviço em execução por trás do Azure Standard Load Balancer para acesso de link privado. Os consumidores do seu serviço podem criar um ponto de extremidade privado dentro de sua rede virtual e mapeá-lo para esse serviço para acessá-lo de forma privada.

Aqui estão os cenários de conectividade disponíveis com o serviço de link privado
- rede virtual da mesma região 
- redes virtuais emparelhadas de região
- redes virtuais emparelhadas globalmente
- cliente local por meio de circuitos de VPN ou de rota expressa

## <a name="deployment-troubleshooting"></a>Solução de problemas de implantação

Examine as informações sobre como [desabilitar as políticas de rede no serviço de vínculo privado](https://docs.microsoft.com/azure/private-link/disable-private-link-service-network-policy) para casos de solução de problemas em que não é possível selecionar o endereço IP de origem da sub-rede de sua escolha para seu serviço de link privado.

Verifique se a configuração **privateLinkServiceNetworkPolicies** está desabilitada para a sub-rede da qual você está selecionando o endereço IP de origem.

## <a name="diagnosing-connectivity-problems"></a>Diagnosticando problemas de conectividade

Se você estiver tendo problemas de conectividade com a configuração de link privado, vá para as etapas listadas abaixo para verificar se todas as configurações usuais estão conforme o esperado.

1. Examinar a configuração do serviço de vínculo privado navegando no recurso 

    a) ir para o **centro de links privado**

      ![Centro de links privado](./media/private-link-tsg/private-link-center.png)

    b) selecione **serviços de vínculo privado** no painel de navegação à esquerda

      ![Serviços de vínculo privado](./media/private-link-tsg/private-link-service.png)

    c) filtrar e selecionar o serviço de link privado que você deseja diagnosticar

    d) revisar as conexões de ponto de extremidade privado
     - Verifique se o ponto de extremidade privado do qual você está procurando conectividade está listado com o estado de conexão **aprovada** . 
     - Se **pendente**, selecione-o e aprove. 

       ![Conexões de ponto de extremidade privado](./media/private-link-tsg/pls-private-endpoint-connections.png)

     - Navegue até o ponto de extremidade privado do qual você está se conectando, clicando no nome. Verifique se o status da conexão mostra **aprovado**.

       ![Visão geral da conexão de ponto de extremidade particular](./media/private-link-tsg/pls-private-endpoint-overview.png)

     - Depois que ambos os lados forem aprovados, tente a conectividade novamente.

    e) revisar o **alias** da guia Visão geral e da **ID do recurso** na guia Propriedades 
     - Verifique se a **ID do alias/recurso** corresponde à **ID do alias/recurso** que você está usando para criar um ponto de extremidade privado para esse serviço. 

       ![Verificar alias](./media/private-link-tsg/pls-overview-pane-alias.png)

       ![Verificar ID do recurso](./media/private-link-tsg/pls-properties-pane-resourceid.png)

    f) examinar as informações de visibilidade (seção de visão geral)
     - Certifique-se de que sua assinatura esteja sob o escopo de **visibilidade**

       ![Verificar visibilidade](./media/private-link-tsg/pls-overview-pane-visibility.png)

    g) examinar informações de Load Balancer (visão geral)
     - Você pode navegar até o balanceador de carga clicando no link do balanceador de carga

       ![Verificar Load Balancer](./media/private-link-tsg/pls-overview-pane-ilb.png)

     - Verifique se as configurações de Load Balancer estão definidas de acordo com suas expectativas
       - Examinar a configuração de IP de front-end
       - Examinar pools de back-end
       - Examinar regras de balanceamento de carga

       ![Verificar Load Balancer Propriedades](./media/private-link-tsg/pls-ilb-properties.png)

     - Verifique se Load Balancer está funcionando de acordo com as configurações acima
       - Selecione uma VM em qualquer sub-rede que não seja a sub-rede em que Load Balancer pool de back-end está disponível
       - Tente acessar o front-end do balanceador de carga da VM acima
       - Se a conexão fizer com o pool de back-end de acordo com as regras de balanceamento de carga, o balanceador de carga estará operacional
       - Você também pode examinar a métrica de Load Balancer por meio de Azure Monitor para ver se os dados estão fluindo pelo balanceador de carga

2. Usar [**Azure monitor**](https://docs.microsoft.com/azure/azure-monitor/overview) para examinar os dados estão fluindo

    a) no recurso de serviço de vínculo privado, selecione **métricas**
     - Selecionar **bytes-in** ou **bytes-out**
     - Os dados de revisão estão fluindo ao tentar se conectar ao serviço de vínculo privado. Espere um atraso de aproximadamente 10 minutos.

       ![Verificar métricas do serviço de vínculo privado](./media/private-link-tsg/pls-metrics.png)

3. Contate a equipe de [suporte do Azure](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) se o problema ainda não estiver resolvido e se o problema de conectividade ainda existir. 

## <a name="next-steps"></a>{1&gt;{2&gt;Próximas etapas&lt;2}&lt;1}

 * [Criar um serviço de vínculo privado (CLI)](https://docs.microsoft.com/azure/private-link/create-private-link-service-cli)

 * [Guia de solução de problemas de ponto de extremidade privado](https://docs.microsoft.com/azure/private-link/private-endpoint-connectivity-troubleshooting)
