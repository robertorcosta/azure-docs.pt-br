---
title: Approved client apps with Conditional Access - Azure Active Directory
description: Learn how to require approved client apps for cloud app access with Conditional Access in Azure Active Directory.
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
ms.openlocfilehash: c173d0e17166911e28fea3d1c5820879d17af4a8
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/22/2019
ms.locfileid: "74381115"
---
# <a name="how-to-require-approved-client-apps-for-cloud-app-access-with-conditional-access"></a>How To: Require approved client apps for cloud app access with Conditional Access 

Os funcionários usam dispositivos móveis para tarefas de pessoais e corporativas. Ao mesmo tempo que garante que seus funcionários sejam produtivos, você também quer impedir a perda de dados. With Azure Active Directory (Azure AD) Conditional Access, you can restrict access to your cloud apps to approved client apps that can protect your corporate data.  

Este tópico explica como configurar políticas de acesso de condição que exigem aplicativos cliente aprovados.

## <a name="overview"></a>Visão Geral

With [Azure AD Conditional Access](overview.md), you can fine-tune how authorized users can access your resources. Por exemplo, você pode limitar o acesso aos seus aplicativos de nuvem a dispositivos confiáveis.

Você pode usar [políticas de proteção do aplicativo Intune](https://docs.microsoft.com/intune/app-protection-policy) para ajudar a proteger os dados da empresa. As políticas de proteção do aplicativo Intune não exigem a solução de gerenciamento de dispositivo móvel (MDM), que permite que você proteja os dados da sua empresa com ou sem registro de dispositivos em uma solução de gerenciamento de dispositivos.

Azure Active Directory Conditional Access enables you to limit access to your cloud apps to client apps that support Intune app protection policies. Por exemplo, você pode restringir o acesso ao Exchange Online para o aplicativo do Outlook.

In the Conditional Access terminology, these client apps are known as **approved client apps**.  

![Acesso condicional](./media/app-based-conditional-access/05.png)

Para obter uma lista de aplicativos cliente aprovada, veja [requisito de aplicativo cliente aprovado](technical-reference.md#approved-client-app-requirement).

You can combine app-based Conditional Access policies with other policies such as [device-based Conditional Access policies](require-managed-devices.md) to provide flexibility in how to protect data for both personal and corporate devices.

## <a name="before-you-begin"></a>Antes de começar

Este tópico pressupõe que você esteja familiarizado com:

- A referência técnica do [requisito de aplicativo cliente aprovado](technical-reference.md#approved-client-app-requirement).
- The basic concepts of [Conditional Access in Azure Active Directory](overview.md).
- How to [configure a Conditional Access policy](app-based-mfa.md).
- The [migration of Conditional Access policies](best-practices.md#policy-migration).

## <a name="prerequisites"></a>Pré-requisitos

To create an app-based Conditional Access policy, you must have an Enterprise Mobility + Security or an Azure Active Directory premium subscription, and the users must be licensed for EMS or Azure AD. 

## <a name="exchange-online-policy"></a>Política do Exchange Online 

This scenario consists of an app-based Conditional Access policy for access to Exchange Online.

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

Any Intune app protection policies are activated at the time the access corporate data and may prompt the user to restart the application, use an additional PIN etc. (if configured for the application and platform).

### <a name="configuration"></a>Configuração 

**Step 1 - Configure an Azure AD Conditional Access policy for Exchange Online**

For the Conditional Access policy in this step, you need to configure the following components:

1. The **Name** of your Conditional Access policy.
1. **Users and groups**: Each Conditional Access policy must have at least one user or group selected.
1. **Aplicativos de nuvem:** como aplicativos de nuvem, você precisa selecionar o **Office 365 Exchange Online**.
1. **Condições:** como **Condições**, você precisa configurar **Plataformas de dispositivo** e **Aplicativos cliente**:
   1. Como **Plataformas de dispositivo**, selecione **Android** e **iOS**.
   1. Como **Aplicativos cliente (versão prévia)** , selecione **Aplicativos móveis e aplicativos da área de trabalho** e **Clientes de autenticação moderna**.
1. Como **Controles de acesso**, você precisa ter a opção **Exigir aplicativo cliente aprovado (versão prévia)** selecionada.

   ![Acesso condicional](./media/app-based-conditional-access/05.png)

**Step 2 - Configure an Azure AD Conditional Access policy for Exchange Online with Active Sync (EAS)**

For the Conditional Access policy in this step, you need to configure the following components:

1. The **Name** of your Conditional Access policy.
1. **Users and groups**: Each Conditional Access policy must have at least one user or group selected.
1. **Aplicativos de nuvem:** como aplicativos de nuvem, você precisa selecionar o **Office 365 Exchange Online**.
1. **Condições:** como **Condições**, você precisa configurar **Aplicativos cliente (versão prévia)** . 
   1. Como **Aplicativos cliente (versão prévia)** , selecione **Aplicativos móveis e aplicativos da área de trabalho** e **Clientes do Exchange ActiveSync**.
   1. Como **Controles de acesso**, você precisa ter a opção **Exigir aplicativo cliente aprovado (versão prévia)** selecionada.

      ![Acesso condicional](./media/app-based-conditional-access/05.png)

**Etapa 3 – configurar a política de proteção de aplicativo do Intune para aplicativos cliente Android e iOS**

Para obter mais informações, consulte [Proteger aplicativos e dados com o Microsoft Intune](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune).

## <a name="exchange-online-and-sharepoint-online-policy"></a>Política do Exchange Online e do SharePoint Online

This scenario consists of a Conditional Access with mobile app management policy for access to Exchange Online and SharePoint Online with approved apps.

### <a name="scenario-playbook"></a>Guia estratégico do cenário

Este cenário pressupõe que um usuário:

- Tenta usar o aplicativo SharePoint para se conectar e para exibir os sites corporativos desse usuário
- Attempts to sign in with the same credentials as the Outlook app credentials
- Não precisa registrar novamente e pode obter acesso aos recursos

### <a name="configuration"></a>Configuração

**Step 1 - Configure an Azure AD Conditional Access policy for Exchange Online and SharePoint Online**

For the Conditional Access policy in this step, you need to configure the following components:

1. The **Name** of your Conditional Access policy.
1. **Users and groups**: Each Conditional Access policy must have at least one user or group selected.
1. **Aplicativos de nuvem:** como aplicativos de nuvem, você precisa selecionar o **Office 365 Exchange Online** e o **Office 365 SharePoint Online**. 
1. **Condições:** como **Condições**, você precisa configurar **Plataformas de dispositivo** e **Aplicativos cliente**:
   1. Como **Plataformas de dispositivo**, selecione **Android** e **iOS**.
   1. Como **Aplicativos cliente (versão prévia)** , selecione **Aplicativos móveis e clientes da área de trabalho** e **Clientes de autenticação moderna**.
1. Como **Controles de acesso**, você precisa ter a opção **Exigir aplicativo cliente aprovado (versão prévia)** selecionada.

   ![Acesso condicional](./media/app-based-conditional-access/05.png)

**Step 2 - Configure an Azure AD Conditional Access policy for Exchange Online with Active Sync (EAS)**

For the Conditional Access policy in this step, you need to configure the following components:

1. The **Name** of your Conditional Access policy.
1. **Users and groups**: Each Conditional Access policy must have at least one user or group selected.
1. **Aplicativos de nuvem:** como aplicativos de nuvem, você precisa selecionar o **Office 365 Exchange Online**. Online 
1. **Condições:** como **Condições**, você precisa configurar **Aplicativos cliente**:
   1. Como **Aplicativos cliente (versão prévia)** , selecione **Aplicativos móveis e aplicativos da área de trabalho** e **Clientes do Exchange ActiveSync**.
   1. Como **Controles de acesso**, você precisa ter a opção **Exigir aplicativo cliente aprovado (versão prévia)** selecionada.

      ![Acesso condicional](./media/app-based-conditional-access/05.png)

**Etapa 3 – configurar a política de proteção de aplicativo do Intune para aplicativos cliente Android e iOS**

![Acesso condicional](./media/app-based-conditional-access/09.png)

Para obter mais informações, consulte [Proteger aplicativos e dados com o Microsoft Intune](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune).

## <a name="app-based-or-compliant-device-policy-for-exchange-online-and-sharepoint-online"></a>Política de dispositivo compatível ou com base no aplicativo para o Exchange Online e o SharePoint Online

This scenario consists of an app-based or compliant device Conditional Access policy for access to Exchange Online.

### <a name="scenario-playbook"></a>Guia estratégico do cenário

Este cenário pressupõe que:
 
- Some users are already enrolled (with or without corporate devices)
- Os usuários que não estão registrados com o Azure AD usando um aplicativo protegido por aplicativo precisam registrar um dispositivo para acessar os recursos
- Os usuários registrados usando o aplicativo protegido não precisam registrar novamente o dispositivo

### <a name="configuration"></a>Configuração

**Step 1 - Configure an Azure AD Conditional Access policy for Exchange Online and SharePoint Online**

For the Conditional Access policy in this step, you need to configure the following components:

1. The **Name** of your Conditional Access policy.
1. **Users and groups**: Each Conditional Access policy must have at least one user or group selected.
1. **Aplicativos de nuvem:** como aplicativos de nuvem, você precisa selecionar o **Office 365 Exchange Online** e o **Office 365 SharePoint Online**. 
1. **Condições:** como **Condições**, você precisa configurar **Plataformas de dispositivo** e **Aplicativos cliente**. 
   1. Como **Plataformas de dispositivo**, selecione **Android** e **iOS**.
   1. Como **Aplicativos cliente (versão prévia)** , selecione **Aplicativos móveis e clientes da área de trabalho** e **Clientes de autenticação moderna**.
1. Como **Controles de acesso**, você precisa ter o seguinte selecionado:
   - **Exigir que o dispositivo seja marcado como em conformidade**
   - **Exigir um aplicativo cliente aprovado (versão prévia)**
   - **Exigir um dos controles selecionados**   
 
      ![Acesso condicional](./media/app-based-conditional-access/11.png)

**Step 2 - Configure an Azure AD Conditional Access policy for Exchange Online with Active Sync (EAS)**

For the Conditional Access policy in this step, you need to configure the following components:

1. The **Name** of your Conditional Access policy.
1. **Users and groups**: Each Conditional Access policy must have at least one user or group selected.
1. **Aplicativos de nuvem:** como aplicativos de nuvem, você precisa selecionar o **Office 365 Exchange Online**. 
1. **Condições:** como **Condições**, você precisa configurar **Aplicativos cliente**. 
   1. Como **Aplicativos cliente (versão prévia)** , selecione **Aplicativos móveis e aplicativos da área de trabalho** e **Clientes do Exchange ActiveSync**.
1. Como **Controles de acesso**, você precisa ter a opção **Exigir aplicativo cliente aprovado (versão prévia)** selecionada.
 
   ![Acesso condicional](./media/app-based-conditional-access/11.png)

**Etapa 3 – configurar a política de proteção de aplicativo do Intune para aplicativos cliente Android e iOS**

![Acesso condicional](./media/app-based-conditional-access/09.png)

Para obter mais informações, consulte [Proteger aplicativos e dados com o Microsoft Intune](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune).

## <a name="app-based-and-compliant-device-policy-for-exchange-online-and-sharepoint-online"></a>Política de dispositivo compatível e com base no aplicativo para o Exchange Online e o SharePoint Online

This scenario consists of an app-based and compliant device Conditional Access policy for access to Exchange Online.

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

**Step 1 - Configure an Azure AD Conditional Access policy for Exchange Online and SharePoint Online**

For the Conditional Access policy in this step, you need to configure the following components:

1. The **Name** of your Conditional Access policy.
1. **Users and groups**: Each Conditional Access policy must have at least one user or group selected.
1. **Aplicativos de nuvem:** como aplicativos de nuvem, você precisa selecionar o **Office 365 Exchange Online** e o **Office 365 SharePoint Online**. 
1. **Condições:** como **Condições**, você precisa configurar **Plataformas de dispositivo** e **Aplicativos cliente**. 
   1. Como **Plataformas de dispositivo**, selecione **Android** e **iOS**.
   1. Como **Aplicativos cliente (versão prévia)** , selecione **Aplicativos móveis e aplicativos da área de trabalho** e **Clientes de autenticação moderna**.
1. Como **Controles de acesso**, você precisa ter o seguinte selecionado:
   - **Exigir que o dispositivo seja marcado como em conformidade**
   - **Exigir um aplicativo cliente aprovado (versão prévia)**
   - **Exigir todos os controles selecionados**   
 
      ![Acesso condicional](./media/app-based-conditional-access/13.png)

**Step 2 - Configure an Azure AD Conditional Access policy for Exchange Online with Active Sync (EAS)**

For the Conditional Access policy in this step, you need to configure the following components:

1. The **Name** of your Conditional Access policy.
1. **Users and groups**: Each Conditional Access policy must have at least one user or group selected.
1. **Aplicativos de nuvem:** como aplicativos de nuvem, você precisa selecionar o **Office 365 Exchange Online**. 
1. **Condições:** como **Condições**, você precisa configurar **Aplicativos cliente (versão prévia)** . 
   1. Como **Aplicativos cliente (versão prévia)** , selecione **Aplicativos móveis e aplicativos da área de trabalho** e **Clientes do Exchange ActiveSync**.

   ![Acesso condicional](./media/app-based-conditional-access/92.png)

1. Como **Controles de acesso**, você precisa ter o seguinte selecionado:
   - **Exigir que o dispositivo seja marcado como em conformidade**
   - **Exigir um aplicativo cliente aprovado (versão prévia)**
   - **Exigir todos os controles selecionados**   

**Etapa 3 – configurar a política de proteção de aplicativo do Intune para aplicativos cliente Android e iOS**

![Acesso condicional](./media/app-based-conditional-access/09.png)

Para obter mais informações, consulte [Proteger aplicativos e dados com o Microsoft Intune](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune).

## <a name="next-steps"></a>Próximos passos

Se você quiser saber como configurar uma política de Acesso Condicional, consulte [Exigir MFA para aplicativos específicos com Acesso Condicional do Azure Active Directory](app-based-mfa.md).

Se você estiver pronto para configurar políticas de acesso condicional para seu ambiente, confira as [melhores práticas para o acesso condicional no Azure Active Directory](best-practices.md). 
