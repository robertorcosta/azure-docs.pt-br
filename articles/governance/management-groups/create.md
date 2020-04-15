---
title: Criar grupos de gestão para organizar recursos - Azure Governance
description: Saiba como criar grupos de gerenciamento do Azure para gerenciar vários recursos usando o portal, o Azure PowerShell e a CLI do Azure.
ms.date: 04/15/2020
ms.topic: conceptual
ms.openlocfilehash: 34815089367512c4aa54f148c118a669625d0ea3
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81381596"
---
# <a name="create-management-groups-for-resource-organization-and-management"></a>Criar grupos de gerenciamento para o gerenciamento e a organização de recursos

Grupos de gerenciamento são contêineres que o ajudarão a gerenciar o acesso, a política e a conformidade entre várias assinaturas. Criar esses contêineres para criar uma hierarquia eficaz e eficiente que pode ser usada com o [Azure Policy](../policy/overview.md) e os [Controles de Acesso Baseados em Função do Azure](../../role-based-access-control/overview.md). Para obter mais informações sobre grupos de gerenciamento, consulte [Organizar seus recursos com grupos de gerenciamento do Azure](overview.md).

O primeiro grupo de gerenciamento criado no diretório pode levar até 15 minutos para ser concluído. Há processos que são executados pela primeira vez para configurar o serviço de grupos de gerenciamento no Azure para seu diretório. Você recebe uma notificação quando o processo é concluído. Para obter mais informações, consulte [a configuração inicial dos grupos de gerenciamento](./overview.md#initial-setup-of-management-groups).

## <a name="create-a-management-group"></a>Crie um grupo de gerenciamento

Qualquer usuário ad do Azure no inquilino pode criar um grupo de gerenciamento sem a permissão de gravação do grupo de gerenciamento atribuída a esse usuário. Este novo grupo de gerenciamento será filho do Root Management Group e o criador receberá uma atribuição de função "Proprietário". O serviço de grupo de gerenciamento permite essa capacidade para que as atribuições de função não sejam necessárias no nível raiz. Nenhum usuário tem acesso ao Root Management Group quando ele é criado. Para evitar o obstáculo de encontrar os Administradores Globais Azure AD para começar a usar grupos de gerenciamento, permitimos a criação dos grupos de gerenciamento iniciais na raiz  
Nível.

Você pode criar o grupo de gerenciamento usando o portal, um [modelo de Gerenciador de Recursos,](../../azure-resource-manager/templates/deploy-to-tenant.md#create-management-group)PowerShell ou Cli Do Zure.

### <a name="create-in-portal"></a>Criar no portal

1. Faça login no [portal Azure](https://portal.azure.com).

1. Selecione **Todos os serviços** > **Gestão + governança**.

1. Selecione **Gerenciamento de Custos + Faturamento**

1. Na página Gerenciamento de Custos + Faturamento - Grupos de gestão, selecione **Grupos de Gestão**

1. Selecione **+ Adicionar grupo de gerenciamento**.

   :::image type="content" source="./media/main.png" alt-text="Página para trabalhar com grupos de gestão" border="false":::

1. Preencha o campo de ID do grupo de gerenciamento.

   - **ID do Grupo de Gerenciamento** é o identificador exclusivo do diretório usado para enviar comandos nesse grupo de gerenciamento. Esse identificador não é editável após a criação, visto que é usado em todo o sistema do Azure para identificar esse grupo. O [grupo de gerenciamento raiz](overview.md#root-management-group-for-each-directory) é criado automaticamente com um ID que é o ID do Diretório Ativo do Azure. Para todos os outros grupos de gerenciamento, atribua um ID único.
   - O campo de nome de exibição é o nome exibido no portal do Azure. Um nome de exibição separado é um campo opcional ao criar o grupo de gerenciamento e pode ser alterado em qualquer  
     tempo.

   :::image type="content" source="./media/create_context_menu.png" alt-text="Painel de opções para a criação de um novo grupo de gestão" border="false":::

1. Clique em **Salvar**.

### <a name="create-in-powershell"></a>Criar no PowerShell

Para o PowerShell, use o cmdlet [do New-AzManagementGroup](/powershell/module/az.resources/new-azmanagementgroup) para criar um novo grupo de gerenciamento.

```azurepowershell-interactive
New-AzManagementGroup -GroupName 'Contoso'
```

**GroupName** é um identificador exclusivo que está sendo criado. Este ID é usado por outros comandos para referenciar este grupo e não pode ser alterado mais tarde.

Se você quiser que o grupo de gerenciamento mostre um nome diferente dentro do portal Azure, adicione o parâmetro **DisplayName.** Por exemplo, para criar um grupo de gerenciamento com o GroupName de Contoso e o nome de exibição do "Grupo Contoso", use o seguinte cmdlet:

```azurepowershell-interactive
New-AzManagementGroup -GroupName 'Contoso' -DisplayName 'Contoso Group'
```

Nos exemplos anteriores, o novo grupo de gerenciamento é criado sob o grupo de gerenciamento raiz. Para especificar um grupo de gerenciamento diferente como pai, use o parâmetro **ParentId.**

```azurepowershell-interactive
$parentGroup = Get-AzManagementGroup -GroupName Contoso
New-AzManagementGroup -GroupName 'ContosoSubGroup' -ParentId $parentGroup.id
```

### <a name="create-in-azure-cli"></a>Criar na CLI do Azure

Para o Azure CLI, use o comando [az account management-group](/cli/azure/account/management-group?view=azure-cli-latest#az-account-management-group-create) para criar um novo grupo de gerenciamento.

```azurecli-interactive
az account management-group create --name Contoso
```

O **nome** é um identificador único sendo criado. Este ID é usado por outros comandos para referenciar este grupo e não pode ser alterado mais tarde.

Se você quiser que o grupo de gerenciamento mostre um nome diferente dentro do portal Azure, adicione o parâmetro **de nome de exibição.** Por exemplo, para criar um grupo de gerenciamento com o GroupName de Contoso e o nome de exibição do "Grupo Contoso", use o seguinte comando:

```azurecli-interactive
az account management-group create --name Contoso --display-name 'Contoso Group'
```

Nos exemplos anteriores, o novo grupo de gerenciamento é criado sob o grupo de gerenciamento raiz. Para especificar um grupo de gerenciamento diferente como pai, use o parâmetro **pai** e forneça o nome do grupo pai.

```azurecli-interactive
az account management-group create --name ContosoSubGroup --parent Contoso
```

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre grupos de gerenciamento, consulte:

- [Criar grupos de gerenciamento para organizar recursos do Azure](./create.md)
- [Como alterar, excluir ou gerenciar seus grupos de gerenciamento](./manage.md)
- [Analisar grupos de gerenciamento no Módulo de Recursos do Azure PowerShell](/powershell/module/az.resources#resources)
- [Revisar grupos de gerenciamento na API REST](/rest/api/resources/managementgroups)
- [Revisar grupos de gerenciamento na CLI do Azure](/cli/azure/account/management-group)
