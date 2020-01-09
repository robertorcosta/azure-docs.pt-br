---
title: Controles de acesso no Azure Active Directory acesso condicional
description: Saiba como os controles de acesso no Azure Active Directory acesso condicional funcionam.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 12/20/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 342ec46aabafec975d780aa03fe75d7e3cf50497
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75424963"
---
# <a name="what-are-access-controls-in-azure-active-directory-conditional-access"></a>O que são controles de acesso no Azure Active Directory acesso condicional?

Com o [acesso condicional do Azure Active Directory (AD do Azure)](../active-directory-conditional-access-azure-portal.md), você pode controlar como os usuários autorizados acessam seus aplicativos de nuvem. Em uma política de acesso condicional, você define a resposta ("fazer isso") para o motivo do acionamento de sua política ("quando isso acontece").

![Control](./media/controls/10.png)

No contexto de acesso condicional,

- "**Quando isso acontece**" é chamado de **condições**
- "**Então faça isso**" é chamado de **controles de acesso**

A combinação de uma instrução Condition com seus controles representa uma política de acesso condicional.

![Control](./media/controls/61.png)

Cada controle é um requisito que deve ser atendido pela pessoa ou sistema que está entrando, ou uma restrição sobre que o usuário poderá fazer depois de entrar.

Há dois tipos de controle:

- **Controles de concessão** - para controle de acesso
- **Controles de sessão** - para restringir o acesso em uma sessão

Este tópico explica os vários controles que estão disponíveis no acesso condicional do Azure AD. 

## <a name="grant-controls"></a>Controles de concessão

Com controles de concessão, você pode bloquear o acesso completamente ou permitir o acesso com requisitos adicionais ao selecionar os controles desejados. Para vários controles, você pode exigir:

- Todos os controles adicionados devem ser preenchidos (*AND*)
- Um controle selecionado a ser preenchido (*OR*)

![Control](./media/controls/18.png)

### <a name="multi-factor-authentication"></a>Autenticação multifator

Você pode usar este controle para exigir autenticação multifator para acessar o aplicativo de nuvem especificado. Esse controle dá suporte aos seguintes provedores multifator:

- Autenticação Multifator do Azure
- Um provedor de autenticação multifator local, combinados com os serviços de Federação do Active Directory (AD FS).

Usar a autenticação multifator ajuda a proteger recursos para impedir que sejam acessados por um usuário não autorizado que obteve acesso às credenciais principais de um usuário válido.

### <a name="compliant-device"></a>Dispositivo em conformidade

Você pode configurar políticas de acesso condicional que são baseadas em dispositivo. O objetivo de uma política de acesso condicional com base em dispositivo é conceder acesso apenas aos aplicativos de nuvem selecionados de [dispositivos gerenciados](require-managed-devices.md). Exigir que um dispositivo seja marcado como compatível é uma opção que você tem para limitar o acesso aos dispositivos gerenciados. Um dispositivo pode ser marcado como compatível pelo Intune (para qualquer SO do dispositivo) ou pelo sistema de MDM de terceiros para dispositivos com Windows 10. Sistemas MDM de terceiros para tipos de dispositivo OS, exceto Windows 10, não são suportados. 

É necessário registrar o dispositivo no Azure AD, antes de poder marcá-lo como compatível. Para registrar um dispositivo, há três opções: 

- Dispositivos registrados no Azure AD
- Dispositivos adicionados ao Azure AD  
- Dispositivos adicionados ao Azure AD híbrido

Essas três opções são discutidas no artigo [o que é uma identidade de dispositivo?](../devices/overview.md)

Para obter mais informações, consulte [como exigir dispositivos gerenciados para acesso ao aplicativo de nuvem com acesso condicional](require-managed-devices.md).

### <a name="hybrid-azure-ad-joined-device"></a>Dispositivo adicionado ao Azure AD híbrido

Exigir um dispositivo ingressado no Azure AD híbrido é outra opção que você precisa configurar políticas de acesso condicional com base no dispositivo. Esse requisito se refere a desktops, laptops e tablets corporativos com Windows que ingressaram em um Active Directory local. Se essa opção for selecionada, sua política de acesso condicional concederá acesso a tentativas de acesso feitas com dispositivos que ingressaram em seu Active Directory local e seu Azure Active Directory. Os dispositivos Mac não dão suporte à junção híbrida do Azure AD.

