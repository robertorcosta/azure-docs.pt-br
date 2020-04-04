---
title: Políticas de proteção de aplicativos com acesso condicional - Diretório Ativo do Azure
description: Saiba como exigir a política de proteção de aplicativos para acesso a aplicativos em nuvem com acesso condicional no Azure Active Directory.
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
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80631888"
---
# <a name="how-to-require-app-protection-policy-and-an-approved-client-app-for-cloud-app-access-with-conditional-access"></a>Como: Exigir política de proteção de aplicativos e um aplicativo cliente aprovado para acesso a aplicativos na nuvem com acesso condicional

As pessoas usam regularmente seus dispositivos móveis para tarefas pessoais e de trabalho. Ao mesmo tempo em que garantem que os funcionários possam ser produtivos, as organizações também querem evitar a perda de dados de aplicativos potencialmente inseguros. Com o Acesso Condicional, as organizações podem restringir o acesso a aplicativos clientes aprovados (capazes de autenticação moderna) com políticas de proteção de aplicativos Intune aplicadas a eles.

Este artigo apresenta dois cenários para configurar políticas de acesso condicional para recursos como Office 365, Exchange Online e SharePoint Online.

- [Cenário 1: Aplicativos do Office 365 exigem aplicativos aprovados com políticas de proteção de aplicativos](#scenario-1-office-365-apps-require-approved-apps-with-app-protection-policies)
- [Cenário 2: Exchange Online e SharePoint Online exigem uma política de proteção de aplicativos e aplicativos aprovados](#scenario-2-exchange-online-and-sharepoint-online-require-an-approved-client-app-and-app-protection-policy)

No Acesso Condicional, esses aplicativos clientes são conhecidos por serem protegidos com uma política de proteção de aplicativos. Mais informações sobre políticas de proteção de aplicativos podem ser encontradas no artigo, visão geral das [políticas de proteção de aplicativos](/intune/apps/app-protection-policy)

Para obter uma lista de aplicativos de cliente elegíveis, consulte o requisito da política de [proteção do aplicativo](concept-conditional-access-grant.md).

> [!NOTE]
>    A cláusula ou cláusula é usada dentro da diretiva para permitir que os usuários utilizem aplicativos que suportam a **política de proteção de aplicativos Exigir** ou exigir controles de concessão de aplicativos de cliente **aprovados.** Para obter mais informações sobre quais aplicativos suportam o controle **de concessão** de políticas de proteção de aplicativos, consulte o requisito da política de [proteção do aplicativo](concept-conditional-access-grant.md).

## <a name="scenario-1-office-365-apps-require-approved-apps-with-app-protection-policies"></a>Cenário 1: Aplicativos do Office 365 exigem aplicativos aprovados com políticas de proteção de aplicativos

Neste cenário, contoso decidiu que todo o acesso móvel aos recursos do Office 365 deve usar aplicativos de clientes aprovados, como outlook mobile, OneDrive e Microsoft Teams protegidos por uma política de proteção de aplicativos antes de receber acesso. Todos os seus usuários já fazem login com credenciais Azure AD e têm licenças atribuídas a eles que incluem Azure AD Premium P1 ou P2 e Microsoft Intune.

As organizações devem concluir as seguintes etapas para exigir o uso de um aplicativo cliente aprovado em dispositivos móveis.

**Passo 1: Configure uma política de acesso condicional a ad azure para o Office 365**

1. Faça login no **portal Azure** como administrador global, administrador de segurança ou administrador de Acesso Condicional.
1. Navegue até o Acesso**Condicional de****Segurança** > do Diretório >  **Ativo do Azure**.
1. Selecione **Nova política**.
1. Dê um nome à sua apólice. Recomendamos que as organizações criem um padrão significativo para os nomes de suas políticas.
1. Em **Atribuições,** selecione **Usuários e grupos**
   1. Em **Incluir,** selecione **Todos os usuários** ou os usuários e **grupos** específicos aos os que deseja aplicar esta diretiva. 
   1. Selecione **Feito**.
1. Em **Aplicativos ou ações** > na Nuvem**Incluem,** selecione **Office 365 (visualização)**.
1. Em **Condições,** selecione **Plataformas de dispositivo**.
   1. Definir **configurar** como **Sim**.
   1. Inclua **Android** e **iOS**.
1. Em **Condições,** selecione **aplicativos cliente (visualização)**.
   1. Definir **configurar** como **Sim**.
   1. Selecione **Aplicativos móveis e clientes da área de trabalho** e **Clientes de autenticação moderna**.
1. Em Access**controls Grant,** selecione as **seguintes** > opções:
   - **Exigir um aplicativo cliente aprovado**
   - **Exigir uma política de proteção do aplicativo (versão prévia)**
   - **Exigir todos os controles selecionados**
1. Confirme suas configurações e **configure Ativar a diretiva** **em**.
1. Selecione **Criar** para criar e habilitar sua política.

**Passo 2: Configure uma política de acesso condicional do Azure AD para intercâmbio on-line com activesync (EAS)**

Para a diretiva Acesso Condicional nesta etapa, configure os seguintes componentes:

1. Navegue até o Acesso**Condicional de****Segurança** > do Diretório >  **Ativo do Azure**.
1. Selecione **Nova política**.
1. Dê um nome à sua apólice. Recomendamos que as organizações criem um padrão significativo para os nomes de suas políticas.
1. Em **Atribuições,** selecione **Usuários e grupos**
   1. Em **Incluir,** selecione **Todos os usuários** ou os usuários e **grupos** específicos aos os que deseja aplicar esta diretiva. 
   1. Selecione **Feito**.
1. Em **Aplicativos ou ações** > na Nuvem**Incluem,** selecione **Office 365 Exchange Online**.
1. Em **Condições:**
   1. **Aplicativos clientes (visualização)**:
      1. Definir **configurar** como **Sim**.
      1. Selecione **aplicativos móveis e clientes de desktop** e **clientes do Exchange ActiveSync**.
1. Em **Controles de acesso,** > **Grant**selecione acesso **a subvenção,** **exija política de proteção de aplicativos**e **selecione Selecionar**.
1. Confirme suas configurações e **configure Ativar a diretiva** **em**.
1. Selecione **Criar** para criar e habilitar sua política.

**Passo 3: Configure a política de proteção de aplicativos Intune para aplicativos clientes iOS e Android**

Reveja o artigo [Como criar e atribuir políticas de proteção](/intune/apps/app-protection-policies)de aplicativos, para medidas para criar políticas de proteção de aplicativos para Android e iOS. 

## <a name="scenario-2-exchange-online-and-sharepoint-online-require-an-approved-client-app-and-app-protection-policy"></a>Cenário 2: Exchange Online e SharePoint Online exigem uma política de proteção de aplicativos e aplicativos aprovados

Neste cenário, contoso decidiu que os usuários só podem acessar dados de e-mail e SharePoint em dispositivos móveis, desde que usem um aplicativo cliente aprovado como o Outlook mobile protegido por uma política de proteção de aplicativos antes de receber acesso. Todos os seus usuários já fazem login com credenciais Azure AD e têm licenças atribuídas a eles que incluem Azure AD Premium P1 ou P2 e Microsoft Intune.

As organizações devem concluir as três etapas seguintes para exigir o uso de um aplicativo cliente aprovado em dispositivos móveis e clientes Do Exchange ActiveSync.

**Passo 1: Política para clientes de autenticação moderna baseados em Android e iOS que exigem o uso de uma política de proteção de aplicativos e aplicativos de cliente aprovado ao acessar o Exchange Online e o SharePoint Online.**

1. Faça login no **portal Azure** como administrador global, administrador de segurança ou administrador de Acesso Condicional.
1. Navegue até o Acesso**Condicional de****Segurança** > do Diretório >  **Ativo do Azure**.
1. Selecione **Nova política**.
1. Dê um nome à sua apólice. Recomendamos que as organizações criem um padrão significativo para os nomes de suas políticas.
1. Em **Atribuições,** selecione **Usuários e grupos**
   1. Em **Incluir,** selecione **Todos os usuários** ou os usuários e **grupos** específicos aos os que deseja aplicar esta diretiva. 
   1. Selecione **Feito**.
1. Em **aplicativos ou ações** > na**Nuvem, selecione** **o Office 365 Exchange Online** e o Office **365 SharePoint Online**.
1. Em **Condições,** selecione **Plataformas de dispositivo**.
   1. Definir **configurar** como **Sim**.
   1. Inclua **Android** e **iOS**.
1. Em **Condições,** selecione **aplicativos cliente (visualização)**.
   1. Definir **configurar** como **Sim**.
   1. Selecione **Aplicativos móveis e clientes da área de trabalho** e **Clientes de autenticação moderna**.
1. Em Access**controls Grant,** selecione as **seguintes** > opções:
   - **Exigir um aplicativo cliente aprovado**
   - **Exigir uma política de proteção do aplicativo (versão prévia)**
   - **Exija um dos controles selecionados**
1. Confirme suas configurações e **configure Ativar a diretiva** **em**.
1. Selecione **Criar** para criar e habilitar sua política.

**Passo 2: Política para clientes Do Exchange ActiveSync que requerem o uso de um aplicativo cliente aprovado.**

1. Navegue até o Acesso**Condicional de****Segurança** > do Diretório >  **Ativo do Azure**.
1. Selecione **Nova política**.
1. Dê um nome à sua apólice. Recomendamos que as organizações criem um padrão significativo para os nomes de suas políticas.
1. Em **Atribuições,** selecione **Usuários e grupos**
   1. Em **Incluir,** selecione **Todos os usuários** ou os usuários e **grupos** específicos aos os que deseja aplicar esta diretiva. 
   1. Selecione **Feito**.
1. Em **Aplicativos ou ações** > na Nuvem**Incluem,** selecione **Office 365 Exchange Online**.
1. Em **Condições:**
   1. **Aplicativos clientes (visualização)**:
      1. Definir **configurar** como **Sim**.
      1. Selecione **aplicativos móveis e clientes de desktop** e **clientes do Exchange ActiveSync**.
1. Em **Controles de acesso,** > **Grant**selecione acesso **a subvenção,** **exija política de proteção de aplicativos**e **selecione Selecionar**.
1. Confirme suas configurações e **configure Ativar a diretiva** **em**.
1. Selecione **Criar** para criar e habilitar sua política.

**Passo 3: Configure a política de proteção de aplicativos Intune para aplicativos clientes iOS e Android.**

Reveja o artigo [Como criar e atribuir políticas de proteção](/intune/apps/app-protection-policies)de aplicativos, para medidas para criar políticas de proteção de aplicativos para Android e iOS. 

## <a name="next-steps"></a>Próximas etapas

[O que é Acesso Condicional?](overview.md)

[Componentes de acesso condicional](concept-conditional-access-policies.md)

[Políticas de Acesso Condicional Comum](concept-conditional-access-policy-common.md)

