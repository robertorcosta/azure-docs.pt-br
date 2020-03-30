---
title: Conceder consentimento de administrador em todo o inquilino para um aplicativo - Azure AD
description: Saiba como conceder o consentimento de um aplicativo para que os usuários finais não sejam solicitados para o consentimento ao fazer login em um aplicativo.
services: active-directory
author: psignoret
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: mimart
ms.reviewer: phsignor
ms.collection: M365-identity-device-management
ms.openlocfilehash: c515fef4997720435c64bd5f3ae7b18f8921fc5d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75480912"
---
# <a name="grant-tenant-wide-admin-consent-to-an-application"></a>Conceder consentimento de admin em todo o inquilino para um aplicativo

Aprenda a simplificar a experiência do usuário concedendo o consentimento do inquilino para um aplicativo. Este artigo dá as diferentes maneiras de conseguir isso. Os métodos se aplicam a todos os usuários finais do locatário do Azure AD (Azure Active Directory).

Para obter mais informações sobre o consentimento de aplicativos, consulte a [estrutura de consentimento do Azure Active Directory](../develop/consent-framework.md).

## <a name="prerequisites"></a>Pré-requisitos

A concessão de consentimento de administrador em todo o inquilino exige que você faça login como [Administrador Global,](../users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator)administrador de [aplicativos](../users-groups-roles/directory-assign-admin-roles.md#application-administrator)ou administrador de aplicativos [na nuvem.](../users-groups-roles/directory-assign-admin-roles.md#cloud-application-administrator)

> [!IMPORTANT]
> Quando um aplicativo tiver recebido o consentimento do admin em todo o inquilino, todos os usuários poderão fazer login no aplicativo, a menos que ele tenha sido configurado para exigir a atribuição do usuário. Para restringir quais usuários podem fazer login em um aplicativo, exija a atribuição do usuário e, em seguida, designe usuários ou grupos para o aplicativo. Para obter mais informações, confira [Métodos para atribuir usuários e grupos](methods-for-assigning-users-and-groups.md).

> [!WARNING]
> A concessão de consentimento de um aplicativo para um aplicativo concederá ao aplicativo e ao editor do aplicativo acesso aos dados da sua organização. Revise cuidadosamente as permissões que o aplicativo está solicitando antes de conceder o consentimento.

## <a name="grant-admin-consent-from-the-azure-portal"></a>Conceder consentimento do administrador do portal Azure

### <a name="grant-admin-consent-in-enterprise-apps"></a>Conceder consentimento de admin em aplicativos Enterprise

Você pode conceder o consentimento do inquilino através de *aplicativos Corporativos* se o aplicativo já tiver sido provisionado em seu inquilino. Por exemplo, um aplicativo pode ser provisionado em seu inquilino se pelo menos um usuário já consentiu com o aplicativo. Para obter mais informações, consulte [Como e por que os aplicativos são adicionados ao Azure Active Directory](../develop/active-directory-how-applications-are-added.md).

Para conceder o consentimento do inquilino em todo o inquilino a um aplicativo listado em **aplicativos Corporativos:**

1. Faça login no [portal Azure](https://portal.azure.com) como [administrador global,](../users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator) [administrador de aplicativos](../users-groups-roles/directory-assign-admin-roles.md#application-administrator)ou administrador de aplicativos [na nuvem.](../users-groups-roles/directory-assign-admin-roles.md#cloud-application-administrator)
2. Selecione **o Azure Active Directory** **e, em seguida, aplicativos Corporativos**.
3. Selecione o aplicativo ao qual deseja conceder o consentimento do inquilino em todo o país.
4. Selecione **Permissões** e clique em Conceder consentimento de **admin**.
5. Revise cuidadosamente as permissões que o aplicativo requer.
6. Se você concordar com as permissões que o aplicativo exige, conceda o consentimento. Caso assim, clique **em Cancelar** ou fechar a janela.

### <a name="grant-admin-consent-in-app-registrations"></a>Conceder consentimento de admin em registros de aplicativos

Para aplicações que sua organização desenvolveu, ou que estão registradas diretamente no seu inquilino Azure AD, você também pode conceder o consentimento do administrador em todo o inquilino a partir de registros de **aplicativos** no portal Azure.

Para conceder o consentimento do inquilino em todo o inquilino a partir de registros do **Aplicativo**:

1. Faça login no [portal Azure](https://portal.azure.com) como [administrador global,](../users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator) [administrador de aplicativos](../users-groups-roles/directory-assign-admin-roles.md#application-administrator)ou administrador de aplicativos [na nuvem.](../users-groups-roles/directory-assign-admin-roles.md#cloud-application-administrator)
2. Selecione **o Azure Active Directory** e, em seguida, **registros do aplicativo**.
3. Selecione o aplicativo ao qual deseja conceder o consentimento do inquilino em todo o país.
4. Selecione **as permissões de API** e clique **em Conceder consentimento de admin**.
5. Revise cuidadosamente as permissões que o aplicativo requer.
6. Se você concordar com as permissões que o aplicativo exige, conceda o consentimento. Caso assim, clique **em Cancelar** ou fechar a janela.

## <a name="construct-the-url-for-granting-tenant-wide-admin-consent"></a>Construa a URL para conceder o consentimento do inquilino em todo o país

Ao conceder o consentimento do administrador em todo o inquilino usando qualquer método descrito acima, uma janela se abre do portal Azure para solicitar o consentimento do administrador em todo o inquilino. Se você conhece o ID do cliente (também conhecido como ID do aplicativo) do aplicativo, você pode construir a mesma URL para conceder o consentimento do inquilino em todo o inquilino.

A URL de consentimento de admin em todo o inquilino segue o seguinte formato:

    https://login.microsoftonline.com/{tenant-id}/adminconsent?client_id={client-id}

onde:

* `{client-id}`é o ID do cliente do aplicativo (também conhecido como ID do aplicativo).
* `{tenant-id}`é o ID do inquilino da sua organização ou qualquer nome de domínio verificado.

Como sempre, revise cuidadosamente as permissões que um pedido solicita antes de conceder o consentimento.

## <a name="next-steps"></a>Próximas etapas

[Configure como os usuários finais concordam com aplicativos](configure-user-consent.md)

[Configure o fluxo de trabalho de consentimento de administrador](configure-admin-consent-workflow.md)

[Permissões e consentimento na plataforma de identidade da Microsoft](../develop/active-directory-v2-scopes.md)

[Azure AD em StackOverflow](https://stackoverflow.com/questions/tagged/azure-active-directory)
