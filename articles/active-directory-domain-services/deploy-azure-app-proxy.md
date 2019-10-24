---
title: Implantar Proxy de Aplicativo do AD do Azure para Azure AD Domain Services | Microsoft Docs
description: Usar o Proxy de Aplicativo do Azure AD nos domínios gerenciados do Azure Active Directory Domain Services
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/14/2019
ms.author: iainfou
ms.openlocfilehash: 80c3b2120a617e5c4c0f8de252b9436753fea011
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/22/2019
ms.locfileid: "72754397"
---
# <a name="deploy-azure-ad-application-proxy-on-an-azure-ad-domain-services-managed-domain"></a>Implantar o Proxy de Aplicativo do Azure AD em um domínio gerenciado do Azure AD Domain Services
O Proxy de Aplicativo do Active Directory (AD) do Azure o ajuda a dar suporte a funcionários remotos publicando aplicativos locais para serem acessados via Internet. Com o Azure AD Domain Services, agora você pode usar modelo lift-and-shift em aplicativos herdados executados localmente para os Serviços de Infraestrutura do Azure. Depois, publique esses aplicativos usando o Proxy de Aplicativo do Azure AD, a fim de fornecer acesso remoto seguro aos usuários em sua organização.

Se você for um novo usuário do Proxy de Aplicativo do Azure AD, aprenda mais sobre este recurso com o artigo: [Como fornecer acesso remoto seguro a aplicativos locais](../active-directory/manage-apps/application-proxy.md).

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="before-you-begin"></a>Antes de começar
Para executar as tarefas listadas neste artigo, você precisa do seguinte:

1. Uma **assinatura do Azure**válida.
2. Um **diretório do AD do Azure** - seja sincronizado com um diretório local ou com um diretório somente na nuvem.
3. Uma **licença de Azure ad Premium** é necessária para usar o proxy de aplicativo do AD do Azure.
4. **Serviços de Domínio do Azure AD** devem ser habilitados para o diretório do Azure AD. Se você ainda não tiver feito isso, execute todas as tarefas descritas no [guia de Introdução](tutorial-create-instance.md).

<br>

## <a name="task-1---enable-azure-ad-application-proxy-for-your-azure-ad-directory"></a>Tarefa 1: Habilitar o Proxy de Aplicativo do Azure AD para o diretório do Azure AD
Execute as etapas a seguir para habilitar o Proxy de Aplicativo do Azure AD para seu diretório do Azure AD.

