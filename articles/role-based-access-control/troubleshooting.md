---
title: Solucionar problemas do RBAC do Azure
description: Solucionar problemas com o Azure RBAC (controle de acesso baseado em função).
services: azure-portal
documentationcenter: na
author: rolyon
manager: mtillman
ms.assetid: df42cca2-02d6-4f3c-9d56-260e1eb7dc44
ms.service: role-based-access-control
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/18/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: seohack1
ms.openlocfilehash: 6baa83037d51e850a9f3535be3cc365e7c35e0a4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82131444"
---
# <a name="troubleshoot-azure-rbac"></a>Solucionar problemas do RBAC do Azure

Este artigo responde a algumas perguntas comuns sobre o controle de acesso baseado em função do Azure (RBAC do Azure), para que você saiba o que esperar ao usar as funções e pode solucionar problemas de acesso.

## <a name="azure-role-assignments-limit"></a>Limite de atribuições de função do Azure

O Azure dá suporte a até **2000** atribuições de função por assinatura. Se você receber a mensagem de erro "não é possível criar mais atribuições de função (código: RoleAssignmentLimitExceeded)" ao tentar atribuir uma função, tente reduzir o número de atribuições de função na assinatura.

> [!NOTE]
> O limite de atribuições de função **2000** por assinatura é fixo e não pode ser aumentado.

Se você estiver se aproximando desse limite, aqui estão algumas maneiras de reduzir o número de atribuições de função:

- Adicione usuários a grupos e atribua funções aos grupos em vez disso. 
- Combine várias funções internas com uma função personalizada. 
- Faça atribuições de função comuns em um escopo mais alto, como assinatura ou grupo de gerenciamento.
- Se você tiver Azure AD Premium P2, torne as atribuições de função qualificadas em [Azure ad Privileged Identity Management](../active-directory/privileged-identity-management/pim-configure.md) em vez de atribuídas permanentemente. 
- Adicione uma assinatura adicional. 

