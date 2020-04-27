---
title: Como remover o acesso de um usuário a um aplicativo | Microsoft Docs
description: Compreenda como remover o acesso de um usuário a um aplicativo
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/17/2018
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5b69502995eff88df53af3671a8e611809f83e59
ms.sourcegitcommit: be32c9a3f6ff48d909aabdae9a53bd8e0582f955
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/26/2020
ms.locfileid: "65826109"
---
# <a name="how-to-remove-a-users-access-to-an-application"></a>Como remover o acesso de um usuário a um aplicativo

Este artigo ajuda-lhe a compreender como remover o acesso de um usuário a um aplicativo.

## <a name="i-want-to-remove-a-specific-users-or-groups-assignment-to-an-application"></a>Quero remover a atribuição de um usuário ou grupo específico para um aplicativo

Para remover uma atribuição de um usuário ou grupo a um aplicativo, siga as etapas listadas no artigo [Remover uma atribuição de usuário ou grupo de um aplicativo empresarial no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-remove-assignment-azure-portal).

## <a name="i-want-to-disable-all-access-to-an-application-for-every-user"></a>Quero desabilitar todo o acesso a um aplicativo para todos os usuários

Para desabilitar todos os logons de usuário em um aplicativo, siga as etapas listadas no artigo [Desabilitar logons de usuário para um aplicativo empresarial no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-disable-app-azure-portal).

## <a name="i-want-to-delete-an-application-entirely"></a>Quero excluir um aplicativo completamente

Para **excluir um aplicativo**, siga estas instruções:

1. Abra o [**portal do Azure**](https://portal.azure.com/) e entre como um **administrador global** ou **coadministrador.**

2. Abra a **Extensão do Azure Active Directory** clicando em **Todos os serviços** na parte superior do menu de navegação esquerdo principal.

3. Digite **“Azure Active Directory**” na caixa de pesquisa do filtro e selecione o item **Azure Active Directory**.

4. Clique em **aplicativos empresariais** no Azure Active Directory menu de navegação à esquerda.

5. Clique em **todos os aplicativos** para exibir uma lista de todos os seus aplicativos.

   * Se você não vir o aplicativo que deseja exibir aqui, use o controle de **filtro** na parte superior da **lista todos os aplicativos** e defina a opção **Mostrar** como **todos os aplicativos.**

6. Selecione o aplicativo que deseja excluir.

7. Após o carregamento do aplicativo, clique no ícone **Excluir** do painel **Visão Geral** do aplicativo.

## <a name="i-want-to-disable-all-future-user-consent-operations-to-any-application"></a>Quero desabilitar todas as futuras operações de consentimento do usuário para qualquer aplicativo

Desabilitar o consentimento do usuário para todo o seu diretório impede que os usuários finais consintam com qualquer aplicativo. Os administradores ainda podem consentir em nome do usuário. Para obter mais informações sobre o consentimento de aplicativos e por que você pode ou não querer fazer isso, leia [Understanding user and admin consent](../develop/howto-convert-app-to-be-multi-tenant.md#understand-user-and-admin-consent) (Noções básicas sobre o consentimento do usuário e do administrador). Confira também, [Permissões e consentimento](../develop/v2-permissions-and-consent.md).

Para **desabilitar todas as futuras operações de consentimento do usuário no diretório inteiro**, siga estas instruções:

1.  Abra o [**portal do Azure**](https://portal.azure.com/) e entre como um **administrador global.**

2.  Abra a **Extensão do Azure Active Directory** 

3.  Clique em **Aplicativos empresariais** no menu de navegação.

5.  Clique em **Configurações de usuário**.

6.  Defina a alternância de **Usuários podem permitir que aplicativos acessem dados empresariais em seu nome** como **Não** e clique no botão Salvar.


## <a name="next-steps"></a>Próximas etapas

[Gerenciando o acesso a aplicativos](what-is-access-management.md)
