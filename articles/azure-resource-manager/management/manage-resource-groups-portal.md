---
title: Gerenciar grupos de recursos - Portal Azure
description: Use o portal Azure para gerenciar seus grupos de recursos através do Azure Resource Manager. Mostra como criar, listar e excluir grupos de recursos.
author: mumian
ms.topic: conceptual
ms.date: 03/26/2019
ms.author: jgao
ms.openlocfilehash: 8087baf7595bfd2497f97fbff4822b356cd1b146
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79274561"
---
# <a name="manage-azure-resource-manager-resource-groups-by-using-the-azure-portal"></a>Gerencie grupos de recursos do Azure Resource Manager usando o portal Azure

Saiba como usar o [portal Azure](https://portal.azure.com) com [o Azure Resource Manager](overview.md) para gerenciar seus grupos de recursos do Azure. Para gerenciar os recursos do Azure, consulte [Gerenciar os recursos do Azure usando o portal Azure](manage-resources-portal.md).

Outros artigos sobre o gerenciamento de grupos de recursos:

- [Gerencie grupos de recursos do Azure usando o Azure CLI](manage-resources-cli.md)
- [Gerencie grupos de recursos do Azure usando o Azure PowerShell](manage-resources-powershell.md)

[!INCLUDE [Handle personal data](../../../includes/gdpr-intro-sentence.md)]

## <a name="what-is-a-resource-group"></a>O que é um grupo de recursos

Um grupo de recursos é um contêiner que mantém os recursos relacionados a uma solução do Azure. O grupo de recursos pode incluir todos os recursos para a solução ou apenas os recursos que você deseja gerenciar como um grupo. Você decide como deseja alocar recursos para grupos de recursos com base no que faz mais sentido para sua organização. Em geral, adicione recursos que compartilham o mesmo ciclo de vida no mesmo grupo de recursos, para que você possa implantar, atualizar e excluí-los como um grupo facilmente.

O grupo de recursos armazena metadados sobre os recursos. Portanto, quando você especifica um local para o grupo de recursos, especifica onde os metadados são armazenados. Por motivos de conformidade, você precisa fazer com que os dados sejam armazenados em determinada região.

O grupo de recursos armazena metadados sobre os recursos. Quando você especifica uma localização para o grupo de recursos, você especifica onde os metadados são armazenados.

## <a name="create-resource-groups"></a>Criar grupos de recursos

1. Faça login no [portal Azure](https://portal.azure.com).
2. Selecionar **grupos de recursos**

    ![adicionar grupo de recursos](./media/manage-resource-groups-portal/manage-resource-groups-add-group.png)
3. Selecione **Adicionar**.
4. Insira os valores a seguir:

   - **Assinatura**: Selecione sua assinatura do Azure. 
   - **Grupo de recursos**: Digite um novo nome de grupo de recursos. 
   - **Região**: Selecione uma localização azure, como **o Central US**.

     ![Criar grupo de recursos](./media/manage-resource-groups-portal/manage-resource-groups-create-group.png)
5. Selecione **Revisão + Criar**
6. Selecione **Criar**. Leva alguns segundos para criar um grupo de recursos.
7. Selecione **Atualizar** no menu superior para atualizar a lista de grupos de recursos e, em seguida, selecione o grupo de recursos recém-criado para abri-la. Ou selecione **Notificação**(o ícone do sino) de cima e selecione Ir para o **grupo de recursos** para abrir o grupo de recursos recém-criado

    ![ir para o grupo de recursos](./media/manage-resource-groups-portal/manage-resource-groups-add-group-go-to-resource-group.png)

## <a name="list-resource-groups"></a>Listar os grupos de recursos

1. Faça login no [portal Azure](https://portal.azure.com).
2. Para listar os grupos de recursos, selecione **grupos de recursos**

    ![procurar grupos de recursos](./media/manage-resource-groups-portal/manage-resource-groups-list-groups.png)

3. Para personalizar as informações exibidas para os grupos de recursos, selecione **Editar colunas**. A captura de tela a seguir mostra as colunas de adição que você pode adicionar ao display:

## <a name="open-resource-groups"></a>Grupos de recursos abertos

1. Faça login no [portal Azure](https://portal.azure.com).
2. Selecione **Grupos de recursos**.
3. Selecione o grupo de recursos que deseja abrir.

## <a name="delete-resource-groups"></a>Excluir grupos de recursos

1. Abra o grupo de recursos que deseja excluir.  Consulte [Grupos de recursos abertos](#open-resource-groups).
2. Selecione **Excluir grupo de recursos**.

    ![excluir grupo de recursos azure](./media/manage-resource-groups-portal/delete-group.png)

Para obter mais informações sobre como o Azure Resource Manager ordena a exclusão de recursos, consulte a exclusão do grupo de [recursos do Azure Resource Manager](delete-resource-group.md).

## <a name="deploy-resources-to-a-resource-group"></a>Implantar recursos em um grupo de recursos

Depois de criar um modelo de Gerenciador de recursos, você pode usar o portal Azure para implantar seus recursos do Azure. Para criar um modelo, consulte [Quickstart: Crie e implante modelos do Azure Resource Manager usando o portal Azure](../templates/quickstart-create-templates-use-the-portal.md). Para implantar um modelo usando o portal, consulte [Implantar recursos com modelos do Gerenciador de Recursos e portal Azure](../templates/deploy-portal.md).

## <a name="move-to-another-resource-group-or-subscription"></a>Mova-se para outro grupo de recursos ou assinatura

Você pode transferir os recursos do grupo para outro grupo de recursos. Para saber mais, confira [Mover recursos para um novo grupo de recursos ou assinatura](move-resource-group-and-subscription.md).

## <a name="lock-resource-groups"></a>Grupos de recursos de bloqueio

O bloqueio impede que outros usuários da sua organização excluam acidentalmente ou modifiquem recursos críticos, como assinatura do Azure, grupo de recursos ou recurso. 

1. Abra o grupo de recursos que deseja excluir.  Consulte [Grupos de recursos abertos](#open-resource-groups).
2. No painel esquerdo, selecione **Fechaduras**.
3. Para adicionar um bloqueio ao grupo de recursos, **selecione Adicionar**.
4. Digite **o nome do bloqueio,** **o tipo de**bloqueio e **as notas**. Os tipos **de**bloqueio incluem somente leitura e **exclusão**.

    ![bloquear grupo de recursos azule](./media/manage-resource-groups-portal/manage-resource-groups-add-lock.png)

Para obter mais informações, consulte [bloquear recursos para evitar alterações inesperadas](lock-resources.md).

## <a name="tag-resource-groups"></a>Grupos de recursos de tag

Você pode aplicar marcas a recursos e grupos de recursos para organizar seus ativos de modo lógico. Para obter informações, consulte [Usando tags para organizar seus recursos do Azure](tag-resources.md#portal).

## <a name="export-resource-groups-to-templates"></a>Exportar grupos de recursos para modelos

Para obter informações sobre a exportação de modelos, consulte [Exportação única e multi-recursos para modelo - Portal](../templates/export-template-portal.md).

## <a name="manage-access-to-resource-groups"></a>Gerencie o acesso a grupos de recursos

O [Controle de acesso baseado em função (RBAC)](../../role-based-access-control/overview.md) é a maneira de gerenciar o acesso aos recursos no Azure. Para obter mais informações, confira [gerenciar o acesso usando o portal do Azure e o RBAC](../../role-based-access-control/role-assignments-portal.md).

## <a name="next-steps"></a>Próximas etapas

- Para aprender o Azure Resource Manager, consulte [a visão geral do Azure Resource Manager](overview.md).
- Para aprender a sintaxe do modelo Resource Manager, consulte [Entenda a estrutura e a sintaxe dos modelos do Azure Resource Manager](../templates/template-syntax.md).
- Para saber como desenvolver modelos, consulte os [tutoriais passo a passo](/azure/azure-resource-manager/).
- Para exibir os esquemas de modelo do Azure Resource Manager, consulte [referência de modelo](/azure/templates/).