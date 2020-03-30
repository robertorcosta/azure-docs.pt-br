---
title: Gerenciar recursos - Portal Azure
description: Use o portal Azure e o Azure Resource Manager para gerenciar seus recursos. Mostra como implantar e excluir recursos.
author: mumian
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: jgao
ms.openlocfilehash: 00d260a74807774d5bf226c3ec00a6b84f93b8d1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79248340"
---
# <a name="manage-azure-resources-by-using-the-azure-portal"></a>Gerencie os recursos do Azure usando o portal Azure

Saiba como usar o [portal Azure](https://portal.azure.com) com [o Azure Resource Manager](overview.md) para gerenciar seus recursos do Azure. Para gerenciar grupos de recursos, consulte [Gerenciar grupos de recursos do Azure usando o portal Azure](manage-resource-groups-portal.md).

Outros artigos sobre gestão de recursos:

- [Gerencie os recursos do Azure usando o Azure CLI](manage-resources-cli.md)
- [Gerenciar recursos do Azure usando o Azure PowerShell](manage-resources-powershell.md)

[!INCLUDE [Handle personal data](../../../includes/gdpr-intro-sentence.md)]

## <a name="deploy-resources-to-a-resource-group"></a>Implantar recursos em um grupo de recursos

Depois de criar um modelo de Gerenciador de recursos, você pode usar o portal Azure para implantar seus recursos do Azure. Para criar um modelo, consulte [Quickstart: Crie e implante modelos do Azure Resource Manager usando o portal Azure](../templates/quickstart-create-templates-use-the-portal.md). Para implantar um modelo usando o portal, consulte [Implantar recursos com modelos do Gerenciador de Recursos e portal Azure](../templates/deploy-portal.md).

## <a name="open-resources"></a>Recursos abertos

Os recursos do Azure são organizados pelos serviços do Azure e por grupos de recursos. Os procedimentos a seguir mostram como abrir uma conta de armazenamento chamada **mystorage0207**. A máquina virtual reside em um grupo de recursos chamado **mystorage0207rg**.

Para abrir um recurso pelo tipo de serviço:

1. Faça login no [portal Azure](https://portal.azure.com).
2. No painel esquerdo, selecione o serviço Azure. Neste caso, **contas de armazenamento**.  Se você não ver o serviço listado, selecione **Todos os serviços**e selecione o tipo de serviço.

    ![abrir recurso azul no portal](./media/manage-resources-portal/manage-azure-resources-portal-open-service.png)

3. Selecione o recurso que deseja abrir.

    ![abrir recurso azul no portal](./media/manage-resources-portal/manage-azure-resources-portal-open-resource.png)

    Uma conta de armazenamento parece:

    ![abrir recurso azul no portal](./media/manage-resources-portal/manage-azure-resources-portal-open-resource-storage.png)

Para abrir um recurso por grupo de recursos:

1. Faça login no [portal Azure](https://portal.azure.com).
2. No painel esquerdo, selecione **Grupos de recursos** para listar o recurso dentro do grupo.
3. Selecione o recurso que deseja abrir. 

## <a name="manage-resources"></a>Gerenciar recursos

Ao exibir um recurso no portal, são exibidas as opções para gerenciar esse recurso específico.

![gerenciar recursos do Azure](./media/manage-resources-portal/manage-azure-resources-portal-manage-resource.png)

A captura de tela mostra as opções de gerenciamento de uma máquina virtual Do Zure. Você pode executar operações como iniciar, reiniciar e parar uma máquina virtual.

## <a name="delete-resources"></a>Excluir recursos

1. Abra o recurso no portal. Para as etapas, consulte [Abrir recursos](#open-resources).
2. Selecione **Excluir**. A captura de tela a seguir mostra as opções de gerenciamento de uma máquina virtual.

    ![excluir recurso azul](./media/manage-resources-portal/manage-azure-resources-portal-delete-resource.png)
3. Digite o nome do recurso para confirmar a exclusão e, em seguida, **selecione Excluir**.

Para obter mais informações sobre como o Azure Resource Manager ordena a exclusão de recursos, consulte a exclusão do grupo de [recursos do Azure Resource Manager](delete-resource-group.md).

## <a name="move-resources"></a>Mover recursos

1. Abra o recurso no portal. Para as etapas, consulte [Abrir recursos](#open-resources).
2. Selecione **Mover**. A captura de tela a seguir mostra as opções de gerenciamento de uma conta de armazenamento.

    ![mover recurso azul](./media/manage-resources-portal/manage-azure-resources-portal-move-resource.png)
3. Selecione **Mover para outro grupo de recursos** ou mover para outra **assinatura,** dependendo de suas necessidades.

Para saber mais, confira [Mover recursos para um novo grupo de recursos ou assinatura](move-resource-group-and-subscription.md).

## <a name="lock-resources"></a>Bloquear recursos

O bloqueio impede que outros usuários da sua organização excluam acidentalmente ou modifiquem recursos críticos, como assinatura do Azure, grupo de recursos ou recurso. 

1. Abra o recurso no portal. Para as etapas, consulte [Abrir recursos](#open-resources).
2. Selecione **Bloqueios**. A captura de tela a seguir mostra as opções de gerenciamento de uma conta de armazenamento.

    ![bloquear recurso azul](./media/manage-resources-portal/manage-azure-resources-portal-lock-resource.png)
3. Selecione **Adicionar**e, em seguida, especifique as propriedades de bloqueio.

Para obter mais informações, consulte [Bloquear recursos com o Azure Resource Manager](lock-resources.md).

## <a name="tag-resources"></a>Recursos de marca

A marcação ajuda a organizar seu grupo de recursos e recursos logicamente. 

1. Abra o recurso no portal. Para as etapas, consulte [Abrir recursos](#open-resources).
2. Selecionar **Marcas**. A captura de tela a seguir mostra as opções de gerenciamento de uma conta de armazenamento.

    ![recurso tag azure](./media/manage-resources-portal/manage-azure-resources-portal-tag-resource.png)
3. Especifique as propriedades da tag e selecione **Salvar**.

Para obter informações, consulte [Usando tags para organizar seus recursos do Azure](tag-resources.md#portal).

## <a name="monitor-resources"></a>Monitorar recursos

Quando você abre um recurso, o portal apresenta gráficos e tabelas padrão para monitorar esse tipo de recurso. A captura de tela a seguir mostra os gráficos de uma máquina virtual:

![monitorar recurso azul](./media/manage-resources-portal/manage-azure-resources-portal-monitor-resource.png)

Você pode selecionar o ícone de pino no canto superior direito dos gráficos para fixar o gráfico no painel. Para aprender a trabalhar com painéis, confira [Criar compartilhar painéis personalizados no Portal do Azure](../../azure-portal/azure-portal-dashboards.md).

## <a name="manage-access-to-resources"></a>Gerencie o acesso aos recursos

O [Controle de acesso baseado em função (RBAC)](../../role-based-access-control/overview.md) é a maneira de gerenciar o acesso aos recursos no Azure. Para obter mais informações, confira [gerenciar o acesso usando o portal do Azure e o RBAC](../../role-based-access-control/role-assignments-portal.md).

## <a name="next-steps"></a>Próximas etapas

- Para aprender o Azure Resource Manager, consulte [a visão geral do Azure Resource Manager](overview.md).
- Para aprender a sintaxe do modelo Resource Manager, consulte [Entenda a estrutura e a sintaxe dos modelos do Azure Resource Manager](../templates/template-syntax.md).
- Para saber como desenvolver modelos, consulte os [tutoriais passo a passo](/azure/azure-resource-manager/).
- Para exibir os esquemas de modelo do Azure Resource Manager, consulte [referência de modelo](/azure/templates/).
