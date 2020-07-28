---
title: Minha página de entrada do Azure AD aceita contas Microsoft | Microsoft Docs
description: Como mensagens na tela refletem a pesquisa de nome de usuário durante a entrada
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: overview
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: kexia
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 81ba7f77bb3396f49c3cf41d2266567aa48a8257
ms.sourcegitcommit: c4ad4ba9c9aaed81dfab9ca2cc744930abd91298
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/12/2020
ms.locfileid: "84731351"
---
# <a name="sign-in-options-for-microsoft-accounts-in-azure-active-directory"></a>Opções de entrada para contas Microsoft no Azure Active Directory

A página de entrada do Microsoft 365 para Azure AD (Azure Active Directory) dá suporte a contas corporativas ou de estudante e contas Microsoft, mas, dependendo da situação do usuário, ela pode ser uma ou outra ou ambas. Por exemplo, a página de entrada do Azure AD dá suporte a:

* Aplicativos que aceitam entradas de ambos os tipos de conta
* Organizações que aceitam convidados

## <a name="identification"></a>Identificação
Você pode saber se a página de entrada que sua organização usa dá suporte a contas Microsoft examinando o texto de dica no campo de nome de usuário. Se o texto de dica diz "Email, telefone ou Skype", a página de entrada dá suporte a contas Microsoft.

![Diferença entre as páginas de entrada da conta](./media/signin-account-support/ui-prompt.png)

[As opções de entrada adicionais só funcionam para contas pessoais da Microsoft](https://azure.microsoft.com/updates/microsoft-account-signin-options/ ), mas não podem ser usadas para entrar em recursos de conta corporativa ou de estudante.

## <a name="next-steps"></a>Próximas etapas

[Personalizar sua identidade visual de entrada](../fundamentals/add-custom-domain.md)