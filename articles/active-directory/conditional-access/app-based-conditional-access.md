---
title: Aplicativos cliente aprovados com acesso condicional-Azure Active Directory
description: Saiba como exigir aplicativos cliente aprovados para acesso ao aplicativo de nuvem com acesso condicional no Azure Active Directory.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: spunukol
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1a8832234978a2c8b2db25d88b5dd6c211b634b7
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/13/2020
ms.locfileid: "77186464"
---
# <a name="how-to-require-approved-client-apps-for-cloud-app-access-with-conditional-access"></a>Como: exigir aplicativos cliente aprovados para acesso ao aplicativo de nuvem com acesso condicional 

Os funcionários usam dispositivos móveis para tarefas de pessoais e corporativas. Ao mesmo tempo que garante que seus funcionários sejam produtivos, você também quer impedir a perda de dados. Com o acesso condicional do Azure Active Directory (AD do Azure), você pode restringir o acesso aos aplicativos de nuvem a aplicativos cliente aprovados que podem proteger seus dados corporativos.  

Este tópico explica como configurar políticas de acesso de condição que exigem aplicativos cliente aprovados.

## <a name="overview"></a>Visão geral

Com o [acesso condicional do Azure ad](overview.md), você pode ajustar como os usuários autorizados podem acessar seus recursos. Por exemplo, você pode limitar o acesso aos seus aplicativos de nuvem a dispositivos confiáveis.

