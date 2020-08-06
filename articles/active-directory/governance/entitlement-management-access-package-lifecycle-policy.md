---
title: Alterar as configurações do ciclo de vida de um pacote de acesso no gerenciamento de direitos do Azure AD-Azure Active Directory
description: Saiba como alterar as configurações do ciclo de vida de um pacote do Access no gerenciamento de direitos Azure Active Directory.
services: active-directory
documentationCenter: ''
author: ajburnle
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 06/18/2020
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9c628c3c1f11cb973176e7a689856b64177036c9
ms.sourcegitcommit: fbb66a827e67440b9d05049decfb434257e56d2d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/05/2020
ms.locfileid: "87798691"
---
# <a name="change-lifecycle-settings-for-an-access-package-in-azure-ad-entitlement-management"></a>Alterar as configurações do ciclo de vida de um pacote de acesso no gerenciamento de direitos do Azure AD

Como um Gerenciador de pacotes do Access, você pode alterar as configurações do ciclo de vida de um pacote do Access a qualquer momento editando uma política existente. Se você alterar a data de validade de uma política, a data de expiração das solicitações que já estão em um estado de aprovação pendente ou aprovado não será alterada.

Este artigo descreve como alterar as configurações de ciclo de vida de um pacote do Access existente.

## <a name="open-lifecycle-settings"></a>Abrir configurações do ciclo de vida

Para alterar as configurações do ciclo de vida de um pacote do Access, você precisa abrir a política correspondente. Siga estas etapas para abrir as configurações de ciclo de vida de um pacote do Access.

**Função de pré-requisito:** Administrador global, Administrador de usuário, Proprietário do catálogo ou Gerenciador de pacote de acesso

1. No portal do Azure, clique em **Azure Active Directory** e, em seguida, em **Governança de Identidade**.

1. No menu à esquerda, clique em **pacotes de acesso** e abra o pacote de acesso.

1. Clique em **políticas** e, em seguida, clique na política que tem as configurações de ciclo de vida que você deseja editar.

    O painel detalhes da política é aberto na parte inferior da página.

    ![Pacote de acesso-painel detalhes da política](./media/entitlement-management-shared/policy-details.png)

1. Clique em **Editar** para editar a política.

    ![Pacote de acesso-editar política](./media/entitlement-management-shared/policy-edit.png)

1. Clique na guia **ciclo de vida** para abrir as configurações do ciclo de vida.

[!INCLUDE [Entitlement management lifecycle policy](../../../includes/active-directory-entitlement-management-lifecycle-policy.md)]

## <a name="next-steps"></a>Próximas etapas

- [Alterar as configurações de solicitação e aprovação para um pacote de acesso](entitlement-management-access-package-request-policy.md)