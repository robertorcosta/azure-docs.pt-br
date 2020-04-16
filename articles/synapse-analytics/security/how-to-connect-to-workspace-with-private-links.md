---
title: Conecte-se a um espaço de trabalho azure Synapse usando links privados
description: Este artigo ensinará como se conectar ao seu espaço de trabalho Azure Synapse usando links privados
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: how-to
ms.date: 04/15/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: 5a00fc44021278a8b910cf454b43b0bae2c3a1f9
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81432184"
---
# <a name="connect-to-your-azure-synapse-workspace-using-private-links-preview"></a>Conecte-se ao seu espaço de trabalho Azure Synapse usando links privados (visualização)

Este artigo ensinará como criar um ponto final privado para o seu espaço de trabalho Azure Synapse. Consulte [links privados e pontos finais privados](https://docs.microsoft.com/azure/private-link/) para saber mais.

## <a name="step-1-open-your-azure-synapse-workspace-in-azure-portal"></a>Passo 1: Abra seu espaço de trabalho Azure Synapse no portal Azure

Selecione **Conexão ponto final privado** em **Segurança** e, em seguida, selecione + ponto **final privado**.
![Abra o espaço de trabalho do Azure Synapse no portal Azure](./media/how-to-connect-to-workspace-with-private-links/private-endpoint-1.png)

## <a name="step-2-select-your-subscription-and-region-details"></a>Passo 2: Selecione os detalhes da sua assinatura e região

Na guia **Básico** na **janela Criar um ponto final privado,** escolha seu **Grupo de Assinaturas** e **Recursos**. Dê um **nome** ao ponto final privado que você deseja criar. Selecione a **Região** onde deseja que o ponto final privado seja criado.

Os pontos finais privados são criados em uma sub-rede. A assinatura, o grupo de recursos e a região selecionada filtram as sub-redes de ponto final privado. Selecione **A seguir: recurso >** quando feito.
![Selecione os detalhes da assinatura e da região](./media/how-to-connect-to-workspace-with-private-links/private-endpoint-2.png)

## <a name="step-3-select-your-azure-synapse-workspace-details"></a>Passo 3: Selecione os detalhes do espaço de trabalho do Azure Synapse

Selecione **Conecte-se a um recurso do Azure no meu diretório** na guia **Recurso.** Selecione a **assinatura** que contém o espaço de trabalho do Azure Synapse. O **tipo de recurso** para criar pontos finais privados para um espaço de trabalho do Azure Synapse é *Microsoft.Synapse/workspaces*.

Selecione seu espaço de trabalho Azure Synapse como **recurso**. Cada espaço de trabalho do Azure Synapse tem três **subrecursos de destino** para os que você pode criar um ponto final privado: Sql, SqlOnDemand e Dev.

Selecione **Next: Configuração>** para avançar para a próxima parte da configuração.
![Selecione os detalhes da assinatura e da região](./media/how-to-connect-to-workspace-with-private-links/private-endpoint-3.png)

Na guia **Configuração,** selecione a **rede Virtual** e a **Sub-rede** na qual o ponto final privado deve ser criado. Você também precisa criar um registro DNS que mapeie para o ponto final privado.

Selecione **Sim** para **Integrar com a zona DNS privada** para integrar seu ponto final privado com uma região de DNS privada. Se você não tiver uma zona de DNS privada associada ao seu VNet, então uma nova zona de DNS privada será criada. Selecione **'Revisar + criar** quando feito'.

![Selecione os detalhes da assinatura e da região](./media/how-to-connect-to-workspace-with-private-links/private-endpoint-4.png)

Quando a implantação estiver concluída, abra seu espaço de trabalho Do Azure Synapse no portal Azure e selecione **conexões de ponto final privado**. O novo ponto final privado e o nome de conexão de ponto final privado associados ao ponto final privado são mostrados.

![Selecione os detalhes da assinatura e da região](./media/how-to-connect-to-workspace-with-private-links/private-endpoint-5.png)

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre [o VNet do espaço de trabalho gerenciado](./synapse-workspace-managed-vnet.md)

Saiba mais sobre [pontos finais privados gerenciados](./synapse-workspace-managed-private-endpoints.md)

[Criar pontos finais privados gerenciados para suas fontes de dados](./how-to-create-managed-private-endpoints.md)
