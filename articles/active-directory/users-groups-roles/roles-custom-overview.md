---
title: Funções de administrador personalizadas no Azure Active Directory | Microsoft Docs
description: Saiba como entender as funções personalizadas do Azure AD no Azure AD (Azure Active Directory) com o controle de acesso baseado em funções e escopos de recursos.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: overview
ms.date: 09/11/2020
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: aac8713affd56d011e5e1f5e9326de501fb3ce67
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/05/2020
ms.locfileid: "90975561"
---
# <a name="custom-administrator-roles-in-azure-active-directory-preview"></a>Funções de administrador personalizadas no Azure Active Directory (versão prévia)

Este artigo descreve como entender as funções personalizadas do Azure AD no Azure AD (Azure Active Directory) com o controle de acesso baseado em funções e escopos de recursos. As funções personalizadas do Azure AD expõem as permissões subjacentes das [funções internas](directory-assign-admin-roles.md), para que você possa criar e organizar suas próprias funções personalizadas. Essa abordagem permite que você conceda acesso de modo mais granular do que as funções internas, sempre que elas forem necessárias. Esta primeira versão das funções personalizadas do Azure AD inclui a capacidade de criar uma função para atribuir permissões para gerenciar registros de aplicativo. Ao longo do tempo, permissões adicionais para recursos da organização, como aplicativos empresariais, usuários e dispositivos, serão adicionadas.  

Além disso, as funções personalizadas do Azure AD dão suporte a atribuições por recurso, além das atribuições mais tradicionais em toda a organização. Essa abordagem oferece a capacidade de permitir acesso para gerenciar alguns recursos (por exemplo, um registro de aplicativo) sem conceder acesso a todos os recursos (todos os registros de aplicativo).

