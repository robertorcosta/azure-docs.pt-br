---
title: Funções de administrador personalizadas no Azure Active Directory | Microsoft Docs
description: Visualize funções personalizadas do Azure AD para delegar o gerenciamento de identidades. Gerencie funções do Azure no portal do Azure, PowerShell ou API do Graph.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 04/22/2020
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: ae244d93d679199aaa0bd08891cd34d4ca3a2ddc
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82085103"
---
# <a name="custom-administrator-roles-in-azure-active-directory-preview"></a>Funções de administrador personalizadas no Azure Active Directory (versão prévia)

Este artigo descreve como entender as funções personalizadas do Azure AD no Azure Active Directory (Azure AD) com o controle de acesso baseado em funções e escopos de recursos. As funções personalizadas do Azure AD Surface as permissões subjacentes das [funções internas](directory-assign-admin-roles.md), para que você possa criar e organizar suas próprias funções personalizadas. Essa abordagem permite que você conceda acesso de forma mais granular do que as funções internas, sempre que elas forem necessárias. Esta primeira versão das funções personalizadas do Azure AD inclui a capacidade de criar uma função para atribuir permissões para gerenciar registros de aplicativo. Ao longo do tempo, permissões adicionais para recursos da organização, como aplicativos empresariais, usuários e dispositivos, serão adicionadas.  

Além disso, as funções personalizadas do Azure AD dão suporte a atribuições por recurso, além das atribuições mais tradicionais em toda a organização. Essa abordagem oferece a capacidade de conceder acesso para gerenciar alguns recursos (por exemplo, um registro de aplicativo) sem conceder acesso a todos os recursos (todos os registros de aplicativo).

