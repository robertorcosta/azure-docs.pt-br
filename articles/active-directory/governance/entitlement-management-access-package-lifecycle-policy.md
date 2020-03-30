---
title: Alterar as configurações do ciclo de vida de um pacote de acesso no gerenciamento de direitos Azure AD - Azure Active Directory
description: Saiba como alterar as configurações do ciclo de vida de um pacote de acesso no gerenciamento de direitos do Azure Active Directory.
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 10/15/2019
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 959d85f496a4a573a969bf736aba137d5b86154a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79261977"
---
# <a name="change-lifecycle-settings-for-an-access-package-in-azure-ad-entitlement-management"></a>Alterar as configurações do ciclo de vida para um pacote de acesso no gerenciamento de direitos Azure AD

Como um gerenciador de pacotes de acesso, você pode alterar as configurações do ciclo de vida de um pacote de acesso a qualquer momento, editando uma diretiva existente. Se você alterar a data de validade de uma apólice, a data de validade das solicitações que já estão em um estado pendente ou aprovado não mudará.

Este artigo descreve como alterar as configurações do ciclo de vida de um pacote de acesso existente.

## <a name="open-lifecycle-settings"></a>Configurações abertas do ciclo de vida

Para alterar as configurações do ciclo de vida de um pacote de acesso, você precisa abrir a diretiva correspondente. Siga estas etapas para abrir as configurações do ciclo de vida de um pacote de acesso.

**Papel pré-requisito:** Administrador global, administrador de usuário, proprietário de catálogo ou gerenciador de pacotes de acesso

1. No portal do Azure, clique em **Azure Active Directory** e, em seguida, em **Governança de Identidade**.

1. No menu à esquerda, clique em **Acessar pacotes** e, em seguida, abra o pacote de acesso.

1. Clique **em Políticas** e clique na diretiva que tem as configurações do ciclo de vida que deseja editar.

    O painel de detalhes da Política é aberto na parte inferior da página.

    ![Pacote de acesso - Painel de detalhes da política](./media/entitlement-management-shared/policy-details.png)

1. Clique **em Editar** para editar a diretiva.

    ![Pacote de acesso - Editar política](./media/entitlement-management-shared/policy-edit.png)

1. Clique na **guia Ciclo de vida** para abrir as configurações do ciclo de vida.

[!INCLUDE [Entitlement management lifecycle policy](../../../includes/active-directory-entitlement-management-lifecycle-policy.md)]

## <a name="next-steps"></a>Próximas etapas

- [Alterar as configurações de solicitação e aprovação de um pacote de acesso](entitlement-management-access-package-request-policy.md)