Para obter mais informações, consulte [configurar Azure Active Directory políticas de acesso condicional com base no dispositivo](require-managed-devices.md).

### <a name="approved-client-app"></a>Aplicativo do cliente aprovado

Como seus funcionários usam dispositivos móveis para as tarefas pessoais e de trabalho, talvez você queira poder proteger os dados da empresa acessados por meio de dispositivos, mesmo no caso em que eles não são gerenciados por você.
Você pode usar [políticas de proteção do aplicativo Intune](https://docs.microsoft.com/intune/app-protection-policy) para ajudar a proteger os dados da empresa independentemente de qualquer solução de gerenciamento de dispositivo móvel (MDM).

Com aplicativos de cliente aprovados, você pode exigir que um aplicativo cliente tente acessar seus aplicativos de nuvem para oferecer suporte a [políticas de proteção do aplicativo Intune](https://docs.microsoft.com/intune/app-protection-policy). Por exemplo, você pode restringir o acesso ao Exchange Online para o aplicativo do Outlook. Uma política de acesso condicional que requer aplicativos cliente aprovados também é conhecida como [política de acesso condicional baseada em aplicativo](app-based-conditional-access.md). Para obter uma lista de aplicativos cliente aprovados e com suporte, veja [requisito de aplicativo cliente aprovado](technical-reference.md#approved-client-app-requirement).

### <a name="app-protection-policy-preview"></a>Política de proteção de aplicativo (visualização)

Como seus funcionários usam dispositivos móveis para as tarefas pessoais e de trabalho, talvez você queira poder proteger os dados da empresa acessados por meio de dispositivos, mesmo no caso em que eles não são gerenciados por você.
Você pode usar [políticas de proteção do aplicativo Intune](https://docs.microsoft.com/intune/app-protection-policy) para ajudar a proteger os dados da empresa independentemente de qualquer solução de gerenciamento de dispositivo móvel (MDM).

Com a política de proteção de aplicativo, você pode limitar o acesso a aplicativos cliente que relataram ao Azure AD que receberam [políticas de proteção de aplicativo do Intune](https://docs.microsoft.com/intune/app-protection-policy). Por exemplo, você pode restringir o acesso ao Exchange Online para o aplicativo do Outlook que tem uma política de proteção de aplicativo do Intune. Uma política de acesso condicional que requer a política de proteção de aplicativo também é conhecida como [política de acesso condicional com base na proteção do aplicativo](app-protection-based-conditional-access.md). 

Seu dispositivo deve ser registrado no Azure AD antes que um aplicativo possa ser marcado como protegido pela política.

Para obter uma lista de aplicativos cliente protegidos por política com suporte, consulte [requisito de política de proteção de aplicativo](technical-reference.md#app-protection-policy-requirement).

### <a name="terms-of-use"></a>Termos de uso

Você pode exigir que um usuário em seu locatário concorde com os termos de uso antes de obter acesso a um recurso. Como administrador, você pode configurar e personalizar termos de uso, carregando um documento PDF. Se um usuário estiver escopo desse controle, ele receberá acesso a um aplicativo somente se concordar com os termos de uso.

## <a name="custom-controls-preview"></a>Controles personalizados (versão prévia)

Os controles personalizados são uma funcionalidade do Azure Active Directory Premium P1 Edition. Ao usar controles personalizados, seus usuários são redirecionais para um serviço compatível a fim de atender a requisitos adicionais fora do Azure Active Directory. Para satisfazer esse controle, o navegador do usuário é redirecionado para um serviço externo, realiza qualquer autenticação ou atividades de validação necessárias e, em seguida, é redirecionado ao Azure Active Directory. Azure Active Directory verifica a resposta e, se o usuário foi autenticado ou validado com êxito, o usuário continua no fluxo de acesso condicional.

Esses controles permitem o uso de determinados serviços externos ou personalizados como controles de acesso condicional e geralmente estendem os recursos de acesso condicional.

Provedores que atualmente oferecem um serviço compatível incluem:

- [Duo Security](https://duo.com/docs/azure-ca)
- [Entrust Datacard](https://www.entrustdatacard.com/products/authentication/intellitrust)
- [GSMA](https://mobileconnect.io/azure/)
- [Ping Identity](https://documentation.pingidentity.com/pingid/pingidAdminGuide/index.shtml#pid_c_AzureADIntegration.html)
- [RSA](https://community.rsa.com/docs/DOC-81278)
- [SecureAuth](https://docs.secureauth.com/pages/viewpage.action?pageId=47238992#)
- [Silverfort](https://www.silverfort.io/company/using-silverfort-mfa-with-azure-active-directory/)
- [Symantec VIP](https://help.symantec.com/home/VIP_Integrate_with_Azure_AD)
- [Thales (Gemalto)](https://resources.eu.safenetid.com/help/AzureMFA/Azure_Help/Index.htm)
- [Trusona](https://www.trusona.com/docs/azure-ad-integration-guide)

Para obter mais informações sobre esses serviços, entre em contato diretamente com os provedores.

### <a name="creating-custom-controls"></a>Criando controles personalizados

Para criar um controle personalizado, você deve entrar em contato com o provedor que você deseja utilizar. Cada provedor que não é da Microsoft tem seu próprio processo e requisitos para inscrever-se, assinar ou se tornar uma parte do serviço e indicar que você deseja integrar com o acesso condicional. Nesse ponto, o provedor lhe fornecerá um bloco de dados no formato JSON. Esses dados permitem que o provedor e o acesso condicional funcionem juntos para seu locatário, cria o novo controle e define como o acesso condicional pode informar se os usuários executaram com êxito a verificação com o provedor.

Os controles personalizados não podem ser usados com a automação da proteção de identidade que requer autenticação multifator ou para elevar funções no PIM (Privileged Identity Manager).

Copie os dados JSON e, em seguida, cole-os na caixa de texto relacionada. Não faça alterações ao JSON, a menos que você compreenda explicitamente a alteração que você está fazendo. Fazer qualquer alteração pode interromper a conexão entre o provedor e a Microsoft e potencialmente bloquear você e seus usuários do acesso às respectivas contas.

A opção para criar um controle personalizado está na seção **gerenciar** da página de **acesso condicional** .

![Control](./media/controls/82.png)

Clicar em **Novo controle personalizado** abrirá uma folha com uma caixa de texto para os dados JSON do seu controle.  

![Control](./media/controls/81.png)

### <a name="deleting-custom-controls"></a>Excluindo controles personalizados

Para excluir um controle personalizado, primeiro você deve verificar se ele não está sendo usado em nenhuma política de acesso condicional. Depois de concluído:

1. Vá para a lista de Controles personalizados
1. Clique em...  
1. Selecione **Excluir**.

### <a name="editing-custom-controls"></a>Editando controles personalizados

Para editar um controle personalizado, você deve excluir o controle atual e criar um novo controle com as informações atualizadas.

## <a name="session-controls"></a>Controles de sessão

Os controles de sessão permitem a limitação da experiência dentro de um aplicativo na nuvem. Os controles de sessão são impostos por aplicativos de nuvem e contam com informações adicionais sobre a sessão fornecidas pelo Azure AD para o aplicativo.

![Control](./media/controls/31.png)

### <a name="use-app-enforced-restrictions"></a>Usar restrições de aplicativo impostas

Você pode usar esse controle para exigir que o Azure AD passe informações sobre o dispositivo para os aplicativos na nuvem selecionados. As informações do dispositivo permitem que os aplicativos na nuvem saibam se uma conexão é iniciada a partir de um dispositivo compatível ou associado a um domínio. Esse controle suporta apenas o SharePoint Online e o Exchange Online como aplicativos de nuvem selecionados. Quando selecionado, o aplicativo na nuvem usa as informações do dispositivo para fornecer aos usuários, dependendo do estado do dispositivo, uma experiência limitada ou completa.

Para obter mais informações, consulte:

- [Ativando acesso limitado com o SharePoint Online](https://aka.ms/spolimitedaccessdocs)
- [Ativando acesso limitado com o Exchange Online](https://aka.ms/owalimitedaccess)

## <a name="next-steps"></a>Próximos passos

- Se você quiser saber como configurar uma política de Acesso Condicional, consulte [Exigir MFA para aplicativos específicos com Acesso Condicional do Azure Active Directory](app-based-mfa.md).
- Se você estiver pronto para configurar políticas de acesso condicional para seu ambiente, confira as [melhores práticas para o acesso condicional no Azure Active Directory](best-practices.md).
