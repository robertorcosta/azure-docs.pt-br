---
title: Aplicativos de clienteaprovados com Acesso Condicional - Azure Active Directory
description: Saiba como exigir aplicativos de clientes aprovados para acesso a aplicativos em nuvem com acesso condicional no Azure Active Directory.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 03/04/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: spunukol, rosssmi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7a215e2bb7d9d1cf9013414037383590456296cd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79480888"
---
# <a name="how-to-require-approved-client-apps-for-cloud-app-access-with-conditional-access"></a>Como: Exigir aplicativos de clientes aprovados para acesso a aplicativos na nuvem com acesso condicional

As pessoas usam regularmente seus dispositivos móveis para tarefas pessoais e de trabalho. Ao mesmo tempo em que garantem que os funcionários possam ser produtivos, as organizações também querem evitar a perda de dados de aplicativos potencialmente inseguros. Com o Conditional Access, as organizações podem restringir o acesso a aplicativos clientes aprovados (capazes de autenticação moderna).

Este artigo apresenta dois cenários para configurar políticas de acesso condicional para recursos como Office 365, Exchange Online e SharePoint Online.

- [Cenário 1: Aplicativos do Office 365 exigem um aplicativo cliente aprovado](#scenario-1-office-365-apps-require-an-approved-client-app)
- [Cenário 2: Exchange Online e SharePoint Online exigem um aplicativo cliente aprovado](#scenario-2-exchange-online-and-sharepoint-online-require-an-approved-client-app)

No Conditional Access, essa funcionalidade é conhecida como exigir um aplicativo cliente aprovado. Para obter uma lista de aplicativos cliente aprovada, veja [requisito de aplicativo cliente aprovado](concept-conditional-access-grant.md#require-approved-client-app).

> [!NOTE]
> Para exigir aplicativos clientes aprovados para dispositivos iOS e Android, esses dispositivos devem primeiro se registrar no Azure AD.

## <a name="scenario-1-office-365-apps-require-an-approved-client-app"></a>Cenário 1: Aplicativos do Office 365 exigem um aplicativo cliente aprovado

Neste cenário, contoso decidiu que os usuários que usam dispositivos móveis podem acessar todos os serviços do Office 365, desde que usem aplicativos de clientes aprovados, como o Outlook mobile, OneDrive e Microsoft Teams. Todos os seus usuários já fazem login com credenciais Azure AD e têm licenças atribuídas a eles que incluem Azure AD Premium P1 ou P2 e Microsoft Intune.

As organizações devem concluir as três etapas seguintes para exigir o uso de um aplicativo cliente aprovado em dispositivos móveis.

**Passo 1: Política para clientes de autenticação moderna baseados em Android e iOS que requerem o uso de um aplicativo cliente aprovado ao acessar o Exchange Online.**

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
1. Em **Controles de acesso,** > **Grant**selecione acesso **a subvenção,** exija um aplicativo de cliente **aprovado**e **selecione Selecionar**.
1. Confirme suas configurações e **configure Ativar a diretiva** **em**.
1. Selecione **Criar** para criar e habilitar sua política.

**Passo 2: Configure uma política de acesso condicional do Azure AD para intercâmbio on-line com activesync (EAS)**

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
1. Em **Controles de acesso,** > **Grant**selecione acesso **a subvenção,** exija um aplicativo de cliente **aprovado**e **selecione Selecionar**.
1. Confirme suas configurações e **configure Ativar a diretiva** **em**.
1. Selecione **Criar** para criar e habilitar sua política.

**Passo 3: Configure a política de proteção de aplicativos Intune para aplicativos clientes iOS e Android.**

Reveja o artigo [Como criar e atribuir políticas de proteção](/intune/apps/app-protection-policies)de aplicativos, para medidas para criar políticas de proteção de aplicativos para Android e iOS. 

## <a name="scenario-2-exchange-online-and-sharepoint-online-require-an-approved-client-app"></a>Cenário 2: Exchange Online e SharePoint Online exigem um aplicativo cliente aprovado

Neste cenário, contoso decidiu que os usuários só podem acessar dados de e-mail e SharePoint em dispositivos móveis, desde que usem um aplicativo cliente aprovado como o Outlook mobile. Todos os seus usuários já fazem login com credenciais Azure AD e têm licenças atribuídas a eles que incluem Azure AD Premium P1 ou P2 e Microsoft Intune.

As organizações devem concluir as três etapas seguintes para exigir o uso de um aplicativo cliente aprovado em dispositivos móveis e clientes Do Exchange ActiveSync.

**Passo 1: Política para clientes de autenticação moderna baseados em Android e iOS que requerem o uso de um aplicativo cliente aprovado ao acessar o Exchange Online e o SharePoint Online.**

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
1. Em **Controles de acesso,** > **Grant**selecione acesso **a subvenção,** exija um aplicativo de cliente **aprovado**e **selecione Selecionar**.
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
1. Em **Controles de acesso,** > **Grant**selecione acesso **a subvenção,** exija um aplicativo de cliente **aprovado**e **selecione Selecionar**.
1. Confirme suas configurações e **configure Ativar a diretiva** **em**.
1. Selecione **Criar** para criar e habilitar sua política.

**Passo 3: Configure a política de proteção de aplicativos Intune para aplicativos clientes iOS e Android.**

Reveja o artigo [Como criar e atribuir políticas de proteção](/intune/apps/app-protection-policies)de aplicativos, para medidas para criar políticas de proteção de aplicativos para Android e iOS. 

## <a name="next-steps"></a>Próximas etapas

[O que é acesso condicional?](overview.md)

[Componentes de acesso condicional](concept-conditional-access-policies.md)

[Políticas de Acesso Condicional Comum](concept-conditional-access-policy-common.md)
