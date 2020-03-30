---
title: Integre-se ao Proxy de aplicativo AD em um servidor NDES
titleSuffix: Azure Active Directory
description: Orientação sobre a implantação de um Proxy de aplicativo de diretório ativo do Azure para proteger seu servidor NDES.
services: active-directory
author: CelesteDG
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/17/2020
ms.author: baselden
ms.reviewer: mimart
ms.openlocfilehash: 4ccd8834671725ace72497391090f81eb197ad6a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77032251"
---
# <a name="integrate-with-azure-ad-application-proxy-on-a-network-device-enrollment-service-ndes-server"></a>Integre-se ao Proxy de aplicativo Azure AD em um servidor NDES (Network Device Registration Service, serviço de registro de dispositivos de rede)

O Azure Active Directory (AD) Application Proxy permite que você publique aplicativos dentro de sua rede. Esses aplicativos são como sites SharePoint, Microsoft Outlook Web App e outros aplicativos da Web. Ele também fornece acesso seguro aos usuários fora da sua rede via Azure.

Se você é novo no Azure AD Application Proxy e deseja saber mais, consulte [Acesso remoto a aplicativos locais através do Proxy de aplicativo AD do Azure.](application-proxy.md)

O Proxy de aplicativo Azure AD é construído no Azure. Ele oferece uma enorme quantidade de largura de banda de rede e infra-estrutura de servidor para uma melhor proteção contra ataques de negação de serviço distribuídos (DDOS) e excelente disponibilidade. Além disso, não há necessidade de abrir portas de firewall externas para sua rede local e nenhum servidor DMZ é necessário. Todo o tráfego é originado de entrada. Para obter uma lista completa de portas de saída, consulte [Tutorial: Adicione um aplicativo local para acesso remoto através do Proxy de aplicativos no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application#prepare-your-on-premises-environment).

> O Azure AD Application Proxy é um recurso disponível apenas se você estiver usando as edições Premium ou Basic do Azure Active Directory. Para obter mais informações, consulte [Preço do Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/). 
> Se você tiver licenças EMS (Enterprise Mobility Suite, suíte de mobilidade corporativa), você será elegível para usar esta solução.
> O conector Proxy do aplicativo Azure AD só é instalado no Windows Server 2012 R2 ou posterior. Isso também é um requisito do servidor NDES.

## <a name="install-and-register-the-connector-on-the-ndes-server"></a>Instale e registre o conector no servidor NDES

1. Entre no [portal do Azure](https://portal.azure.com/) como um administrador de aplicativos do diretório que usa o Proxy de Aplicativo. Por exemplo, se o domínio de locatário for contoso.com, o administrador deverá ser admin@contoso.com ou qualquer outro alias de administrador nesse domínio.
1. Escolha o nome de usuário no canto superior direito. Verifique se você está conectado a um diretório que usa o Proxy de Aplicativo. Se for necessário alterar diretórios, escolha **Mudar diretório** e escolha um diretório que usa o Proxy de Aplicativo.
1. No painel de navegação à esquerda, escolha **Azure Active Directory**.
1. Em **Gerenciar**, escolha **Proxy de Aplicativo**.
1. Escolha **Baixar o serviço do conector**.

    ![Baixe o serviço do conector para ver os Termos de serviço](./media/active-directory-app-proxy-protect-ndes/application-proxy-download-connector-service.png)

1. Leia os Termos de serviço. Quando estiver pronto, escolha **Aceitar termos e baixar**.
1. Copie o arquivo de configuração do conector proxy do aplicativo Azure AD para o servidor NDES. 
   > Você pode instalar o conector em qualquer servidor da sua rede corporativa com acesso ao NDES. Você não precisa instalá-lo no próprio servidor NDES.
1. Execute o arquivo de configuração, como *AADApplicationProxyConnectorInstaller.exe*. Aceite os termos de licença de software.
1. Durante a instalação, você é solicitado a registrar o conector com o Proxy do aplicativo em seu diretório Azure AD.
   * Forneça as credenciais para um administrador global ou de aplicativos em seu diretório Azure AD. As credenciais globais ou administradoras de aplicativos do Azure AD podem ser diferentes das credenciais do Azure no portal.

        > [!NOTE]
        > A conta global ou administradora de aplicativos usada para registrar o conector deve pertencer ao mesmo diretório onde você habilita o serviço Proxy de aplicativo.
        >
        > Por exemplo, se o domínio Azure AD for *contoso.com,* o administrador global/aplicativo deve ser `admin@contoso.com` ou outro alias válido nesse domínio.

   * Se a configuração de segurança aprimorada do Internet Explorer estiver ligada para o servidor onde você instala o conector, a tela de registro pode ser bloqueada. Para permitir o acesso, siga as instruções na mensagem de erro ou desligue a Segurança Aprimorada do Internet Explorer durante o processo de instalação.
   * Se o registro do conector falhar, consulte [Proxy do aplicativo 'Solução de problemas'.](application-proxy-troubleshoot.md)
1. No final da configuração, uma nota é mostrada para ambientes com um proxy de saída. Para configurar o conector proxy do aplicativo Azure AD para funcionar através do `C:\Program Files\Microsoft AAD App Proxy connector\ConfigureOutBoundProxy.ps1`proxy de saída, execute o script fornecido, como .
1. Na página proxy do aplicativo no portal Azure, o novo conector é listado com um status de *Ativo,* conforme mostrado no exemplo a seguir:

    ![O novo conector proxy do aplicativo Azure AD mostrado como ativo no portal Azure](./media/active-directory-app-proxy-protect-ndes/connected-app-proxy.png)

    > [!NOTE]
    > Para fornecer alta disponibilidade para aplicativos autenticados através do Proxy do aplicativo Azure AD, você pode instalar conectores em várias VMs. Repita as mesmas etapas listadas na seção anterior para instalar o conector em outros servidores unidos ao domínio gerenciado pelo Azure AD DS.

1. Após a instalação bem sucedida, volte para o portal Azure.

1. Selecione **Aplicativos empresariais**.

   ![garantir que você está engajando as partes interessadas certas](./media/active-directory-app-proxy-protect-ndes/azure-active-directory-enterprise-applications.png)

1. Selecione **+Novo aplicativo**e selecione **aplicativo on-premises**. 

1. No **aplicativo Adicionar seu próprio local,** configure os seguintes campos:

   * **Nome**: Digite um nome para o aplicativo.
   * **Url interno**: Digite a URL/FQDN interna do servidor NDES no qual você instalou o conector.
   * **Pré Autenticação**: Selecione **Passagem**. Não é possível usar qualquer forma de pré-autenticação. O protocolo usado para solicitações de certificados (SCEP) não fornece tal opção.
   * Copie a **URL externa** fornecida para sua área de transferência.

1. Selecione **+Adicionar** para salvar sua aplicação.

1. Teste se você pode acessar seu servidor NDES através do proxy do aplicativo Azure AD colando o link copiado na etapa 10 em um navegador. Você deve ver uma página de boas-vindas padrão do IIS.

1. Como teste final, adicione o caminho *mscep.dll* à URL existente que você colou na etapa anterior:

   https://scep-test93635307549127448334.msappproxy.net/certsrv/mscep/mscep.dll

1. Você deve ver um **erro HTTP 403 – Resposta proibida.**

1. Altere a URL do NDES fornecida (via Microsoft Intune) para dispositivos, essa alteração pode estar no Centro de Configuração do Ponto Final do Microsoft ou no Intune Cloud.

   * Para Centro de Configuração, vá até o Ponto de Registro de Certificado (CRP) e ajuste a URL. Esta URL é o que os dispositivos chamam e apresentam seu desafio.
   * Para intune Cloud Only, também conhecido como Intune Standalone, edite ou crie uma nova política SCEP e adicione a nova URL.

## <a name="next-steps"></a>Próximas etapas

Com o Proxy de aplicativo Azure AD integrado ao NDES, publique aplicativos para os usuários acessarem. Para obter mais informações, consulte [publicar aplicativos usando o Proxy de aplicativo Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application).