Você pode usar [políticas de proteção do aplicativo Intune](https://docs.microsoft.com/intune/app-protection-policy) para ajudar a proteger os dados da empresa. As políticas de proteção do aplicativo Intune não exigem a solução de gerenciamento de dispositivo móvel (MDM), que permite que você proteja os dados da sua empresa com ou sem registro de dispositivos em uma solução de gerenciamento de dispositivos.

Azure Active Directory acesso condicional permite que você limite o acesso aos aplicativos de nuvem para aplicativos cliente que dão suporte a políticas de proteção de aplicativo do Intune. Por exemplo, você pode restringir o acesso ao Exchange Online para o aplicativo do Outlook.

Na terminologia de acesso condicional, esses aplicativos cliente são conhecidos como **aplicativos cliente aprovados**.  

![Acesso Condicional](./media/app-based-conditional-access/05.png)

Para obter uma lista de aplicativos cliente aprovada, veja [requisito de aplicativo cliente aprovado](concept-conditional-access-grant.md).

Você pode combinar políticas de acesso condicional com base no aplicativo com outras políticas, como [políticas de acesso condicional com base no dispositivo](require-managed-devices.md) , para fornecer flexibilidade na proteção de dados para dispositivos pessoais e corporativos.

## <a name="before-you-begin"></a>Antes de começar

Este tópico pressupõe que você esteja familiarizado com:

- O [requisito de aplicativo cliente aprovado](concept-conditional-access-grant.md).
- Os conceitos básicos de [acesso condicional no Azure Active Directory](overview.md).
- Como [Configurar uma política de acesso condicional](app-based-mfa.md).
- A [migração de políticas de acesso condicional](best-practices.md#policy-migration).

## <a name="prerequisites"></a>Prerequisites

Para criar uma política de acesso condicional com base no aplicativo, você deve ter uma assinatura Enterprise Mobility + Security ou Azure Active Directory Premium e os usuários devem ser licenciados para o EMS ou o Azure AD. 

## <a name="exchange-online-policy"></a>Política do Exchange Online 

Esse cenário consiste em uma política de acesso condicional baseada em aplicativo para acesso ao Exchange Online.

### <a name="scenario-playbook"></a>Guia estratégico do cenário

Este cenário pressupõe que um usuário:

- Configura o email usando um aplicativo de email nativo no iOS ou Android para se conectar ao Exchange
- Recebe um email que indica que o acesso só está disponível usando o aplicativo Outlook
- Baixa o aplicativo com o link
- Abre o aplicativo Outlook e o assina com as credenciais do Azure AD
- É solicitado a instalar o Authenticator (iOS) ou o Portal da Empresa (Android) para continuar
- Instala o aplicativo e pode retornar ao aplicativo Outlook para continuar
- É solicitado a registrar um dispositivo
- É capaz de acessar o email

Todas as políticas de proteção de aplicativo do Intune são ativadas no momento em que os dados corporativos do Access e podem solicitar que o usuário reinicie o aplicativo, use um PIN adicional, etc. (se configurado para o aplicativo e a plataforma).

### <a name="configuration"></a>Configuração 

**Etapa 1-configurar uma política de acesso condicional do Azure AD para o Exchange Online**

Para a política de acesso condicional nesta etapa, você precisa configurar os seguintes componentes:

1. O **nome** da política de acesso condicional.
1. **Usuários e grupos**: cada política de acesso condicional deve ter pelo menos um usuário ou grupo selecionado.
1. **Aplicativos de nuvem:** como aplicativos de nuvem, você precisa selecionar o **Office 365 Exchange Online**.
1. **Condições:** como **Condições**, você precisa configurar **Plataformas de dispositivo** e **Aplicativos cliente**:
   1. Como **Plataformas de dispositivo**, selecione **Android** e **iOS**.
   1. Como **Aplicativos cliente (versão prévia)** , selecione **Aplicativos móveis e aplicativos da área de trabalho** e **Clientes de autenticação moderna**.
1. Como **Controles de acesso**, você precisa ter a opção **Exigir aplicativo cliente aprovado (versão prévia)** selecionada.

   ![Acesso Condicional](./media/app-based-conditional-access/05.png)

**Etapa 2 – configurar uma política de acesso condicional do Azure AD para o Exchange Online com o EAS (Active Sync)**

Para a política de acesso condicional nesta etapa, você precisa configurar os seguintes componentes:

1. O **nome** da política de acesso condicional.
1. **Usuários e grupos**: cada política de acesso condicional deve ter pelo menos um usuário ou grupo selecionado.
1. **Aplicativos de nuvem:** como aplicativos de nuvem, você precisa selecionar o **Office 365 Exchange Online**.
1. **Condições:** como **Condições**, você precisa configurar **Aplicativos cliente (versão prévia)** . 
   1. Como **Aplicativos cliente (versão prévia)** , selecione **Aplicativos móveis e aplicativos da área de trabalho** e **Clientes do Exchange ActiveSync**.
   1. Como **Controles de acesso**, você precisa ter a opção **Exigir aplicativo cliente aprovado (versão prévia)** selecionada.

      ![Acesso Condicional](./media/app-based-conditional-access/05.png)

**Etapa 3 – configurar a política de proteção de aplicativo do Intune para aplicativos cliente Android e iOS**

Para obter mais informações, consulte [Proteger aplicativos e dados com o Microsoft Intune](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune).

## <a name="exchange-online-and-sharepoint-online-policy"></a>Política do Exchange Online e do SharePoint Online

Esse cenário consiste em um acesso condicional com a política de gerenciamento de aplicativo móvel para acesso ao Exchange Online e ao SharePoint Online com aplicativos aprovados.

### <a name="scenario-playbook"></a>Guia estratégico do cenário

Este cenário pressupõe que um usuário:

- Tenta usar o aplicativo SharePoint para se conectar e para exibir os sites corporativos desse usuário
- Tentativas de entrar com as mesmas credenciais que as credenciais do aplicativo Outlook
- Não precisa registrar novamente e pode obter acesso aos recursos

### <a name="configuration"></a>Configuração

**Etapa 1-configurar uma política de acesso condicional do Azure AD para o Exchange Online e o SharePoint Online**

Para a política de acesso condicional nesta etapa, você precisa configurar os seguintes componentes:

1. O **nome** da política de acesso condicional.
1. **Usuários e grupos**: cada política de acesso condicional deve ter pelo menos um usuário ou grupo selecionado.
1. **Aplicativos de nuvem:** como aplicativos de nuvem, você precisa selecionar o **Office 365 Exchange Online** e o **Office 365 SharePoint Online**. 
1. **Condições:** como **Condições**, você precisa configurar **Plataformas de dispositivo** e **Aplicativos cliente**:
   1. Como **Plataformas de dispositivo**, selecione **Android** e **iOS**.
   1. Como **Aplicativos cliente (versão prévia)** , selecione **Aplicativos móveis e clientes da área de trabalho** e **Clientes de autenticação moderna**.
1. Como **Controles de acesso**, você precisa ter a opção **Exigir aplicativo cliente aprovado (versão prévia)** selecionada.

   ![Acesso Condicional](./media/app-based-conditional-access/05.png)

**Etapa 2 – configurar uma política de acesso condicional do Azure AD para o Exchange Online com o EAS (Active Sync)**

Para a política de acesso condicional nesta etapa, você precisa configurar os seguintes componentes:

1. O **nome** da política de acesso condicional.
1. **Usuários e grupos**: cada política de acesso condicional deve ter pelo menos um usuário ou grupo selecionado.
1. **Aplicativos de nuvem:** como aplicativos de nuvem, você precisa selecionar o **Office 365 Exchange Online**. Online 
1. **Condições:** como **Condições**, você precisa configurar **Aplicativos cliente**:
   1. Como **Aplicativos cliente (versão prévia)** , selecione **Aplicativos móveis e aplicativos da área de trabalho** e **Clientes do Exchange ActiveSync**.
   1. Como **Controles de acesso**, você precisa ter a opção **Exigir aplicativo cliente aprovado (versão prévia)** selecionada.

      ![Acesso Condicional](./media/app-based-conditional-access/05.png)

**Etapa 3 – configurar a política de proteção de aplicativo do Intune para aplicativos cliente Android e iOS**

![Acesso Condicional](./media/app-based-conditional-access/09.png)

Para obter mais informações, consulte [Proteger aplicativos e dados com o Microsoft Intune](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune).

## <a name="app-based-or-compliant-device-policy-for-exchange-online-and-sharepoint-online"></a>Política de dispositivo compatível ou com base no aplicativo para o Exchange Online e o SharePoint Online

Esse cenário consiste em uma política de acesso condicional de dispositivo compatível ou baseada em aplicativo para acesso ao Exchange Online.

### <a name="scenario-playbook"></a>Guia estratégico do cenário

Este cenário pressupõe que:
 
- Alguns usuários já estão registrados (com ou sem dispositivos corporativos)
- Os usuários que não estão registrados com o Azure AD usando um aplicativo protegido por aplicativo precisam registrar um dispositivo para acessar os recursos
- Os usuários registrados usando o aplicativo protegido não precisam registrar novamente o dispositivo

### <a name="configuration"></a>Configuração

**Etapa 1-configurar uma política de acesso condicional do Azure AD para o Exchange Online e o SharePoint Online**

Para a política de acesso condicional nesta etapa, você precisa configurar os seguintes componentes:

1. O **nome** da política de acesso condicional.
1. **Usuários e grupos**: cada política de acesso condicional deve ter pelo menos um usuário ou grupo selecionado.
1. **Aplicativos de nuvem:** como aplicativos de nuvem, você precisa selecionar o **Office 365 Exchange Online** e o **Office 365 SharePoint Online**. 
1. **Condições:** como **Condições**, você precisa configurar **Plataformas de dispositivo** e **Aplicativos cliente**. 
   1. Como **Plataformas de dispositivo**, selecione **Android** e **iOS**.
   1. Como **Aplicativos cliente (versão prévia)** , selecione **Aplicativos móveis e clientes da área de trabalho** e **Clientes de autenticação moderna**.
1. Como **Controles de acesso**, você precisa ter o seguinte selecionado:
   - **Exigir que o dispositivo seja marcado como em conformidade**
   - **Exigir um aplicativo cliente aprovado (versão prévia)**
   - **Exigir um dos controles selecionados**   
 
      ![Acesso Condicional](./media/app-based-conditional-access/11.png)

**Etapa 2 – configurar uma política de acesso condicional do Azure AD para o Exchange Online com o EAS (Active Sync)**

Para a política de acesso condicional nesta etapa, você precisa configurar os seguintes componentes:

1. O **nome** da política de acesso condicional.
1. **Usuários e grupos**: cada política de acesso condicional deve ter pelo menos um usuário ou grupo selecionado.
1. **Aplicativos de nuvem:** como aplicativos de nuvem, você precisa selecionar o **Office 365 Exchange Online**. 
1. **Condições:** como **Condições**, você precisa configurar **Aplicativos cliente**. 
   1. Como **Aplicativos cliente (versão prévia)** , selecione **Aplicativos móveis e aplicativos da área de trabalho** e **Clientes do Exchange ActiveSync**.
1. Como **Controles de acesso**, você precisa ter a opção **Exigir aplicativo cliente aprovado (versão prévia)** selecionada.
 
   ![Acesso Condicional](./media/app-based-conditional-access/11.png)

**Etapa 3 – configurar a política de proteção de aplicativo do Intune para aplicativos cliente Android e iOS**

![Acesso Condicional](./media/app-based-conditional-access/09.png)

Para obter mais informações, consulte [Proteger aplicativos e dados com o Microsoft Intune](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune).

## <a name="app-based-and-compliant-device-policy-for-exchange-online-and-sharepoint-online"></a>Política de dispositivo compatível e com base no aplicativo para o Exchange Online e o SharePoint Online

Esse cenário consiste em uma política de acesso condicional de dispositivo compatível e com base no aplicativo para acesso ao Exchange Online.

### <a name="scenario-playbook"></a>Guia estratégico do cenário

Este cenário pressupõe que um usuário:
 
- Configura o email usando um aplicativo de email nativo no iOS ou Android para se conectar ao Exchange
- Recebe um email que indica que o acesso requer que seu dispositivo seja registrado
- Baixa e entra no Portal da Empresa
- Verifica o email e é solicitado a usar o aplicativo Outlook
- Baixa o aplicativo Outlook
- Abre o aplicativo Outlook e insere as credenciais usadas no registro
- O usuário é capaz de acessar o email

Quaisquer eventuais políticas de Proteção de Aplicativo do Intune são ativadas no momento do acesso aos dados corporativos e podem solicitar que o usuário reinicie o aplicativo, que use um PIN adicional, etc. (se configuradas para o aplicativo e a plataforma)

### <a name="configuration"></a>Configuração

**Etapa 1-configurar uma política de acesso condicional do Azure AD para o Exchange Online e o SharePoint Online**

Para a política de acesso condicional nesta etapa, você precisa configurar os seguintes componentes:

1. O **nome** da política de acesso condicional.
1. **Usuários e grupos**: cada política de acesso condicional deve ter pelo menos um usuário ou grupo selecionado.
1. **Aplicativos de nuvem:** como aplicativos de nuvem, você precisa selecionar o **Office 365 Exchange Online** e o **Office 365 SharePoint Online**. 
1. **Condições:** como **Condições**, você precisa configurar **Plataformas de dispositivo** e **Aplicativos cliente**. 
   1. Como **Plataformas de dispositivo**, selecione **Android** e **iOS**.
   1. Como **Aplicativos cliente (versão prévia)** , selecione **Aplicativos móveis e aplicativos da área de trabalho** e **Clientes de autenticação moderna**.
1. Como **Controles de acesso**, você precisa ter o seguinte selecionado:
   - **Exigir que o dispositivo seja marcado como em conformidade**
   - **Exigir um aplicativo cliente aprovado (versão prévia)**
   - **Exigir todos os controles selecionados**   
 
      ![Acesso Condicional](./media/app-based-conditional-access/13.png)

**Etapa 2 – configurar uma política de acesso condicional do Azure AD para o Exchange Online com o EAS (Active Sync)**

Para a política de acesso condicional nesta etapa, você precisa configurar os seguintes componentes:

1. O **nome** da política de acesso condicional.
1. **Usuários e grupos**: cada política de acesso condicional deve ter pelo menos um usuário ou grupo selecionado.
1. **Aplicativos de nuvem:** como aplicativos de nuvem, você precisa selecionar o **Office 365 Exchange Online**. 
1. **Condições:** como **Condições**, você precisa configurar **Aplicativos cliente (versão prévia)** . 
   1. Como **Aplicativos cliente (versão prévia)** , selecione **Aplicativos móveis e aplicativos da área de trabalho** e **Clientes do Exchange ActiveSync**.

   ![Acesso Condicional](./media/app-based-conditional-access/92.png)

1. Como **Controles de acesso**, você precisa ter o seguinte selecionado:
   - **Exigir que o dispositivo seja marcado como em conformidade**
   - **Exigir um aplicativo cliente aprovado (versão prévia)**
   - **Exigir todos os controles selecionados**   

**Etapa 3 – configurar a política de proteção de aplicativo do Intune para aplicativos cliente Android e iOS**

![Acesso Condicional](./media/app-based-conditional-access/09.png)

Para obter mais informações, consulte [Proteger aplicativos e dados com o Microsoft Intune](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune).

## <a name="next-steps"></a>Próximas etapas

Se você quiser saber como configurar uma política de Acesso Condicional, consulte [Exigir MFA para aplicativos específicos com Acesso Condicional do Azure Active Directory](app-based-mfa.md).

Se você estiver pronto para configurar políticas de acesso condicional para seu ambiente, confira as [melhores práticas para o acesso condicional no Azure Active Directory](best-practices.md). 
