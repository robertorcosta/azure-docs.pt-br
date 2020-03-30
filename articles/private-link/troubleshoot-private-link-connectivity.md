---
title: Solucionar problemas de conectividade do Link Privado do Azure
description: Orientação passo a passo para diagnosticar conectividade de links privados
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
ms.openlocfilehash: 1e5253d617c87d5869cebc817da6d265ebfdfa7e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77539460"
---
# <a name="troubleshoot-azure-private-link-connectivity-problems"></a>Solucionar problemas de conectividade do Link Privado do Azure

Este artigo fornece orientações passo a passo para validar e diagnosticar conectividade para sua configuração do Azure Private Link.

Com o Azure Private Link, você pode acessar a plataforma Azure como serviço (PaaS), como Azure Storage, Azure Cosmos DB e Azure SQL Database, e o Azure hospedou serviços de clientes ou parceiros em um ponto final privado em sua rede virtual. O tráfego entre sua rede virtual e o serviço atravessa a rede backbone da Microsoft, o que elimina a exposição da internet pública. Você também pode criar seu próprio serviço de link privado em sua rede virtual e entregá-lo em particular para seus clientes.

Você pode habilitar seu serviço que executa atrás do nível Padrão do Azure Load Balancer para acesso ao Private Link. Os consumidores do seu serviço podem criar um ponto final privado dentro de sua rede virtual e mapeá-lo para este serviço para acessá-lo privadamente.

Aqui estão os cenários de conectividade disponíveis com o Private Link:

- Rede virtual da mesma região
- Redes virtuais regionalmente peered
- Redes virtuais de sionizadas globalmente
- Cliente no local sobre circuitos VPN ou Azure ExpressRoute

## <a name="deployment-troubleshooting"></a>Solução de problemas de implantação

Revise as informações sobre [desativar políticas de rede no serviço de link privado](https://docs.microsoft.com/azure/private-link/disable-private-link-service-network-policy) para resolver problemas em casos em que você não pode selecionar o endereço IP de origem da sub-rede de sua escolha para o serviço de link privado.

Certifique-se de que a configuração **privateLinkServiceNetworkPolicies** está desativada para a sub-rede da qual você está selecionando o endereço IP de origem.

## <a name="diagnose-connectivity-problems"></a>Diagnosticar problemas de conectividade

Se você tiver problemas de conectividade com sua configuração de link privado, revise essas etapas para ter certeza de que todas as configurações usuais são como esperado.

1. Revise a configuração do Private Link navegando pelo recurso.

    a. Vá para **Private Link Center**.

      ![Centro de Link Privado](./media/private-link-tsg/private-link-center.png)

    b. No painel esquerdo, selecione **Serviços de link privados**.

      ![Serviços de link privado](./media/private-link-tsg/private-link-service.png)

    c. Filtre e selecione o serviço de link privado que deseja diagnosticar.

    d. Revise as conexões de ponto final privado.
     - Certifique-se de que o ponto final privado do qual você está buscando conectividade esteja listado com um estado de conexão **aprovado.**
     - Se o estado estiver **pendente,** selecione-o e aprove-o.

       ![Conexões de ponto final privado](./media/private-link-tsg/pls-private-endpoint-connections.png)

     - Vá para o ponto final privado do que você está se conectando selecionando o nome. Certifique-se de que o status da conexão é **mostrado**como aprovado .

       ![Visão geral da conexão de ponto final privado](./media/private-link-tsg/pls-private-endpoint-overview.png)

     - Depois que ambos os lados forem aprovados, tente a conectividade novamente.

    e. Revise **alias** na guia **Visão geral** e **iD de recursos** na guia **Propriedades.**
     - Certifique-se de que as informações **de Alias** e **ID de recursos** correspondem ao **Alias** e **ao Resource ID** que você está usando para criar um ponto final privado para este serviço.

       ![Verificar informações de alias](./media/private-link-tsg/pls-overview-pane-alias.png)

       ![Verifique informações de id de recursos](./media/private-link-tsg/pls-properties-pane-resourceid.png)

    f. Revisar **informações de visibilidade** na guia Visão **Geral.**
     - Certifique-se de que sua assinatura está no escopo **visibilidade.**

       ![Verificar informações de visibilidade](./media/private-link-tsg/pls-overview-pane-visibility.png)

    g. Revisar as informações **do balanceador de** carga na guia **Visão geral.**
     - Você pode ir ao balanceador de carga selecionando o link balanceador de carga.

       ![Verifique as informações do balanceador de carga](./media/private-link-tsg/pls-overview-pane-ilb.png)

     - Certifique-se de que as configurações do balanceador de carga estão configuradas de acordo com suas expectativas.
       - Revise **a configuração de IP do Frontend**.
       - Revisar **pools backend**.
       - Revisar **regras de balanceamento de carga**.

       ![Verifique as propriedades do balanceador de carga](./media/private-link-tsg/pls-ilb-properties.png)

     - Certifique-se de que o balanceador de carga está funcionando de acordo com as configurações anteriores.
       - Selecione uma VM em qualquer sub-rede que não seja a sub-rede onde o pool back-end do balanceador de carga está disponível.
       - Tente acessar o front-end balanceador de carga da VM anterior.
       - Se a conexão chegar ao pool de back-end de acordo com as regras de balanceamento de carga, o balanceador de carga está operacional.
       - Você também pode rever a métrica do balanceador de carga através do Azure Monitor para ver se os dados estão fluindo através do balanceador de carga.

1. Use [o Monitor Do Azure](https://docs.microsoft.com/azure/azure-monitor/overview) para ver se os dados estão fluindo.

    a. No recurso de serviço de link privado, selecione **Métricas**.
     - Selecione **Bytes In** ou **Bytes Out**.
     - Veja se os dados estão fluindo quando você tenta se conectar ao serviço de link privado. Espere um atraso de aproximadamente 10 minutos.

       ![Verifique métricas de serviço de link privado](./media/private-link-tsg/pls-metrics.png)

1. Entre em contato com a equipe [de suporte do Azure](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) se seu problema ainda não foi resolvido e um problema de conectividade ainda existe.

## <a name="next-steps"></a>Próximas etapas

 * [Crie um serviço de link privado (CLI)](https://docs.microsoft.com/azure/private-link/create-private-link-service-cli)
 * [Guia de solução de problemas do Azure Private Endpoint](troubleshoot-private-endpoint-connectivity.md)
