---
title: Minha página de login do Azure AD aceita contas da Microsoft | Microsoft Docs
description: Como as mensagens na tela refletem a aparência do nome de usuário durante o login
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: kexia
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 221ab7c50a84650f1b2adf3fdb2b284365795f42
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74024279"
---
# <a name="sign-in-options-for-microsoft-accounts-in-azure-active-directory"></a>Opções de login para contas Microsoft no Azure Active Directory

A página de login do Microsoft 365 para o Azure Active Directory (Azure AD) suporta contas de trabalho ou escola e contas da Microsoft, mas dependendo da situação do usuário, pode ser uma ou outra ou ambas. Por exemplo, a página de login do Azure AD suporta:

* Aplicativos que aceitam logins de ambos os tipos de conta
* Organizações que aceitam convidados

## <a name="identification"></a>Identificação
Você pode dizer se a página de login que sua organização usa suporta contas microsoft olhando para o texto de dica no campo nome de usuário. Se o texto de sugestão diz "E-mail, telefone ou Skype", a página de login suporta contas da Microsoft.

![Diferença entre páginas de login de conta](./media/signin-account-support/ui-prompt.png)

[Opções adicionais de login funcionam apenas para contas pessoais da Microsoft,](https://azure.microsoft.com/updates/microsoft-account-signin-options/ ) mas não podem ser usadas para fazer login no trabalho ou recursos da conta da escola.

## <a name="next-steps"></a>Próximas etapas

[Personalize sua marca de login](../fundamentals/add-custom-domain.md)