---
title: incluir arquivo
description: incluir arquivo
services: azure-resource-manager
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 03/19/2020
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: a00291182059506aeab9cde965fa4cbd5177ecf7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80132286"
---
Se um usuário não tiver o acesso necessário para aplicar tags, você pode atribuir a função **de Contribuinte de Marca** ao usuário. Para obter mais informações, consulte [Tutorial: Conceda a um usuário acesso aos recursos do Azure usando o RBAC e o portal Azure](../articles/role-based-access-control/quickstart-assign-role-user-portal.md).

1. Para visualizar as tags de um recurso ou um grupo de recursos, procure tags existentes na visão geral. Se você não tiver aplicado marcas anteriormente, a lista estará vazia.

   ![Visualizar marcas para recurso ou grupo de recursos](./media/resource-manager-tag-resources/view-tags.png)

1. Para adicionar uma marca, selecione **Clique aqui para adicionar marcas**.

1. Forneça um nome e valor.

   ![Adicionar marca](./media/resource-manager-tag-resources/add-tag.png)

1. Continue adicionando marcas conforme necessário. Ao terminar, escolha **Salvar**.

   ![Salvar marcas](./media/resource-manager-tag-resources/save-tags.png)

1. As marcas são exibidas agora na visão geral.

   ![Mostrar marcas](./media/resource-manager-tag-resources/view-new-tags.png)

1. Para adicionar ou excluir uma marca, selecione **alterar**.

1. Para excluir uma marca, selecione o ícone de lixeira. Em seguida, **selecione Salvar**.

   ![Excluir marca](./media/resource-manager-tag-resources/delete-tag.png)

Para atribuir marcas em massa para vários recursos:

1. A partir de qualquer lista de recursos, selecione a caixa de seleção para os recursos aos quais você deseja atribuir a marca. Em seguida, **selecione Atribuir tags**.

   ![Selecionar vários recursos](./media/resource-manager-tag-resources/select-multiple-resources.png)

1. Adicione nomes e valores. Ao terminar, escolha **Salvar**.

   ![Selecione atribuir](./media/resource-manager-tag-resources/select-assign.png)

Para exibir todos os recursos com uma marca:

1. No menu do portal Azure, procure **por tags**. Selecione-o entre as opções disponíveis.

   ![Localizar por marca](./media/resource-manager-tag-resources/find-tags-general.png)

1. Selecione a marca para recursos de visualização.

   ![Selecionar marca](./media/resource-manager-tag-resources/select-tag.png)

1. Todos os recursos com essa marca são exibidos.

   ![Visualizar recursos por marca](./media/resource-manager-tag-resources/view-resources-by-tag.png)
