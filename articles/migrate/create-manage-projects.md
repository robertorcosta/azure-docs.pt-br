---
title: Criar e gerenciar projetos de Migrações para Azure
description: Encontre, crie, gerencie e exclua projetos no Azure Migrate.
ms.topic: how-to
ms.date: 04/19/2020
ms.openlocfilehash: f5079ed979d98f2c6f0c654c860c6f176f366497
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81676385"
---
# <a name="create-and-manage-azure-migrate-projects"></a>Criar e gerenciar projetos de Migrações para Azure

Este artigo descreve como criar, gerenciar e excluir projetos [do Azure Migrate.](migrate-services-overview.md)


## <a name="create-a-project-for-the-first-time"></a>Crie um projeto pela primeira vez

A primeira vez que você configura o Azure Migrate, você cria um projeto e adiciona uma ferramenta de avaliação ou migração. [Siga estas instruções](how-to-add-tool-first-time.md) para configurar pela primeira vez.

## <a name="create-additional-projects"></a>Criar projetos adicionais

Se você já tem um projeto do Azure Migrate e deseja criar um projeto adicional, faça o seguinte:  

1. No [portal público azure](https://portal.azure.com) ou [governo azure](https://portal.azure.us), procure **por Azure Migrate**.
2. No painel do Azure Migrate > **Servers**, selecione **a alteração** no canto superior direito.

   ![Alterar projeto do Azure Migrate](./media/create-manage-projects/switch-project.png)

3. Para criar um novo projeto, selecione **clique aqui**.

   ![Crie um segundo projeto do Azure Migrate](./media/create-manage-projects/create-new-project.png)


## <a name="find-a-project"></a>Encontre um projeto

Encontre um projeto da seguinte forma:

1. No [portal Azure,](https://portal.azure.com)procure **o Azure Migrate**.
2. No painel do Azure Migrate > **Servers**, selecione **a alteração** no canto superior direito.

    ![Mude para um projeto existente do Azure Migrate](./media/create-manage-projects/switch-project.png)

3. Selecione a assinatura apropriada e o projeto Azure Migrate.


Se você criou o projeto na [versão anterior](migrate-services-overview.md#azure-migrate-versions) do Azure Migrate, encontre-o da seguinte forma:

1. No [portal Azure,](https://portal.azure.com)procure **o Azure Migrate**.
2. No painel Do Azure Migrate, se você criou um projeto na versão anterior, um banner referenciando projetos mais antigos será exibido. Selecione o banner.

    ![Acesse projetos existentes](./media/create-manage-projects/access-existing-projects.png)

3. Revise a lista de projetos antigos.


## <a name="delete-a-project"></a>Excluir um projeto

Excluir da seguinte forma:

1. Abra o grupo de recursos do Azure no qual o projeto foi criado.
2. Na página do grupo de recursos, selecione **Mostrar tipos ocultos**.
3. Selecione o projeto migrar que deseja excluir e seus recursos associados.
    - O tipo de recurso é **Microsoft.Migrate/migrateprojects**.
    - Se o grupo de recursos for usado exclusivamente pelo projeto Azure Migrate, você poderá excluir todo o grupo de recursos.


Observe que:

- Quando você exclui, tanto o projeto quanto os metadados sobre máquinas descobertas são excluídos.
- Se você estiver usando a versão mais antiga do Azure Migrate, abra o grupo de recursos do Azure no qual o projeto foi criado. Selecione o projeto migrar que deseja excluir (o tipo de recurso é **projeto Migração**).
- Se você estiver usando a análise de dependência com um espaço de trabalho do Azure Log Analytics:
    - Se você tiver conectado um espaço de trabalho do Log Analytics à ferramenta Avaliação do servidor, o espaço de trabalho não será excluído automaticamente. O mesmo espaço de trabalho do Log Analytics pode ser usado para vários cenários.
    - Se você quiser excluir o espaço de trabalho do Log Analytics, faça isso manualmente.

### <a name="delete-a-workspace-manually"></a>Exclua um espaço de trabalho manualmente

1. Navegue até o espaço de trabalho do Log Analytics associado ao projeto.

    - Se você não excluiu o projeto Azure Migrate, você pode encontrar o link para o espaço de trabalho no **Essentials** > **Server Assessment**.
       ![Espaço de](./media/create-manage-projects/loganalytics-workspace.png)trabalho la .
       
    - Se você já excluiu o projeto Azure Migrate, selecione **Grupos de recursos** no painel esquerdo do portal Azure e encontre o espaço de trabalho.
       
2. [Siga as instruções](https://docs.microsoft.com/azure/azure-monitor/platform/delete-workspace) para excluir o espaço de trabalho.

## <a name="next-steps"></a>Próximas etapas

Adicione ferramentas [de avaliação](how-to-assess.md) ou [migração](how-to-migrate.md) aos projetos do Azure Migrate.
