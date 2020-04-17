---
title: Aplicativos ou ações em nuvem na política de acesso condicional - Azure Active Directory
description: O que são aplicativos ou ações em nuvem em uma política de acesso condicionado ao Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 02/11/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: b15b022726c09ccbaf9674775d114c8dd1916e1d
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81457290"
---
# <a name="conditional-access-cloud-apps-or-actions"></a>Acesso Condicional: Aplicativos ou ações em nuvem

Aplicativos ou ações em nuvem são um sinal-chave em uma política de Acesso Condicional. As políticas de acesso condicional permitem que os administradores atribuam controles a aplicativos ou ações específicas.

- Os administradores podem escolher entre a lista de aplicativos que incluem aplicativos Microsoft incorporados e quaisquer [aplicativos integrados do Azure AD,](../manage-apps/what-is-application-management.md) incluindo galeria, não galeria e aplicativos publicados através [do Application Proxy](../manage-apps/what-is-application-proxy.md).
- Os administradores podem optar por definir a política não com base em um aplicativo na nuvem, mas em uma ação do usuário. A única ação suportada é registrar informações de segurança (visualização), permitindo que o Acesso Condicional imponha controles em torno da experiência combinada de [registro de informações de segurança](../authentication/howto-registration-mfa-sspr-combined.md).

![Defina uma política de acesso condicional e especifique aplicativos na nuvem](./media/concept-conditional-access-cloud-apps/conditional-access-cloud-apps-or-actions.png)

## <a name="microsoft-cloud-applications"></a>Aplicativos em nuvem da Microsoft

Muitos dos aplicativos de nuvem da Microsoft existentes estão incluídos na lista de aplicativos que você pode selecionar. 

Os administradores podem atribuir uma política de acesso condicional aos seguintes aplicativos de nuvem da Microsoft. Alguns aplicativos como o Office 365 (preview) e o Microsoft Azure Management incluem vários aplicativos ou serviços relacionados para crianças. A lista a seguir não é exaustiva e está sujeita a alterações.

- [Escritório 365 (visualização)](#office-365-preview)
- Azure Analysis Services
- Azure DevOps
- [Banco de Dados SQL do Azure e SQL Data Warehouse do Azure](../../sql-database/sql-database-conditional-access.md)
- Dynamics CRM Online
- Análise do Microsoft Application Insights
- [Proteção de Informações do Microsoft Azure](/azure/information-protection/faqs#i-see-azure-information-protection-is-listed-as-an-available-cloud-app-for-conditional-accesshow-does-this-work)
- [Gerenciamento do Microsoft Azure](#microsoft-azure-management)
- Gerenciamento de assinaturas do Microsoft Azure
- Microsoft Cloud App Security
- Portal de controle de acesso de ferramentas de comércio da Microsoft
- Serviço de autenticação de ferramentas de comércio da Microsoft
- Microsoft Flow
- Microsoft Forms
- Microsoft Intune
- [Registro do Microsoft Intune](/intune/enrollment/multi-factor-authentication)
- Microsoft Planner
- Microsoft PowerApps
- Pesquisa da Microsoft em Bing
- Microsoft StaffHub
- Microsoft Stream
- Equipes da Microsoft
- Office 365 Exchange Online
- Office 365 SharePoint Online
- Office 365 Yammer
- Office Delve
- Office Sway
- Outlook Groups
- Serviço do Power BI
- Project Online
- Skype for Business Online
- Rede Privada Virtual (VPN)
- Windows Defender ATP

### <a name="office-365-preview"></a>Escritório 365 (visualização)

O Office 365 fornece serviços de produtividade e colaboração baseados em nuvem, como Exchange, SharePoint e Microsoft Teams. Os serviços em nuvem do Office 365 estão profundamente integrados para garantir experiências suaves e colaborativas. Essa integração pode causar confusão ao criar políticas, pois alguns aplicativos, como o Microsoft Teams, têm dependências de outros, como SharePoint ou Exchange.

O aplicativo Office 365 (preview) permite direcionar esses serviços de uma só vez. Recomendamos o uso do novo aplicativo Office 365 (preview), em vez de direcionar aplicativos individuais em nuvem. Direcionar esse grupo de aplicativos ajuda a evitar problemas que podem surgir devido a políticas e dependências inconsistentes.

Os administradores podem optar por excluir aplicativos específicos da política, se desejarem, incluindo o aplicativo Office 365 (preview) e excluindo os aplicativos específicos de sua escolha na política.

Principais aplicativos incluídos no aplicativo cliente Office 365 (preview):

   - Microsoft Flow
   - Microsoft Forms
   - Microsoft Stream
   - Microsoft To-Do
   - Equipes da Microsoft
   - Office 365 Exchange Online
   - Office 365 SharePoint Online
   - Serviço de busca do Office 365
   - Office 365 Yammer
   - Office Delve
   - Office Online
   - Office.com
   - OneDrive
   - PowerApps
   - Skype for Business Online
   - Sway

### <a name="microsoft-azure-management"></a>Gerenciamento do Microsoft Azure

O aplicativo Microsoft Azure Management inclui vários serviços subjacentes. 

   - Portal do Azure
   - Provedor de gerenciador de recursos do Azure
   - APIs do modelo de implantação clássica
   - Azure PowerShell
   - Portal de administrador de assinaturas do Visual Studio
   - Azure DevOps
   - Portal Azure Data Factory

> [!NOTE]
> O aplicativo Microsoft Azure Management aplica-se ao Azure PowerShell, que chama de API do Azure Resource Manager. Não se aplica ao PowerShell do Azure Active Directory, que chama o Microsoft Graph.

## <a name="other-applications"></a>Outros aplicativos

Além dos aplicativos da Microsoft, os administradores podem adicionar qualquer aplicativo registrado no Azure AD às políticas de Acesso Condicional. Esses aplicativos podem incluir: 

- Aplicativos publicados através [do Proxy de aplicativo Azure AD](../manage-apps/what-is-application-proxy.md)
- [Aplicativos adicionados da galeria](../manage-apps/add-application-portal.md)
- [Aplicações personalizadas que não estão na galeria](../manage-apps/add-non-gallery-app.md)
- [Aplicativos legados publicados através de controladores e redes de entrega de aplicativos](../manage-apps/secure-hybrid-access.md)

## <a name="user-actions"></a>Ações do usuário

As ações do usuário são tarefas que podem ser executadas por um usuário. A única ação suportada atualmente é **registrar informações de segurança,** o que permite que a política de Acesso Condicional se aplique quando os usuários habilitados para o registro combinado tentam registrar suas informações de segurança. Mais informações podem ser encontradas no artigo, [registro combinado de informações de segurança.](../authentication/concept-registration-mfa-sspr-combined.md)

## <a name="next-steps"></a>Próximas etapas

- [Acesso Condicional: Condições](concept-conditional-access-conditions.md)

- [Políticas comuns de Acesso Condicional](concept-conditional-access-policy-common.md)
- [Dependências de aplicativos de cliente](service-dependencies.md)
