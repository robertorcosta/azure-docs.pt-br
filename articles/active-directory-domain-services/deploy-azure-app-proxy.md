---
title: Implantar proxy de aplicativo Azure AD para serviços de domínio Azure AD | Microsoft Docs
description: Saiba como fornecer acesso seguro a aplicativos internos para trabalhadores remotos, implantando e configurando o Proxy de aplicativo do Diretório Ativo do Azure em um domínio gerenciado do Azure Active Directory Domain Services
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 11/6/2019
ms.author: iainfou
ms.openlocfilehash: c6e4e6a45fbbeab64184d8ae4b0684ba055d7735
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77613972"
---
# <a name="deploy-azure-ad-application-proxy-for-secure-access-to-internal-applications-in-an-azure-ad-domain-services-managed-domain"></a>Implantar proxy de aplicativo AD do Azure para acesso seguro a aplicativos internos em um domínio gerenciado do Azure AD Domain Services

Com o Azure AD Domain Services (Azure AD DS), você pode levantar e mudar aplicativos legados que executam no local no Azure. O Proxy de aplicativos do Azure Active Directory (AD) ajuda você a suportar trabalhadores remotos publicando com segurança esses aplicativos internos parte de um domínio gerenciado pelo Azure AD DS para que eles possam ser acessados pela internet.

Se você é novo no Proxy do aplicativo Azure AD e quer saber mais, veja [como fornecer acesso remoto seguro a aplicativos internos](../active-directory/manage-apps/application-proxy.md).

Este artigo mostra como criar e configurar um conector Proxy de aplicativo Azure AD para fornecer acesso seguro a aplicativos em um domínio gerenciado pelo Azure AD DS.

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="before-you-begin"></a>Antes de começar

Para concluir este artigo, você precisa dos seguintes recursos e privilégios:

