---
title: Azure Active Directory perguntas frequentes de acesso condicional | Microsoft Docs
description: Obtenha respostas para perguntas frequentes sobre o acesso condicional no Azure Active Directory.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 01/15/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.custom: has-adal-ref
ms.openlocfilehash: f2df8140d2eb791e83af5ae47b947d614ac2b899
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83199412"
---
# <a name="azure-active-directory-conditional-access-faqs"></a>Azure Active Directory FAQs de acesso condicional

## <a name="which-applications-work-with-conditional-access-policies"></a>Quais aplicativos funcionam com políticas de acesso condicional?

Para obter informações sobre aplicativos que funcionam com políticas de acesso condicional, consulte [aplicativos e navegadores que usam regras de acesso condicional no Azure Active Directory](concept-conditional-access-cloud-apps.md).

## <a name="are-conditional-access-policies-enforced-for-b2b-collaboration-and-guest-users"></a>As políticas de acesso condicional são impostas para colaboração B2B e usuários convidados?

As políticas são impostas para usuários de colaboração B2B (entre empresas). No entanto, em alguns casos, um usuário pode não ser capaz de atender aos requisitos da política. Por exemplo, a organização de um usuário convidado pode não oferecer suporte à autenticação multifator. 

## <a name="does-a-sharepoint-online-policy-also-apply-to-onedrive-for-business"></a>A política do SharePoint Online também se aplica ao OneDrive for Business?

Sim. A política do SharePoint Online também se aplica ao OneDrive for Business.

## <a name="why-cant-i-set-a-policy-directly-on-client-apps-like-word-or-outlook"></a>Por que não posso definir uma política diretamente em aplicativos cliente, como o Word ou o Outlook?

Uma política de acesso condicional define os requisitos para acessar um serviço. É imposta quando ocorre a autenticação para esse serviço. A política não é definida diretamente em um aplicativo cliente. Em vez disso, ela será aplicada quando um cliente chamar um serviço. Por exemplo, um conjunto de políticas no SharePoint se aplica aos clientes que chamam o SharePoint. Um conjunto de políticas no Exchange se aplica ao Outlook.

## <a name="does-a-conditional-access-policy-apply-to-service-accounts"></a>Uma política de acesso condicional se aplica a contas de serviço?

As políticas de acesso condicional se aplicam a todas as contas de usuário. Isso inclui as contas de usuário usadas como contas de serviço. Geralmente, uma conta de serviço que é executada de forma autônoma não pode atender aos requisitos de uma política de acesso condicional. Por exemplo, autenticação multifator pode ser necessária. As contas de serviço podem ser excluídas de uma política usando as configurações de gerenciamento de política de acesso condicional. 

## <a name="are-microsoft-graph-apis-available-for-configuring-conditional-access-policies"></a>Há Microsoft Graph APIs disponíveis para configurar políticas de acesso condicional?

No momento, não. 

## <a name="what-is-the-default-exclusion-policy-for-unsupported-device-platforms"></a>Qual é a política de exclusão padrão para plataformas de dispositivos sem suporte?

Atualmente, as políticas de acesso condicional são impostas seletivamente em usuários de dispositivos Android e iOS. Os aplicativos em outras plataformas de dispositivo são, por padrão, não afetados pela política de acesso condicional para dispositivos iOS e Android. A administração de locatário poderá substituir a política global para não permitir o acesso a usuários em plataformas sem suporte.

## <a name="how-do-conditional-access-policies-work-for-microsoft-teams"></a>Como as políticas de acesso condicional funcionam para o Microsoft Teams?

O Microsoft Teams depende muito do Exchange Online e do SharePoint Online para cenários de produtividade de núcleo, como reuniões, calendários e compartilhamento de arquivos. As políticas de acesso condicional definidas para esses aplicativos de nuvem se aplicam ao Microsoft Teams quando um usuário assina diretamente no Microsoft Teams.

O Microsoft Teams também tem suporte separadamente como um aplicativo de nuvem em Azure Active Directory políticas de acesso condicional. As políticas de acesso condicional definidas para um aplicativo de nuvem se aplicam ao Microsoft Teams quando um usuário faz logon. No entanto, sem as políticas corretas em outros aplicativos, como o Exchange Online e o SharePoint Online, os usuários ainda poderão acessar esses recursos diretamente.

Os clientes de área de trabalho do Microsoft Teams para Windows e Mac oferecem suporte a autenticação moderna. A autenticação moderna traz a entrada com base no Azure Active Directory Authentication Library (ADAL) para aplicativos cliente do Microsoft Office entre plataformas.

## <a name="next-steps"></a>Próximas etapas

- Para configurar políticas de acesso condicional para seu ambiente, consulte as [práticas recomendadas para acesso condicional no Azure Active Directory](best-practices.md). 
