---
title: Access controls in Azure Active Directory Conditional Access
description: Learn how access controls in Azure Active Directory Conditional Access work.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: cac92da744b3d5b7aeaa325c7cc564a3d7e2abdd
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/22/2019
ms.locfileid: "74380803"
---
# <a name="what-are-access-controls-in-azure-active-directory-conditional-access"></a>What are access controls in Azure Active Directory Conditional Access?

With [Azure Active Directory (Azure AD) Conditional Access](../active-directory-conditional-access-azure-portal.md), you can control how authorized users access your cloud apps. In a Conditional Access policy, you define the response ("do this") to the reason for triggering your policy ("when this happens").

![Controle](./media/controls/10.png)

In the context of Conditional Access,

- "**Quando isso acontece**" é chamado de **condições**
- "**Então faça isso**" é chamado de **controles de acesso**

The combination of a condition statement with your controls represents a Conditional Access policy.

![Controle](./media/controls/61.png)

Cada controle é um requisito que deve ser atendido pela pessoa ou sistema que está entrando, ou uma restrição sobre que o usuário poderá fazer depois de entrar.

Há dois tipos de controle:

- **Controles de concessão** - para controle de acesso
- **Controles de sessão** - para restringir o acesso em uma sessão

This topic explains the various controls that are available in Azure AD Conditional Access. 

## <a name="grant-controls"></a>Controles de concessão

Com controles de concessão, você pode bloquear o acesso completamente ou permitir o acesso com requisitos adicionais ao selecionar os controles desejados. Para vários controles, você pode exigir:

- Todos os controles adicionados devem ser preenchidos (*AND*)
- Um controle selecionado a ser preenchido (*OR*)

![Controle](./media/controls/18.png)

### <a name="multi-factor-authentication"></a>Autenticação multifator

Você pode usar este controle para exigir autenticação multifator para acessar o aplicativo de nuvem especificado. Esse controle dá suporte aos seguintes provedores multifator:

- Autenticação Multifator do Azure
- Um provedor de autenticação multifator local, combinados com os serviços de Federação do Active Directory (AD FS).

Usar a autenticação multifator ajuda a proteger recursos para impedir que sejam acessados por um usuário não autorizado que obteve acesso às credenciais principais de um usuário válido.

### <a name="compliant-device"></a>Dispositivo em conformidade

You can configure Conditional Access policies that are device-based. The objective of a device-based Conditional Access policy is to only grant access to the selected cloud apps from [managed devices](require-managed-devices.md). Exigir que um dispositivo seja marcado como compatível é uma opção que você tem para limitar o acesso aos dispositivos gerenciados. Um dispositivo pode ser marcado como compatível pelo Intune (para qualquer SO do dispositivo) ou pelo sistema de MDM de terceiros para dispositivos com Windows 10. Sistemas MDM de terceiros para tipos de dispositivo OS, exceto Windows 10, não são suportados. 

É necessário registrar o dispositivo no Azure AD, antes de poder marcá-lo como compatível. Para registrar um dispositivo, há três opções: 

- Dispositivos registrados no Azure AD
- Dispositivos adicionados ao Azure AD  
- Dispositivos adicionados ao Azure AD híbrido

These three options are discussed in the article [What is a device identity?](../devices/overview.md)

For more information, see [how to require managed devices for cloud app access with Conditional Access](require-managed-devices.md).

### <a name="hybrid-azure-ad-joined-device"></a>Dispositivo adicionado ao Azure AD híbrido

Requiring a Hybrid Azure AD joined device is another option you have to configure device-based Conditional Access policies. Esse requisito se refere a desktops, laptops e tablets corporativos com Windows que ingressaram em um Active Directory local. If this option is selected, your Conditional Access policy grants access to access attempts made with devices that are joined to your on-premises Active Directory and your Azure Active Directory.  

For more information, see [set up Azure Active Directory device-based Conditional Access policies](require-managed-devices.md).

