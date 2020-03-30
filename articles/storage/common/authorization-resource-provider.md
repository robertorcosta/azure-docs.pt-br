---
title: Use o provedor de recursos do Azure Storage para acessar recursos de gerenciamento
description: O provedor de recursos Do Azure Storage é um serviço que fornece acesso aos recursos de gerenciamento para o Azure Storage. Você pode usar o provedor de recursos do Azure Storage para criar, atualizar, gerenciar e excluir recursos, como contas de armazenamento, pontos finais privados e chaves de acesso à conta.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 12/12/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: f5d42a6a0567d3949bc4b0fb1947450a9c957f18
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75972357"
---
# <a name="use-the-azure-storage-resource-provider-to-access-management-resources"></a>Use o provedor de recursos do Azure Storage para acessar recursos de gerenciamento

O Azure Resource Manager é p serviço de implantação e gerenciamento do Azure. O provedor de recursos do Azure Storage é um serviço baseado no Azure Resource Manager e que fornece acesso aos recursos de gerenciamento para o Azure Storage. Você pode usar o provedor de recursos do Azure Storage para criar, atualizar, gerenciar e excluir recursos, como contas de armazenamento, pontos finais privados e chaves de acesso à conta. Para obter mais informações sobre o Azure Resource Manager, consulte [a visão geral do Azure Resource Manager](/azure/azure-resource-manager/resource-group-overview).

Você pode usar o provedor de recursos do Azure Storage para executar ações como criar ou excluir uma conta de armazenamento ou obter uma lista de contas de armazenamento em uma assinatura. Para autorizar solicitações contra o provedor de recursos do Azure Storage, use o Azure Active Directory (Azure AD). Este artigo descreve como atribuir permissões aos recursos de gerenciamento e aponta exemplos que mostram como fazer solicitações contra o provedor de recursos do Azure Storage.

## <a name="management-resources-versus-data-resources"></a>Recursos de gestão versus recursos de dados

A Microsoft fornece duas APIs REST para trabalhar com recursos de armazenamento do Azure. Essas APIs formam a base de todas as ações que você pode executar contra o Azure Storage. A API Azure Storage REST permite que você trabalhe com dados em sua conta de armazenamento, incluindo blob, fila, arquivo e dados de tabela. O provedor de recursos do Azure Storage REST API permite que você trabalhe com a conta de armazenamento e recursos relacionados.

Uma solicitação que lê ou grava dados blob requer permissões diferentes de uma solicitação que executa uma operação de gerenciamento. O RBAC fornece controle fino sobre as permissões para ambos os tipos de recursos. Quando você atribuir uma função RBAC a um diretor de segurança, certifique-se de entender quais permissões esse principal será concedido. Para obter uma referência detalhada que descreva quais ações estão associadas a cada função RBAC incorporada, consulte [Funções incorporadas para recursos do Azure](../../role-based-access-control/built-in-roles.md).

O Azure Storage suporta o uso do Azure AD para autorizar solicitações contra o armazenamento Blob e Queue. Para obter informações sobre as funções rbac para operações de dados de blob e fila, consulte [Autorizar o acesso a bolhas e filas usando o Active Directory](storage-auth-aad.md).

## <a name="assign-management-permissions-with-role-based-access-control-rbac"></a>Atribuir permissões de gerenciamento com controle de acesso baseado em função (RBAC)

Cada assinatura do Azure tem um Diretório Ativo azure associado que gerencia usuários, grupos e aplicativos. Um usuário, grupo ou aplicativo também é referido como um princípio de segurança no contexto da plataforma de [identidade Microsoft](/azure/active-directory/develop/). Você pode conceder acesso a recursos em uma assinatura de um princípio de segurança definido no Active Directory usando o RBAC (Role-Based Access Control).

