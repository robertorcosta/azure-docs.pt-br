---
title: Ocultar ou excluir pacote de acesso no gerenciamento de direitos - Azure AD
description: Saiba como ocultar ou excluir um pacote de acesso no gerenciamento de direitos do Azure Active Directory.
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
ms.openlocfilehash: cf410db5a483fc4f5d9efc1a6bd5c6202694ef4c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79261990"
---
# <a name="hide-or-delete-an-access-package-in-azure-ad-entitlement-management"></a>Ocultar ou excluir um pacote de acesso no gerenciamento de direitos Ad do Azure

Os pacotes de acesso são descobertos por padrão. Isso significa que, se uma política permitir que um usuário solicite o pacote de acesso, ele verá automaticamente o pacote de acesso listado em seu portal Meu Acesso. No entanto, você pode alterar a configuração **Oculta** para que o pacote de acesso não seja listado no portal Meu Acesso do usuário.

Este artigo descreve como ocultar ou excluir um pacote de acesso.

## <a name="change-the-hidden-setting"></a>Alterar a configuração Oculta

Siga estas etapas para alterar a configuração **Oculta** para um pacote de acesso.

**Papel pré-requisito:** Administrador global, administrador de usuário, proprietário de catálogo ou gerenciador de pacotes de acesso

1. No portal do Azure, clique em **Azure Active Directory** e, em seguida, em **Governança de Identidade**.

1. No menu à esquerda, clique em **Acessar pacotes** e, em seguida, abra o pacote de acesso.

1. Na página Visão geral, clique em **Editar**.

1. Defina a **configuração Oculta.**

    Se definido como **Não,** o pacote de acesso será listado no portal Meu Acesso do usuário.

    Se definido como **Sim,** o pacote de acesso não será listado no portal Meu Acesso do usuário. A única maneira de o usuário visualizar o pacote de acesso é se tiver o link direto do **portal Meu Acesso** para o pacote de acesso. Para obter mais informações, consulte [compartilhar link para solicitar um pacote de acesso](entitlement-management-access-package-settings.md).

## <a name="delete-an-access-package"></a>Excluir um pacote de acesso

Um pacote de acesso só pode ser excluído se não tiver atribuições ativas do usuário. Siga estas etapas para excluir um pacote de acesso.

**Papel pré-requisito:** Administrador global, administrador de usuário, proprietário de catálogo ou gerenciador de pacotes de acesso

1. No portal do Azure, clique em **Azure Active Directory** e, em seguida, em **Governança de Identidade**.

1. No menu à esquerda, clique em **Acessar pacotes** e, em seguida, abra o pacote de acesso.

1. No menu à esquerda, clique **em Atribuições** e remova o acesso para todos os usuários.

1. No menu à esquerda, clique **em Visão geral** e clique em **Excluir**.

1. Na mensagem de exclusão que aparece, clique **em Sim**.

## <a name="next-steps"></a>Próximas etapas

- [Exibir, adicionar e remover atribuições para um pacote de acesso](entitlement-management-access-package-assignments.md)
- [Exibir relatórios e logs](entitlement-management-reports.md)
