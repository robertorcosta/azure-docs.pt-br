---
title: Conectar-se a um espaço de trabalho Synapse do Azure usando links privados
description: Este artigo ensinará como se conectar ao seu espaço de trabalho Synapse do Azure usando links privados
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: how-to
ms.date: 04/15/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: 5a00fc44021278a8b910cf454b43b0bae2c3a1f9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81432184"
---
# <a name="connect-to-your-azure-synapse-workspace-using-private-links-preview"></a>Conectar-se ao espaço de trabalho Synapse do Azure usando links privados (visualização)

Este artigo ensinará como criar um ponto de extremidade privado para seu espaço de trabalho Synapse do Azure. Consulte [links privados e pontos de extremidade privados](https://docs.microsoft.com/azure/private-link/) para saber mais.

## <a name="step-1-open-your-azure-synapse-workspace-in-azure-portal"></a>Etapa 1: abrir seu espaço de trabalho Synapse do Azure no portal do Azure

Selecione **conexão de ponto de extremidade privada** em **segurança** e, em seguida, selecione **+ ponto de extremidade privado**.
![Abrir o espaço de trabalho Synapse do Azure no portal do Azure](./media/how-to-connect-to-workspace-with-private-links/private-endpoint-1.png)

## <a name="step-2-select-your-subscription-and-region-details"></a>Etapa 2: selecione os detalhes da sua assinatura e da região

Na guia **noções básicas** da janela **criar um ponto de extremidade privado** , escolha sua **assinatura** e **grupo de recursos**. Dê um **nome** ao ponto de extremidade privado que você deseja criar. Selecione a **região** onde você deseja que o ponto de extremidade particular seja criado.

Pontos de extremidade privados são criados em uma sub-rede. A assinatura, o grupo de recursos e a região selecionados filtram as sub-redes do ponto de extremidade privado. Selecione **Avançar: recurso >** quando concluído.
![Selecionar detalhes da assinatura e da região](./media/how-to-connect-to-workspace-with-private-links/private-endpoint-2.png)

## <a name="step-3-select-your-azure-synapse-workspace-details"></a>Etapa 3: selecione os detalhes do espaço de trabalho Synapse do Azure

Selecione **conectar-se a um recurso do Azure em meu diretório** na guia **recurso** . Selecione a **assinatura** que contém o espaço de trabalho Synapse do Azure. O **tipo de recurso** para criar pontos de extremidade privados para um espaço de trabalho Synapse do Azure é *Microsoft. Synapse/Workspaces*.

Selecione o espaço de trabalho Synapse do Azure como o **recurso**. Cada espaço de trabalho Synapse do Azure tem três **subrecurso de destino** que você pode criar um ponto de extremidade privado para: SQL, OnDemand e dev.

Selecione **Avançar: configuração>** para avançar para a próxima parte da instalação.
![Selecionar detalhes da assinatura e da região](./media/how-to-connect-to-workspace-with-private-links/private-endpoint-3.png)

Na guia **configuração** , selecione a **rede virtual** e a **sub-rede** na qual o ponto de extremidade privado deve ser criado. Você também precisa criar um registro DNS que é mapeado para o ponto de extremidade privado.

Selecione **Sim** para **integrar com a zona DNS privada** para integrar seu ponto de extremidade privado a uma zona DNS privada. Se você não tiver uma zona DNS privada associada à sua VNet, uma nova zona DNS privada será criada. Selecione **revisar + criar** quando terminar.

![Selecionar detalhes da assinatura e da região](./media/how-to-connect-to-workspace-with-private-links/private-endpoint-4.png)

Quando a implantação for concluída, abra o espaço de trabalho Synapse do Azure em portal do Azure e selecione **conexões de ponto de extremidade privado**. O novo ponto de extremidade privado e o nome de conexão de ponto de extremidade privado associado ao ponto de extremidade privado são mostrados.

![Selecionar detalhes da assinatura e da região](./media/how-to-connect-to-workspace-with-private-links/private-endpoint-5.png)

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre a [VNet do espaço de trabalho gerenciado](./synapse-workspace-managed-vnet.md)

Saiba mais sobre [Pontos de extremidade privados gerenciados](./synapse-workspace-managed-private-endpoints.md)

[Criar Pontos de extremidade privados gerenciados para suas fontes de dados](./how-to-create-managed-private-endpoints.md)
