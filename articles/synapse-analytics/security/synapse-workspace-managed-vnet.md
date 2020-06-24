---
title: Rede virtual gerenciada no Azure Synapse Analytics
description: Um artigo que explica a Rede virtual gerenciada no Azure Synapse Analytics
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: security
ms.date: 04/15/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: b46ce6f6164479853bc762cb1ca45d67f7f80930
ms.sourcegitcommit: 3988965cc52a30fc5fed0794a89db15212ab23d7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/22/2020
ms.locfileid: "85194358"
---
# <a name="azure-synapse-analytics-managed-virtual-network-preview"></a>Rede virtual gerenciada do Azure Synapse Analytics (versão prévia)

Este artigo explicará a Rede Virtual Gerenciada no Azure Synapse Analytics.

## <a name="managed-workspace-vnet"></a>VNet de workspace gerenciada

Ao criar seu workspace do Azure Synapse, você pode optar por associá-lo a uma VNet. A VNet associada ao seu workspace é gerenciada pelo Azure Synapse. Essa VNet é chamada *VNet de workspace gerenciada*.

A VNet de workspace gerenciada oferece vantagens de quatro maneiras:

- Com uma VNet de workspace gerenciada, você pode deixar a carga do gerenciamento da VNet para o Azure Synapse.
- Você não precisa configurar regras de NSG de entrada em suas VNets para permitir que o tráfego de gerenciamento do Azure Synapse entre em sua VNet. A configuração incorreta dessas regras de NSG causa a interrupção do serviço para os clientes.
- Você não precisa criar uma sub-rede para seus clusters Spark com base na carga de pico.
- A VNet de workspace gerenciada juntamente com Pontos de extremidade privados gerenciados protege contra exfiltração dos dados. Você só pode criar Pontos de extremidade privados gerenciados em um workspace que tenha uma VNet de workspace gerenciada associada a ele.

A criação de um workspace com uma VNet de workspace gerenciada associada a ele garante que seu workspace seja isolado de outros na rede. O Azure Synapse oferece vários recursos de análise em um workspace: Integração de dados, Apache Spark, pool de SQL e SQL sob demanda.

Se o workspace tiver uma VNet de workspace gerenciada, a Integração de dados e os recursos do Spark serão implantados nele. Uma VNet de workspace gerenciada também fornece isolamento de nível de usuário para atividades do Spark porque cada cluster Spark fica em uma sub-rede própria.

O pool de SQL e o SQL sob demanda são recursos multilocatários e, portanto, residem fora da VNet de workspace gerenciada. Comunicação entre workspaces com o pool de SQL e o SQL sob demanda usam links privados do Azure. Esses links privados são criados automaticamente para você quando você cria um workspace com uma VNet de workspace gerenciada associada a ele.

>[!IMPORTANT]
>Você não pode alterar essa configuração do workspace depois que ele é criado. Por exemplo, você não pode reconfigurar um workspace que não tenha uma VNet de workspace gerenciada associada a ela e associar uma VNet a ele. Da mesma forma, você não pode reconfigurar um workspace com uma VNet de workspace gerenciada associada a ele e desassociar a VNet dele.

## <a name="create-an-azure-synapse-workspace-with-a-managed-workspace-vnet"></a>Criar um workspace do Azure Synapse com uma VNet de workspace gerenciada

Registre o provedor de recursos de rede, caso ainda não tenha feito isso. O registro de um provedor de recursos configura sua assinatura para trabalhar com o provedor de recursos. Escolha *Microsoft.Network* na lista de provedores de recursos ao se [registrar](https://docs.microsoft.com/azure/azure-resource-manager/management/resource-providers-and-types).

Para criar um workspace do Azure Synapse que tenha uma VNet de workspace gerenciada associada a ele, selecione a guia **Segurança + rede** no portal do Azure e marque a caixa de seleção **Habilitar rede virtual gerenciada**.

Se você deixar a caixa de seleção desmarcada, o workspace não terá uma VNet associada a ela.

>[!IMPORTANT]
>Você só pode usar links privados em um workspace que tenha uma VNet de workspace gerenciada.

![Habilitar a VNet de workspace gerenciada](./media/synapse-workspace-managed-vnet/enable-managed-vnet-1.png)

>[!NOTE]
>Todo o tráfego de saída da VNet de workspace gerenciada, exceto por meio de pontos de extremidade privados gerenciados, será bloqueado no futuro. É recomendável que você crie pontos de extremidade privados gerenciados para se conectar a todas as suas fontes de dados do Azure externas ao workspace. 

Você pode verificar se o workspace do Azure Synapse está associado a uma VNet de workspace gerenciada selecionando **Visão geral** no portal do Azure.

![Visão geral do workspace no portal do Azure](./media/synapse-workspace-managed-vnet/enable-managed-vnet-2.png)

## <a name="next-steps"></a>Próximas etapas

Criar um [Workspace do Azure Synapse](../quickstart-create-workspace.md)

Saiba mais sobre [Pontos de extremidade privados gerenciados](./synapse-workspace-managed-private-endpoints.md)

[Criar Pontos de extremidade privados gerenciados para suas fontes de dados](./how-to-create-managed-private-endpoints.md)
