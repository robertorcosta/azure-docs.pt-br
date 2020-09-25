---
title: Conectar-se a um espaço de trabalho do Synapse usando links privados
description: Este artigo ensina como conectar-se ao workspace do Azure Synapse usando links privados
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: security
ms.date: 04/15/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: ea791e4fc1031c0a5c041119c409f8623ce7aee9
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91260369"
---
# <a name="connect-to-your-azure-synapse-workspace-using-private-links-preview"></a>Conexão ao workspace do Azure Synapse usando links privados (versão prévia)

Este artigo ensina como criar um ponto de extremidade privado para o workspace do Azure Synapse. Veja [links privados e pontos de extremidade privados](https://docs.microsoft.com/azure/private-link/) para saber mais.

## <a name="step-1-register-network-resource-provider"></a>Etapa 1: Registrar o provedor de recursos de rede

Se você ainda não tiver feito isso, registre o provedor de recursos de rede. O registro de um provedor de recursos configura sua assinatura para trabalhar com o provedor de recursos. Escolha *Microsoft.Network* na lista de provedores de recursos ao se [registrar](https://docs.microsoft.com/azure/azure-resource-manager/management/resource-providers-and-types). Se o provedor de recursos de rede já estiver registrado, vá para a Etapa 2.


## <a name="step-2-open-your-azure-synapse-workspace-in-azure-portal"></a>Etapa 2: Abra o workspace do Azure Synapse no portal do Azure

Selecione **Conexão de ponto de extremidade privado** em **Segurança** e, em seguida, selecione **+ Ponto de extremidade privado**.
![Abrir o workspace do Azure Synapse no portal do Azure](./media/how-to-connect-to-workspace-with-private-links/private-endpoint-1.png)

## <a name="step-3-select-your-subscription-and-region-details"></a>Etapa 3: Selecione os detalhes da assinatura e da região

Na guia **Noções básicas** da janela **Criar um ponto de extremidade privado**, escolha a **Assinatura** e o **Grupo de recursos**. Dê um **Nome** ao ponto de extremidade privado que você deseja criar. Selecione a **Região** onde você deseja criar o ponto de extremidade privado.

Os pontos de extremidade privados são criados em uma sub-rede. A assinatura, o grupo de recursos e a região selecionados filtram as sub-redes do ponto de extremidade privado. Selecione **Avançar: Recurso >** quando concluído.
![Selecionar detalhes da assinatura e da região 1](./media/how-to-connect-to-workspace-with-private-links/private-endpoint-2.png)

## <a name="step-4-select-your-azure-synapse-workspace-details"></a>Etapa 4: Selecione os detalhes do workspace do Azure Synapse

Selecione **Conectar-se a um recurso do Azure em meu diretório** na guia **Recurso**. Selecione a **Assinatura** que contém o workspace do Azure Synapse. O **Tipo de recurso** para criar pontos de extremidade privados para um workspace do Azure Synapse é *Microsoft.Synapse/workspaces*.

Selecione o workspace do Azure Synapse como o **Recurso**. Cada workspace do Azure Synapse tem três **Subrecursos de destino** para os quais você pode criar um ponto de extremidade privado: Sql, SqlOnDemand e Dev.

Selecione **Avançar: Configuração** para avançar para a próxima parte da configuração.
![Selecionar detalhes da assinatura e da região 2](./media/how-to-connect-to-workspace-with-private-links/private-endpoint-3.png)

Na guia **Configuração**, selecione a **Rede virtual** e a **Sub-rede** nas quais o ponto de extremidade privado deve ser criado. Também é necessário criar um registro DNS que é mapeado para o ponto de extremidade privado.

Selecione **Sim** para **Integrar com a zona DNS privada** para integrar seu ponto de extremidade privado a uma zona DNS privada. Se você não tiver uma zona DNS privada associada à sua Rede Virtual do Microsoft Azure, uma nova zona DNS privada será criada. Selecione **Revisar + Criar** quando concluído.

![Selecionar detalhes da assinatura e da região 3](./media/how-to-connect-to-workspace-with-private-links/private-endpoint-4.png)

Quando a implantação for concluída, abra o workspace do Azure Synapse no portal do Azure e selecione **Conexões do ponto de extremidade privado**. O novo ponto de extremidade privado e o nome da conexão do ponto de extremidade privado associado ao ponto de extremidade privado são exibidos.

![Selecionar detalhes da assinatura e da região 4](./media/how-to-connect-to-workspace-with-private-links/private-endpoint-5.png)

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre a [rede virtual do espaço de trabalho gerenciado](./synapse-workspace-managed-vnet.md)

Saiba mais sobre [Pontos de extremidade privados gerenciados](./synapse-workspace-managed-private-endpoints.md)

[Criar Pontos de extremidade privados gerenciados para suas fontes de dados](./how-to-create-managed-private-endpoints.md)
