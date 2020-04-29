---
title: Políticas de proteção de aplicativo com acesso condicional-Azure Active Directory
description: Saiba como exigir a política de proteção de aplicativo para acesso ao aplicativo de nuvem com acesso condicional no Azure Active Directory.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 04/02/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: spunukol, rosssmi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6b282962cc713487b8ee5113b02b8533a1538fff
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80631888"
---
# <a name="how-to-require-app-protection-policy-and-an-approved-client-app-for-cloud-app-access-with-conditional-access"></a>Como: exigir a política de proteção de aplicativo e um aplicativo cliente aprovado para acesso de aplicativo de nuvem com acesso condicional

As pessoas usam regularmente seus dispositivos móveis para tarefas pessoais e de trabalho. Embora garanta que a equipe possa ser produtiva, as organizações também desejam evitar a perda de dados de aplicativos potencialmente não seguros. Com o acesso condicional, as organizações podem restringir o acesso a aplicativos cliente aprovados (com capacidade de autenticação moderna) com políticas de proteção de aplicativo do Intune aplicadas a eles.

Este artigo apresenta dois cenários para configurar políticas de acesso condicional para recursos como o Office 365, o Exchange Online e o SharePoint Online.

- [Cenário 1: aplicativos do Office 365 exigem aplicativos aprovados com políticas de proteção de aplicativo](#scenario-1-office-365-apps-require-approved-apps-with-app-protection-policies)
- [Cenário 2: o Exchange Online e o SharePoint Online exigem um aplicativo cliente aprovado e uma política de proteção de aplicativo](#scenario-2-exchange-online-and-sharepoint-online-require-an-approved-client-app-and-app-protection-policy)

No acesso condicional, esses aplicativos cliente são conhecidos como protegidos por uma política de proteção de aplicativo. Mais informações sobre as políticas de proteção de aplicativo podem ser encontradas no artigo [visão geral das políticas de proteção de aplicativo](/intune/apps/app-protection-policy)

Para obter uma lista de aplicativos cliente qualificados, consulte [requisito de política de proteção de aplicativo](concept-conditional-access-grant.md).

> [!NOTE]
>    A cláusula or é usada na política para permitir que os usuários utilizem aplicativos que dão suporte à **política de proteção de aplicativo necessária** ou exigem controles de concessão de **aplicativo cliente aprovados** . Para obter mais informações sobre quais aplicativos dão suporte ao controle **exigir a política de proteção de aplicativo** , consulte requisito de política de proteção de [aplicativo](concept-conditional-access-grant.md).

## <a name="scenario-1-office-365-apps-require-approved-apps-with-app-protection-policies"></a>Cenário 1: aplicativos do Office 365 exigem aplicativos aprovados com políticas de proteção de aplicativo

Nesse cenário, a Contoso decidiu que todo o acesso móvel aos recursos do Office 365 deve usar aplicativos cliente aprovados, como Outlook Mobile, OneDrive e Microsoft Teams protegidos por uma política de proteção de aplicativo antes de receber acesso. Todos os seus usuários já entram com as credenciais do Azure AD e têm licenças atribuídas a eles que incluem Azure AD Premium P1 ou P2 e Microsoft Intune.

As organizações devem concluir as seguintes etapas para exigir o uso de um aplicativo cliente aprovado em dispositivos móveis.

**Etapa 1: configurar uma política de acesso condicional do Azure AD para o Office 365**

1. Entre no **portal do Azure** como administrador global, administrador de segurança ou administrador de acesso condicional.
1. Navegue até **Azure Active Directory** > **Security** > **acesso condicional**de segurança.
1. Selecione **nova política**.
1. Dê um nome à sua política. Recomendamos que as organizações criem um padrão significativo para os nomes de suas políticas.
1. Em **atribuições**, selecione **usuários e grupos**
   1. Em **incluir**, selecione **todos os usuários** ou os **usuários e grupos** específicos aos quais você deseja aplicar essa política. 
   1. Selecione **Concluído**.
1. Em **aplicativos de nuvem ou ações** > **incluem**, selecione **Office 365 (versão prévia)**.
1. Em **condições**, selecione **plataformas de dispositivo**.
   1. Defina **Configurar** como **Sim**.
   1. Incluir **Android** e **Ios**.
1. Em **condições**, selecione **aplicativos cliente (versão prévia)**.
   1. Defina **Configurar** como **Sim**.
   1. Selecione **Aplicativos móveis e clientes da área de trabalho** e **Clientes de autenticação moderna**.
1. Em **Access controls** > **concessão**de controles de acesso, selecione as seguintes opções:
   - **Exigir o aplicativo cliente aprovado**
   - **Exigir uma política de proteção do aplicativo (versão prévia)**
   - **Exigir todos os controles selecionados**
1. Confirme suas configurações e defina **habilitar política** como **ativado**.
1. Selecione **criar** para criar e habilitar sua política.

**Etapa 2: configurar uma política de acesso condicional do Azure AD para o Exchange Online com o ActiveSync (EAS)**

Para a política de acesso condicional nesta etapa, configure os seguintes componentes:

1. Navegue até **Azure Active Directory** > **Security** > **acesso condicional**de segurança.
1. Selecione **nova política**.
1. Dê um nome à sua política. Recomendamos que as organizações criem um padrão significativo para os nomes de suas políticas.
1. Em **atribuições**, selecione **usuários e grupos**
   1. Em **incluir**, selecione **todos os usuários** ou os **usuários e grupos** específicos aos quais você deseja aplicar essa política. 
   1. Selecione **Concluído**.
1. Em **aplicativos de nuvem ou ações** > **incluem**, selecione **Office 365 Exchange Online**.
1. Em **condições**:
   1. **Aplicativos cliente (versão prévia)**:
      1. Defina **Configurar** como **Sim**.
      1. Selecione **aplicativos móveis e clientes de área de trabalho** e **clientes do Exchange ActiveSync**.
1. Em **Access controls** > **concessão**de controles de acesso, selecione **conceder acesso**, **exigir política de proteção de aplicativo**e selecione **selecionar**.
1. Confirme suas configurações e defina **habilitar política** como **ativado**.
1. Selecione **criar** para criar e habilitar sua política.

**Etapa 3: configurar a política de proteção de aplicativo do Intune para aplicativos cliente iOS e Android**

Examine o artigo [como criar e atribuir políticas de proteção de aplicativo](/intune/apps/app-protection-policies), para obter as etapas para criar políticas de proteção de aplicativo para Android e Ios. 

## <a name="scenario-2-exchange-online-and-sharepoint-online-require-an-approved-client-app-and-app-protection-policy"></a>Cenário 2: o Exchange Online e o SharePoint Online exigem um aplicativo cliente aprovado e uma política de proteção de aplicativo

Nesse cenário, a Contoso decidiu que os usuários só podem acessar dados de email e do SharePoint em dispositivos móveis, contanto que eles usem um aplicativo cliente aprovado como o Outlook Mobile protegido por uma política de proteção de aplicativo antes de receber o acesso. Todos os seus usuários já entram com as credenciais do Azure AD e têm licenças atribuídas a eles que incluem Azure AD Premium P1 ou P2 e Microsoft Intune.

As organizações devem concluir as três etapas a seguir para exigir o uso de um aplicativo cliente aprovado em dispositivos móveis e clientes do Exchange ActiveSync.

**Etapa 1: política para clientes de autenticação moderna baseados em Android e iOS que exigem o uso de um aplicativo cliente aprovado e uma política de proteção de aplicativo ao acessar o Exchange Online e o SharePoint Online.**

1. Entre no **portal do Azure** como administrador global, administrador de segurança ou administrador de acesso condicional.
1. Navegue até **Azure Active Directory** > **Security** > **acesso condicional**de segurança.
1. Selecione **nova política**.
1. Dê um nome à sua política. Recomendamos que as organizações criem um padrão significativo para os nomes de suas políticas.
1. Em **atribuições**, selecione **usuários e grupos**
   1. Em **incluir**, selecione **todos os usuários** ou os **usuários e grupos** específicos aos quais você deseja aplicar essa política. 
   1. Selecione **Concluído**.
1. Em **aplicativos de nuvem ou ações** > **incluem**, selecione **Office 365 Exchange Online** e **Office 365 SharePoint Online**.
1. Em **condições**, selecione **plataformas de dispositivo**.
   1. Defina **Configurar** como **Sim**.
   1. Incluir **Android** e **Ios**.
1. Em **condições**, selecione **aplicativos cliente (versão prévia)**.
   1. Defina **Configurar** como **Sim**.
   1. Selecione **Aplicativos móveis e clientes da área de trabalho** e **Clientes de autenticação moderna**.
1. Em **Access controls** > **concessão**de controles de acesso, selecione as seguintes opções:
   - **Exigir o aplicativo cliente aprovado**
   - **Exigir uma política de proteção do aplicativo (versão prévia)**
   - **Exigir um dos controles selecionados**
1. Confirme suas configurações e defina **habilitar política** como **ativado**.
1. Selecione **criar** para criar e habilitar sua política.

**Etapa 2: política para clientes do Exchange ActiveSync que exigem o uso de um aplicativo cliente aprovado.**

1. Navegue até **Azure Active Directory** > **Security** > **acesso condicional**de segurança.
1. Selecione **nova política**.
1. Dê um nome à sua política. Recomendamos que as organizações criem um padrão significativo para os nomes de suas políticas.
1. Em **atribuições**, selecione **usuários e grupos**
   1. Em **incluir**, selecione **todos os usuários** ou os **usuários e grupos** específicos aos quais você deseja aplicar essa política. 
   1. Selecione **Concluído**.
1. Em **aplicativos de nuvem ou ações** > **incluem**, selecione **Office 365 Exchange Online**.
1. Em **condições**:
   1. **Aplicativos cliente (versão prévia)**:
      1. Defina **Configurar** como **Sim**.
      1. Selecione **aplicativos móveis e clientes de área de trabalho** e **clientes do Exchange ActiveSync**.
1. Em **Access controls** > **concessão**de controles de acesso, selecione **conceder acesso**, **exigir política de proteção de aplicativo**e selecione **selecionar**.
1. Confirme suas configurações e defina **habilitar política** como **ativado**.
1. Selecione **criar** para criar e habilitar sua política.

**Etapa 3: configurar a política de proteção de aplicativo do Intune para aplicativos cliente iOS e Android.**

Examine o artigo [como criar e atribuir políticas de proteção de aplicativo](/intune/apps/app-protection-policies), para obter as etapas para criar políticas de proteção de aplicativo para Android e Ios. 

## <a name="next-steps"></a>Próximas etapas

[O que é Acesso Condicional?](overview.md)

[Componentes de acesso condicional](concept-conditional-access-policies.md)

[Políticas de acesso condicional comum](concept-conditional-access-policy-common.md)