* Uma assinatura ativa do Azure.
    * Se você não tiver uma assinatura do Azure, [crie uma conta](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Um locatário do Azure Active Directory associado com a assinatura, sincronizado com um diretório local ou somente em nuvem.
    * Se necessário, [crie um locatário do Azure Active Directory][create-azure-ad-tenant] ou [associe uma assinatura do Azure à sua conta][associate-azure-ad-tenant].
    * Uma **licença Azure AD Premium** é necessária para usar o Proxy de aplicativo Azure AD.
* Um domínio gerenciado do Azure Active Directory Domain Services habilitado e configurado no locatário do Azure AD.
    * Se necessário, [crie e configure uma instância do Azure Active Directory Domain Services][create-azure-ad-ds-instance].

## <a name="create-a-domain-joined-windows-vm"></a>Crie uma VM windows com adesão ao domínio

Para direcionar o tráfego para aplicativos em execução no ambiente, você instala o componente de conector proxy do aplicativo Azure AD. Este conector Proxy do aplicativo Azure AD deve ser instalado em máquinas virtuais do Windows Server (VM) que se juntam ao domínio gerenciado pelo Azure AD DS. Para alguns aplicativos, você pode implantar vários servidores que cada um tem o conector instalado. Essa opção de implantação fornece mais disponibilidade e ajuda a lidar com cargas mais pesadas de autenticação.

A VM que executa o conector Proxy do aplicativo Azure AD deve estar na mesma, ou em uma rede virtual com peered na qual você habilitou o Azure AD DS. As VMs que hospedam os aplicativos que você publica usando o Proxy de aplicativo também devem ser implantadas na mesma rede virtual do Azure.

Para criar uma VM para o conector proxy do aplicativo Azure AD, complete as seguintes etapas:

1. [Criar uma UO personalizada](create-ou.md). Você pode delegar permissões para gerenciar esse OU personalizado para usuários dentro do domínio gerenciado pelo Azure AD DS. As VMs para Proxy de aplicativo Ad Azure e que executam seus aplicativos devem fazer parte do OU personalizado, não do Ou padrão *da AAD DC Computers* OU.
1. [Junte-se ao domínio das máquinas virtuais][create-join-windows-vm], tanto a que executa o conector Proxy do aplicativo Azure AD, quanto as que executam seus aplicativos, para o domínio gerenciado pelo Azure AD DS. Crie essas contas de computador no OU personalizado da etapa anterior.

## <a name="download-the-azure-ad-application-proxy-connector"></a>Baixe o conector proxy do aplicativo Azure AD

Execute as seguintes etapas para baixar o conector Proxy do aplicativo Azure AD. O arquivo de configuração que você baixa é copiado para o seu Proxy VM do aplicativo na próxima seção.

1. Faça login no [portal Do Zure](https://portal.azure.com) com uma conta de usuário que tenha permissões *de administrador corporativo* no Azure AD.
1. Procure e selecione **o Azure Active Directory** na parte superior do portal e escolha **aplicativos Corporativos**.
1. Selecione proxy de **aplicativo** no menu do lado esquerdo. Para criar seu primeiro conector e ativar o App Proxy, selecione o link para **baixar um conector**.
1. Na página de download, aceite os termos de licença e o contrato de privacidade e selecione **Aceitar termos & Download**.

    ![Baixe o conector proxy do aplicativo Azure AD](./media/app-proxy/download-app-proxy-connector.png)

## <a name="install-and-register-the-azure-ad-application-proxy-connector"></a>Instale e registre o conector proxy do aplicativo Azure AD

Com uma VM pronta para ser usada como conector proxy do aplicativo Azure AD, agora copie e execute o arquivo de configuração baixado do portal Azure.

1. Copie o arquivo de configuração do conector proxy do aplicativo Azure AD para sua VM.
1. Execute o arquivo de configuração, como *AADApplicationProxyConnectorInstaller.exe*. Aceite os termos de licença de software.
1. Durante a instalação, você é solicitado a registrar o conector com o Proxy do aplicativo em seu diretório Azure AD.
   * Forneça as credenciais para um administrador global em seu diretório Azure AD. As credenciais de administrador global do Azure AD podem ser diferentes das credenciais do Azure no portal

        > [!NOTE]
        > A conta de administrador global usada para registrar o conector deve pertencer ao mesmo diretório onde você habilita o serviço Proxy de aplicativo.
        >
        > Por exemplo, se o domínio Azure AD for `admin@aaddscontoso.com` *aaddscontoso.com,* o administrador global deve ser ou outro alias válido nesse domínio.

   * Se a configuração de segurança aprimorada do Internet Explorer estiver ligada para a VM onde você instala o conector, a tela de registro pode ser bloqueada. Para permitir o acesso, siga as instruções na mensagem de erro ou desligue a Segurança Aprimorada do Internet Explorer durante o processo de instalação.
   * Se o registro do conector falhar, consulte [Proxy do aplicativo 'Solução de problemas'.](../active-directory/manage-apps/application-proxy-troubleshoot.md)
1. No final da configuração, uma nota é mostrada para ambientes com um proxy de saída. Para configurar o conector proxy do aplicativo Azure AD para funcionar através do `C:\Program Files\Microsoft AAD App Proxy connector\ConfigureOutBoundProxy.ps1`proxy de saída, execute o script fornecido, como .
1. Na página proxy do aplicativo no portal Azure, o novo conector é listado com um status de *Ativo,* conforme mostrado no exemplo a seguir:

    ![O novo conector proxy do aplicativo Azure AD mostrado como ativo no portal Azure](./media/app-proxy/connected-app-proxy.png)

> [!NOTE]
> Para fornecer alta disponibilidade para aplicativos autenticados através do Proxy do aplicativo Azure AD, você pode instalar conectores em várias VMs. Repita as mesmas etapas listadas na seção anterior para instalar o conector em outros servidores unidos ao domínio gerenciado pelo Azure AD DS.

## <a name="enable-resource-based-kerberos-constrained-delegation"></a>Habilitar a delegação restrita kerberos baseada em recursos

Se você quiser usar o login único em seus aplicativos usando iWA (Integrated Windows Authentication, autenticação integrada do Windows), conceda aos conectores proxy do aplicativo Azure AD permissão para se passar por usuários e enviar e receber tokens em seu nome. Para conceder essas permissões, você configura a delegação restrita Kerberos (KCD) para que o conector acesse recursos no domínio gerenciado pelo Azure AD DS. Como você não tem privilégios de administrador de domínio em um domínio gerenciado pelo Azure AD DS, o KCD tradicional de nível de conta não pode ser configurado em um domínio gerenciado. Em vez disso, use KCD baseado em recursos.

Para obter mais informações, consulte [Configure Kerberos restrita delegação (KCD) em Azure Active Directory Domain Services](deploy-kcd.md).

> [!NOTE]
> Você deve estar conectado a uma conta de usuário que seja um membro do grupo de *administradores do Azure AD DC* no seu inquilino Azure AD para executar os seguintes cmdlets powershell.
>
> As contas do computador para o conector proxy do aplicativo VM e vMs de aplicativo devem estar em um OU personalizado onde você tem permissões para configurar KCD baseado em recursos. Você não pode configurar o KCD baseado em recursos para uma conta de computador no contêiner *AAD DC Computers* incorporado.

Use o [Get-ADComputer][Get-ADComputer] para recuperar as configurações do computador no qual o conector Proxy do aplicativo Azure AD está instalado. A partir de sua VM de gerenciamento de domínio e logado como conta de usuário que é um membro do grupo de *administradores Azure AD DC,* execute os cmdlets a seguir.

O exemplo a seguir obtém informações sobre a conta do computador chamada *appproxy.aaddscontoso.com*. Forneça seu próprio nome de computador para o Proxy Proxy do aplicativo Azure AD configurado nas etapas anteriores.

```powershell
$ImpersonatingAccount = Get-ADComputer -Identity appproxy.aaddscontoso.com
```

Para cada servidor de aplicativo que executa os aplicativos por trás do Azure AD Application Proxy, use o cmdlet [Set-ADComputer][Set-ADComputer] PowerShell para configurar o KCD baseado em recursos. No exemplo a seguir, o conector Proxy do aplicativo Azure AD recebe permissões para usar o *computador appserver.aaddscontoso.com:*

```powershell
Set-ADComputer appserver.aaddscontoso.com -PrincipalsAllowedToDelegateToAccount $ImpersonatingAccount
```

Se você implantar vários conectores proxy de aplicativo Azure AD, você deve configurar o KCD baseado em recursos para cada instância do conector.

## <a name="next-steps"></a>Próximas etapas

Com o Proxy de aplicativo Azure AD integrado com o Azure AD DS, publique aplicativos para os usuários acessarem. Para obter mais informações, consulte [publicar aplicativos usando o Proxy de aplicativo Azure AD](../active-directory/manage-apps/application-proxy-publish-azure-portal.md).

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[create-join-windows-vm]: join-windows-vm.md
[azure-bastion]: ../bastion/bastion-create-host-portal.md
[Get-ADComputer]: /powershell/module/addsadministration/get-adcomputer
[Set-ADComputer]: /powershell/module/addsadministration/set-adcomputer