### <a name="approved-client-app"></a>Aplicativo do cliente aprovado

Como seus funcionários usam dispositivos móveis para as tarefas pessoais e de trabalho, talvez você queira poder proteger os dados da empresa acessados por meio de dispositivos, mesmo no caso em que eles não são gerenciados por você.
Você pode usar [políticas de proteção do aplicativo Intune](https://docs.microsoft.com/intune/app-protection-policy) para ajudar a proteger os dados da empresa independentemente de qualquer solução de gerenciamento de dispositivo móvel (MDM).

Com aplicativos de cliente aprovados, você pode exigir que um aplicativo cliente tente acessar seus aplicativos de nuvem para oferecer suporte a [políticas de proteção do aplicativo Intune](https://docs.microsoft.com/intune/app-protection-policy). Por exemplo, você pode restringir o acesso ao Exchange Online para o aplicativo do Outlook. A Conditional Access policy that requires approved client apps is  also known as [app-based Conditional Access policy](app-based-conditional-access.md). Para obter uma lista de aplicativos cliente aprovados e com suporte, veja [requisito de aplicativo cliente aprovado](technical-reference.md#approved-client-app-requirement).

### <a name="app-protection-policy-preview"></a>App protection policy (preview)

Como seus funcionários usam dispositivos móveis para as tarefas pessoais e de trabalho, talvez você queira poder proteger os dados da empresa acessados por meio de dispositivos, mesmo no caso em que eles não são gerenciados por você.
Você pode usar [políticas de proteção do aplicativo Intune](https://docs.microsoft.com/intune/app-protection-policy) para ajudar a proteger os dados da empresa independentemente de qualquer solução de gerenciamento de dispositivo móvel (MDM).

With app protection policy, you can limit access to client applications that have reported to Azure AD has having received [Intune app protection policies](https://docs.microsoft.com/intune/app-protection-policy). For example, you can restrict access to Exchange Online to the Outlook app that has an Intune app protection policy. A Conditional Access policy that requires app protection policy is also known as [app protection-based Conditional Access policy](app-protection-based-conditional-access.md). 

Your device must be registered to Azure AD before an application can be marked as policy protected.

For a list of supported policy protected client apps, see [app protection policy requirement](technical-reference.md#app-protection-policy-requirement).

### <a name="terms-of-use"></a>Termos de uso

Você pode exigir que um usuário em seu locatário concorde com os termos de uso antes de obter acesso a um recurso. Como administrador, você pode configurar e personalizar termos de uso, carregando um documento PDF. Se um usuário estiver escopo desse controle, ele receberá acesso a um aplicativo somente se concordar com os termos de uso.

## <a name="custom-controls-preview"></a>Controles personalizados (versão prévia)

Os controles personalizados são uma funcionalidade do Azure Active Directory Premium P1 Edition. Ao usar controles personalizados, seus usuários são redirecionais para um serviço compatível a fim de atender a requisitos adicionais fora do Azure Active Directory. Para satisfazer esse controle, o navegador do usuário é redirecionado para um serviço externo, realiza qualquer autenticação ou atividades de validação necessárias e, em seguida, é redirecionado ao Azure Active Directory. Azure Active Directory verifies the response and, if the user was successfully authenticated or validated, the user continues in the Conditional Access flow.

These controls allow the use of certain external or custom services as Conditional Access controls, and generally extend the capabilities of Conditional Access.

Provedores que atualmente oferecem um serviço compatível incluem:

- [Duo Security](https://duo.com/docs/azure-ca)
- [Entrust Datacard](https://www.entrustdatacard.com/products/authentication/intellitrust)
- [GSMA](https://mobileconnect.io/azure/)
- [Ping Identity](https://documentation.pingidentity.com/pingid/pingidAdminGuide/index.shtml#pid_c_AzureADIntegration.html)
- RSA
- [SecureAuth](https://docs.secureauth.com/pages/viewpage.action?pageId=47238992#)
- [Silverfort](https://www.silverfort.io/company/using-silverfort-mfa-with-azure-active-directory/)
- [Symantec VIP](https://help.symantec.com/home/VIP_Integrate_with_Azure_AD)
- [Thales (Gemalto)](https://resources.eu.safenetid.com/help/AzureMFA/Azure_Help/Index.htm)
- [Trusona](https://www.trusona.com/docs/azure-ad-integration-guide)

Para obter mais informações sobre esses serviços, entre em contato diretamente com os provedores.

### <a name="creating-custom-controls"></a>Criando controles personalizados

Para criar um controle personalizado, você deve entrar em contato com o provedor que você deseja utilizar. Each non-Microsoft provider has its own process and requirements to sign up, subscribe, or otherwise become a part of the service, and to indicate that you wish to integrate with Conditional Access. Nesse ponto, o provedor lhe fornecerá um bloco de dados no formato JSON. This data allows the provider and Conditional Access to work together for your tenant, creates the new control and defines how Conditional Access can tell if your users have successfully performed verification with the provider.

Custom controls cannot be used with Identity Protection's automation requiring multi-factor authentication or to elevate roles in Privileged Identity Manager (PIM).

Copie os dados JSON e, em seguida, cole-os na caixa de texto relacionada. Não faça alterações ao JSON, a menos que você compreenda explicitamente a alteração que você está fazendo. Fazer qualquer alteração pode interromper a conexão entre o provedor e a Microsoft e potencialmente bloquear você e seus usuários do acesso às respectivas contas.

The option to create a custom control is in the **Manage** section of the **Conditional Access** page.

![Controle](./media/controls/82.png)

Clicar em **Novo controle personalizado** abrirá uma folha com uma caixa de texto para os dados JSON do seu controle.  

![Controle](./media/controls/81.png)

### <a name="deleting-custom-controls"></a>Excluindo controles personalizados

To delete a custom control, you must first ensure that it isn’t being used in any Conditional Access policy. Depois de concluído:

1. Vá para a lista de Controles personalizados
1. Clique em...  
1. Selecione **Excluir**.

### <a name="editing-custom-controls"></a>Editando controles personalizados

Para editar um controle personalizado, você deve excluir o controle atual e criar um novo controle com as informações atualizadas.

## <a name="session-controls"></a>Controles de sessão

Os controles de sessão permitem a limitação da experiência dentro de um aplicativo na nuvem. Os controles de sessão são impostos por aplicativos de nuvem e contam com informações adicionais sobre a sessão fornecidas pelo Azure AD para o aplicativo.

![Controle](./media/controls/31.png)

### <a name="use-app-enforced-restrictions"></a>Usar restrições de aplicativo impostas

Você pode usar esse controle para exigir que o Azure AD passe informações sobre o dispositivo para os aplicativos na nuvem selecionados. As informações do dispositivo permitem que os aplicativos na nuvem saibam se uma conexão é iniciada a partir de um dispositivo compatível ou associado a um domínio. Esse controle suporta apenas o SharePoint Online e o Exchange Online como aplicativos de nuvem selecionados. Quando selecionado, o aplicativo na nuvem usa as informações do dispositivo para fornecer aos usuários, dependendo do estado do dispositivo, uma experiência limitada ou completa.

Para obter mais informações, consulte:

- [Ativando acesso limitado com o SharePoint Online](https://aka.ms/spolimitedaccessdocs)
- [Ativando acesso limitado com o Exchange Online](https://aka.ms/owalimitedaccess)

## <a name="next-steps"></a>Próximos passos

- Se você quiser saber como configurar uma política de Acesso Condicional, consulte [Exigir MFA para aplicativos específicos com Acesso Condicional do Azure Active Directory](app-based-mfa.md).
- Se você estiver pronto para configurar políticas de acesso condicional para seu ambiente, confira as [melhores práticas para o acesso condicional no Azure Active Directory](best-practices.md).
