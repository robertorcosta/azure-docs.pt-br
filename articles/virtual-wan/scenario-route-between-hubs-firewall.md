---
title: 'Cenário: Firewall do Azure – roteamento Interhub'
titleSuffix: Azure Virtual WAN
description: Cenários do roteamento de roteamento entre vários hubs que têm o Firewall do Azure
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 06/29/2020
ms.author: cherylmc
ms.openlocfilehash: 1057dc28682c90374b2f8e8e0297f3d769c08bd6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85566987"
---
# <a name="scenario-azure-firewall---interhub-preview"></a>Cenário: Firewall do Azure – Interhub (visualização)

Ao trabalhar com o roteamento de Hub virtual de WAN virtual, há alguns cenários disponíveis. Nesse cenário, o objetivo é rotear entre vários hubs que contenham o Firewall do Azure. Para obter informações sobre roteamento de Hub virtual, consulte [sobre roteamento de Hub virtual](about-virtual-hub-routing.md).

## <a name="scenario-architecture"></a><a name="architecture"></a>Arquitetura de cenário

Neste cenário, presumimos a seguinte configuração:

* Você tem vários hubs com o Firewall do Azure em cada Hub.
* Você está usando o Secure virtual Hub.
* As políticas apropriadas para o tráfego privado (VNet), Internet e tráfego de ramificação foram configuradas.
* V2I (VNet para a Internet), rede virtual para ramificação (V2B), ramificação para VNet (B2V), todos passam pelo firewall do Azure em cada Hub.

Considerações adicionais:

* Para um cenário entre hubs com o Firewall do Azure, a suposição é que o spoke VNets não se associa a tabelas de rotas separadas. (por exemplo, **vnet 1** associado à **tabela de rotas a**e **VNET 2** associada à **tabela de rotas B**). Todos os VNets associados à mesma tabela de rotas em que as rotas para o Firewall do Azure residem.
* No momento, a proteção por meio do firewall do Azure está limitada a somente **tráfego de** **rede e de VNet < >** . Não há suporte para o fluxo de Branch para Branch por meio do firewall do Azure no momento.

**Figura 1**

:::image type="content" source="./media/routing-scenarios/between-hubs-firewall/architecture.png" alt-text="arquitectura":::

## <a name="scenario-workflow"></a><a name="workflow"></a>Fluxo de trabalho do cenário

Para configurar esse cenário, leve em consideração as seguintes etapas:

### <a name="step-1"></a><a name="step-1"></a>Etapa 1

Supondo que você já tenha protegido as conexões por meio do Gerenciador de firewall do Azure, a primeira etapa é garantir que todas as conexões de Branch e VNet propaguem **None**. Isso é necessário para garantir que o tráfego esteja sendo definido por meio do firewall do Azure.

1. Selecione o Hub e edite a tabela de rotas **None** .
1. Atualize a **propagação** para selecionar todas as ramificações e todas as VNets.

### <a name="step-2"></a><a name="step-2"></a>Etapa 2

Configure uma tabela de rotas personalizada por Hub.

1. Para o **Hub 1**, crie a tabela de rotas **RT_Hub1**.

    * Se você usou o Gerenciador de firewall do Azure, ele criará automaticamente uma rota para 0.0.0.0/0 com o próximo salto **AZFW1** na tabela de rotas padrão do Hub. Modificaremos essa configuração na etapa 3. Para **RT_Hub1**, crie uma entrada para 0.0.0.0/0 explicitamente com o próximo salto **AZFW1**. Essa configuração ativará V2V, B2V e V2I por meio de AZFW1.
    * Como você deseja que as ramificações do **Hub 2** e VNets sejam atingidas por meio do **AZFW2** do **Hub 1** (em vez de via AZFW1), você precisa adicionar outras duas rotas agregadas para ramificações do **hub 2** (10.5.0.0/16->AZFW2) e VNets (10.2.0.0/16->AZFW2).

1. Para o **Hub 2**, crie a tabela de rotas **RT_Hub2**.

    * Se você usou o Gerenciador de firewall do Azure, ele criará automaticamente uma rota para 0.0.0.0/0 com o próximo salto **AZFW2** na tabela de rotas padrão do Hub. Modificaremos essa configuração na etapa 3. Para **RT_Hub2**, crie uma entrada para 0.0.0.0/0 explicitamente com o próximo salto **AZFW2**. Essa configuração ativará V2V, B2V e V2I por meio de **AZFW2**.
    * Como você deseja que o tráfego entre hubs seja protegido pelo **AZFW2**do Hub 2, não é necessário adicionar explicitamente uma etapa semelhante ao segundo marcador na etapa anterior do hub 1.

### <a name="step-3"></a><a name="step-3"></a>Etapa 3

Modifique a **tabela de rotas padrão** em cada um dos hubs para adicionar uma rota estática para os fluxos de B2V ( **Branch para VNet** ) e B2I ( **Branch para Internet** ) por meio do firewall do Azure. No momento, não há suporte para Branch para Branch por meio do firewall do Azure.

1. Para a **tabela de rotas padrão do hub 1**:

    * Branch **para ramificações de Hub 2 via AZFW2**: 10.5.0.0/16->AZFW2. Essa configuração configura uma rota para o Firewall do Hub 2.
    * **Branch para o Hub 2 VNets por meio de AZFW2**: 10.2.0.0/16->AZFW2. Essa configuração configura uma rota para o Firewall do Hub 2.
    * **Branch para ramificação (local)/Branch para VNet (local)/Branch para Internet**: 0.0.0.0/0->AZFW1.

2. Para a **tabela de rotas padrão do Hub 2**:

    * Branch para Branch (local e remoto)/Branch para VNet (local e remoto)/Branch para Internet: 0.0.0.0/0->AZFW2.

Isso resultará nas alterações de configuração de roteamento, como mostra a figura abaixo

**Figura 2**

:::image type="content" source="./media/routing-scenarios/between-hubs-firewall/workflow.png" alt-text="workflow":::

## <a name="next-steps"></a>Próximas etapas

* Para obter mais informações sobre a WAN Virtual, veja as [Perguntas frequentes](virtual-wan-faq.md).
* Para obter mais informações sobre roteamento de Hub virtual, consulte [sobre roteamento de Hub virtual](about-virtual-hub-routing.md).
* Para obter mais informações sobre como configurar o roteamento de Hub virtual, consulte [como configurar o roteamento de Hub virtual](how-to-virtual-hub-routing.md).