Quando você atribui uma função RBAC a um diretor de segurança, você também indica o escopo no qual as permissões concedidas pela função estão em vigor. Para operações de gerenciamento, você pode atribuir uma função ao nível da assinatura, do grupo de recursos ou da conta de armazenamento. Você pode atribuir uma função RBAC a um principal de segurança usando o [portal Azure,](https://portal.azure.com/)as [ferramentas Azure CLI,](../../cli-install-nodejs.md) [PowerShell](/powershell/azureps-cmdlets-docs)ou o [provedor de recursos de armazenamento Azure REST API](/rest/api/storagerp).

Para obter mais informações sobre o RBAC, consulte [O que é rbac (controle de acesso baseado em função) para recursos do Azure?](../../role-based-access-control/overview.md) e [funções clássicas de administrador de assinaturas, funções de Administrador Azure RBAC e administrador Azure AD](../../role-based-access-control/rbac-and-directory-admin-roles.md).

### <a name="built-in-roles-for-management-operations"></a>Funções incorporadas para operações de gerenciamento

O Azure fornece funções incorporadas que concedem permissões para chamadas de operações de gerenciamento. O Azure Storage também fornece funções incorporadas especificamente para uso com o provedor de recursos do Azure Storage.

Funções incorporadas que concedem permissões para chamadas de operações de gerenciamento de armazenamento incluem as funções descritas na tabela a seguir:

|    Função RBAC    |    Descrição    |    Inclui acesso às chaves da conta?    |
|---------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------|
| **Proprietário** | Pode gerenciar todos os recursos de armazenamento e acesso a recursos.  | Sim, fornece permissões para visualizar e regenerar as chaves da conta de armazenamento. |
| **Colaborador**  | Pode gerenciar todos os recursos de armazenamento, mas não pode gerenciar atribuir recursos. | Sim, fornece permissões para visualizar e regenerar as chaves da conta de armazenamento. |
| **Leitor** | Pode exibir informações sobre a conta de armazenamento, mas não é possível visualizar as chaves da conta. | Não. |
| **Contribuinte de conta de armazenamento** | Pode gerenciar a conta de armazenamento, obter informações sobre os grupos e recursos de recursos da assinatura e criar e gerenciar implantações de grupos de recursos de assinatura. | Sim, fornece permissões para visualizar e regenerar as chaves da conta de armazenamento. |
| **Administrador de Acesso do Usuário** | Pode gerenciar o acesso à conta de armazenamento.   | Sim, permite que um diretor de segurança atribua permissões a si mesmo si mesmo e aos outros. |
| **Colaborador de Máquina Virtual** | Pode gerenciar máquinas virtuais, mas não a conta de armazenamento à qual estão conectadas.   | Sim, fornece permissões para visualizar e regenerar as chaves da conta de armazenamento. |

A terceira coluna na tabela indica se a função incorporada suporta o **Microsoft.Storage/storageAccounts/listkeys/action**. Esta ação concede permissões para ler e regenerar as chaves da conta de armazenamento. As permissões para acessar os recursos de gerenciamento do Azure Storage também não incluem permissões para acessar dados. No entanto, se um usuário tiver acesso às chaves da conta, então ele pode usar as chaves da conta para acessar os dados do Azure Storage através da autorização de chave compartilhada.

### <a name="custom-roles-for-management-operations"></a>Funções personalizadas para operações de gerenciamento

O Azure também suporta a definição de funções personalizadas de RBAC para acesso a recursos de gerenciamento. Para obter mais informações sobre funções personalizadas, consulte [Funções personalizadas para os recursos do Azure](../../role-based-access-control/custom-roles.md).

## <a name="code-samples"></a>Exemplos de código

Para exemplos de código que mostram como autorizar e chamar operações de gerenciamento das bibliotecas de gerenciamento de armazenamento do Azure, consulte as seguintes amostras:

- [.NET](https://github.com/Azure-Samples/storage-dotnet-resource-provider-getting-started)
- [Java](https://github.com/Azure-Samples/storage-java-manage-storage-accounts)
- [Node.js](https://github.com/Azure-Samples/storage-node-resource-provider-getting-started)
- [Python](https://github.com/Azure-Samples/storage-python-manage)

## <a name="azure-resource-manager-versus-classic-deployments"></a>Azure Resource Manager versus implantações clássicas

O Gerenciador de Recursos e os modelos de implantação clássicos representam duas maneiras de implantar e gerenciar soluções do Azure. A Microsoft recomenda o uso do modelo de implantação do Azure Resource Manager quando você cria uma nova conta de armazenamento. Se possível, a Microsoft também recomenda que você recrie contas de armazenamento clássicas existentes com o modelo Resource Manager. Embora você possa criar uma conta de armazenamento usando o modelo clássico de implantação, o modelo clássico é menos flexível e eventualmente será preterido.

Para obter mais informações sobre os modelos de implantação do Azure, consulte [Gerenciador de recursos e implantação clássica](../../azure-resource-manager/management/deployment-models.md).

## <a name="next-steps"></a>Próximas etapas

- [Visão geral do Azure Resource Manager](/azure/azure-resource-manager/resource-group-overview)
- [O que é o RBAC (controle de acesso baseado em função) para recursos do Azure?](../../role-based-access-control/overview.md)
- [Metas de escalabilidade para o provedor de recursos do Azure Storage](scalability-targets-resource-provider.md)