1. Entre como administrador no [Portal do Azure](https://portal.azure.com).

2. Clique em **Azure Active Directory** para exibir a visão geral do diretório. Clique em **Aplicativos empresariais**.

3. Clique em **Proxy de aplicativo**.

4. Para baixar o conector, clique no botão **Conector**.

5. Na página de download, aceite os termos de licença e o contrato de privacidade e clique no botão **Baixar**.

    ![Confirme o download](./media/app-proxy/app-proxy-enabled-confirm-download.png)


## <a name="task-2---provision-domain-joined-windows-servers-to-deploy-the-azure-ad-application-proxy-connector"></a>Tarefa 2: Provisionar servidores Windows ingressados em domínio para implantar o conector do Proxy de Aplicativo do Azure AD
Você precisa de máquinas virtuais com Windows Server ingressadas em domínio nas quais pode instalar o conector do Proxy de Aplicativo do Azure AD. Para alguns aplicativos, você pode optar por provisionar vários servidores nos quais o conector está instalado. Essa opção de implantação fornece mais disponibilidade e ajuda a lidar com cargas mais pesadas de autenticação.

Provisione os servidores do conector na mesma rede virtual (ou uma rede virtual conectada/emparelhada), na qual você habilitou seu domínio gerenciado do Azure AD Domain Services. Da mesma forma, os servidores que hospedam os aplicativos publicados por meio do Proxy de Aplicativo precisam ser instalados na mesma rede virtual do Azure.

Para provisionar os servidores do conector, execute as tarefas descritas no artigo [Ingressar uma máquina virtual do Windows em um domínio gerenciado](active-directory-ds-admin-guide-join-windows-vm.md).


## <a name="task-3---install-and-register-the-azure-ad-application-proxy-connector"></a>Tarefa 3: Instalar e registrar o conector do Proxy de Aplicativo do Azure AD
Antes, você provisionou uma máquina virtual com Windows Server e ingressou no domínio gerenciado. Nesta tarefa, você instalará o conector do Proxy de Aplicativo do Azure AD nesta máquina virtual.

1. Copie o pacote de instalação do conector na VM na qual você instalou o conector do Proxy de Aplicativo Web do Azure AD.

2. Execute **AADApplicationProxyConnectorInstaller.exe** na máquina virtual. Aceite os termos de licença de software.

    ![Aceite os termos de instalação](./media/app-proxy/app-proxy-install-connector-terms.png)
3. Durante a instalação, você receberá uma solicitação para registrar o conector com o Proxy de Aplicativo de seu locatário do Azure AD.
   * Forneça suas **credenciais de administrador do aplicativo do Azure ad**. Seu locatário do administrador de aplicativos pode ser diferente de suas credenciais de Microsoft Azure.
   * A conta de administrador usada para registrar o conector deve pertencer ao mesmo diretório no qual você habilitou o serviço Proxy de Aplicativo. Por exemplo, se o domínio de locatário for contoso.com, o administrador deverá ser admin@contoso.com ou qualquer outro alias válido nesse domínio.
   * Se a Configuração de Segurança Aprimorada do Internet Explorer estiver ativada no servidor em que você estiver instalando o conector, a tela de registro poderá ser bloqueada. Siga as instruções na mensagem de erro para permitir o acesso. Certifique-se de que a Segurança Melhorada do Internet Explorer está desativada.
   * Se o registro do conector não for bem-sucedido, confira [Solucionar problemas de Proxy de Aplicativo](../active-directory/manage-apps/application-proxy-troubleshoot.md).

     ![Conector instalado](./media/app-proxy/app-proxy-connector-installed.png)
4. Para garantir o funcionamento correto do conector, execute a Solução de Problemas do Conector do Proxy de Aplicativo do Azure AD. O resultado da execução da solução de problemas deve ser um relatório de êxito.

    ![Êxito da solução de problemas](./media/app-proxy/app-proxy-connector-troubleshooter.png)
5. Você deverá ver o conector recém-instalado listado na página do Proxy de aplicativo em seu diretório do Azure AD.

    ![No portal do Azure, o conector instalado é exibido como disponível](./media/app-proxy/app-proxy-connector-page.png)

> [!NOTE]
> Escolha instalar os conectores em vários servidores para garantir a alta disponibilidade aos aplicativos de autenticação publicados por meio do Proxy de Aplicativo do Azure AD. Execute as mesmas etapas listadas acima para instalar o conector em outros servidores ingressados em seu domínio gerenciado.
>
>

## <a name="next-steps"></a>Próximas etapas
Você configurou o Proxy de Aplicativo do Azure AD e o integrou a seu domínio gerenciado do Azure AD Domain Services.

* **Migrar seus aplicativos para máquinas virtuais do Azure:** use o modelo lift-and-shift para migrar seus aplicativos de servidores locais para máquinas virtuais do Azure ingressadas em seu domínio gerenciado. Isso ajuda você a se livrar dos custos com infraestrutura gerados pela execução de servidores localmente.

* **Publicar aplicativos usando o Proxy de Aplicativo do Azure AD:** publique aplicativos em execução em suas máquinas virtuais do Azure usando o Proxy de Aplicativo do Azure AD. Para saber mais, confira [publicar aplicativos usando o Proxy de Aplicativo do Azure AD](../active-directory/manage-apps/application-proxy-publish-azure-portal.md)


## <a name="deployment-note---publish-iwa-integrated-windows-authentication-applications-using-azure-ad-application-proxy"></a>Nota sobre a implantação: publique aplicativos IWA (Autenticação Integrada do Windows) usando o Proxy de Aplicativo do Azure AD
Habilite o logon único para seus aplicativos usando a IWA (Autenticação Integrada do Windows) concedendo permissão aos Conectores de Proxy de Aplicativo para representar usuários e enviar e receber tokens em seu nome. Configure a KCD (Delegação restrita de Kerberos) para que o conector conceda as permissões necessárias para o acesso de recursos no domínio gerenciado. Use o mecanismo KCD baseado em recursos em domínios gerenciados para aumentar a segurança.


### <a name="enable-resource-based-kerberos-constrained-delegation-for-the-azure-ad-application-proxy-connector"></a>Habilitar a delegação restrita de Kerberos com base em recursos para o conector de Proxy de Aplicativo do Azure AD
O conector do Proxy de Aplicativo do Azure deve ser configurado para KCD (delegação restrita de Kerberos), para que possa representar usuários no domínio gerenciado. Em um domínio gerenciado do Azure AD Domain Services, você não tem privilégios de administrador de domínio. Portanto, **não é possível configurar um KCD tradicional no nível da conta em um domínio gerenciado**.

Use o KCD baseado em recursos conforme descrito neste [artigo](deploy-kcd.md).

> [!NOTE]
> Você precisará ser um membro do grupo "Administradores do controlador de domínio do AAD" para administrar o domínio gerenciado usando cmdlets do AD PowerShell.
>
>

Use o cmdlet Get-ADComputer do PowerShell para recuperar as configurações do computador no qual o conector de Proxy de Aplicativo do Azure AD está instalado.
```powershell
$ConnectorComputerAccount = Get-ADComputer -Identity contoso-proxy.contoso.com
```

Depois disso, use o cmdlet Set-ADComputer para configurar o KCD baseado em recursos para o servidor do recurso.
```powershell
Set-ADComputer contoso-resource.contoso.com -PrincipalsAllowedToDelegateToAccount $ConnectorComputerAccount
```

Se você tiver implantado vários conectores de Proxy de Aplicativo em seu domínio gerenciado, será necessário configurar o KCD baseado em recursos para cada instância do conector.


## <a name="related-content"></a>Conteúdo relacionado
* [Serviços de Domínio do Azure AD - Guia de Introdução](tutorial-create-instance.md)
* [Configurar a Delegação Restrita de Kerberos em um domínio gerenciado](deploy-kcd.md)
* [Visão geral da Delegação Restrita de Kerberos](https://technet.microsoft.com/library/jj553400.aspx)