Para obter o número de atribuições de função, você pode exibir o [gráfico na página de controle de acesso (iam)](role-assignments-list-portal.md#list-number-of-role-assignments) no portal do Azure. Você também pode usar os seguintes comandos de Azure PowerShell:

```azurepowershell
$scope = "/subscriptions/<subscriptionId>"
$ras = Get-AzRoleAssignment -Scope $scope | Where-Object {$_.scope.StartsWith($scope)}
$ras.Count
```

## <a name="problems-with-azure-role-assignments"></a>Problemas com as atribuições de função do Azure

- Se não for possível adicionar uma atribuição de função no portal do Azure no **controle de acesso (iam)** porque a opção **Adicionar** > **Adicionar atribuição de função** está desabilitada ou porque você recebe o erro de permissões "o cliente com a ID de objeto não tem autorização para executar a ação", verifique se você está conectado no momento com um usuário que recebe uma `Microsoft.Authorization/roleAssignments/write` função que tem a permissão como [proprietário](built-in-roles.md#owner) ou administrador de [acesso do usuário](built-in-roles.md#user-access-administrator) no escopo ao qual você está tentando atribuir a função.

## <a name="problems-with-custom-roles"></a>Problemas com funções personalizadas

- Se você precisar de etapas para criar uma função personalizada, consulte os tutoriais de função personalizada usando o [portal do Azure](custom-roles-portal.md) (atualmente em visualização), [Azure PowerShell](tutorial-custom-role-powershell.md)ou [CLI do Azure](tutorial-custom-role-cli.md).
- Se não for possível atualizar uma função personalizada existente, verifique se você está conectado no momento com um usuário que recebe uma função que tem a `Microsoft.Authorization/roleDefinition/write` permissão como [proprietário](built-in-roles.md#owner) ou administrador de acesso do [usuário](built-in-roles.md#user-access-administrator).
- Se não for possível excluir uma função personalizada e receber a mensagem de erro "há atribuições de função existentes que fazem referência à função (código: RoleDefinitionHasAssignments)", há atribuições de função que ainda usam a função personalizada. Remova essas atribuições de função e tente excluir a função personalizada novamente.
- Se você receber a mensagem de erro "Limite de definição de função excedido. Não é possível criar mais definições de função (código: RoleDefinitionLimitExceeded) "quando você tenta criar uma nova função personalizada, exclua todas as funções personalizadas que não estão sendo usadas. O Azure dá suporte a até **5000** funções personalizadas em um diretório. (Para Azure Alemanha e Azure China 21Vianet, o limite é de 2000 funções personalizadas.)
- Se você receber um erro semelhante a "o cliente tem permissão para executar a ação ' Microsoft. Authorization/roleDefinitions/Write ' no escopo '/subscriptions/{SubscriptionId} ', no entanto, a assinatura vinculada não foi encontrada" quando você tentar atualizar uma função personalizada, verifique se um ou mais [escopos atribuíveis](role-definitions.md#assignablescopes) foram excluídos no diretório. Se o escopo tiver sido excluído, crie um tíquete de suporte, pois não há nenhuma solução de autoatendimento disponível no momento.

## <a name="custom-roles-and-management-groups"></a>Funções personalizadas e grupos de gerenciamento

- Você só pode definir um grupo de gerenciamento `AssignableScopes` em uma função personalizada. A adição de um grupo `AssignableScopes` de gerenciamento ao está em visualização no momento.
- Funções personalizadas com `DataActions` não podem ser atribuídas no escopo do grupo de gerenciamento.
- Azure Resource Manager não valida a existência do grupo de gerenciamento no escopo atribuível da definição de função.
- Para obter mais informações sobre funções personalizadas e grupos de gerenciamento, consulte [organizar seus recursos com grupos de gerenciamento do Azure](../governance/management-groups/overview.md#custom-rbac-role-definition-and-assignment).

## <a name="transferring-a-subscription-to-a-different-directory"></a>Transferindo uma assinatura para um diretório diferente

- Se você precisar de etapas sobre como transferir uma assinatura para um diretório diferente do Azure AD, consulte [transferir a propriedade de uma assinatura do Azure para outra conta](../cost-management-billing/manage/billing-subscription-transfer.md).
- Se você transferir uma assinatura para um diretório diferente do Azure AD, todas as atribuições de função serão excluídas **permanentemente** do diretório do Azure AD de origem e não serão migradas para o diretório de destino do Azure AD. Você deve recriar as atribuições de função no diretório de destino. Você também precisa recriar manualmente as identidades gerenciadas dos recursos do Azure. Para obter mais informações, consulte [perguntas frequentes e problemas conhecidos com identidades gerenciadas](../active-directory/managed-identities-azure-resources/known-issues.md).
- Se você for um administrador global do Azure AD e não tiver acesso a uma assinatura após sua transferência entre os diretórios, use a opção **Gerenciamento de acesso para recursos do Azure** para [elevar temporariamente seu acesso](elevate-access-global-admin.md) para obter acesso à assinatura.

## <a name="issues-with-service-admins-or-co-admins"></a>Problemas com os administradores de serviço ou coadministradores

- Se você estiver tendo problemas com o administrador de serviços ou coadministradores, consulte [Adicionar ou alterar administradores de assinatura do Azure](../cost-management-billing/manage/add-change-subscription-administrator.md) e [funções de administrador de assinatura clássica, funções do Azure e funções de administrador do Azure ad](rbac-and-directory-admin-roles.md).

## <a name="access-denied-or-permission-errors"></a>Acesso negado ou erros de permissão

- Se você receber o erro de permissões "o cliente com a ID de objeto não tem autorização para executar a ação sobre o escopo (código: AuthorizationFailed)" ao tentar criar um recurso, verifique se você está conectado no momento com um usuário que recebe uma função que tem permissão de gravação para o recurso no escopo selecionado. Por exemplo, para gerenciar máquinas virtuais em um grupo de recursos, você deverá ter a função [Colaborador da Máquina Virtual](built-in-roles.md#virtual-machine-contributor) no grupo de recursos (ou escopo pai). Para obter uma lista das permissões de cada função interna, confira [Funções internas para recursos do Azure](built-in-roles.md).
- Se você receber o erro de permissões "você não tem permissão para criar uma solicitação de suporte" ao tentar criar ou atualizar um tíquete de suporte, verifique se você está conectado no momento com um usuário que recebe uma função que tem a `Microsoft.Support/supportTickets/write` permissão, como [colaborador de solicitação de suporte](built-in-roles.md#support-request-contributor).

## <a name="role-assignments-with-unknown-security-principal"></a>Atribuições de função com entidade de segurança desconhecida

Se você atribuir uma função a uma entidade de segurança (usuário, grupo, entidade de serviço ou identidade gerenciada) e, posteriormente, excluir essa entidade de segurança sem remover a atribuição de função, o tipo de entidade de segurança para a atribuição de função será listado como **desconhecido**. A captura de tela a seguir mostra um exemplo no portal do Azure. O nome da entidade de segurança está listado como **identidade excluída** e a **identidade não existe mais**. 

![Grupo de recursos do aplicativo Web](./media/troubleshooting/unknown-security-principal.png)

Se você listar essa atribuição de função usando Azure PowerShell, verá um vazio `DisplayName` e um `ObjectType` definido como desconhecido. Por exemplo, [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment) retorna uma atribuição de função semelhante à seguinte:

```
RoleAssignmentId   : /subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Authorization/roleAssignments/22222222-2222-2222-2222-222222222222
Scope              : /subscriptions/11111111-1111-1111-1111-111111111111
DisplayName        :
SignInName         :
RoleDefinitionName : Storage Blob Data Contributor
RoleDefinitionId   : ba92f5b4-2d11-453d-a403-e96b0029c9fe
ObjectId           : 33333333-3333-3333-3333-333333333333
ObjectType         : Unknown
CanDelegate        : False
```

Da mesma forma, se você listar essa atribuição de função usando CLI do Azure, verá `principalName`um vazio. Por exemplo, a [lista de atribuição de função AZ](/cli/azure/role/assignment#az-role-assignment-list) retorna uma atribuição de função semelhante à seguinte:

```
{
    "canDelegate": null,
    "id": "/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Authorization/roleAssignments/22222222-2222-2222-2222-222222222222",
    "name": "22222222-2222-2222-2222-222222222222",
    "principalId": "33333333-3333-3333-3333-333333333333",
    "principalName": "",
    "roleDefinitionId": "/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Authorization/roleDefinitions/ba92f5b4-2d11-453d-a403-e96b0029c9fe",
    "roleDefinitionName": "Storage Blob Data Contributor",
    "scope": "/subscriptions/11111111-1111-1111-1111-111111111111",
    "type": "Microsoft.Authorization/roleAssignments"
}
```

Não é um problema deixar essas atribuições de função, mas você pode removê-las usando etapas semelhantes a outras atribuições de função. Para obter informações sobre como remover atribuições de função, consulte [portal do Azure](role-assignments-portal.md#remove-a-role-assignment), [Azure PowerShell](role-assignments-powershell.md#remove-a-role-assignment)ou [CLI do Azure](role-assignments-cli.md#remove-a-role-assignment)

No PowerShell, se você tentar remover as atribuições de função usando a ID de objeto e o nome de definição de função, e mais de uma atribuição de função corresponder aos parâmetros, você receberá a mensagem de erro: "as informações fornecidas não são mapeadas para uma atribuição de função". Veja a seguir um exemplo da mensagem de erro:

```
PS C:\> Remove-AzRoleAssignment -ObjectId 33333333-3333-3333-3333-333333333333 -RoleDefinitionName "Storage Blob Data Contributor"

Remove-AzRoleAssignment : The provided information does not map to a role assignment.
At line:1 char:1
+ Remove-AzRoleAssignment -ObjectId 33333333-3333-3333-3333-333333333333 ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
+ CategoryInfo          : CloseError: (:) [Remove-AzRoleAssignment], KeyNotFoundException
+ FullyQualifiedErrorId : Microsoft.Azure.Commands.Resources.RemoveAzureRoleAssignmentCommand
```

Se você receber essa mensagem de erro, certifique-se de também `-Scope` especificar `-ResourceGroupName` os parâmetros ou.

```
PS C:\> Remove-AzRoleAssignment -ObjectId 33333333-3333-3333-3333-333333333333 -RoleDefinitionName "Storage Blob Data Contributor" - Scope /subscriptions/11111111-1111-1111-1111-111111111111
```

## <a name="role-assignment-changes-are-not-being-detected"></a>As alterações de atribuição de função não estão sendo detectadas

Às vezes, o Azure Resource Manager armazena em cache configurações e dados para melhorar o desempenho. Quando você adiciona ou remove atribuições de função, pode levar até 30 minutos para que as alterações entrem em vigor. Se estiver usando o portal do Azure, o Azure PowerShell ou a CLI do Azure, será possível forçar uma atualização das alterações de atribuição de função, saindo e entrando novamente. Se estiver fazendo alterações de atribuição de função com chamadas à API REST, poderá forçar uma atualização atualizando o token de acesso.

Se você for adicionar ou remover uma atribuição de função no escopo do grupo de gerenciamento e `DataActions`a função tiver, o acesso no plano de dados poderá não ser atualizado por várias horas. Isso se aplica somente ao escopo do grupo de gerenciamento e ao plano de dados.

## <a name="web-app-features-that-require-write-access"></a>Recursos de aplicativo Web que exigem acesso para gravação

Se você conceder a um usuário o acesso somente leitura a um aplicativo Web, para sua surpresa, alguns recursos estarão desabilitados. Os seguintes recursos de gerenciamento exigem acesso de **gravação** a um aplicativo Web (colaborador ou proprietário) e não estão disponíveis em nenhum cenário somente leitura.

* Comandos (como iniciar, parar, etc.)
* Alterar configurações como configuração geral, configurações de escala, configurações de backup e configurações de monitoramento.
* Acessar credenciais de publicação e outros segredos como configurações de aplicativos e cadeias de conexão.
* Logs de streaming
* Configuração de logs de recursos
* Console (prompt de comando)
* Ativo e implantações recentes (para a implantação contínua do git local)
* Gasto estimado
* Testes da Web
* Rede virtual (somente visível para um leitor se uma rede virtual foi anteriormente configurada por um usuário com acesso para gravação).

Se você não conseguir acessar nenhum desses blocos, precisará solicitar ao administrador o acesso de Colaborador ao aplicativo Web.

## <a name="web-app-resources-that-require-write-access"></a>Recursos de aplicativos Web que exigem acesso para gravação

Os aplicativos Web são complicados pela presença de alguns recursos diferentes que interagem. Aqui está um grupo de recursos típico com alguns sites:

![Grupo de recursos do aplicativo Web](./media/troubleshooting/website-resource-model.png)

Como resultado, se você conceder a alguém o acesso somente ao aplicativo Web, muitas das funcionalidades na folha do site no portal do Azure estarão desabilitadas.

Estes itens exigem acesso para **gravação** no **Plano do Serviço de Aplicativo** que corresponde ao seu site:  

* Exibindo o tipo de preço do aplicativo Web (Gratuito ou Standard)  
* Configuração de escala (número instâncias, tamanho da máquina virtual, configurações de escalonamento automático)  
* Cotas (armazenamento, largura de banda, CPU)  

Estes itens exigem acesso para **gravação** no **Grupo de recursos** inteiro que contém o seu site:  

* Certificados e associações TLS/SSL (certificados TLS/SSL podem ser compartilhados entre sites no mesmo grupo de recursos e localização geográfica)  
* Regras de alerta  
* Configurações de autoescala  
* Componentes do Application insights  
* Testes da Web  

## <a name="virtual-machine-features-that-require-write-access"></a>Recursos da máquina virtual que exigem acesso para gravação

Semelhante aos aplicativos Web, alguns recursos na folha da máquina virtual exigem acesso para gravação à máquina virtual ou a outros recursos no grupo de recursos.

As máquinas virtuais são relacionadas a nomes de domínio, redes virtuais, contas de armazenamento e regras de alerta.

Estes itens exigem acesso para **gravação** na **Máquina virtual**:

* Pontos de extremidade  
* Endereços IP  
* Discos  
* Extensões  

Estes exigem acesso para **gravação** tanto na **Máquina virtual** quanto no **Grupo de recursos** (juntamente com o Nome de domínio) encontrados em:  

* Conjunto de disponibilidade  
* Conjunto de balanceamento de carga  
* Regras de alerta  

Se você não conseguir acessar nenhum desses blocos, solicite ao administrador o acesso de Colaborador ao Grupo de recursos.

## <a name="azure-functions-and-write-access"></a>Azure Functions e acesso para gravação

Alguns recursos do [Azure Functions](../azure-functions/functions-overview.md) exigem acesso de gravação. Por exemplo, se uma função de [leitor](built-in-roles.md#reader) for atribuída a um usuário, ela não poderá exibir as funções em um aplicativo de funções. O portal exibirá **(Sem acesso)**.

![Aplicativos de funções sem acesso](./media/troubleshooting/functionapps-noaccess.png)

Um leitor pode clicar na guia **Recursos da plataforma** e, em seguida, clicar em **Todas as configurações** para exibir algumas configurações relacionadas a um aplicativo de funções (semelhante a um aplicativo Web), mas não pode modificar essas configurações. Para acessar esses recursos, você precisará da função de [colaborador](built-in-roles.md#contributor) .

## <a name="next-steps"></a>Próximas etapas

- [Solucionar problemas de usuários convidados](role-assignments-external-users.md#troubleshoot)
- [Gerenciar o acesso aos recursos do Azure usando o RBAC e o portal do Azure](role-assignments-portal.md)
- [Exibir logs de atividades para alterações de RBAC para recursos do Azure](change-history-report.md)
