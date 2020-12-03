---
title: Criar e gerenciar tempos de execução de integração
description: Este artigo explica as etapas para criar e gerenciar tempos de execução de integração no Azure alcance.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/16/2020
ms.openlocfilehash: 0d365787ea3603ef0adb8ad0b48bef9792ffb003
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/03/2020
ms.locfileid: "96551397"
---
# <a name="create-and-manage-a-self-hosted-integration-runtime"></a>Criar e gerenciar um tempo de execução de integração auto-hospedado

Este artigo descreve como criar e gerenciar um SHIR (Integration Runtime) auto-hospedado para auxiliar na verificação de fontes de dados.

## <a name="create-a-self-hosted-integration-runtime"></a>Criar um Integration Runtime auto-hospedado

1. Na home page do alcance Studio, selecione **centro de gerenciamento** no painel de navegação esquerdo.

2. Em **fontes e verificação** no painel esquerdo, selecione **tempos de execução de integração** e, em seguida, selecione **+ novo**.

    :::image type="content" source="media/manage-integration-runtimes/select-integration-runtimes.png" alt-text="clique em IR.":::

3. Na página **instalação do Integration Runtime** , selecione **auto-hospedado** para criar um Self-Hosted ir e, em seguida, selecione **continuar**.

    :::image type="content" source="media/manage-integration-runtimes/select-self-hosted-ir.png" alt-text="criar novo SHIR.":::

4. Insira um nome para o IR e selecione criar.

5. Na página **configurações de Integration Runtime** , siga as etapas na seção **configuração manual** . Você precisará baixar o Integration Runtime do site de download para uma VM ou computador em que você pretende executá-lo.

    :::image type="content" source="media/manage-integration-runtimes/integration-runtime-settings.png" alt-text="obter chave":::

    a. Copie e cole a chave de autenticação.
        
    b. Baixe o tempo de execução de integração auto-hospedado de [Azure Data Factory Integration Runtime](https://www.microsoft.com/download/details.aspx?id=39717) em uma máquina local do Windows. Execute o instalador.
        
    c. Na página **registrar Integration Runtime (auto-hospedado)** , Cole uma das duas chaves salvas anteriormente e selecione **registrar**.

    :::image type="content" source="media/manage-integration-runtimes/register-integration-runtime.png" alt-text="chave de entrada.":::

    d. Na página **Novo nó do Integration Runtime (auto-hospedado)** , selecione **Concluir**.

6. Depois que o tempo de execução de integração auto-hospedado for registrado com êxito, você verá a seguinte janela:

    :::image type="content" source="media/manage-integration-runtimes/successfully-registered.png" alt-text="registrado com êxito.":::

## <a name="manage-a-self-hosted-integration-runtime"></a>Gerenciar um Integration Runtime de hospedagem interna

Você pode editar um tempo de execução de integração auto-hospedado navegando até os **tempos de execução de integração** no centro de **Gerenciamento**, selecionando o ir e clicando em Editar. Agora você pode atualizar a descrição, copiar a chave ou regenerar novas chaves.

:::image type="content" source="media/manage-integration-runtimes/edit-integration-runtime.png" alt-text="editar IR.":::

:::image type="content" source="media/manage-integration-runtimes/edit-integration-runtime-settings.png" alt-text="Edite os detalhes de IR.":::

Você pode excluir um tempo de execução de integração auto-hospedado navegando até os **tempos de execução de integração** no centro de gerenciamento, selecionando o ir e clicando em **excluir**. Depois que um IR for excluído, qualquer verificação contínua que dependa dele falhará.

## <a name="next-steps"></a>Próximas etapas

* [Como as verificações detectam ativos excluídos](concept-detect-deleted-assets.md)