O controle de acesso baseado em função do AD do Azure é um recurso de visualização pública do Azure AD e está disponível com qualquer plano de licença pago do Azure AD. Para saber mais sobre versões prévias, consulte os [Termos de Uso Complementares para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="understand-azure-ad-role-based-access-control"></a>Entender o controle de acesso baseado em função do Azure AD

Conceder permissão usando funções personalizadas do Azure AD é um processo de duas etapas que envolve a criação de uma definição de função personalizada e, em seguida, a atribuição dela usando uma atribuição de função. Uma definição de função personalizada é uma coleção de permissões que você adiciona de uma lista predefinida. Essas permissões são as mesmas permissões usadas nas funções internas.  

Depois de criar a definição de função, você pode atribuí-la a um usuário criando uma atribuição de função. Uma atribuição de função concede ao usuário as permissões em uma definição de função em um escopo especificado. Esse processo de duas etapas permite criar uma única definição de função e atribuí-la muitas vezes em escopos diferentes. Um escopo define o conjunto de recursos do Azure AD ao qual o membro da função tem acesso. O escopo mais comum é o escopo de toda a organização (toda a empresa). Uma função personalizada pode ser atribuída em escopo de toda a organização, o que significa que o membro da função tem as permissões de função sobre todos os recursos na empresa. Uma função personalizada também pode ser atribuída em um escopo de objeto. Um exemplo de escopo de objeto seria um único aplicativo. A mesma função pode ser atribuída a um usuário em todos os aplicativos da organização e, em seguida, a outro usuário com um escopo apenas do aplicativo de relatórios de despesas da contoso.  

As funções internas e personalizadas do Azure AD operam em conceitos semelhantes ao [controle de acesso baseado em função do Azure](../../role-based-access-control/overview.md). A [diferença entre esses dois sistemas de controle de acesso baseado em função](../../role-based-access-control/rbac-and-directory-admin-roles.md) é que o RBAC do Azure controla o acesso aos recursos do Azure, como máquinas virtuais ou armazenamento usando o gerenciamento de recursos do Azure, e as funções personalizadas do Azure ad controlam o acesso aos recursos do AD do azure usando API do Graph. Ambos os sistemas aproveitam o conceito de definições de função e atribuições de função.

### <a name="how-azure-ad-determines-if-a-user-has-access-to-a-resource"></a>Como o Azure AD determina se um usuário tem acesso a um recurso

A seguir estão as etapas de alto nível que o AD do Azure usa para determinar se você tem acesso a um recurso de gerenciamento. Use essas informações para solucionar problemas de acesso.

1. Um usuário (ou entidade de serviço) adquire um token para o Microsoft Graph ou o ponto de extremidade do Azure AD Graph.

1. O usuário faz uma chamada à API para Azure Active Directory (Azure AD) por meio do Microsoft Graph ou do Azure AD Graph usando o token emitido.

1. Dependendo da circunstância, o Azure AD executa uma das seguintes ações:

    - Avalia as associações de função do usuário com base na [declaração wids](https://docs.microsoft.com/azure/active-directory/develop/access-tokens) no token de acesso do usuário.
    - Recupera todas as atribuições de função que se aplicam ao usuário, diretamente ou por meio da Associação de grupo, ao recurso no qual a ação está sendo executada.

1. O Azure AD determina se a ação na chamada à API está incluída nas funções que o usuário tem para esse recurso.
1. Se o usuário não tem uma função com a ação no escopo solicitado, o acesso não é concedido. Caso contrário, o acesso será permitido.

### <a name="role-assignments"></a>Atribuições de função

Uma atribuição de função é o objeto que anexa uma definição de função a um usuário em um determinado escopo para conceder acesso aos recursos do Azure AD. O acesso é concedido criando uma atribuição de função, e é revogado removendo uma atribuição de função. Em seu núcleo, uma atribuição de função consiste em três elementos:

- Usuário (um indivíduo que tem um perfil de usuário no Azure Active Directory)
- Definição de função
- Escopo de recurso

Você pode [criar atribuições de função](roles-create-custom.md) usando o portal do Azure, o PowerShell do Azure ad ou o API do Graph. Você também pode [exibir as atribuições para uma função personalizada](roles-view-assignments.md#view-the-assignments-of-a-role).

O diagrama a seguir mostra um exemplo de uma atribuição de função. Neste exemplo, Chris Green recebeu a função personalizada de administrador de registro de aplicativo no escopo do registro do aplicativo do compilador de widget da contoso. A atribuição concede Chris às permissões da função de administrador de registro de aplicativo somente para esse registro de aplicativo específico.

![A atribuição de função é como as permissões são impostas e tem três partes](./media/roles-custom-overview/rbac-overview.png)

### <a name="security-principal"></a>Entidade de segurança

Uma entidade de segurança representa o usuário que deve receber acesso aos recursos do Azure AD. Um *usuário* é um indivíduo que tem um perfil de usuário no Azure Active Directory.

### <a name="role"></a>Função

Uma definição de função, ou função, é uma coleção de permissões. Uma definição de função lista as operações que podem ser executadas nos recursos do Azure AD, como criar, ler, atualizar e excluir. Há dois tipos de funções no Azure AD:

- Funções internas criadas pela Microsoft que não podem ser alteradas.
- Funções personalizadas criadas e gerenciadas pela sua organização.

### <a name="scope"></a>Escopo

Um escopo é a restrição de ações permitidas para um recurso específico do Azure AD como parte de uma atribuição de função. Ao atribuir uma função, você pode especificar um escopo que limita o acesso do administrador a um recurso específico. Por exemplo, se você quiser conceder a um desenvolvedor uma função personalizada, mas apenas para gerenciar um registro de aplicativo específico, poderá incluir o registro de aplicativo específico como um escopo na atribuição de função.

  > [!Note]
  > As funções personalizadas podem ser atribuídas no escopo do diretório e no escopo do recurso. Eles ainda não podem ser atribuídos no escopo da unidade administrativa.
  > As funções internas podem ser atribuídas no escopo do diretório e, em alguns casos, no escopo da unidade administrativa. Eles ainda não podem ser atribuídos no escopo de recursos do Azure AD.

## <a name="required-license-plan"></a>Plano de licença necessário

[!INCLUDE [License requirement for using custom roles in Azure AD](../../../includes/active-directory-p1-license.md)]

## <a name="next-steps"></a>Próximas etapas

- Criar atribuições de função personalizadas usando [o portal do Azure, o PowerShell do Azure AD e o API do Graph](roles-create-custom.md)
- [Exibir as atribuições para uma função personalizada](roles-view-assignments.md#view-assignments-of-single-application-scope)
