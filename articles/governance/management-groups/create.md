---
title: Criar grupos de gerenciamento para organizar recursos-governança do Azure
description: Saiba como criar grupos de gerenciamento do Azure para gerenciar vários recursos usando o portal, o Azure PowerShell e a CLI do Azure.
ms.date: 12/18/2019
ms.topic: conceptual
ms.openlocfilehash: d9bb2e82404c0188094298f40da3346ee132eec3
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75436523"
---
# <a name="create-management-groups-for-resource-organization-and-management"></a>Criar grupos de gerenciamento para o gerenciamento e a organização de recursos

Grupos de gerenciamento são contêineres que o ajudarão a gerenciar o acesso, a política e a conformidade entre várias assinaturas. Criar esses contêineres para criar uma hierarquia eficaz e eficiente que pode ser usada com o [Azure Policy](../policy/overview.md) e os [Controles de Acesso Baseados em Função do Azure](../../role-based-access-control/overview.md). Para obter mais informações sobre grupos de gerenciamento, consulte [Organizar seus recursos com grupos de gerenciamento do Azure](overview.md).

O primeiro grupo de gerenciamento criado no diretório pode levar até 15 minutos para ser concluído. Há processos que são executados pela primeira vez para configurar o serviço de grupos de gerenciamento no Azure para seu diretório. Você recebe uma notificação quando o processo é concluído. Para obter mais informações, consulte [configuração inicial dos grupos de gerenciamento](./overview.md#initial-setup-of-management-groups). 

## <a name="create-a-management-group"></a>Crie um grupo de gerenciamento

Qualquer usuário do Azure AD no locatário pode criar um grupo de gerenciamento sem a permissão de gravação do grupo de gerenciamento atribuída a esse usuário.  Esse novo grupo de gerenciamento será um filho do grupo de gerenciamento raiz e o criador receberá uma atribuição de função de "proprietário". O serviço de grupo de gerenciamento permite essa capacidade para que as atribuições de função não sejam necessárias no nível raiz. Nenhum usuário tem acesso ao grupo de gerenciamento raiz quando ele é criado.  Para evitar o obstáculo de localizar os administradores globais do Azure AD para começar a usar grupos de gerenciamento, permitimos a criação dos grupos de gerenciamento iniciais no nível raiz.      

Você pode criar o grupo de gerenciamento usando o portal, o PowerShell ou a CLI do Azure. Atualmente, é possível usar modelos do Resource Manager para criar grupos de gerenciamento.

### <a name="create-in-portal"></a>Criar no portal

1. Faça logon no [Portal do Azure](https://portal.azure.com).

1. Selecione **todos os serviços** > **Gerenciamento + governança**.

1. Selecionar **Gerenciamento de custos + cobrança**

1. Na página Gerenciamento de custos + cobrança-grupos de gerenciamento, selecione **grupos de gerenciamento**

1. Selecione **+ Adicionar grupo de gerenciamento**.

   ![Página para trabalhar com grupos de gerenciamento](./media/main.png)

1. Preencha o campo de ID do grupo de gerenciamento.

   - **ID do Grupo de Gerenciamento** é o identificador exclusivo do diretório usado para enviar comandos nesse grupo de gerenciamento. Esse identificador não é editável após a criação, visto que é usado em todo o sistema do Azure para identificar esse grupo. O [grupo de gerenciamento raiz](overview.md#root-management-group-for-each-directory) é criado automaticamente com uma ID que é a id de Azure Active Directory. Para todos os outros grupos de gerenciamento, atribua uma ID exclusiva.
   - O campo de nome de exibição é o nome exibido no portal do Azure. Um nome de exibição separado é um campo opcional ao criar o gerenciamento de grupo e pode ser alterado a qualquer momento.  

   ![Painel de opções para criar um novo grupo de gerenciamento](./media/create_context_menu.png)  

1. Clique em **Salvar**.

### <a name="create-in-powershell"></a>Criar no PowerShell

Para o PowerShell, use o cmdlet [New-AzManagementGroup](/powershell/module/az.resources/new-azmanagementgroup) para criar um novo grupo de gerenciamento.

```azurepowershell-interactive
New-AzManagementGroup -GroupName 'Contoso'
```

**GroupName** é um identificador exclusivo que está sendo criado. Essa ID é usada por outros comandos para fazer referência a esse grupo e não pode ser alterada posteriormente.

Se você quiser que o grupo de gerenciamento mostre um nome diferente dentro do portal do Azure, adicione o parâmetro **DisplayName** . Por exemplo, para criar um grupo de gerenciamento com o GroupName de contoso e o nome de exibição de "grupo contoso", use o seguinte cmdlet:

```azurepowershell-interactive
New-AzManagementGroup -GroupName 'Contoso' -DisplayName 'Contoso Group'
```

Nos exemplos anteriores, o novo grupo de gerenciamento é criado no grupo de gerenciamento raiz. Para especificar um grupo de gerenciamento diferente como pai, use o parâmetro **parentID** .

```azurepowershell-interactive
$parentGroup = Get-AzManagementGroup -GroupName Contoso
New-AzManagementGroup -GroupName 'ContosoSubGroup' -ParentId $parentGroup.id
```

### <a name="create-in-azure-cli"></a>Criar na CLI do Azure

Para CLI do Azure, use o comando [AZ Account Management-Group Create](/cli/azure/account/management-group?view=azure-cli-latest#az-account-management-group-create) para criar um novo grupo de gerenciamento.

```azurecli-interactive
az account management-group create --name Contoso
```

O **nome** é um identificador exclusivo que está sendo criado. Essa ID é usada por outros comandos para fazer referência a esse grupo e não pode ser alterada posteriormente.

Se você quiser que o grupo de gerenciamento mostre um nome diferente dentro do portal do Azure, adicione o parâmetro **Display-Name** . Por exemplo, para criar um grupo de gerenciamento com o GroupName de contoso e o nome de exibição de "grupo contoso", use o seguinte comando:

```azurecli-interactive
az account management-group create --name Contoso --display-name 'Contoso Group'
```

Nos exemplos anteriores, o novo grupo de gerenciamento é criado no grupo de gerenciamento raiz. Para especificar um grupo de gerenciamento diferente como pai, use o parâmetro **pai** e forneça o nome do grupo pai.

```azurecli-interactive
az account management-group create --name ContosoSubGroup --parent Contoso
```

## <a name="next-steps"></a>Próximos passos

Para saber mais sobre grupos de gerenciamento, consulte:

- [Criar grupos de gerenciamento para organizar recursos do Azure](create.md)
- [Como alterar, excluir ou gerenciar seus grupos de gerenciamento](manage.md)
- [Analisar grupos de gerenciamento no Módulo de Recursos do Azure PowerShell](/powershell/module/az.resources#resources)
- [Revisar grupos de gerenciamento na API REST](/rest/api/resources/managementgroups)
- [Revisar grupos de gerenciamento na CLI do Azure](/cli/azure/account/management-group)