O controle de acesso baseado em função do Azure AD é uma versão prévia do recurso pública do Azure AD e está disponível com qualquer plano de licença pago do Azure AD. Para saber mais sobre versões prévias, consulte os [Termos de Uso Complementares para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="understand-azure-ad-role-based-access-control"></a>Noções básicas sobre o controle de acesso baseado em função do Azure AD

Conceder permissão usando funções personalizadas do Azure AD é um processo de duas etapas que envolve a criação de uma definição de função personalizada e, em seguida, a atribuição dela usando uma atribuição de função. Uma definição de função personalizada é uma coleção de permissões que podem ser adicionadas de uma lista predefinida. Essas permissões são as mesmas permissões usadas nas funções internas.  

Após criar sua definição de função, você poderá atribuí-la a um usuário criando uma atribuição de função. Uma atribuição de função concede ao usuário as permissões em uma definição de função em um escopo especificado. Esse processo de duas etapas permite criar uma única definição de função e atribuí-la muitas vezes em escopos diferentes. Um escopo define o conjunto de recursos do Azure AD ao qual o membro da função tem acesso. O escopo mais comum é o escopo de toda a organização (toda a empresa). Uma função personalizada pode ser atribuída no escopo de toda a organização, o que significa que o membro da função tem as permissões de função sobre todos os recursos na empresa. Uma função personalizada também pode ser atribuída em um escopo de objeto. Um exemplo de escopo de objeto seria um único aplicativo. A mesma função pode ser atribuída a um usuário em todos os aplicativos da organização e, em seguida, a outro usuário com um escopo apenas do aplicativo de Relatórios de Despesas da Contoso.  

As funções internas e personalizadas do Azure AD operam em conceitos semelhantes ao [Azure RBAC (controle de acesso baseado em função do Azure)](../../role-based-access-control/overview.md). A [diferença entre esses dois sistemas de controle de acesso baseado em função](../../role-based-access-control/rbac-and-directory-admin-roles.md) é que o Azure RBAC controla o acesso aos recursos do Azure, como máquinas virtuais ou armazenamento usando o Gerenciamento de Recursos do Azure, e as funções personalizadas do Azure AD controlam o acesso aos recursos do Azure AD usando a API do Graph. Os dois sistemas utilizam o conceito de definições de função e atribuições de função.

### <a name="how-azure-ad-determines-if-a-user-has-access-to-a-resource"></a>Como o Azure AD determina se um usuário tem acesso a um recurso

Veja a seguir as etapas de alto nível que o Azure AD usa para determinar se você tem acesso a um recurso de gerenciamento. Use essas informações para solucionar problemas de acesso.

1. Um usuário (ou entidade de serviço) adquire um token para o ponto de extremidade do Microsoft Graph ou do Azure AD Graph.

1. O usuário faz uma chamada à API para o Azure AD (Azure Active Directory) por meio do Microsoft Graph ou do Azure AD Graph usando o token emitido.

1. Dependendo da circunstância, o Azure AD usa uma das seguintes ações:

    - Avalia as associações de função do usuário com base na [declaração de wids](../develop/access-tokens.md) no token de acesso do usuário.
    - Recupera todas as atribuições de função que se aplicam para usuário, diretamente ou por meio da associação de grupo, ao recurso no qual a ação está sendo executada.

1. O Azure AD determina se a ação na chamada à API está incluída nas funções que o usuário tem para este recurso.
1. Se o usuário não tem uma função com a ação no escopo solicitado, o acesso não é concedido. Caso contrário, o acesso será permitido.

### <a name="role-assignments"></a>Atribuições de função

Uma atribuição de função é o objeto que anexa uma definição de função a um usuário em um escopo específico para conceder acesso de recurso do Azure AD. O acesso é concedido criando uma atribuição de função, e é revogado removendo uma atribuição de função. Em essência, uma atribuição de função consiste em três elementos:

- Usuário (um indivíduo que tem um perfil do usuário no Azure Active Directory)
- Definição de função
- Escopo do recurso

Você pode [criar atribuições de função](roles-create-custom.md) usando o portal do Azure, o Azure AD PowerShell ou a API do Graph. Você também pode [exibir as atribuições de uma função personalizada](roles-view-assignments.md#view-the-assignments-of-a-role).

O diagrama a seguir mostra um exemplo de uma atribuição de função. Neste exemplo, foi atribuída a Chris Green a função personalizada de Administrador de registro de aplicativo no escopo do registro de aplicativo do Construtor de Widgets da Contoso. A atribuição concede a Chris as permissões da função de Administrador de registro de aplicativo apenas para esse registro de aplicativo específico.

![A atribuição de função é como as permissões são impostas e tem três partes](./media/roles-custom-overview/rbac-overview.png)

### <a name="security-principal"></a>Entidade de segurança

A entidade de segurança representa o usuário que deve receber acesso aos recursos do Azure AD. Um *usuário* é um indivíduo que tem um perfil do usuário no Azure Active Directory.

### <a name="role"></a>Função

Uma definição de função, ou função, é um conjunto de permissões. Uma definição de função lista as operações que podem ser executadas em recursos do Azure AD, como criação, leitura, atualização e exclusão. Há dois tipos de funções no Azure AD:

- Funções internas criadas pela Microsoft que não podem ser alteradas.
- Funções personalizadas criadas e gerenciadas pela sua organização.

### <a name="scope"></a>Escopo

Um escopo é a restrição de ações permitidas a um recurso específico do Azure AD como parte de uma atribuição de função. Ao atribuir uma função, você pode especificar um escopo que limita o acesso do administrador a um recurso específico. Por exemplo, se desejar conceder uma função personalizada a um desenvolvedor, mas apenas para gerenciar um registro de aplicativo específico, você poderá incluir o registro de aplicativo específico como um escopo na atribuição de função.

  > [!Note]
  > Funções personalizadas podem ser atribuídas no escopo do diretório e no escopo do recurso. Elas ainda não podem ser atribuídas no escopo da Unidade Administrativa.
  > As funções internas podem ser atribuídas no escopo do diretório e, em alguns casos, no escopo da Unidade Administrativa. Elas ainda não podem ser atribuídas no escopo do recurso do Azure AD.

## <a name="required-license-plan"></a>Plano de licença necessária

[!INCLUDE [License requirement for using custom roles in Azure AD](../../../includes/active-directory-p1-license.md)]

## <a name="next-steps"></a>Próximas etapas

- Criar atribuições de função personalizada usando [o portal do Azure, o Azure AD PowerShell e a API do Graph](roles-create-custom.md)
- [Exibir as atribuições de uma função personalizada](roles-view-assignments.md#view-assignments-of-single-application-scope)