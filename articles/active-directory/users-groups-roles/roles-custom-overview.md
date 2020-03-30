---
title: Funções de administrador personalizado no Diretório Ativo do Azure | Microsoft Docs
description: Visualizar funções personalizadas do Azure AD para delegar o gerenciamento de identidade. Gerencie as funções do Azure no portal Azure, PowerShell ou API do Graph.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: e5c7919dcc89e34831cb4cae7921b60b35eb4c69
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74024959"
---
# <a name="custom-administrator-roles-in-azure-active-directory-preview"></a>Funções de administrador personalizado no Azure Active Directory (visualização)

Este artigo descreve como entender as funções personalizadas do Azure AD no Azure Active Directory (Azure AD) com controles de acesso baseados em funções e escopos de recursos. As funções Personalizadas do Azure AD superam as permissões subjacentes das [funções incorporadas,](directory-assign-admin-roles.md)para que você possa criar e organizar suas próprias funções personalizadas. Esta abordagem permite que você conceda acesso de uma forma mais granular do que papéis incorporados, sempre que eles são necessários. Esta primeira versão das funções personalizadas do Azure AD inclui a capacidade de criar uma função para atribuir permissões para gerenciar registros de aplicativos. Com o tempo, permissões adicionais para recursos da organização, como aplicativos corporativos, usuários e dispositivos serão adicionadas.  

Além disso, as funções personalizadas do Azure AD suportam atribuições por recurso, além das atribuições mais tradicionais em toda a organização. Essa abordagem lhe dá a capacidade de conceder acesso para gerenciar alguns recursos (por exemplo, um registro de aplicativo) sem dar acesso a todos os recursos (todos os registros de aplicativos).

O controle de acesso baseado em função Azure AD é um recurso de visualização pública do Azure AD e está disponível com qualquer plano de licença ad pago do Azure. Para saber mais sobre versões prévias, consulte os [Termos de Uso Complementares para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="understand-azure-ad-role-based-access-control"></a>Entenda o controle de acesso baseado em função do Azure AD

A concessão de permissão usando funções Ad personalizadas do Azure é um processo de duas etapas que envolve criar uma definição de função personalizada e, em seguida, atribuí-la usando uma atribuição de função. Uma definição de função personalizada é uma coleção de permissões que você adiciona de uma lista predefinida. Essas permissões são as mesmas permissões usadas nas funções incorporadas.  

Depois de criar sua definição de função, você pode atribuí-la a um usuário criando uma atribuição de função. Uma atribuição de função concede ao usuário as permissões em uma definição de função em um escopo especificado. Este processo de duas etapas permite criar uma definição de função única e atribuí-la muitas vezes em diferentes escopos. Um escopo define o conjunto de recursos Azure AD aos quais o membro da função tem acesso. O escopo mais comum é o escopo de toda a organização (org-wide). Uma função personalizada pode ser atribuída em escopo de abrangência org, o que significa que o membro da função tem as permissões de função sobre todos os recursos da organização. Uma função personalizada também pode ser atribuída em um escopo de objeto. Um exemplo de escopo de objeto seria um único aplicativo. A mesma função pode ser atribuída a um usuário em todos os aplicativos da organização e, em seguida, a outro usuário com um escopo apenas do aplicativo Contoso Expense Reports.  

As funções incorporadas e personalizadas do Azure AD operam em conceitos semelhantes ao [controle de acesso baseado em função do Azure.](../../role-based-access-control/overview.md) A [diferença entre esses dois sistemas de controle de acesso baseados em função](../../role-based-access-control/rbac-and-directory-admin-roles.md) é que o Azure RBAC controla o acesso aos recursos do Azure, como máquinas virtuais ou armazenamento usando o Azure Resource Management, e as funções personalizadas do Azure AD controlam o acesso aos recursos do Azure AD usando a API do Graph. Ambos os sistemas aproveitam o conceito de definições de papéis e atribuições de papéis.

### <a name="role-assignments"></a>Atribuições de função

Uma atribuição de função é o objeto que anexa uma definição de função a um usuário em um escopo específico para conceder acesso a recursos Azure AD. O acesso é concedido criando uma atribuição de função, e é revogado removendo uma atribuição de função. Em sua essência, uma atribuição de função consiste em três elementos:

- Usuário (um indivíduo que tem um perfil de usuário no Azure Active Directory)
- Definição de função
- Escopo de recursos

Você pode [criar atribuições de função](roles-create-custom.md) usando o portal Azure, Azure AD PowerShell ou API do Graph. Você também pode [visualizar as atribuições de uma função personalizada](roles-view-assignments.md#view-the-assignments-of-a-role).

O diagrama a seguir mostra um exemplo de uma atribuição de função. Neste exemplo, Chris Green foi designado para a função personalizada do administrador de registro do aplicativo no escopo do registro do aplicativo Contoso Widget Builder. A atribuição concede a Chris as permissões da função de administrador de registro do App apenas para este registro específico do aplicativo.

![Atribuição de função é como as permissões são aplicadas e tem três partes](./media/roles-custom-overview/rbac-overview.png)

### <a name="security-principal"></a>Entidade de segurança

Um principal de segurança representa o usuário que deve ter acesso aos recursos do Azure AD. Um *usuário* é um indivíduo que tem um perfil de usuário no Azure Active Directory.

### <a name="role"></a>Função

Uma definição de função, ou papel, é uma coleção de permissões. Uma definição de função lista as operações que podem ser executadas nos recursos do Azure AD, como criar, ler, atualizar e excluir. Existem dois tipos de funções no Azure AD:

- Funções incorporadas criadas pela Microsoft que não podem ser alteradas.
- Funções personalizadas criadas e gerenciadas pela sua organização.

### <a name="scope"></a>Escopo

Um escopo é a restrição de ações permitidas a um determinado recurso Azure AD como parte de uma atribuição de função. Quando você atribui uma função, você pode especificar um escopo que limita o acesso do administrador a um recurso específico. Por exemplo, se você quiser conceder a um desenvolvedor uma função personalizada, mas apenas para gerenciar um registro específico de aplicativo, você pode incluir o registro específico do aplicativo como um escopo na atribuição da função.

  > [!Note]
  > As funções personalizadas podem ser atribuídas no escopo do diretório e no escopo dos recursos. Eles ainda não podem ser atribuídos no escopo da Unidade Administrativa.
  > As funções incorporadas podem ser atribuídas no escopo do diretório e, em alguns casos, no escopo da Unidade Administrativa. Eles ainda não podem ser atribuídos no escopo de recursos do Azure AD.

## <a name="required-license-plan"></a>Plano de licença necessário

[!INCLUDE [License requirement for using custom roles in Azure AD](../../../includes/active-directory-p1-license.md)]

## <a name="next-steps"></a>Próximas etapas

- Crie atribuições de função personalizadas usando [o portal Azure, o Azure AD PowerShell e a API do Graph](roles-create-custom.md)
- [Exibir as atribuições de uma função personalizada](roles-view-assignments.md#view-assignments-of-single-application-scope)
