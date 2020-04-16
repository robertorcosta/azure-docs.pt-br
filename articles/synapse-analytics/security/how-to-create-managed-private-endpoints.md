---
title: Crie um ponto final privado gerenciado para se conectar aos resultados da fonte de dados.
description: Este artigo ensinará como criar um ponto final privado gerenciado para suas fontes de dados a partir de um espaço de trabalho Do Azure Synapse.
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: how-to
ms.date: 04/15/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: 363dc4d469d912c14f5f89ef6ff433a2243587e8
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81428895"
---
# <a name="create-a-managed-private-endpoint-to-your-data-source-preview"></a>Crie um ponto final privado gerenciado para sua fonte de dados (visualização)

Este artigo ensinará como criar um ponto final privado gerenciado para sua fonte de dados no Azure. Consulte [Pontos finais privados gerenciados](./synapse-workspace-managed-private-endpoints.md) para saber mais.

## <a name="step-1-open-your-azure-synapse-workspace-in-azure-portal"></a>Passo 1: Abra seu espaço de trabalho Azure Synapse no portal Azure

Você pode criar um ponto final privado gerenciado para sua fonte de dados do Azure Synapse Studio. Selecione a guia **Visão geral** no portal Azure e selecione **Launch Synapse Studio**.
![Lançar o Azure Synapse Studio](./media/how-to-create-managed-private-endpoints/managed-private-endpoint-1.png)

## <a name="step-2-navigate-to-the-managed-virtual-networks-tab-in-synapse-studio"></a>Passo 2: Navegue até a guia Redes Virtuais Gerenciadas no Synapse Studio

No Azure Synapse Studio, selecione a guia **Gerenciar** na navegação à esquerda. Selecione **Redes Virtuais Gerenciadas** e selecione **+ Novo**.
![Criar um novo ponto final privado gerenciado](./media/how-to-create-managed-private-endpoints/managed-private-endpoint-2.png)

## <a name="step-3-select-the-data-source-type"></a>Passo 3: Selecione o tipo de origem de dados

Selecione o tipo de origem de dados. Neste caso, a fonte de dados de destino é uma conta ADLS gen2. Selecione **Continuar**.
![Selecione um tipo de fonte de dados de destino](./media/how-to-create-managed-private-endpoints/managed-private-endpoint-3.png)

## <a name="step-4-enter-information-about-the-data-source"></a>Passo 4: Insira informações sobre a fonte de dados

Na janela seguinte, digite informações sobre a fonte de dados. Neste exemplo, estamos criando um ponto final privado gerenciado para uma conta ADLS gen2. Digite um **nome** para o ponto final privado gerenciado. Forneça uma **assinatura do Azure** e um **nome de conta de armazenamento**. Selecione **Criar**.
![Digite os detalhes da fonte de dados de destino](./media/how-to-create-managed-private-endpoints/managed-private-endpoint-4.png)

## <a name="step-5-verify-that-your-managed-private-endpoint-was-successfully-created"></a>Passo 5: Verifique se o seu ponto final privado gerenciado foi criado com sucesso

Depois de enviar a solicitação, você verá seu status. Para verificar se foi criada a criação bem-sucedida do seu ponto final privado gerenciado, verifique o *estado de provisionamento .* Você pode precisar esperar 1 minuto e selecionar **Atualizar** para atualizar o estado de provisionamento. Você pode ver que o ponto final privado gerenciado para a conta ADLS gen2 foi criado com sucesso.

Você também pode ver que o *Estado de Aprovação* está *pendente*. O proprietário do recurso de destino pode aprovar ou negar a solicitação de conexão de ponto final privado. Se o proprietário aprovar a solicitação de conexão de ponto final privado, então um link privado será estabelecido. Se negado, então um link privado não é estabelecido.
![Status de solicitação de criação de ponto final privado gerenciado](./media/how-to-create-managed-private-endpoints/managed-private-endpoint-5.png)

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre [pontos finais privados gerenciados](./synapse-workspace-managed-private-endpoints.md)