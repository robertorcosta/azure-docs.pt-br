---
title: Como remover o acesso de um usuário a um aplicativo no Azure Active Directory
description: Entenda como remover o acesso de um usuário a um aplicativo no Azure Active Directory
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 11/02/2020
ms.author: kenwith
ms.openlocfilehash: 28b31d98f283dc957927ab2a35f0ab95bf066473
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/17/2020
ms.locfileid: "94654109"
---
# <a name="how-to-remove-a-users-access-to-an-application"></a>Como remover o acesso de um usuário a um aplicativo

Este artigo ajuda-lhe a compreender como remover o acesso de um usuário a um aplicativo.

## <a name="i-want-to-remove-a-specific-users-or-groups-assignment-to-an-application"></a>Quero remover a atribuição de um usuário ou grupo específico para um aplicativo

Para remover uma atribuição de um usuário ou grupo a um aplicativo, siga as etapas listadas no artigo [Remover uma atribuição de usuário ou grupo de um aplicativo empresarial no Azure Active Directory](./assign-user-or-group-access-portal.md).

## <a name="i-want-to-disable-all-access-to-an-application-for-every-user"></a>Quero desabilitar todo o acesso a um aplicativo para todos os usuários

Para desabilitar todos os logons de usuário em um aplicativo, siga as etapas listadas no artigo [Desabilitar logons de usuário para um aplicativo empresarial no Azure Active Directory](./disable-user-sign-in-portal.md).

## <a name="i-want-to-delete-an-application-entirely"></a>Quero excluir um aplicativo completamente

A [série de guias de início rápido sobre o gerenciamento de aplicativos](delete-application-portal.md) inclui orientações sobre como excluir um aplicativo do seu locatário Azure Active Directory.

## <a name="i-want-to-disable-all-future-user-consent-operations-to-any-application"></a>Quero desabilitar todas as futuras operações de consentimento do usuário para qualquer aplicativo

Desabilitar o consentimento do usuário para todo o seu diretório impede que os usuários finais consintam com qualquer aplicativo. Os administradores ainda podem consentir em nome do usuário. Para obter mais informações sobre o consentimento de aplicativos e por que você pode ou não querer fazer isso, leia [Understanding user and admin consent](../develop/howto-convert-app-to-be-multi-tenant.md#understand-user-and-admin-consent) (Noções básicas sobre o consentimento do usuário e do administrador). Confira também, [Permissões e consentimento](../develop/v2-permissions-and-consent.md).

Para **desabilitar todas as futuras operações de consentimento do usuário no diretório inteiro**, siga estas instruções:

1.  Abra o [**Portal do Azure**](https://portal.azure.com/) e entre como um **Administrador Global.**

2.  Abra a **Extensão do Azure Active Directory** 

3.  Clique em **Aplicativos empresariais** no menu de navegação.

5.  Clique em **Configurações de usuário**.

6.  Defina a alternância de **Usuários podem permitir que aplicativos acessem dados empresariais em seu nome** como **Não** e clique no botão Salvar.


## <a name="next-steps"></a>Próximas etapas

[Gerenciamento do acesso aos aplicativos](what-is-access-management.md)