---
title: Habilitar notebooks na conta Azure Cosmos DB (visualização)
description: Os notebooks embutidos do Azure Cosmos DB permitem analisar e visualizar seus dados dentro do Portal. Este artigo descreve como habilitar esse recurso para contas Cosmos.
author: deborahc
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/22/2019
ms.author: dech
ms.openlocfilehash: dcec310db43baa513b2d574d03f3f35dee3f773b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76768011"
---
# <a name="enable-notebooks-for-azure-cosmos-db-accounts-preview"></a>Habilitar notebooks para contas Azure Cosmos DB (visualização)

> [!IMPORTANT]
> Os notebooks embutidos para o Azure Cosmos DB estão atualmente disponíveis nas seguintes regiões do Azure: Austrália Leste, Leste dos EUA, Leste dos EUA 2, Norte da Europa, Centro-Sul dos EUA, Sudeste Asiático, Reino Unido Sul, Europa Ocidental e Oeste dos EUA 2. Para usar notebooks, [crie uma nova conta com notebooks](#enable-notebooks-in-a-new-cosmos-account) ou [habilite notebooks em uma conta existente](#enable-notebooks-in-an-existing-cosmos-account) em uma dessas regiões.

Os notebooks Jupyter incorporados no Azure Cosmos DB permitem analisar e visualizar seus dados a partir do portal Azure. Este artigo descreve como habilitar esse recurso para sua conta do Azure Cosmos DB.

## <a name="enable-notebooks-in-a-new-cosmos-account"></a>Habilite notebooks em uma nova conta cosmos
1. Inscreva-se no [portal Azure](https://portal.azure.com/).
1. Selecione **Criar um banco** > de**dados de** > recursos**Azure Cosmos DB**.
1. Na página **Criar conta do Azure Cosmos DB,** selecione **Notebooks**. 
 
    ![Selecione a opção de notebooks no Azure Cosmos DB Criar lâmina](media/enable-notebooks/create-new-account-with-notebooks.png)
1. Selecione **Revisão + criar**. Você pode pular a opção **Rede** e **Tags.** 
1. Examine as configurações da conta e selecione **Criar**. São necessários alguns minutos para criar a conta. Aguarde até que a página do portal exiba **Sua implantação está concluída**. 

    ![O painel Notificações do portal do Azure](media/enable-notebooks/create-new-account-with-notebooks-complete.png)
1. Selecione **Ir para recurso** para ir para a página da conta do Azure Cosmos DB. 

    ![A página da conta do Azure Cosmos DB](../../includes/media/cosmos-db-create-dbaccount/azure-cosmos-db-account-created-3.png)

1. Navegue até o painel **do Data Explorer.** Agora você deve ver seus cadernos espaço de trabalho.

    ![Novo espaço de trabalho de notebooks Azure Cosmos DB](media/enable-notebooks/new-notebooks-workspace.png)

## <a name="enable-notebooks-in-an-existing-cosmos-account"></a>Habilite notebooks em uma conta Cosmos existente
Você também pode habilitar notebooks em contas existentes. Essa etapa precisa ser feita apenas uma vez por conta.

1. Navegue até o painel **data explorer** em sua conta Cosmos.
1. Selecione **Ativar notebooks**.

    ![Crie um novo espaço de trabalho de notebooks no Data Explorer](media/enable-notebooks/enable-notebooks-workspace.png)
1. Isso solicitará que você crie um novo espaço de trabalho de notebooks. Selecione **Configuração completa.**
1. Sua conta agora está habilitada para usar notebooks!

## <a name="create-and-run-your-first-notebook"></a>Crie e execute seu primeiro notebook

Para verificar se você pode usar notebooks, selecione um dos notebooks em Cadernos de Amostra. Isso salvará uma cópia do notebook no seu espaço de trabalho e abrirá-o.

Neste exemplo, usaremos **GettingStarted.ipynb**. 

![Ver o notebook GettingStarted.ipynb](media/enable-notebooks/select-getting-started-notebook.png)

Para executar o notebook:
1. Selecione a primeira célula de código que contenha código Python. 
1. Selecione **Executar** para executar o celular. Você também pode usar **Shift + Enter** para executar o celular.
1. Atualize o painel de recursos para ver o banco de dados e o contêiner que foi criado.

    ![Corra começando o notebook](media/enable-notebooks/run-first-notebook-cell.png)

Você também pode selecionar **Novo Notebook** para criar um novo notebook ou carregar um arquivo de notebook existente (.ipynb) selecionando **Upload File** no menu **Meus Notebooks.** 

![Criar ou carregar um novo notebook](media/enable-notebooks/create-or-upload-new-notebook.png)

## <a name="next-steps"></a>Próximas etapas

- Conheça os benefícios dos [notebooks Azure Cosmos DB Jupyter](cosmosdb-jupyter-notebooks.md)
